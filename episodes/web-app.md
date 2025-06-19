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

| Functional Requirement    | Solution |
| -------- | ------- |
| A free, intuitive, and collaborative database solution that can be easily edited by multiple volunteers without prior database knowledge.  | Google Sheets is a free, cloud-based spreadsheet solution that is intuitive to use, supports real-time collaboration by multiple users, and does not require extensive database knowledge.    |
| Free web hosting services to store and access website files. | GitHub Pages allows you to easily publish static websites directly from your GitHub repositories and use custom URL.     |
| The client-side technology should be open-source and avoid the use of front-end frameworks, which have a steep learning curve and can dissuade collaboration among those unfamiliar with a given framework.    | Vanilla JavaScript is the core programming language of the web and is open-source. It provides for collaboration and easy maintenance as it is framework independent.    |
| The user interface should allow users to search through the data using keywords and present resources in an attractive, easy-to-read format that includes links to the original resources.    | JavaScript can make API calls, filter data, and display data on a webpage by injecting HTML into the site at run time.    |
| The website must be translatable.    | JavaScript can create a translation table inside of an object to switch the site’s text content.    |

With this solution, the website data is edited and stored using Google Sheets. The added benefit is using Google’s security via its login credentials and you can also manage access of users. Then we use JavaScript to get the data from the spreadsheet and display it on our website. The JavaScript file that does that data manipulation, as well as the HTML and CSS files that give structure and style to our site, all live in a GitHub repository and are served to the public via their GitHub Pages service.

:::::::::::::::::::::::::::::::::::::::: challenge
### Best Practices
Based on the functional requirement assessment created above, name three best practices for writing effective functional requirements.

::::::::::::::::::::::::::::::::::::::::::::::::::
