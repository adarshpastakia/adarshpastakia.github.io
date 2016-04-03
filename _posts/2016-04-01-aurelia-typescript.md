---

title: Aurelia Typescript

layout: post

tags: [aurelia,typescript]

category: aurelia

excerpt: Getting started with aurelia and typescript, this post describes how to setup and bundle applications.

---


### Get started with [Aurelia](//aurelia.io) and [Typescript](//typescriptlang.org)

Before getting started make sure to have [Node](//nodejs.org) installed. Start by running `npm init` command which will create a package.json file, fill in the appropriate project name and description when prompted, and then proceed to install required node modules that will be used in building your project

```sh
npm install jspm gulp gulp-typescript aurelia-bundler --save-dev
```

*Note: This will install the minimal required node modules, incase you choose to use sass you can install gulp-sass also*

Next step will be to initialize a jspm project by running `jspm init` command and follow the prompts below

```
Would you like jspm to prefix the jspm package.json properties under jspm? [yes]: y

Enter server baseURL (public folder path) [./]: .

Enter jspm packages folder [./jspm_packages]: ⏎ #use the default location

Enter config file path [./config.js]: ⏎ #use the default location

Configuration file config.js doesn't exist, create it? [yes]: y

Enter client baseURL (public folder URL) [/]: .

Which ES6 transpiler would you like to use, Traceur, TypeScript or Babel? [babel]: TypeScript
```

Now we will proceed to installing the required jspm packages

```
jspm install text #required for bundling html
jspm install fetch #required by aurelia-fetch-client
jspm install aurelia-framework
jspm install aurelia-fetch-client
```
----

### Creating an Aurelia app

An aurelia app requires a main entry point, which we will call `main.ts`. The file will basically initialize aurelia and set the root view of the application, for this post will initialize the basic aurelia framework with no plugins.

#### main.ts
```javascript
import "aurelia-framework";
export function configure(aurelia:Aurelia) {
  aurelia.use
    .standardConfiguration()
    .developmentLogging();

  aurelia.start().then(a=>a.setRoot('./src/app.js'));
}
```

> NOTE: disable developmentLogging for production by commenting the line

Upon aurelia start we will set the root view of the application to `src/app.js`, the application views and view-models we shall place into the `src` folder, you could however choose a different approach.

#### Sample app structure
```
|- jspm_packages/
|- node_modules/
|- src/
  |- app.ts
  |- app.html
  |- users/
    |- view.ts
    |- view.html
    |- edit.ts
    |- edit.html
|- main.ts
|- gulpfile.js
```
So under the source folder we have the main application view and view-model (`app.js`, `app.html`), then we also have a folder called `users` which will contain the views and view-models pertaining to the users section of the app. This approach keeps all related views for each section under its own folder.

> Will describe configuring the app router, and app navigation in the next post

----

### Bundling an Aurelia app

Bundling an aurelia using a task in `gulpfile.js`

#### gulpfile.js
```javascript
var gulp = require('gulp');
var runSequence = require('run-sequence');
var ts = require('gulp-typescript');
var bundler = require('aurelia-bundler');

// Typescript Compiler
// Compile all typescript files in its own folder
var tsProject = ts.createProject({
    declaration: false,
    noExternalResolve: true,
    sortOutput: true,
    target: 'ES5',
    module: 'amd',
    noImplicitAny: false,
    removeComments: true,
    emitDecoratorMetadata: true,
    experimentalDecorators: true
  });
gulp.task('scripts:compile', function () {
  var tsRoot = gulp.src([
    './jspm_packages/npm/aurelia-*/*.d.ts',
    './src/**/*.ts'], {base: './'})
  .pipe(ts(tsProject));
	return tsRoot.js.pipe(gulp.dest('.'));
});

// Aurelia Bundle
var config = {
  force: true,
  packagePath: '.',
  bundles: {
    "dist/app": {
      // Do not inject imported files,
      //  any external files that required to be imported must be added to the list
      //  placing files in [..] prevents the bundler from injecting imported files as well
      includes: [
        '[main.js]',
        '[./src/**/*.js]',
        '[./src/**/*.html!text]'
      ],
      options: {
        inject: true,
        minify: true
      }
    },
    "dist/aurelia": {
      includes:[
        './jspm_packages/npm/**/aurelia-*.js',
        './jspm_packages/npm/**/aurelia-validation*/resources/*.js'
      ],
      options: {
        inject: true,
        minify: true
      }
    }
  }
};
gulp.task('aurelia:bundle', function () {
  return bundler.bundle(config);
});
gulp.task('aurelia:unbundle', function () {
  return bundler.unbundle(config);
});
// Copy deployment specific files to a build folder
gulp.task('aurelia:build', function () {
  return gulp.src(['./index.html', './config.js',
    './jspm_packages/system*', './dist/**/*',
    './fonts/**/*', './styles/**/*', './images/**/*' // add other dependent folders
  ], {base: './'})
  .pipe(gulp.dest('./build'));
});

// Default gulp task
gulp.task('default', ['build'], function () {
  runSequence('scripts:compile', 'aurelia:bundle', 'aurelia:build', 'aurelia:unbundle');
});
```

Steps for bundling

* Compile application typescript files
* Bundle all application relevant files into `app.js`, aurelia framework into `aurelia.js`
* Copy relevant files and folders to a `build` folder which makes it easier for deployment
* Last step is to unbundle the application, due to the fact that aurelia bundle will modify the config.js file to map all files to the dist/js files.
