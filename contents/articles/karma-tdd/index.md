---
title: Automated Testing and Continuous Integration with Karma, Grunt, and Travis C-I
author: cql
date: 2013-08-20
template: article.jade
---

I am currently working on <a title="Influence Project" href="https://github.com/IdeaHaven/influence/">a project</a> that aggregates data from several political data APIs such as the <a title="Sunlight Foundation" href="http://sunlightfoundation.com/">Sunlight Foundation</a> and <a title="Open Secrets" href="http://www.opensecrets.org/">Open Secrets</a>. Our intent is to deliver our own API using these and other data sources using an <a title="actionHero.js node server" href="http://actionherojs.com/">actionHero.js</a> server. One of our priorities in this project is to write spec tests for every part of our app as we develop, and to make our testing results public so that users can feel confident that our API is tested and stable if they decide to use it themselves.

<span class="more"></span>

Continuous integration frameworks such as <a title="Travis-CI" href="https://travis-ci.org/">Travis-CI</a> and code coverage trackers such as <a title="Coveralls" href="https://coveralls.io/">Coveralls</a> are great for coordinating TDD among your team and conveying to users that you have been diligent about testing. Both Travis-CI and Coveralls offer excellent support for widely used testing frameworks such as <a title="Mocha Testing Framework" href="http://visionmedia.github.io/mocha/">Mocha</a>, but less support for the AngularJS-centric <a title="Karma Testing Framework" href="http://karma-runner.github.io/0.10/index.html">Karma</a> testing suite. I'd like to discuss two issues with integrating Karma with Travis-CI and Coveralls.

<strong>Yeoman seed files do not format karma.conf.js appropriately for use with node and Travis-CI.</strong>

We used Yeoman to build the scaffolding for our node project, yet the karma configuration file resulting from this was not in the appropriate format for export as a node module.

Below you will find the karma configuration file as generated by Yeoman.

```javascript
basePath = '';

files = [
  JASMINE,
  JASMINE_ADAPTER,
  'app/bower_components/angular/angular.js',
  'app/bower_components/angular-mocks/angular-mocks.js',
  'app/scripts/*.coffee',
  'app/scripts/**/*.coffee',
  'test/mock/**/*.coffee',
  'test/spec/**/*.coffee'
];
...
```
In order to integrate testing well with Travis-CI, it was necessary to redefine configureation for export as a node module.
```javascript
module.exports = function(config) {
  config.set({

    basePath: '',

    frameworks: ['jasmine'],

    files: [
      'app/bower_components/angular/angular.js',
      'app/bower_components/angular-mocks/angular-mocks.js',
      'app/scripts/**/*.coffee',
      'test/mock/**/*.coffee',
      'test/spec/**/*.coffee'
    ],
    . . .
  });
};
```

Travis-CI builds your project from scratch and installs all node modules it requires at the time of testing. If you are using Yeoman to generate your project's scaffolding, you will find that you have to adjust your configuration files for use with Travis-CI.

<b>Coveralls is not yet able to obtain Karma test data out of the box; get ready to pipe.</b>

Some folks have written NPM modules that attempt to integrate Karma testing results with Coveralls  such as the <a title="grunt-karma-coveralls npm module." href="https://npmjs.org/package/grunt-karma-coveralls">grunt-karma-coveralls</a> npm package and the <a title="karma-coverage npm package" href="https://npmjs.org/package/karma-coverage">karma-coverage</a> npm module. Unfortunately our architecture did not interface perfectly with these packages. The most agnostic option for integrating Karma with Coveralls is to first <a title="Karma official documentation: Coverage" href="http://karma-runner.github.io/0.8/config/coverage.html">configure Karma's coverage preprocessor and coverage reporter</a> to save test results in lcov format and <a title="Coveralls Documentation" href="https://npmjs.org/package/coveralls">pipe these results directly to Coveralls</a>.

<strong>Watch our project's integrated tests as we develop:</strong>

Below you can find our projects hosted on Travis-CI. We'll publicize Coveralls badges as our project nears completion and coverage metrics become more meaningful.

<a href="https://travis-ci.org/IdeaHaven/influence">https://travis-ci.org/IdeaHaven/influence</a>

<a href="https://travis-ci.org/IdeaHaven/influence-server">https://travis-ci.org/IdeaHaven/influence-server</a>