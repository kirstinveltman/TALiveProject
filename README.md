# The Tech Academy - LiveProject

At the completion of the main coursework at The Tech Academy, all students participate in a 2 week "Live Project." This gives students experience in working with other programmers in a work environment.

## Project Overview

The project that I worked on was for new functionality on The Tech Academy website. The Job Placement Director was having students track the jobs they applied for with Google Sheets. As this is not an efficient way of tracking student application progress, we were building functionality to be able to acommodate the Job Placement Director's needs. We were working with C#, MVC (code first), ASP.NET, CSS, HTML, Razor, and Entity Framework.

## Stories

### External Links for URLs Saved in the Database

Each student of The Tech Academy has both a Portfolio page and LinkedIn page. The URLs for these pages are saved in the database. I needed to find a way to take the URL data for each student and display them in an external link on multiple site pages. Since we were already using Razor syntax as the method of displaying each student's data, I created a Helper Method that would return the data as a string, and then used that in the hyperlink tag on the cshtml page.

**Helper method in order to allow @Html.ExternalLink:**
```
public static class LinkHelper
{
    public static string ExternalLink(this HtmlHelper helper, string url)
    {
        return String.Format("{0}", url);
    }

}
```
**Redirecting external links to URLs saved in the db (for ~ 3 different index pages):**
```
<td>
	<a href="http://@Html.ExternalLink(item.JPLinkedIn)" target="_blank">LinkedIn Profile</a>
</td>
<td>
	<a href="http://@Html.ExternalLink(item.JPPortfolio)" target="_blank">Student Portfolio</a>
</td>
```

### Saving URLs Without Protocol

In order to have student LinkedIn and Portfolio pages saved in the database, we needed to create an input method on the cshtml page, as well as the backend logic to strip the protocol from the URL. I utilized Regex to test if there was a protocol on what the student entered, and if so, strip it. Otherwise, it would save the URL to the database.

**Controller logic on Create to save URL without protocol:
```
// Test to see if URL provided has http or https in it. If not, then save as is. If it does, strip the protocol.
string linkedInUrl = jPStudent.JPLinkedIn;
Regex regexLI = new Regex(@"^http(s)?://");
Match matchLI = regexLI.Match(linkedInUrl);
if (matchLI.Success)
{
    Uri linkedInNewURL = new Uri(linkedInUrl);
    string linkedInOutput = linkedInNewURL.Host + linkedInNewURL.PathAndQuery;
    jPStudent.JPLinkedIn = linkedInOutput;
}
else
{
    jPStudent.JPLinkedIn = linkedInUrl;
}

// Test to see if URL provided has http or https in it. If not, then save as is. If it does, strip the protocol.
string portfolioUrl = jPStudent.JPPortfolio;
Regex regexP = new Regex(@"^http(s)?://");
Match matchP = regexP.Match(portfolioUrl);
if (matchP.Success)
{
    Uri portfolioNewURL = new Uri(portfolioUrl);
    string portfolioOutput = portfolioNewURL.Host + portfolioNewURL.PathAndQuery;
    jPStudent.JPPortfolio = portfolioOutput;
}
else
{
    jPStudent.JPPortfolio = portfolioUrl;
}
```
**HTML for input box:**
```
<div class="form-group">
    @Html.LabelFor(model => model.JPLinkedIn, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-5">
        @Html.EditorFor(model => model.JPLinkedIn, new { htmlAttributes = new { @class = "form-control" } })
        @Html.ValidationMessageFor(model => model.JPLinkedIn, "", new { @class = "text-danger" })
    </div>
</div>

<div class="form-group">
    @Html.LabelFor(model => model.JPPortfolio, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-5">
        @Html.EditorFor(model => model.JPPortfolio, new { htmlAttributes = new { @class = "form-control" } })
        @Html.ValidationMessageFor(model => model.JPPortfolio, "", new { @class = "text-danger" })
    </div>
</div>
```

### Sharing LinkedIn URL

One of the functions of the site was to ask the students if they would like to share their LinkedIn page with other students, for networking. As this was already set up in the database to be a boolean function, I added the radio button function and styling to the page where students were asked this question.

```
<div class="form-group row">
    <label class="col-sm-2 col-form-label pt-0"></label>
    <div class="col-sm-9 ">
        <small class="form-text" style="font-size: 14px; font-family: Montserrat;">Are you okay sharing your LinkedIn profile with fellow students who might wish to contact you for the purpose of networking?</small>
        <div class="form-check">
            @Html.RadioButtonFor(model => model.JPContact, true, new { Checked = "checked" }) Yes
        </div>
        <div class="form-check">
            @Html.RadioButtonFor(model => model.JPContact, false) No
        </div>
    </div>
</div>
```

### Creating Data Strings via the Models Pages

This is a code first MVC project, and because we were modifying what data needed to be saved in the database, we would need to add and delete data strings in the Models pages for the project. I ended up doing this about 8 times. Here's an example:

```
[DisplayName("Company Careers Page")]
public string JPCareersPage { get; set; }
```

### Modify Search Functions

The story was a requesting to modify the search functions for a particular page to include the Company City and Company State for the location of an application. I modified an existing search string to include these parameters:

```
if (!String.IsNullOrEmpty(searchString))
{
    hires = hires.Where(s => s.JPStudent.JPName.Contains(searchString)||s.JPCompanyName.Contains(searchString)||s.JPJobCategory.ToString().Contains(searchString) || s.JPCompanyCity.Contains(searchString) || s.JPCompanyState.ToString().Contains(searchString) || s.JPSalary.ToString().Contains(searchString));
}
```

### Create New Index Page

We added functionality for View Models, so we needed a new Index page that would display the content. I added the page and then modified the HTML to display the content requested.

[Index.cshtml](https://github.com/kirstinveltman/TALiveProject/blob/master/Index.cshtml)

### Change Layout Page for New Menu Functions

This was an old story that changed the layout for one of the menu items relating to Job Placement. The request was to have 2 categories "Student Dashboard" and "Job Placement Dashboard" and for each of those to have multiple linked items below it. I mapped all of the new links, then added styling for the categories (as each of the linked items had inherited styling).

```
<li>
    <a href="#">Job Placement</a>
    <ul class="sub-menu">
        <li style="color:white; font-weight:900; font-size: 1em; text-shadow: 1px 1px #000000;">Student Dashboard</li>
        <li>@Html.ActionLink("Create an Account", "Create", "JPStudents")</li>
        <li>@Html.ActionLink("Applications", "Create", "JPApplications")</li>
        <li>@Html.ActionLink("Offer", "Create", "JPHires")</li>
        <li style="color:white; font-weight:900; font-size: 1em; text-shadow: 1px 1px #000000">Job Placement Dashboard</li>
        <li>@Html.ActionLink("Students", "Index", "JPStudents")</li>
        <li>@Html.ActionLink("Applications", "Index", "JPApplications")</li>
        <li>@Html.ActionLink("Offer", "Index", "JPHires")</li>
    </ul>
</li>
```
**Screenshot of the result**
![alt text](https://github.com/kirstinveltman/TALiveProject/blob/master/layout_result.png "Layout Result")

### Save DateTime on User Creation

The Job Placement Director wanted to know the DateTime that each application was created, so I modified the Controller to save that data on creation.

```
if (ModelState.IsValid)
{
    jPHire.JPHireId = Guid.NewGuid();
    jPHire.JPHireDate = DateTime.Now;
    db.JPHires.Add(jPHire);
    db.SaveChanges();
    return RedirectToAction("Index");
}
```

### Export Data on Demand to CSV

The Job Placement Director wanted to be able to export a list of student emails on demand from a button on the web page. This needed to be a CSV file. I researched the best way to do this and found that it was better to have the file created by a stream of the data instead of saving a file locally and then accessing it. I created a method in the Controller that would return a file with each of the student's emails.

```
public ActionResult Result()
{
    var emails = db.JPStudents.Select(x => x.JPEmail).ToList();
    string csv = string.Empty;
    foreach (var email in emails)
    {
        csv += email;
        csv += "\r\n";
    }

    return File(new System.Text.UTF8Encoding().GetBytes(csv), "text/csv", "StudentEmails.csv");
}
```

Then I created the button for the cshtml page that would call that Controller method.

```
<div style="margin-left: 50px">
    @Html.ActionLink("Export Student Email Addresses", "Result", null, new { @class = "btn btn-primary" })
</div>
```

### Display Number of Total Applications and Applications This Week

The story requested to display the total number of applications that a student had entered total, and then also how many they had done in the last week. As this was two separate database calls, I needed to do a join of the two in the Controller, then pass it to the View Model for this page, and then display it to the Index page. One of the issues I ran into was that Lambda expressions don't like DateTime, so I created a variable for calculating DateTime for within with last week and used it in the Lambda expression.

**Controller:**

```
public ViewResult Index()
{
    List<JPStudentRundown> jPStudentRundownList = new List<JPStudentRundown>();

    foreach (var student in db.JPStudents)
    {
        var applicationCount = db.JPApplications.Where(a => a.JPStudentId == student.JPStudentId).Count();
        var dateCriteria = DateTime.Now.AddDays(-7);
        var thisWeek = db.JPApplications.Where(a => a.JPStudentId == student.JPStudentId && a.JPApplicationDate >= dateCriteria ).Count();
        var jPStudent = new JPStudentRundown(student, applicationCount);
        jPStudentRundownList.Add(jPStudent);
    }

    return View(jPStudentRundownList);
}
```

**Index page:**

```
<th>
    Total Applications
    @*@Html.DisplayNameFor(model => model.totalApplications)*@
</th>
<th>
    Total Applications This Week
    @*@Html.DisplayNameFor(model => model.totalApplicationsThisWeek)*@
</th>
```

### Join Two Database Tables When Displaying on the Index Page

The City and State for each company application entered into the database were saved into separate columns. The request was for these to be displayed together, and for the states to display as their two letter initial. There was an additional request to include Canadian Provinces and Territories.

I first added Display Names for all US States and Canadian Provinces and Territories in the Enum that held this data.

```
[Display(Name = "AL")]
Alabama,
```

I then researched how to display an Enum display's name. I found code and logic from other sources, but ended up having to test and modify things in order to implement a solution that would work in our application.

I found code from the solution [here](https://stackoverflow.com/questions/13099834/how-to-get-the-display-name-attribute-of-an-enum-member-via-mvc-razor-code?rq=1) that would get the Display Name from the Enum.

```
public static class EnumExtensions
{
    public static string GetDisplayName(this Enum enumValue)
    {
        return enumValue.GetType()
               .GetMember(enumValue.ToString())
               .First()
               .GetCustomAttribute<DisplayAttribute>()
               .GetName();
    }
}
```

I then added the two new Enum files that are found in [this](https://www.codeproject.com/Articles/776908/Dealing-with-Enum-in-MVC) article:

(https://github.com/kirstinveltman/TALiveProject/blob/master/enum_templates.png "Enum files")

Finally, I modified the Controller to pass the GetDisplayName data because there was already functions to order the data that is displayed on the Index page:

```
case "CompanyState":
    hires = hires.OrderBy(s => s.JPCompanyState.GetDisplayName());
    break;
case "companyState_desc":
    hires = hires.OrderByDescending(s => s.JPCompanyState.GetDisplayName());
    break;
```

**The result:**

(https://github.com/kirstinveltman/TALiveProject/blob/master/enum_result.png "City and State")


## Conclusion

I gained a lot of knowledge from working on this project about how MVC, Entity Framework, and Razor works, as well as expanded my knowledge of C#, LINQ, and Lambda Expressions, and grew a lot of confidence in myself that I can figure out tasks by research, cobbling stuff together, and experimenting. Especially that persistance will pay off in the end.



