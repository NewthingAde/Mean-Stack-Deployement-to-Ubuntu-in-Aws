# Mean-Stack-Deployement-to-Ubuntu-in-Aws

This Project is to implement a web solution based on MEAN stack to Ubuntu in AWS Cloud.

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
