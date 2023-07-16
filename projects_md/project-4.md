## MEAN STACK DEPLOYMENT TO UBUNTU IN AWS
Mark as CompletedSubmit Project for Review
Since we have already learned how to deploy LAMP, LEMP and MERN Web stacks – it is time to get yourself familiar with MEAN stack.

### Step 0 – Preparing prerequisites
Refer to step 0 of project-one

### Step 1: Install NodeJs
- Update ubuntu using the command

  `sudo apt update`
  
- Upgrade ubuntu using the command below:

  `sudo apt upgrade`
  
- Add certificates:

  ```
      sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
      curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
  ```
- Install NodeJS:

  `sudo apt install -y nodejs`
  
  ### Step 2: Install MongoDB
Mongodb is a Document based database suitable for semi-structured data.

- Run the commands below to seed the database add momgodb repository to your Ubuntu server:

  
      `sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`
  
  
      `echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`
  

- Install MongoDB:

  `sudo apt install -y mongodb`
  
- Start The server:

  `sudo service mongodb start`
  
- Verify that the service is up and running

  `sudo systemctl status mongodb`
  
- Your terminal should look like the screenshot below:

![Screenshot from 2022-10-03 19-36-00](https://user-images.githubusercontent.com/23356682/193663617-266c65a1-d942-465a-a258-07d7a68b693e.png)


- Install npm – Node package manager:

  `sudo apt install -y npm`
  
- Install body-parser package:

  `sudo npm install body-parser`
  
- Create a folder named ‘Books’:

  `mkdir Books && cd Books`
  
- In the Books directory, Initialize npm project:

  `npm init`
  
- Add a file to it named server.js:

  `vi server.js`
  
- Copy and paste the web server code below into the server.js file:

  ```
      var express = require('express');
      var bodyParser = require('body-parser');
      var app = express();
      app.use(express.static(__dirname + '/public'));
      app.use(bodyParser.json());
      require('./apps/routes')(app);
      app.set('port', 3300);
      app.listen(app.get('port'), function() {
      console.log('Server up: http://localhost:' + app.get('port'));
      });


### Step 3: Install Express and set up routes to the server
- Install mongoose with the command below:

  `sudo npm install express mongoose`
  
- In ‘Books’ folder, create a folder named apps

  `mkdir apps && cd apps`
  
- Create a file named routes.js

  `vi routes.js`
  
- Copy and paste the code below into routes.js:

  ```
      var Book = require('./models/book');
      module.exports = function(app) {
      app.get('/book', function(req, res) {
      Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
      });
      }); 
      app.post('/book', function(req, res) {
      var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
      });
      book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
      });
      });
      app.delete("/book/:isbn", function(req, res) {
      Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
      });
      });
      var path = require('path');
      app.get('*', function(req, res) {
      res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
      };
  ```
  
- In the ‘apps’ folder, create a folder named models:

  `mkdir models && cd models`
  
- Create a file named book.js:

  `vi book.js`
  
- Copy and paste the code below into ‘book.js’

  ```
      var mongoose = require('mongoose');
      var dbHost = 'mongodb://localhost:27017/test';
      mongoose.connect(dbHost);
      mongoose.connection;
      mongoose.set('debug', true);
      var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
      });
      var Book = mongoose.model('Book', bookSchema);
      module.exports = mongoose.model('Book', bookSchema);
    
  ```
  
### Step 4 – Access the routes with AngularJS
AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.

- Change the directory back to ‘Books’:

  `cd ../..`
  
- Create a folder named public:

  `mkdir public && cd public`
  
- Add a file named script.js

  `vi script.js`
  
- Copy and paste the Code below (controller configuration defined) into the script.js file.

  ``` var app = angular.module('myApp', []);
      app.controller('myCtrl', function($scope, $http) {
      $http( {
      method: 'GET',
      url: '/book'
      }).then(function successCallback(response) {
      $scope.books = response.data;
      }, function errorCallback(response) {
      console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
      $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
      }).then(function successCallback(response) {
      console.log(response);
      }, function errorCallback(response) {
      console.log('Error: ' + response);
      });
      };
      $scope.add_book = function() {
      var body = '{ "name": "' + $scope.Name + 
      '", "isbn": "' + $scope.Isbn +
      '", "author": "' + $scope.Author + 
      '", "pages": "' + $scope.Pages + '" }';
      $http({
      method: 'POST',
      url: '/book',
      data: body
      }).then(function successCallback(response) {
      console.log(response);
      }, function errorCallback(response) {
      console.log('Error: ' + response);
      });
      };
      });
  
  ```  

- In public folder, create a file named index.html:

  `vi index.html`
  
- Cpoy and paste the code below into index.html file:

  ```  
      <!doctype html>
      <html ng-app="myApp" ng-controller="myCtrl">
      <head>
      <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
      <script src="script.js"></script>
      </head>
      <body>
      <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
      </div>
      <hr>
      <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>
        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>
          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
        </table>
        </div>
        </body>
        </html>
  
  ```
  
- Change the directory back up to Books:

  `cd ..`
  
- Start the server by running this command:

  `node server.js`
  
- You should see something like the screenshot below printed on your terminal:

![Screenshot from 2022-10-03 19-58-28](https://user-images.githubusercontent.com/23356682/193665260-9637cfb9-6303-4858-ba22-87432b4159e8.png)

  
- Open TCP port 3300 in your security group on AWS to access the website on your browser. 

- Using your public IP address from your Ubuntu server you can access the website. You should find something like the screenshot below:


![Screenshot from 2022-10-03 19-58-08](https://user-images.githubusercontent.com/23356682/193664516-a9d84284-5c49-41ca-81e1-2241efbb4447.png)

  
  
