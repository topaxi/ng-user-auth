# ng-user-auth

[![Build Status](https://img.shields.io/travis/puzzle/ng-user-auth.svg)](https://travis-ci.org/puzzle/ng-user-auth) [![Code Climate](https://codeclimate.com/github/puzzle/ng-user-auth/badges/gpa.svg)](https://codeclimate.com/github/puzzle/ng-user-auth)

User authentication and authorization module for AngularJS.

## Features

* Transparently handles authentication and authorization for your token based REST API
* Authenticates with a backend, stores the token in the local storage of the browser and adds the `Authorization` HTTP header to every request
* Redirects the user to the login page when the token is no longer valid
* Expects to get a list of user permissions from the backend that is then used for the client side authorization
* Integrates with `angular-ui-router` and adds route based authorization
* Provides a directive that can be used for element based authorization

This is work in progress, please be patient. We'll add a HOW TO USE section here as soon as the module is ready to be used.

## Integration into your application
Follow these steps to add the ng-user-auth module to your application:
* Clone the repo
* Add the file `dist/ng-user-auth.min.js` to your HTML header
* Configure the module
* Add permission based protection to your UI routes or HTML elements

## Configuration
Configure the `ngUserAuthServiceProvider` to adjust it to your application's needs:

```
myApp.config(['ui.router', 'ngUserAuthServiceProvider', function($urlRouterProvider, ngUserAuthServiceProvider) {
  // backend/server REST URI that will be called to get the authentication and authorization information
  ngUserAuthServiceProvider.setApiEndpoint('/authentication');

  // client side UI route URL that the user will be redirected to if he is not authenticated
  ngUserAuthServiceProvider.setUnauthorizedUrl('/unauthorized');

  // request parameter that contains the originally requested path the user wanted to navigate to.
  // this is appended to the 'unauthorized' URL
  ngUserAuthServiceProvider.setRequestedPathParameterName('requestedPath');

  // URLs starting with this prefix will be cancelled if a HTTP status code 401 is returned to prevent multiple
  // redirects to the 'unauthorized' URL
  ngUserAuthServiceProvider.setAbortRequestsUrlPrefix('/');

  // add functions that will be called when the user is logged out
  ngUserAuthServiceProvider.addLogoutAction(function ($injector) {
    $injector.get('$mdDialog').hide();
  });

  // the default permission name that every user needs to have to signal he is logged in
  ngUserAuthServiceProvider.setDefaultLoggedInPermissionName('token_read');

  // in case you need a default/otherwise route, let ngUserAuth handle it by creating a handler function
  $urlRouterProvider.otherwise(ngUserAuthServiceProvider.getOtherwiseRouteHandler('/home'));
}]);
```

## UI route based authorization
Here are some basic examples. For further details please consult the demo application.
```
$stateProvider
  // every state that does not have the data.anonymousAccessAllowed is protected and the user needs
  // to be logged in to visit it. if there are no permissions specified, at least the default permission
  // that can be set with setDefaultLoggedInPermissionName needs to be present
  .state('home', {
    url: '/home',
    template: '<h1>Home</h1>'
  })

  // special permission required
  .state('secure', {
    url: '/secure',
    template: '<h1>Secure</h1>',
    data: {
      hasPermission: 'secret_agent',
    }
  })

  // anonymous access needs to be allowed on the login and logout URIs
  .state('login', {
    url: '/login',
    template: '<login></login>',
    data: {
      anonymousAccessAllowed: true
    }
  })
```

## Element/directive based authorization
```
<div ng-user-auth has-permission="admin">
  <!-- this element is only shown if the user has the permission 'admin' -->
</div>

<div ng-user-auth has-permission="['admin', 'staff']">
  <!-- this element is only shown if the user has both the permission 'admin' AND 'staff'-->
</div>

<div ng-user-auth has-any-permission="['admin', 'superuser']">
  <!-- this element is only shown if the user has any of the permissions 'admin' OR 'staff' -->
</div>

<div ng-user-auth lacks-permission="superuser">
  <!-- this element is only shown if the user does NOT have the permission 'superuser' -->
</div>
```

## Running the example application
If you want to clone the repo and run the example application please follow these steps:
### Installation
This will download all dependencies and tools that are needed to run the demo:
* npm install -g bower
* npm install
* bower install

## Run example/demo page
This will start a web server on port 3000 (and on 3001 with [Browser Sync](https://github.com/BrowserSync/browser-sync) enabled):
* npm start
