---

title: Aurelia Typescript

layout: post

tags: [aurelia,typescript]

category: aurelia

---

A test post using jekyll
------------------------

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
