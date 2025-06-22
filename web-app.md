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
![Screenshot of the spreadsheet's URL.](media/spreadsheet-url.png)<br>
3. `var headers = dataValues[2]`, set the index of where the JSON should read header names. Because in our spreadsheet we have 2 rows of contextual information, we want the JSON to start on row 3 to find column headers.<br>
4. `var rows = [],` the script will loop over all rows to add them to the JSON.<br>

The last step is launching the Apps Script web app:

<iframe src="https://drive.google.com/file/d/1h3vF1022Ksv5TrI7_gnhYYw1FcAdhnL5/preview" width="640" height="480" allow="autoplay"></iframe>

::: callout
### Keep the URL handy!

At the end of the video, you see that a red box appears around the Apps Script web app URL. This URL will act as our API endpoint and enable our discovery system to import all the data from our Google Sheet in JSON. Save this URL. You can also copy this URL into your browser, hit return, and you'll see all our spreadsheet data represented in JSON.
:::

## Connect your Website and Database
In this section we will take the API endpoint, which we created with Apps Script, and connect it to our website. We’ll then display that data on our website.

To get started, download `webapp`, which contains the starting files for our website. Your file structure will look like this:

```
–webapp
	–app.js
	–index.html
	–style.css
```

Open index.html in the browser and you will see a basic structure for our discovery system. I’ve already given you some basic styling. We won’t be looking at style.css during this lesson, but feel free to modify it after. 

From top to bottom: we have our language selection buttons, which we will use in a later episode to translate our page. Then we have our search bar, search button, and refresh button. We will use these in the next episode to enable users to search through the research data by with keywords. Last, in the blank white space is where we will populate our website with our research data.

![Basic discovery interface.](media/basic-discovery-system.png)

Now open the `webapp` folder in your preferred Integrated development environment (IDE). Let’s take a look at `app.js`, which will drive our site’s functionality. Right now, we only have a road map of how we will build our system:

```app.js
// Define API endpoint and DOM elements
const googleSheet = '';


// Get Data


// Filter Data


// Display Data

```

In `app.js`, we have a variable `googleSheet`. Let’s set this to the URL of the Apps Script web app by copying the URL between the single quotes. 

```app.js
const googleSheet = 'https://script.google.com/macros/s/....';
```

Now we need to have `app.js` request that our webapp send our site the JSON data. We will do this by creating our first function, `getData()`.
1. Create an object called apiData where we will store the incoming data.

```app.js
let apiData = [];
```

2. Create an async function that will make a fetch request to our Google Sheet.

```app.js
// Get data
let apiData = [];


async function getData(url) {
   const response = await fetch(url);
   const data = await response.json();
   apiData = data;
}
```

The `getData` function will use an API endpoint (the URL of our Apps Script web app) to fetch data and save it in a object called `response`. Next, we will format the response as JSON in an object called `data`. Lastly, we assign that formatted data from the fetch request as our `apiData` object.

A unique attribute of JavaScript are promises. Promises are objects that represents the eventual completion (or failure) of an asynchronous operation. Promises typically do not happen as soon as a page loads, but take some time. In our case, we assign the async keyword before declaring the getData function because we know that the request for the Google Sheet data will take some time to reach our webpage. Similarly, the await keyword is used when we make the fetch request and format our fetch response in JSON because we are anticipating that the data will not arrive when the page loads.

3. As a result of using promises, it is best practice too use the try/catch error handling block in our function in case there is some error retrieving the data. Our final getData() function should look like this:

```app.js
// Get data
let apiData = [];


async function getData(url) {
 try {
   const response = await fetch(url);
   const data = await response.json();
   apiData = data;
 } catch (error) {
   window.alert(error.message);
 }
}
```

4. Lets test to make sure we are getting the data from our Google Sheet. We can log our incoming data to the console. Right below line 7, let’s add `console.log(apiData);` Our full `app.js` should look like this:

```app.js
// Define API endpoint and DOM elements
const googleSheet = 'https://script.google.com/macros/s/...';

// Get data
let apiData = [];

async function getData(url) {
 try {
   const response = await fetch(url);
   const data = await response.json();
   apiData = data;
   console.log(apiData); //Test if data fetch is working correctly
 } catch (error) {
   window.alert(error.message);
 }
};

getData(googleSheet);
```

5. We now need to link our `app.js` file and our `index.html`. Open up `index.html` and just before the end body tag, link the `app.js` file:

```index.html
   </main>
<script src="app.js"></script>
</body>
</html>
```

6. Open `index.html` in the browser (I suggest Chrome) and open developer tools. Then click on the console tab and you should see your data in an array:

<iframe src="https://drive.google.com/file/d/1haD32ZBJpX2MTLqcu2fG4IJ0swM5Z5yQ/preview" width="640" height="480" allow="autoplay"></iframe>

::: callout
### Examine the JSON Object

Success! Lets look at this data to get a sense of what is going on. Let’s expand the first item in the array:

```console
0:
	Assuntos_em_Portugues: "Povos indígenas; Ambientalismo"
	Countries: "Brasil"
	Institutional_Hosts: "Instituto Socioambiental "
	Languages: "Português"
	Materias_en_Espanol: "Pueblos indígenas; Ambientalismo"
	Resource_Title: "Acervo Digital sobre Povos Indígenas, Populações Tradicionais e Meio Ambiente"
	Resource_Types: "Audiovisual Materials; Books; Texts (Other); Visual Materials"
	Subjects_in_English: "Indigenous peoples; Environmentalism"
	Summary: "\"O ISA possui um vasto acervo relativo a povos indígenas, populações tradicionais e meio ambiente, formado desde 1974, compreendendo diversos tipos de materiais arquivísticos, audiovisuais, bibliográficos (artigos, livros, dissertações e teses), e notícias de jornais.  O acervo está indexado por etnias, categorias de população tradicional (caiçaras, quilombolas, seringueiros), unidades de conservação, terras indígenas, biomas, bacias, temas (biodiversidade, educação e saúde indígena, energia, estradas, etnografia, florestas, mudanças climáticas, recursos hídricos, recursos minerais, política socioambiental, entre outros), sub-temas e palavras-chave.\""
	Time_Coverage: "20th century; 21st century"
	URL: "https://acervo.socioambiental.org/"
```

JSON works in key-value format. This format will be the same for every entry in our data. All the keys are exactly the column headings in our Google Sheet. This is very useful because we can call specific pieces of data about a resource in our data table by using the column headings. The values, presented within quotes, is the data per row of our data. So here we are look at all the data, for the first row in our table. So, for example, if we want to display the title of a resource in our data table, we can ask JavaScript to show us the Resource_Title for a specific row.
:::

## Display your Data on the Site
