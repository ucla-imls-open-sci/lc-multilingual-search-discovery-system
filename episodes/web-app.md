---
title: "Building the Search Web App"
teaching: 45
exercises: 3
---

::::::::::::::::::::::::::::::::::::::: objectives

- Establish functional requirements for a web application.
- Transform a Google Sheet into a simple API endpoint with Google App Script.
- Build three JavaScript functions that filter and display your Google Sheet data on a website.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Based on my data, what are the functional requirements for a simple search and discovery web app?
- What is an API endpoint?
- How can I use the JavaScript methods .get(), .filter(), and .map() to retrieve my data, filter my data, and display my data on the web app.

::::::::::::::::::::::::::::::::::::::::::::::::::

In this part we will create a single page website that displays a search and discovery system for data on a Google Sheet using JavaScript.

## Establish Functional Requirements
In any technical project, it's essential to establish clear functional requirements before diving into the technical details. This ensures that the team stays focused on the core objectives of the project, rather than getting sidetracked by fancy features or bells and whistles.

During the development of the LACLI project, we arrived at a series of functional requirements. Many of which are shared by digital projects in the humanities and social sciences:

|     Functional Requirement    |    Solution    |
| :--------: | :-------: |
| A free, intuitive, and collaborative database solution that can be easily edited by multiple volunteers without prior database knowledge.  | Google Sheets is a free, cloud-based spreadsheet solution that is intuitive to use, supports real-time collaboration by multiple users, and does not require extensive database knowledge.    |
| Free web hosting services to store and access website files. | GitHub Pages allows you to easily publish static websites directly from your GitHub repositories and use custom URL.     |
| The client-side technology should be open-source and avoid the use of front-end frameworks, which have a steep learning curve and can dissuade collaboration among those unfamiliar with a given framework.    | Vanilla JavaScript is the core programming language of the web and is open-source. It provides for collaboration and easy maintenance as it is framework independent.    |
| The user interface should allow users to search through the data using keywords and present resources in an attractive, easy-to-read format that includes links to the original resources.    | JavaScript can make API calls, filter data, and display data on a webpage by injecting HTML into the site at run time.    |
| The website must be translatable.    | JavaScript can create a translation table inside of an object to switch the site’s text content.    |

With this solution, the website data is edited and stored using Google Sheets. The added benefit is that Google already has an entire security infrastructure: you can set permission of who can view, comment, or edit files in Google Drive. Then we use JavaScript to get the data from the spreadsheet and display it on our website. The JavaScript file that does that data manipulation, as well as the HTML and CSS files that give structure and style to our site, all live in a GitHub repository and are served to the public via their GitHub Pages service.

:::::::::::::::::::::::::::::::::::::::: challenge
### Write Your Own
Based on the functional requirement assessment above, create your own two column table and list the functional requirements for your project on one side. As you progress through this lesson, note what suggestions provided here could be solutions to your specific functional requirements.

::::::::::::::::::::::::::::::::::::::::::::::::::

## Transform a Google Sheet into an API Endpoint

The first technological hurtle is the fact that a Google Sheet is not automatically set up like a database. We will do this in three steps:
1. Create a Google App Script file that uses the Google Sheets API.
2. Write a script in the file that wraps all data up in a JSON.
3. Launch our script as a web app, which creates a unique API endpoint for our data and will permit our website to get that data and bring it to the user’s browser.

An API is a set of protocols that delivers data to a website. We want our site to receive the data in our spreadsheet as JSON (JavaScript Object Notation), which is a text-based data format to transmit data objects between a web server and a client-side application. When we talk about server-side, we are talking about operations that happen in the database on the servers. When we talk about client-side, we are talking about operations that occur in the user’s browser.

To activate the Google Sheet API and transform our Spreadsheet into JSON data requires creating a Google App Script file. In the Google Drive folder where you copied lacli-sample-data create a Google App Script File and name it Convert Sheet to JSON. When you open it, add the Google Sheets API under Services:

<iframe src="https://drive.google.com/file/d/1WScfIWv2PFTf4IoY_Z01r3qIUa194Muk/preview" width="640" height="480" allow="autoplay"></iframe>

Now that the Sheets API is set up, we will instruct it to wrap up our spreadsheet data in JSON. First, in the text editing window of Google App Script, delete the default function:

```javascript
function myFunction() {
 }
```

We will replace it with the following code:

```javascript
function doGet(request) {
  var spreadsheetId = '....'; // Replace with Google Sheet ID
  var sheetName = 'Main'; // Replace with the sheet you want to retrieve data from
  // make sure the advanced sheets service is enabled for this script
  var dataValues = Sheets.Spreadsheets.Values.get(spreadsheetId, sheetName).values;
  
  // Change the index from 0 to 2 to use row 3 as column headings
  var headers = dataValues[2];
  
  var rows = [];
  for (var i = 3; i < dataValues.length; i++) { // Start from row 4, as row 3 is the column headings
    var row = {};
    for (var j = 0; j < headers.length; j++) {
      row[headers[j]] = dataValues[i][j];
    }
    rows.push(row);
  }
  
  var output = JSON.stringify(rows);
  return ContentService.createTextOutput(output).setMimeType(ContentService.MimeType.JSON);
}
```

Let's take this code step by step:<br>
1. `function doGet(request)` is a stock function in Google App Script that handles a GET request made to your script's web app URL.<br>
2. Your spreadsheetid is available in the URL for the spreadsheet. Copy the string of alphanumeric characters in the URL of your spreadsheet:<br>
![Screenshot of the spreadsheet's URL.](spreadsheet-url.png)<br>
3. `var headers = dataValues[2]`, set the index of where the JSON should read header names. Because in our spreadsheet we have 2 rows of contextual information, we want the JSON to start on row 3 to find column headers.<br>
4. `var rows = [],` the script will loop over all rows to add them to the JSON.<br>

The last step is launching the web app:

<iframe src="https://drive.google.com/file/d/1h3vF1022Ksv5TrI7_gnhYYw1FcAdhnL5/preview" width="640" height="480" allow="autoplay"></iframe>

::: callout
### Keep the URL handy!

At the end of the video, you see that a red box appears around the Webapp URL. This URL will act as our API endpoint and enable our discovery system to import all the data from our Google Sheet in JSON. Take a moment to copy this URL into your browser. You’ll see that now all our spreadsheet data is represented in JSON.
:::

## Connect your Website and Database
In this section we will take the API endpoint we created in episode 2.2 and connect it to our website. We’ll then display that data on our website.

To get started, download `webapp`, which contains the starting files for our website. Your file structure will look like this:

```
–webapp
	–app.js
	–index.html
	–style.css
```

Open index.html in the browser and you will see a basic structure for our discovery system. I’ve already given you some basic styling. We won’t be looking at style.css during this lesson, but feel free to modify it after. 

From top to bottom: we have our language selection buttons, which we will use in a later episode to translate our page. Then we have our search bar, search button, and refresh button. We will use these in the next episode to enable users to search through the research data by with keywords. Last, in the blank white space is where we will populate our website with our research data.

