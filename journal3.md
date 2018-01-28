# Journal 3

middleware is in the middle of the route chain we capture data and manipulate it some how

middleware on the front end says lets attach a property or some data to an object before we pass it to the next callback

page('/user/:id', load);

function load(ctx, next) {
  var id = ctx.params.id;
  $.getJson('/user/'+id+'.json',function(user){
      ctx.user = user;
      next();
    })
}
this function uses the user id to get the user name and setrs it to ctx.user

PAGE.JS AND STATE
  saving state is good for when users go back and forth, back button
  use ctx.save() and ctx.state.images

  function show(ctx) {
  if(ctx.state.images) {
  displayImages(ctx.state.images)
  } else {
  $.getJSON('/photos', function(images) {
  ctx.state.images =images;
  ctx.save();
  displayImages(images);
  })
  }
  }



.then() and .catch() are promises
  promises return a promise object and the only way to use it is the .then() and .catch()
next() is a callback


async await is taking over promises and callbacks
****  ASYNC AWAIT EXAMPLE  ****
const makeRequest = () =>
  getJSON()
    .then(data => {
      console.log(data)
      return "done"
    })

makeRequest()


const makeRequest = async () => {
  console.log(await getJSON())
  return "done"
}

makeRequest()


deploy final project on heroku, front end will have a server that will serve index.html

page('book/:id', book.load, book.show)
page('book/:id/edit', book.load, book.edit)

emails
slack way makes a href that mails to;

use module node mailer
  real contact form
  or admin link

*** SINGLE PAGE WEB APP DEV ***
URLS AND ROUTING

client side routing, capture a url and use it to call specific functions in a specific order and render it to our page

page.js is most light weight tool w/out using a front end framework

req parameters are a replacement for a query string parameter

URLS AND routing
  CLIENT ROUTING
    page('/account/:userid', initAcctPage);

  ROUTING & CONTROLLERS
    controller in web app has a web-server that maps incoming http url requests to a particular handler
      - get requests map to a particular handler
        - the handler or action is the controller or function that's called from the route
        - then call data from database and push it into the template
    purpose of client side routing
      single page web app has index.html with header footer
      - SPA's are ridiculously fast and performant because it does not make 15 get requests for all the scripts for javascript at the end of the body

    TONs of controllers that handle routes,
      and  a template for each route

we want programatic control of what we do
  GET: /about
  PUT /articles/42/edit

ONLY HAVE ONCE CONTROLLER PER RESOURCE
  ArticlesController
  FlightsController
  UsersController

page('/shop', getData, initUi);
  ** this is called a callback chain when you hit /shop url call getData then call initUi

  page('/', user.list)
  page('/', index);
  page('/about', about);
  page('/contact', contact);

history.back(); goes back one page

history.pushState(state, 'account page', 'account.html');

state has a lightweight persistence layer
  console.log(history)

page('user/:user', show);
  :user is a parameter

app.get('/user', (req, res) {

});
  perfectly secure as long as you have multiple layers of functions that check authorization

  you can push data between controller functions

  cdn for pages.js <script src="https://cdn.rawgit.com/visionmedia/page.js/master/page.js"></script>

  uses pushState api built into history object built into browser


WE CONTROL INCOMING REQUESTS


*** LETS BUILD A SPA  ***
  -scaffolding
  create the javscript and view
  'use strict';

(function(module) {
  const aboutView = {};

  aboutView.init = function() {
      $('#contacts').empty();
      $('body').css('background', 'orange');
      $('#content').text('welcome to my about page');
  }

  module.aboutView = aboutView;
})(window);

in routes
'use strict';
page('/about', aboutView.init);

page();

db.query(req.params.id)
  ctx.params.id in page.js

  *** WEEK 3 AND DAY 1 HEROKU DEPLOYMENT ***
npm faker for fake data

by nature of object oriented programming you will have more code

GOAL is to push local up to github and then have github hook into heroku
  github will recognize that github has new code
    check box that enables automatic deployments
  front end will be on github
    but backend will be heroku
      that you have to make aip posts and gets to

can create a pipeline which is a container for multiple apps
  staging app w/ staging db and environment different from prod db and prod env

can host front end and backend on heroku

heroku
  CLICK INTO PROJECT
    - MAYBE CLICK NEW APP
    -click deploy, deploy method chooe github,
    - click repo you want, and click automatic deploys


staging and prod if you have both staging is automatic, prod is not

resources tab has addons
  heroku postgres
    hobbydev free click provision
    go to environment variables for db
  activity tab shows history


mkdir server - has server
mkdir client - has app.js and index.html

go to github create organization
  settings then organization tab

create a repo for client directory
  from command line
    git init
    git remote add origin url

create repo from server directory
  git init
    git remote add origin url

inside of server add gitignore
  git add .
  git commit -m ""
  git push origin master

go to heroku.com
  click new in top right
  - deployment method connect to github
    choose repo
      - missing github org grant permission to the org you created
          -connect server-demo (make sure you point to your org)
          - enable automatic deployments
        -click deploy
    click settings tab in top right and view your app

    click activity tab and look at build log
      _no database
    got to resources tab type postgres for addon and select hobby-dev free

  go to settings tab in top right
    reveal config vars (same as .env)
    in server.js
      delete the || 3000 from server.js
        - PORT = process.env.PORT
        - const connectionString = process.env.DATABASE_URL;
      remove app.use(express.static('./public'));
      remove app.get('/', (request, response) => response.sendFile('index.html', {root: './public'}));
      remove all gets that serve up static pages

SKIP TO FRONT END
  front end live at github.io
  git add .
  git commit -m "aefe"
  git push origin master

in app.js change api request og $.get(\)

in setting sof github for any master pushes deploy
  click on link to deplot front end
    go to /index.html
      api needs to route to heroku

create base url that points to backend at heroku

in app.js
  var __API_URL__ = 'postgres://ufupbwasntcdow:2b91e2e8820a8f374e22283400b001b89103da1de642bc3d6821264e4d4087e9@ec2-54-163-237-249.compute-1.amazonaws.com:5432/ddohnecq4qookv'; //******* REMOVE SLASH AFTER.COM *******

update all routes to include __API_URL__ &&& add .catch

$.POST(`${__API_URL__}/db/person`, DATA)
.then(function() {
  pageLoad();
})
.catch(function(err) {
  console.log(error(err));
  pageLoad();
});

go to console of github url
  no access control allow origin error
    - need to give everyone access to api

got to server.js in VS code
  npm i -S cors
  - add cors
    const cors = require('cors');
    app.use(cors());

git add .
git commit -m "addes cors"
git push origin master


SERVER ON HEROKU SITE SHOULD SAY CANNOT GET /



RECAP OF PROCESS
  - create org in github
  - crete 2 repos inside of org and add collaborator
  - create backend api w/server install all packages and .gitignore push server up to heroku
  - on heroku provision db on resources tab; add db
  - use db url
  create app.js file with simple route that responds with something

DEPLOY FIRST

book title author
build a form that posts add a book page
