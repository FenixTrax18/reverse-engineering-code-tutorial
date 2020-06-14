# Reverse Engineering Code Tutorial

# Table of Contents
1. [Flowchart](#Flowchart)
2. [config](#Config)
3. [config.json](#Config.json)
4. [middleware](#Middleware)
5. [isAuthenticated.js](#IsAuthenticated.js)
6. [passport.js](#Passport.js)
7. [models](#Models)
8. [index.js](#Index.js)
9. [user.js](#User.js)
10. [package.json](#Package.json)
11. [public](#Public)
12. [js](#Js)
13. [login.js](#Login.js)
14. [members.js](#Members.js)
15. [signup.js](#Signup.js)
16. [login.html](#Login.html)
17. [members.html](#Members.html)
18. [signup.html](#Signup.html)
19. [stylesheets](#Stylesheets)
20. [style.css](#Style.css)
21. [routes](#Usage)
22. [api-routes.js](#Api-routes.js)
23. [html-routes.js](#Html-routes.js)
24. [server.js](#Server.js)

## Flowchart
The following diagram shows the file structure of the codebase:

```
.
├── config
│   ├── config.json
│   ├── middleware
│   │	└── isAuthenticated.js
│   └── passport.js
│
├── models
│   ├── index.js
│   └── user.js
│ 
├── package.json
│
├── public
│   ├── js
│   │	 ├── login.js
│   │	 ├── members.js
│   │	 └── signup.js
│   ├── login.html
│   ├── members.html
│   ├── signup.html
│   └── stylesheets
│   	 └── style.css
│
├── routes
│   ├── api-routes.js
│   └── html-routes.js
│
└── server.js
```

The following sections provide descriptions of the responsibilities of each file and their purpose:

### Config
This folder contains configuration files.

#### Config.json
The config.json file tells the CLI how to connect to the database. This file provides configurations to the database for each type of environment including the development environment, the test environment, and the production environment.

#### Middleware
This folder contains middleware files.

##### IsAuthenticated.js
This is middleware that is injected into every route that requires authentication. If a user is not logged in, then this middleware restricts the routes that they are allowed to visit. If the user is logged in, then the route request continues; but if the user is not logged in, then this middleware redirects the user to the login page for authentication. Bascially, this middleware will force the user to login if they are not already logged in. 

#### Passport.js
First for dependencies, this fie requires:
* the passport module, 
* the passport-local module, and 
* the user class within the models folder.

The passport.use function creates a new instance of the LocalStrategy class. The constructor passes in an email object as the first property and then a function. 

The function takes in the parameters:
* email, 
* password, and 
* the done callback function. 

When the user attempts to sign-in, the findOne function runs. The findOne function finds the user in the database (via the db object connecting to the database). Then, it promise-chains to another function where the email is validated and then the password is validated. If the email is not found in the database, then an error message is returned. If the password is invalid, then an error message is returned. If the email address is found in the database and the password is validated, then it returns the dbUser.

This file also defines the serializeUser and deserializeUser functions.
Then the passport file is exported as passport.

If changes were to be made: the error messages do not follow best security practices, because an attacker could extrapolate information from the current error messages. For example, an attacker could infer that they have a correct email if the password error message is returned. The error messages should be more generic and follow secure best practices error handling.

### Models
This folder stores the model definitions.

#### Index.js
This file is using strict mode which forces declaration of all variables. 
First for dependencies, this file requires:
* the fs module, 
* the path module, 
* the sequelize module, and
* the config.json file (require the config info for the current environment).

This file scans for all the models defined in the models folder and sets them up. The following steps are performed to set up models:

* connect to the database and store the connection into the db object,
* scan the model folder and load all model definitions it finds, and
* call the associate() function on model (if defined) so that a model can define it’s association to the other models.

The db object interfaces with sequelize. Finally, the db object is exported.

#### User.js
First for dependencies, this file requires:
* the bcryptjs module (used for password hashing).

This file creates the User model. The parts of the User model include:
* the email object and its properties,
* the password object and its properties,
* a method to validate the password by comparing the unhashed password entered by the user to the hashed password stored in the database for that user, and
* a hook to hash the password before storing it and before creating a new User.

### Package.json
The package.json file identifies the project's dependencies and other metadata including project name, version, description, main, scripts, author, license, and keywords. This file is usually located at the root directory of the project. By running npm install, a user can install the required dependencies for the project. If new or updated dependencies are required, they would be added to the dependencies list when the user installs the module.

### Public
The public folder allows express.js to serve static files such as images, CSS, and Javascript files with the express.static built-in middleware function in Express (see server.js file).

#### Js
This folder holds logic (Javascript) files for the frontend views.

##### Login.js
The document.ready funtion is getting the document ready first before everything else executes.
This file takes the user's email and password data upon submission of the data (as long as the data submitted is not an empty string). On submit, the default action is prevented. The user input is set to the corresponding porperties of the userData object and ensures that the perameters are not empty. The loginUser function calls the api login route using a post method. Then, it redirects to the members route. The function also console logs an error, for example, if the post method throws and error. Finally, the form is reset. 

##### Members.js
The document.ready function is getting the document ready first before everything else is executed. The function calls the user_data route using a get method. The returned data is set to the tag defined by the member-name class in the HTML. Basically, this logic is used to display the user's email address to the user.

##### Signup.js
The document.ready funtion is getting the document ready first before everything else executes.
This file takes the user's email and password data upon submission of the data (as long as the data submitted is not an empty string). On submit, the default action is prevented. The user input is set to the corresponding porperties of the userData object. The signUpUser function calls the api signup route using a post method. Then, it redirects to the members route. The function also catches an error, for example, if the data submitted is an empty string. The handleLoginErr function sets the text in the error message span tag with the error message that is returned from the responseJSON; the error message is faded in over 500 milliseconds. Finally, the form is reset. 

#### Login.html
This page uses BootStrap and the style.css stylesheet file for styling. The login page contains a login form element. The form is made up of an input text field for email address, an input text field for password, and a login button. The user is able to input text into the email and password fields, and the user can submit that data with the login button. The page also contains a link to the signup page (which redirects to the root path). This page also connects to the login.js file.
The page could be improved by using semantic HTML tags.

#### Members.html
This page uses BootStrap and the style.css stylesheet file for styling. The members page contains a navbar with a logout link. The logout link redirects to the logout path. The page displays a welcome heading and an empty span used to populate member data (it displays the user's email address). This page also connects to the members.js file. The page could be improved by using semantic HTML tags.

#### Signup.html
This page uses BootStrap and the style.css stylesheet file for styling. The signup page contains a signup form element. This form is made up of an input text field for email address, an input text field for password, and a sign up button. The user is able to input text into the email and password fields, and the user can submit that data with the sign up button. If there is an error on submit, then an alert displays. The page also contains a link to the login page (which redirects to the login path). This page also connects to the signup.js file. The page could be improved by using semantic HTML tags.

#### Stylesheets
This folder holds any stylesheets used to style the application.

##### Style.css
The HTML pages import the BootStrap framework for styling, but they also use this file for additional styling. Currently, this file styles the application with a top margin that is applied to the signup and login forms. 

### Routes
This folder contains files that set up the api and html routes.

#### Api-routes.js
First for dependencies, this file requires:
* the models folder, and
* the passport file in the config folder.

The API routes are used to determine what to do with the data. The login route uses the passport.authentication middleware with the local strategy to authenticate the user and returns the user object as json. The signup route creates the user object with the email and password properties from the request then redirects to the login path. If creation fails, a 401 error is returned. The logout route logs the user out (invalidates the session) and returns the user to the root page. The user_data route gets the user data if the user is logged in. If the user is logged in, the user's data is returned as json. if the user is not logged in, then an empty json is returned.

#### Html-routes.js
First for dependencies, this file requires:
* the path module, and
* the isAuthenticated file in the middleware folder.

The HTML routes are used to determine how the application shows the data to the user. The root path redirects the user to the members path if the user has an authenticated account. However, if the user does not have an account, they are served the signup page. The login path redirects the user to the members path if the user has an authenticated account, otherwise they are redirected to login page. The isAuthenticated middleware is added to the members route. The isAuthenticated logic will be run whenever this path is requested. If the isAuthenticated middleware continues and does not restrict the path, then the user is served the members page.

### Server.js
First for dependencies, this file requires:
* the express module,
* the express-session module,
* the passport file in the config folder,
* the models folder,
* the html-routes file, and
* the api-routes file.

This file sets up the port connections to the server, creates the app object (using express), and syncing the database.

Express sets up urlencoded to recognize incoming request objects as strings or arrays. Express sets up the parsing of incoming requests with JSON payloads. Express sets up the public folder to serve static files (HTML, CSS, JS). A session is created with the following options:
* a secret which specifies the session ID cookie,
* a resave which is a place where session data is stored on a server, and
* a saveUninitialized which specifies it is a new unmodified session.

Passport is initialized. Passport.session is a middleware used to alter the req object and change the 'user' value that is currently the session id (from the client cookie) into the true deserialized user object.

When the app syncs to the database, the app listens on the designated port, and the console logs a message of what port it is listening on.