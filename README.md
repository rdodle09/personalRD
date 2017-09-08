# ChatBot with AngularJS apps

It is a chatbot application with [AngularJS](http://angularjs.org/) `version1.x` framework.

This repo contains a AngularJS MVC application and NodeJS with ExpressJS Framework for initating the server.

# Getting Started

To get you started you can simply clone the chat repository and install the dependencies.

## Install Dependencies

We get the tools we depend  upon via `npm`, the [node package manager][npm].

We have preconfigured `npm` to automatically run `bower` so we can simply do:

```bash
npm install
```

We have 2 kinds of dependencies in this project:Tools and Libraries. This libraries help us in building client side web apps and tools helps us manage the application.

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

## Interacting with Socket.IO

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

### Run the Application

We have preconfigured the project with a development web server. The simplest way to start this server is:

```bash
node server.js
```

Now browse to the app at `http://localhost:8080`.

## Directory Layout

```
```
## Testing

There are two kinds of tests in the angularjs-template application: Unit tests and End to End tests.

### Running Unit Tests

The angularjs-template app comes preconfigured with unit tests. These are written in [Jasmine][jasmine], which we run with the [Karma Test Runner][karma]. We provide a Karma configuration file to run them.

* the configuration is found at `karma.conf.js`
* the unit test files (specs) are placed side-by-side with client code: `app/**/*Spec.js`.

The easiest way to run the unit tests is to use the supplied npm script:

```bash
npm test
```

This script will start the Karma test runner to execute the unit tests. Moreover, Karma will sit and watch the source and test files for changes and then re-run the tests whenever any of them change. This is the recommended strategy; if your unit tests are being run every time you save a file then you receive instant feedback on any changes that break the expected code functionality.

You can also ask Karma to do a single run of the tests and then exit. This is useful if you want to check that a particular version of the code is operating as expected. The project contains a predefined script to do this:

```bash
npm run test-single-run
```

### End to end testing

The angularjs-template app comes with end-to-end tests, again written in [Jasmine][jasmine]. These tests are run with the [Protractor][protractor] End-to-End test runner. It uses native events and has special features for Angular applications.

* the configuration is found at `protractor.conf.js`
* the end-to-end tests are found in `tests/e2e/scenarios.js`

Protractor simulates interaction with our web app and verifies that the application responds correctly. Therefore, our web server needs to be serving up the application, so that Protractor can interact with it.

```bash
npm start
```

In addition, since Protractor is built upon WebDriver we need to install this. The angularjs-template project comes with a predefined script to do this:

```bash
npm run update-webdriver
```

This will download and install the latest version of the stand-alone WebDriver tool.

Once you have ensured that the development web server hosting our application is up and running and WebDriver is updated, you can run the end-to-end tests using the supplied npm script:

```bash
npm run protractor
```

This script will execute the end-to-end tests against the application being hosted on the development server.

## Updating Angular

Previously we recommended that you merge in changes to angularjs-template into your own fork of the project. Now that the angular framework library code and tools are acquired through package managers (npm and bower) you can use these tools instead to update the dependencies.

You can update the tool dependencies by running:

```bash
npm update
```

This will find the latest versions that match the version ranges specified in the `package.json` file.

You can update the Angular dependencies by running:

```bash
bower update
```

This will find the latest versions that match the version ranges specified in the `bower.json` file.

## Serving the Application Files

While angular is client-side-only technology and it's possible to create angular webapps that don't require a backend server at all, we recommend serving the project files using a local webserver during development to avoid issues with security restrictions (sandbox) in browsers. The sandbox implementation varies between browsers, but quite often prevents things like cookies, xhr, etc to function properly when an html page is opened via `file://` scheme instead of `http://`.

### Running the App during Development

The angularjs-template project comes preconfigured with a local development webserver. You can start this webserver with:

```bash
npm start
```

Alternatively, you can choose to configure your own webserver, such as apache or nginx. Just
configure your server to serve the files under the `app/` directory.

### Running the App in Production

This really depends on how complex your app is and the overall infrastructure of your system, but the general rule is that all you need in production are all the files under the `app/` directory. Everything else should be omitted.

Angular apps are really just a bunch of static html, css and js files that just need to be hosted somewhere they can be accessed by browsers.

If your Angular app is talking to the backend server via xhr or other means, you need to figure out what is the best way to host the static files to comply with the same origin policy if applicable. Usually this is done by hosting the files by the backend server or through reverse-proxying the backend server(s) and webserver(s).

## Continuous Integration

### Travis CI

[Travis CI][travis] is a continuous integration service, which can monitor GitHub for new commits to your repository and execute scripts such as building the app or running tests. The angularjs-template project contains a Travis configuration file, `.travis.yml`, which will cause Travis to run your tests when you push to GitHub.

You will need to enable the integration between Travis and GitHub. See the Travis website for more instruction on how to do this.

## Cloud

### Heroku

[Heroku][heroku] is a platform as a service (PaaS) that enables developers to build, deliver, monitor, scale and run applications entirely in the cloud. It is supporting several programming languages.

You will need to enable the integration between Heroku and GitHub. See the Heroku website for more instruction on how to do this.

## Automation tool

### Grunt

[Grunt][grunt] is a JavaScript task runner for improving front-end development workflow. With the use of a number of grunt plugins you can automate repetitive tasks such as minification, compilation, unit testing or linting.

### Tasks

The following list of tasks is preconfigured in `Gruntfile.js` file:

- `grunt jshint`

    Validate JavaScript code using predefined checking options located in `.jshintrc` file.

- `grunt jscs`

    Validate JavaScript code using predefined checking options located in `.jscsrc` file.

- `grunt concat`

    Concatenate JavaScript files and put them to `dist/` directory.

- `grunt uglify`

    Minify JavaScript files and put them to `build/` directory.

- `grunt watch`

    Run `concat`, `uglify`, `jshint` or `jscs` tasks whenever watched file patterns are added, changed or deleted.

- `grunt protractor`

    Run e2e tests with protractor.
    
- `grunt karma`

    Run unit tests with karma.

- `grunt default`

    Run tasks in the following order: `jshint:all`, `jscs:all`, `karma:singleRun`, `concat`, `uglify:concat`.

## License

The MIT License, Copyright (c) 2016 Michal Pietrzak

[git]: http://git-scm.com
[grunt]: http://gruntjs.com
[bower]: http://bower.io
[npm]: https://www.npmjs.org
[node]: http://nodejs.org
[protractor]: https://github.com/angular/protractor
[jasmine]: http://jasmine.github.io
[karma]: http://karma-runner.github.io
[travis]: https://travis-ci.org/
[heroku]: https://www.heroku.com
[bootstrap]: http://getbootstrap.com/
[angularjs]: http://angularjs.org/
[angular-route]:https://docs.angularjs.org/api/ngRoute
[angular-sanitize]: https://docs.angularjs.org/api/ngSanitize
[ngStorage]:https://www.npmjs.com/package/ng-storage
[Socket.io]:https://github.com/socketio/socket.io/blob/master/Readme.md