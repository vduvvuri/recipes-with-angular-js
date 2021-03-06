---
layout: recipe
title: Consuming JSONP APIs
chapter: consuming-external-services
order: 3
---

### Problem
You wish to call a JSONP API.

### Solution
Use the `$resource` service and configure it to use JSONP. As an example we will take the Twitter search API here.

The HTML template lets you enter a search term in an input field and will render the search result in a list.

{% prism markup %}
{% raw %}
<body ng-app="MyApp">
  <div ng-controller="MyCtrl">
    <input type="text" ng-model="searchTerm" placeholder="Search term">
    <button ng-click="search()">Search</button>
    <ul ng-repeat="tweet in searchResult.results">
      <li>{{tweet.text}}</li>
    </ul>
  </div>
</body>
{% endraw %}
{% endprism %}

The `$resource` configuration can be done in a controller requesting the data:

{% prism javascript %}
var app = angular.module("MyApp", ["ngResource"]);

function MyCtrl($scope, $resource) {
  var TwitterAPI = $resource("http://search.twitter.com/search.json",
    { callback: "JSON_CALLBACK" },
    { get: { method: "JSONP" }});

  $scope.search = function() {
    $scope.searchResult = TwitterAPI.get({ q: $scope.searchTerm });
  };
}
{% endprism %}

You can find the complete example on [github](https://github.com/fdietz/recipes-with-angular-js-examples/tree/master/chapter5/recipe3).

### Discussion
The Twitter search API supports a `callback` attribute for the JSON format as described in their [documentation](https://dev.twitter.com/docs/api/1/get/search). The `$resource` definition sets the `callback` attribute to `JSON_CALLBACK`, which is a convention from Angular when using [JSONP](http://en.wikipedia.org/wiki/JSONP). It is a placeholder that is replaced with the real callback function, generated by Angular. Additionally, we configure the get method to use `JSONP`. Now, when calling the API we use the `q` URL parameter to pass the entered `searchTerm`.
