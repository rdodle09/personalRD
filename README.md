# ChatBot with AngularJS apps

It is a chatbot application with [AngularJS](http://angularjs.org/) `version1.x` framework.

This repo contains a AngularJS MVC application and NodeJS with ExpressJS Framework for initating the server.

## Run the Application

We have preconfigured the project with a development web server. The simplest way to start this server is:

```bash
node server.js
```

Now browse to the app at `http://localhost:8080`.

# Getting Started

To get you started you can simply clone the chat repository and install the dependencies.

## Install Dependencies

We have 2 kinds of dependencies in this project:Tools and Libraries. This libraries help us in building client side web apps and tools helps us manage the application.

We get the tools we depend  upon via `npm`, the [node package manager][npm].

We have preconfigured `npm` to automatically run `bower` so we can simply do:

```bash
npm install
```

#### Libraries included in `index.html`:

* We are using `cisco-collab-ui` library which is cisco standard for `Font-Styles, Colors, Pannels e.t.c`.

* We use [AngularJS v1.x][angularjs] library as a framework for building complex, data-heavy web apps with JavaScript better.

* We use [Bootstrap][bootstrap] library for making design responsive for multiple screen size and `CSS` for styling.

* We are using `jQuery`.

#### Angular Services included in `index.html`:

* We are using `angular-route` as it provides routing and deeplinking services and directives for AngularJS apps. For reference: [angular-route][angular-route].

* We include `angular-sanitize` script, as its inputs are sanitized by parsing the HTML into tokens. All tokens are thn sereialized back to properly escaped html string, i.e no unsafe input can make it into the returned string. For reference: [angular-sanitize][angular-sanitize].

* we include `ngStorage` service for accessing webstorage in angular way.It contains two services: `$localStorage` and `$sessionStorage`. For reference: [ngStorage][ngStorage]

* we include `socket.io`which enables real-time bidirectional event-based communication. For reference: [Socket.io][Socket.io]

* we are using `socket.srvc.js` <-- Custom Service for interacting witk Socket.IO

* we are using  `uuid.js` <-- Custom Service for creating a uuid.

* we included `localStorage.js`  <-- Custom Service for accessing userDetails from browser localStorage to generate `content`.

* We included `main.Ctrl.js` which has angular controller model(logic) for view.

## AngularJS Controllers used in code:

1. 'MainCtrl'

```javascript
(function () {
    // Define app module with controller 'MainCtrl'
    angular
        .module('app')
        .controller('MainCtrl', MainCtrl);
    //inject the services
    MainCtrl.$inject = ['$scope', 'localStorage', 'socket', '$timeout', 'uuid'];
    //pass the parameters to the controller
    function MainCtrl($scope, localStorage, socket, $timeout, uuid) {
      /* Controller logic */
      }
}());
```

## AngularJS  Services:

```javascript
   function MainCtrl($scope, localStorage, socket, $timeout, uuid) {
  /* Controller logic */
}
```
 1. Socket
 2. localStorage
 3. uuid
 4. $scope
 5. $timeout
 6. $sce


### Interacting with Socket.IO

Although Socket.IO exposes an `io` variable on the `window`, it's better to encapsulate it in AngularJS's [Dependency Injection system](http://docs.angularjs.org/guide/di). So, we'll start by writing a service to wrap the `socket` object returned by Socket.IO. This is awesome, because it will make it much easier to test our controller later. Open `public/js/services.js` and replace the contents with:

```javascript
app.factory('socket', function ($rootScope) {
  var socket = io.connect();
  return {
    on: function (eventName, callback) {
      socket.on(eventName, function () {
        var args = arguments;
        $rootScope.$apply(function () {
          callback.apply(socket, args);
        });
      });
    },
    emit: function (eventName, data, callback) {
      socket.emit(eventName, data, function () {
        var args = arguments;
        $rootScope.$apply(function () {
          if (callback) {
            callback.apply(socket, args);
          }
        });
      })
    }
  };
});
```

Notice that at we wrap each socket callback in `$scope.$apply`. This tells AngularJS that it needs to check the state of the application and update the templates if there was a change after running the callback passed to it. Internally, `$http` works in the same way; after some XHR returns, it calls `$scope.$apply`, so that AngularJS can update its views accordingly.

Note that this service doesn't wrap the entire Socket.IO API (that's left as an exercise for the reader ;P ). However, it covers the methods used in this tutorial, and should point you in the right direction if you want to expand on it. I may revisit writing a complete wrapper, but that's beyond the scope of this article.

Now, within our controller, we can ask for the `socket` object :

```javascript
   function MainCtrl($scope, localStorage, socket, $timeout, uuid) {
  /* Controller logic */
}
```

## Directory Layout

Here is the basic suggested skeleton for your app repo that each of the starter templates conforms to:

```bash
├── repo
│   ├── chat
│   │   ├── view
│   │   │  ├── index.html
│   │   ├── botAdaptor.js
│   │   ├── chat.js
│   ├── compensation
│   │   ├── api
│   │   │  ├── api.js
│   │   │   ├── comInquiry.js
│   │   │   ├── test.js
│   │   │   ├── visibility.js
│   │   ├── json
│   │   │   ├── ChatMessageResponseJson
│   │   │   │   ├── chatResponse.json
│   │   │   │   ├── linksResponse.json
│   │   │   │   ├── multipleTextResponse.json
│   │   │   │   ├── singleImageResponse.json
│   │   │   │   ├── singleTextResponse.json
│   │   │   │   ├── typeTableResponse.json
│   │   │   ├── conversation.json
│   │   │   ├── messageRequest.json
│   │   │   ├── messageResponse.json
│   │   ├── test
│   │   │   ├── adaptor.js
│   │   ├── app.js
│   │   ├── bot.js
│   │   ├── compRoutes.js
│   │   ├── context.js
│   │   ├── customError.js
│   │   ├── request.js
│   │   ├── test.js
│   │   ├── watsonAdaptor.js
│   ├── node_modules
│   ├── public
│   │   ├── collabui
│   │   │   ├── css
│   │   │   ├── fonts
│   │   │   ├── images
│   │   │   ├── js
│   │   ├── components
│   │   │   ├── localStorage.js
│   │   │   ├── socket.Srvc.js
│   │   │   ├── uuid.js
│   │   ├── css
│   │   │   ├── style2.css
│   │   │   ├── styles.css
│   │   ├── images
│   │   ├── included
│   │   │   ├── angular
│   │   │   ├── angular-cookies
│   │   │   ├── angular-route
│   │   │   ├── angular-sanitize
│   │   │   ├── bootstrap
│   │   │   ├── font-awesome
│   │   │   ├── jQuery
│   │   │   ├── ng-loadash
│   │   │   ├── ngStorage
│   │   │── main
│   │   │   ├── main.Ctrl.js
│   │   │   ├── main.html
│   │   │   ├── test.js
│   │   │── videos
│   │   ├── app.js
│   ├── index.html
│   ├── .gitignore
│   ├── config.js
│   ├── deplist.txt
│   ├── LICENSE
│   ├── npm_global_module_list
│   ├── package.json
│   ├── README
│   ├── README.md
│   └── server.js
├── assembly.xml
├── deploy.txt
├── pom.xml
└── readme.md
```


## JSON Message request from 'MainCtrl' i.e user Request Message 


## JSON Message received from "botAdaptor" i.e watson Response Message




[npm]: https://www.npmjs.org
[node]: http://nodejs.org
[bootstrap]: http://getbootstrap.com/
[angularjs]: http://angularjs.org/
[angular-route]:https://docs.angularjs.org/api/ngRoute
[angular-sanitize]: https://docs.angularjs.org/api/ngSanitize
[ngStorage]:https://www.npmjs.com/package/ng-storage
[Socket.io]:https://github.com/socketio/socket.io/blob/master/Readme.md