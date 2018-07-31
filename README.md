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

We added functionality for View Models



