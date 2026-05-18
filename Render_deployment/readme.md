mkdir render-app

cd render-app

npm install

node server.js

// this is a basic static website of node js

git init

git add .

git commit -m "Initial Commit"

git remote add origin YOUR_REPOSITORY_LINK

git branch -M main

git push -u origin main


steps to deploy on render 

Deploy Application on Render
Steps
1) Click:
    New +
2) Select:
   Web Service
3) Connect GitHub repository
4) Select repository:
    render-app
5) Configure:
    Field	              Value
    Name	              render-app
    Environment	        Node
    Build Command	      npm install
    Start Command	      node server.js
6) Click:
    Create Web Service


