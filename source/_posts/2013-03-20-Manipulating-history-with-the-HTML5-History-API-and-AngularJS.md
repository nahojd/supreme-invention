---
title: "Manipulating history with the HTML5 History API and AngularJS "
extid: posts/9537
author: johan
date: 2013-03-20 09:56:32
updated: 2013-03-20 11:17:17
slug: Manipulating-history-with-the-HTML5-History-API-and-AngularJS
tags: [angularjs, html5, html, javascript]
---

As I've mentioned earlier, I've been working quite a lot with [AngularJS](http://angularjs.org) lately, most recently on a search function on a website. Naturally, since this is an ajax application, the search result page never reloads when I perform a search. Never the less, I would like to

1. Be able to go back and forth between my searches with the standard browser functions
2. See my new query in the location bar
3. Reload the page and have the latest query - not the initial one - execute again
4. Make this invisible to the user, that means no [hashbangs](http://en.wikipedia.org/wiki/Shebang_%28Unix%29 "And she bangs, she bangs, oh baby when she moves, she moves. I go crazy 'cause she looks like a flower but she stings like a bee. Like every girl in history she bangs, she bangs. - Ricky Martin") - only a nice `?query=likethis`.

Fortunately, HTML5 is there for me, with [the history api](http://diveintohtml5.info/history.html "There is an interesting history about this site.")! This is [supported in recent versions of Chrome, Firefox and Safari, as well as in Internet Explorer 10](http://caniuse.com/#search=history). Unfortunately, no support in IE9 or earlier. Anyway, in AngularJS, we don't want to access the `history` object directly, but rather use the [`$location`](http://docs.angularjs.org/api/ng.$location "It's all about location.") abstraction.

The first thing we need to to is set AngularJS to use HTML5 mode for $location. This changes the way $location works, from the default hash mode to querystring mode.

``` js
angular.module('Foobar', [])
    .config(['$locationProvider', function($locationProvider) {
         $locationProvider.html5Mode(true);
    }]);
```

Note: Setting html5Mode to true seems to cause problems in browsers that doesn't support it, even though it's supposed to just ignore it and use the default mode. So it might be a good idea to check for support before turning it on, for example by checking `Modernizr.history`.

Now, all I have to do to whenever I perform a search is to update the location to reflect the new query.

``` js
$scope.search = function() {
    //Do magic search stuff

    var path = $location.path(); //Path without parameters, e.g. /search (without ?q=test)
    $location.url(path + '?q=' + $scope.query);
};
```

This makes the querystring change when I perform a search, and it also takes care of the reloading of the page. It does not, however make the back and forward button work. Sure, the location changes when you click back, but the query isn't actually performed again. In order to make this work, you need to do some work when the location changes.

In plain javascript, you would add a listener to the popstate event. But, you know, AngularJS and all that, we wan't to use the `$location` abstraction. So instead, we create a `[$watch](http://docs.angularjs.org/api/ng.$rootScope.Scope#$watch)` that checks for changes in `$location.url()`.

``` js
$scope.$watch(function () { return $location.url(); }, function (url) {
    if (url) {
        $scope.query = $location.search().q
        $scope.search();
    }
});
```

And that's pretty much it! Now you can step back and forth in history with the browser buttons, and have AngularJS perform the search correctly every time!