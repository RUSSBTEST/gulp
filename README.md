<p align="center">
  <a href="https://gulpjs.com">
    <img height="257" width="114" src="https://raw.githubusercontent.com/gulpjs/artwork/master/gulp-2x.png">
  </a>
  <p align="center">The streaming build system</p>
</p>

[![NPM version][npm-image]][npm-url] [![Downloads][downloads-image]][npm-url] [![Build Status][travis-image]][travis-url] [![Coveralls Status][coveralls-image]][coveralls-url] [![OpenCollective Backers][backer-badge]][backer-url] [![OpenCollective Sponsors][sponsor-badge]][sponsor-url] [![Gitter chat][gitter-image]][gitter-url]


## What is gulp?

- **Automation** - gulp is a toolkit that helps you automate painful or time-consuming tasks in your development workflow.
- **Platform-agnostic** - Integrations are built into all major IDEs and people are using gulp with PHP, .NET, Node.js, Java, and other platforms.
- **Strong Ecosystem** - Use npm modules to do anything you want + over 2000 curated plugins for streaming file transformations
- **Simple** - By providing only a minimal API surface, gulp is easy to learn and simple to use

## Documentation

For a Getting started guide, API docs, recipes, making a plugin, etc. check out our docs!

- Need something reliable? Check out the [documentation for the current release](/docs/README.md)!
- Want to help us test the latest and greatest? Check out the [documentation for the next release](https://github.com/gulpjs/gulp/tree/4.0)!

## Sample `gulpfile.js`

This file will give you a taste of what gulp does.

```js
var gulp = require('gulp');
var coffee = require('gulp-coffee');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');
var imagemin = require('gulp-imagemin');
var sourcemaps = require('gulp-sourcemaps');
var del = require('del');

var paths = {
  scripts: ['client/js/**/*.coffee', '!client/external/**/*.coffee'],
  images: 'client/img/**/*'
};

/* Register some tasks to expose to the cli */
gulp.task('build', gulp.series(
  clean,
  gulp.parallel(scripts, images)
));
gulp.task(clean);
gulp.task(watch);

// The default task (called when you run `gulp` from cli)
gulp.task('default', gulp.series('build'));


/* Define our tasks using plain functions */

// Not all tasks need to use streams
// But it must return either a Promise or Stream or take a Callback and call it
function clean() {
  // You can use multiple globbing patterns as you would with `gulp.src`
  // If you are using del 2.0 or above, return its promise
  return del(['build']);
}

// Copy all static images
function images() {
  return gulp.src(paths.images)
    // Pass in options to the task
    .pipe(imagemin({optimizationLevel: 5}))
    .pipe(gulp.dest('build/img'));
}

// Minify and copy all JavaScript (except vendor scripts)
// with sourcemaps all the way down
function scripts() {
  return gulp.src(paths.scripts)
    .pipe(sourcemaps.init())
      .pipe(coffee())
      .pipe(uglify())
      .pipe(concat('all.min.js'))
    .pipe(sourcemaps.write())
    .pipe(gulp.dest('build/js'));
}

// Rerun the task when a file changes
function watch() {
  gulp.watch(paths.scripts, scripts);
  gulp.watch(paths.images, images);
}
```

## Incremental Builds

You can filter out unchanged files between runs of a task using
the `gulp.src` function's `since` option and `gulp.lastRun`:
```js
function images() {
  return gulp.src(paths.images, {since: gulp.lastRun('images')})
    .pipe(imagemin({optimizationLevel: 5}))
    .pipe(gulp.dest('build/img'));
}

function watch() {
  gulp.watch(paths.images, images);
}
```
Task run times are saved in memory and are lost when gulp exits. It will only
save time during the `watch` task when running the `images` task
for a second time.

If you want to compare modification time between files instead, we recommend these plugins:
- [gulp-changed];
- or [gulp-newer] - supports many:1 source:dest.

[gulp-newer] example:
```js
function images() {
  var dest = 'build/img';
  return gulp.src(paths.images)
    .pipe(newer(dest))  // pass through newer images only
    .pipe(imagemin({optimizationLevel: 5}))
    .pipe(gulp.dest(dest));
}
```

If you can't simply filter out unchanged files, but need them in a later phase
of the stream, we recommend these plugins:
- [gulp-cached] - in-memory file cache, not for operation on sets of files
- [gulp-remember] - pairs nicely with gulp-cached

[gulp-remember] example:
```js
function scripts() {
  return gulp.src(scriptsGlob)
    .pipe(cache('scripts'))    // only pass through changed files
    .pipe(header('(function () {')) // do special things to the changed files...
    .pipe(footer('})();'))     // for example,
                               // add a simple module wrap to each file
    .pipe(remember('scripts')) // add back all files to the stream
    .pipe(concat('app.js'))    // do things that require all files
    .pipe(gulp.dest('public/'))
}
```

## Want to test the latest and greatest?

We're hard at work on our latest release, but we need your help testing it!

```sh
npm install gulpjs/gulp#4.0
```

There's a slew of major (wonderful) changes in 4.0, so make sure you check out the [docs on that branch](https://github.com/gulpjs/gulp/tree/4.0)!

## Want to contribute?

Anyone can help make this project better - check out our [Contributing guide](/CONTRIBUTING.md)!

## Backers

Support us with a monthly donation and help us continue our activities.

[![Backers][backers-image]][support-url]

## Sponsors

Become a sponsor to get your logo on our README on Github.

[![Sponsors][sponsors-image]][support-url]

[downloads-image]: https://img.shields.io/npm/dm/gulp.svg
[npm-url]: https://www.npmjs.com/package/gulp
[npm-image]: https://img.shields.io/npm/v/gulp.svg

[travis-url]: https://travis-ci.org/gulpjs/gulp
[travis-image]: https://img.shields.io/travis/gulpjs/gulp/master.svg

[coveralls-url]: https://coveralls.io/r/gulpjs/gulp
[coveralls-image]: https://img.shields.io/coveralls/gulpjs/gulp/master.svg

[gitter-url]: https://gitter.im/gulpjs/gulp
[gitter-image]: https://badges.gitter.im/gulpjs/gulp.svg

[backer-url]: #backers
[backer-badge]: https://opencollective.com/gulpjs/backers/badge.svg?color=blue
[sponsor-url]: #sponsors
[sponsor-badge]: https://opencollective.com/gulpjs/sponsors/badge.svg?color=blue

[support-url]: https://opencollective.com/gulpjs#support

[backers-image]: https://opencollective.com/gulpjs/backers.svg
[sponsors-image]: https://opencollective.com/gulpjs/sponsors.svg

[gulp-cached]: https://github.com/contra/gulp-cached
[gulp-remember]: https://github.com/ahaurw01/gulp-remember
[gulp-changed]: https://github.com/sindresorhus/gulp-changed
[gulp-newer]: https://github.com/tschaub/gulp-newer
