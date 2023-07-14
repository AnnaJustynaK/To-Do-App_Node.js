# nodejs-todo

<h2> A simple To Do List application built with Node.js and Express</h2>

<p> Nodejs application that let's you add and complete task on a single page, storing both new and completed task in a different array. This appllication makes use of: </p>

<ul>
<li> EJS - A simple templating engine that lets you generate HTML markup with plain JS </li>

<li> Body-parser - This extracts the entire body portion of an incoming request stream and exposes it on req.body </li>
</ul>

![png](https://github.com/missating/nodejs-todo/blob/master/todo.png?raw=true 'web todo')

<br>

<p> How to run the app locally: </p>

<ol>
<li> Run <code> npm install </code> to install all needed dependencies </li>

<li> Then start the server using <code> node index.js </code> </li>

<li> Navigate to your browser <code> http://localhost:3000/ </code> to view the app </li>
</ol>

This application is a very basic one but it has the following features:

Add and complete a task on a single page
Store both new and completed task in a different array
Uses the express framework
And a very minimal CSS styling
Base Setup:

Create an empty directory named todo-app
Open up the console, navigate to the directory and run npm init
Fill out the required information to initialize the project
Within the todo-app directory, create a new file named index.js — this is where we will put most of our code.
Server Setup: To get our server up and running we need to use Express. Express is a minimalist web framework for Node.js, it makes it very easy to create and run a web server with Node. Install express in the console by running:

npm install express --save
Once installed we can then add the following code into the index.js file

//require the just installed express app
var express = require('express');
//then we call express
var app = express();
//takes us to the root(/) URL
app.get('/', function (req, res) {
//when we visit the root URL express will respond with 'Hello World'
  res.send('Hello World!');
});
//the server is listening on port 3000 for connections
app.listen(3000, function () {
  console.log('Example app listening on port 3000!')
});
Now we can test our server by running:

node index.js
// Example app listening on port 3000!
And if you open your browser and visit: localhost:3000 and you should see Hello World!

View Setup: For our view setup, we would likely not respond with plain text Hello World when anyone visits our root route, we would send back an HTML file that will have different heading text, buttons and a form for adding a new task. And for that, we’ll be using EJS (Embedded JavaScript). EJS is a templating language.

To get an idea on how EJS works you can read this post

Install EJS by running:

npm install ejs --save
Then set up the template engine with this line of code (just below the require statements) in the index.js file:

app.set('view engine', 'ejs');
EJS is accessed by default in the views directory. So, create a new folder named views in your directory. Within that views folder, add a file named index.ejs. Think of our index.ejs file as an HTML file.

<html>
<head>
    <title> ToDo App </title>
    <link href="https://fonts.googleapis.com/css?family=Lato:100"     rel="stylesheet">
    <link href="/styles.css" rel="stylesheet">
  </head>
<body>
  <div class="container">
<h2> A Simple ToDo List App </h2>
<form action ="/addtask" method="POST">
       <input type="text" name="newtask" placeholder="add new task">        <button> Add Task </button>
<h2> Added Task </h2>
<button formaction="/removetask" type="submit"> Remove </button>
</form>
<h2> Completed task </h2>
</div>
</body>
</html>
Now let’s replace our app.get code in the index.js file in other to send the equivalent HTML to the client.

app.get('/', function(req, res){
   res.render('index');
});
At this point, once you run node index.js and navigate to localhost:3000 in your browser, you should see the index.ejs file being displayed.

Add Task Setup: We have just one route now. However, for our application to work, we need a post route as well. If you look at our index.ejs file, you can see that our form is submitting a post request to the /addtask route:

<form action="/addtask" method="POST">
Now that we know where our form is posting, we can set up the route. A post request looks like a get request, but with one minor change:

app.post('/addtask', function (req, res) {
   res.render('index')
});
But instead of just responding with the same HTML template, we have to access the name of the newtask typed in by the user as well. For this, we need to use an Express Middleware. Middlewares are functions that have access to the req and res bodies in order to perform more advanced tasks.

We’re going to make use of the body-parser middleware. body-parser allows us to make use of the key-value pairs stored on the req-body object. In this case, we’ll be able to access the name of the newtask typed in by the user on the client-side and save it into an array on the server-side.

To use body-parser, we must install it first:

npm install body-parser --save
Once installed, we can require it, and then make use of our middleware with the following line of code in our index.js

var bodyParser = require("body-parser");
app.use(bodyParser.urlencoded({ extended: true }));
Finally, we can now update our post request to store the value of newtask in an array, while also adding a loop to our index.ejs file to display a list of all the newtask added by the user.

The index.js file:

//the task array with initial placeholders for added task
var task = ["buy socks", "practise with nodejs"];
//post route for adding new task
app.post('/addtask', function (req, res) {
    var newTask = req.body.newtask;
//add the new task from the post route into the array
    task.push(newTask);
//after adding to the array go back to the root route
    res.redirect("/");
});
//render the ejs and display added task, task(index.ejs) = task(array)
app.get("/", function(req, res) {    
  res.render("index", { task: task, complete: complete });
});
The index.ejs file:

<h2> Added Task </h2>
   <% for( var i = 0; i < task.length; i++){ %>
<li><input type="checkbox" name="check" value="<%= task[i] %>" /> <%= task[i] %> </li>
<% } %>
Testing the app: Run node index.js then open your browser and visit: localhost:3000, type a new task into the text field and hit enter, you would see your task being displayed below the Added task heading

Delete Task Setup: After adding a new task, we need to be able to remove the task from the added task section after its been completed and move it into the complete task array on the server-side. We can achieve this by checking the completed task and using the remove button in our EJS file.

<button formaction="/removetask" type="submit"> Remove </button>
The index.js file:

//the completed task array with initial placeholders for removed task
var complete = ["finish jquery"];
app.post("/removetask", function(req, res) {
     var completeTask = req.body.check;
//check for the "typeof" the different completed task, then add into the complete task
if (typeof completeTask === "string") {
     complete.push(completeTask);
//check if the completed task already exist in the task when checked, then remove using the array splice method
  task.splice(task.indexOf(completeTask), 1);
} else if (typeof completeTask === "object") {
    for (var i = 0; i < completeTask.length; i++) {     complete.push(completeTask[i]);
    task.splice(task.indexOf(completeTask[i]), 1);
}
}
   res.redirect("/");
});
The index.ejs file:

<h2> Completed task </h2>
    <% for(var i = 0; i < complete.length; i++){ %>
      <li><input type="checkbox" checked><%= complete[i] %> </li>
<% } %>
Finishing the App: To complete the app you might need to check the complete code here. It has the basic CSS styles to make the app look somewhat better or you can go ahead and style yours whichever way you like.

Note: For the CSS, you’ll need to add a new folder to the project called public and within that folder create a css file named styles.css. Here’s what the file structure should look like:


File structure for the project
But, Express won’ t allow access to this file by default, so we need to expose it with the following line of code in our index.js file:

app.use(express.static("public"));
And with that, you can now go ahead and add your CSS styles.
