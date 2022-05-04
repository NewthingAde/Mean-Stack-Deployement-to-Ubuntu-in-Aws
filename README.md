# Mean-Stack-Deployement-to-Ubuntu-in-Aws

This Project is to implement a web solution based on MEAN stack to Ubuntu in AWS Cloud. MEAN (MongoDB, ExpressJS, AngularJS, NodeJS

## About MEAN Stack

MEAN Stack is a combination of following components:

1. MongoDB (Document database) – Stores and allows to retrieve data.
2. Express (Back-end application framework) – Makes requests to Database for Reads and Writes.
3. Angular (Front-end application framework) – Handles Client and Server Requests
4. Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user


## Creating Ec2 Instance On Aws

- Select region (the cl and launch a new EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS (HVM)

- Create a pair Key as the EC2 is created ( You can call the key pair MERN_Project)

- Move into the folder where the pair key is downloaded and run the following command to first change the mode

                                 sudo chmod 0400 MERN_Project.pem
                                 
- Use the command to connect to the instances

                               ssh -i MERN_Project.pem ubuntu@<Public-IP-address>
                               

## Install NodeJs

- Update ubuntu using this command line

                                  sudo apt update
                                  
- Upgrade ubuntu using the command line

                                   sudo apt upgrade
                                   
- Now we will add certificate 

                         sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
                         
- Now will we curl and install the NodeSource Node.js repo        

                         curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -  
                         
- Now we will install node js

                        sudo apt install -y nodejs

## Install MongoDB

MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For this application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

- we will input this in mages/WebConsole.gif using the following command

                      sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
                                            
                      
              echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
              
- Next we will install our mongodb

                            sudo apt install -y mongodb
                            
- After the installation is complete, we will start the server uinsing the command below

                            sudo service mongodb start
                            
- Now that we have started the server, we will need to check if the server is up and running perfectly we use the command below

                              sudo systemctl status mongodb
                              
If everthing works perfectly then we should the the something similar to the below 

    <img width="581" alt="Screenshot 2022-04-28 at 12 45 19" src="https://user-images.githubusercontent.com/80678596/165736028-8df1a03e-5fc6-4c65-be24-11c80d0b2ab0.png">

- Next we are going to install Node Module package to use. WE can use either yarm or npm module. But for this project we will focus on using Npm module. We will install npm module using the commsnd bellow
                              
                                        sudo apt install -y npm
                                
- We need ‘body-parser’ package to help us process JSON files passed in requests to the server. We will install it using the command below           
                                
                                      sudo npm install body-parser
                                      
- Next, we will Create a folder named ‘Books’ and we will go inside the 'Books' directly using this command 

                                      mkdir Books && cd Books
                                      
- Inside the Books directory, we will initiate Npm project

                                        npm init
                                        
- We will create a file called server.js and open the file with this command

                                        touch server.js && vi server.js
                            
- Copy and paste the web server code below into the server.js file then exit (Remember to present i to insert, then press escape key after you have pasted it then use :w to save and :q to exit the vim file)                               
                                
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

## Install Express and set up Route to the Server

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.

We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register

- We will install Mongoose using the following command

                              sudo npm install express mongoose
                              
- In the Books folder we will create a folder called apps and navigate in to the folder

                                mkdir apps && cd apps
                                
- Create a file named routes.js in the apps folder and open the file

                                touch routes.js && vi routes.js
                                
- Copy and paste the following command into the file

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

- In the ‘apps’ folder, we will create a folder named models and navigate in the the folder we just created 

                                mkdir models && cd models
                                
- Next , we create a file named book.js and open the file 

                                touch book.js && vi book.js
                                
- Copy and paste the following command into the book.js file

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

### We acccess the routes with Angular Js

AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.

- Now we will navigate back to our 'Books' folder

                              cd /home/ubuntu/Books/
                              
- We will create a folder named public and navigate inside it

                              mkdir public && cd public
                              
- Inside the public folder we will create a file script.js and open the file.

                              touch script.js && vi script.js
                              
- Copy and paste the command into the file 

                            var app = angular.module('myApp', []);
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
                            
- In public folder, we will create a file named index.html

                            touch index.html && vi index.html
                            
                            
                            
                            
                            
                            
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

- Next, we will change the directory back up to Books

                                       cd /home/ubuntu/Books/
                                       
- Next, we start the server using the command

                                    node server.js
                                    
- If everthing is done well we should get something similar to this 

<img width="497" alt="Screenshot 2022-04-28 at 13 55 58" src="https://user-images.githubusercontent.com/80678596/165746878-4695f1d4-d419-4a9b-ba76-49728b281aa6.png">

- The server is now up and running, we can connect it via port 3300. YTo be able to do this we can open a new terminal and ssh into our ubuntu instance to test what curl command returns locally. 

                             curl -s http://localhost:3300

- We should get something similar to the beloow

<img width="732" alt="Screenshot 2022-04-28 at 14 00 38" src="https://user-images.githubusercontent.com/80678596/165747500-4ad363e2-e465-4ee3-b288-07544b69b5ce.png">

- We will need to open TCP port 3300 in your AWS Web Console for your EC2 Instance.

- You can paste the aws ip address on your webpage using poet 3300. You would get something similar to the below

<img width="591" alt="Screenshot 2022-04-28 at 14 06 27" src="https://user-images.githubusercontent.com/80678596/165748514-e1353328-d108-491e-8516-b8048a94ab95.png">

