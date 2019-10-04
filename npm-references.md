# Managing `package` files & `npm` dependencies

Here is a quick selection of general helpers and scripts needed to create, customize, and manage your application's build environment.

---

## 01 - Creating a `package.json` file

Move into your project directory:

`$ cd path/..`

And run:

`$ npm init`

This will allow you to start the process of creating a package for your project. A message will then be displayed indicating the package creation process has started:

`Press ^C at any time to quit.`

You will then be prompted to answer a series of questions in order to create your file shell:

```
package name: (package) ".."
version: (1.0.0)
description: ""
entry point: (index.js)
test command: ""
git repository: ""
keywords: ""
author: ""
license: (ISC)
```
Once you have answered the questions (not all of them need an _immediate_ answer), press [↵] to confirm and create the **JSON** file:

```
About to write to /Users/curtislee/Desktop/package/package.json:

{
  "name": "my package",
  "version": "1.0.0",
  "description": "package test",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "test",
    "testing"
  ],
  "author": "Curtis",
  "license": "ISC"
}


Is this OK? (yes)
```

Your file will now be created! From there, you can begin adding your `npm` scripts, as well as install and manage any required dependencies for your project.

---

## 02 - Installing dependencies

Once you've created your `package.json` file, you are now ready to start installing packages to your project, using:

`npm install <package name> --save`

This will make sure to install the package to your project in a `node_modules` folder, but also `--save` it to the `package.json` file you created.

If required, you can save dependencies for development purposes only, using `--save-dev` in order to store the packages under `"devDependencies": {..}` instead. When someone downloads or clones your project, they can simple run `npm install` in the root folder in order to replicate your build environment and start using all of the packages.

```
"devDependencies": {
    "autoprefixer": "^7.1.4",
    "concat": "^1.0.3",
    "node-sass": "^4.5.3",
    "npm-run-all": "^4.1.1",
    "postcss-cli": "^4.1.1"
  }
```

#### A few examples:

```
$ npm install node-sass
$ npm install jquery
```

If you get an error message, you may need to use `sudo` in order to install it as the root user:

```
$ sudo npm install node-sass
Password:

// This applies to all installs in general
```

It's really easy to install and manage these dependencies, and from here, you can begin writing the required scripts in order to use them in conjunction with other packages!

---

## 03 - Adding `npm` [Scripts](https://docs.npmjs.com/misc/scripts.html)

In order to run your developer environment, you need to specify which scripts you'd like to execute using commands such as `npm start`, and `npm build:css`.

To do this, you need to make sure you are within the `"scripts": {..},` code block, and from there, you can pass a script such as:

`"watch:sass": "node-sass sass/main.scss css/style.css -w",`

which would essentially watch for changes in the `.scss` file, and automatically compile them to the `.css` file whenever you make (and save) a change.

An example of using multiple scripts could look something like this:

```
"scripts": {
    "watch:sass": "node-sass sass/main.scss css/style.css -w",
    "devserver": "live-server",
    "start": "npm-run-all --parallel devserver watch:sass",
    "compile:sass": "node-sass sass/main.scss css/style.comp.css",
    "prefix:css": "postcss --use autoprefixer -b 'last 10 versions' css/style.comp.css -o css/style.prefix.css",
    "compress:css": "node-sass css/style.prefix.css css/style.css --output-style compressed",
    "build:css": "npm-run-all compile:sass prefix:css compress:css"
  },
```

#### A breakdown of the above scripts:

- You have **7** scripts in total being executed in the above project, with **5** of those being performed at different stages by **2** specified command scripts.

- Using `--parallel`, the `"start"` command makes sure to execute both the `"watch:sass"` and `"devserver"` scripts at the same time, and the `"build:css"` command makes sure to execute the remaining **3** in stages.

- In order to prevent the wrong scripts from all running at once, such as the scripts you may not need until the end for example, you can simply parent them to a main command such as `"start"`, which would then allow you to run `"$ npm start"` in the terminal.

- Each script begins with its own command, so theoretically speaking, you could run each of the scripts individually, but that defeats the purpose of using them in conjunction with each other.

#### Using the scripts:

As mentioned above, simply `cd` into your master directory, and use `$ npm start` to run the first **2** scripts. This should immediately launch your server, and also begin detecting any changes in the `main.scss` file, which is what the `sass watcher` uses to map it to the `main.css -o` file.

You can also append something like `--browser=firefox` to the `devserver` script for example, to make sure that the server always launches in Firefox by default (good when working with grids...).

When the site / application is complete, you can run the next command `npm build:css`, in order to compile everything accordingly, compress the `CSS` file for better load times, and automatically `prefix` any required properties so that they are supported by older browsers.

#### Extra reference:

You could also add something like `"concat:css"` if you are using multiple `CSS` files (including libraries) within a project. This would allow you to select an output file, as well the list of files you wish to join together in the build process, and using [concat](https://www.npmjs.com/package/concat), you can then compile them into one file so that the browser only has to request the `CSS` code once instead of pulling in multiple `stylesheets`.

---

## 04 - Updating dependencies

If you ever duplicate a `package.json` file, you can run a version check on the packages and update them if required. If you aren't starting from afresh, and you're instead jumping on an existing project that depends on these packages, then make sure that the necessary rick assessments have been considered before updating, as you may end up breaking a thing or two...

To prevent the latter from happening accidentally, `npm` requires you to install `npm-check-updates`, and then run it in order to automatically check for updates and convert all of your projects packages to their latest versions.

You can also see [here](https://flaviocopes.com/update-npm-dependencies/), but the process from scratch would work like this:

1. `$ npm install -g npm-check-updates`

2. `$ ncu -u`

3. `$ npm update`

If you haven't already got a `node_modules` folder in your project, simply run `npm install` afterwards, and watch as your dependencies update to their latest versions.

Your overall terminal output should look something like:

(`macOS`)

```
 autoprefixer  ^7.1.4  →   ^9.6.1 
 node-sass     ^4.5.3  →  ^4.12.0 
 npm-run-all   ^4.1.1  →   ^4.1.5 
 postcss-cli   ^4.1.1  →   ^6.1.3 

Run npm install to install new versions.
```

Once you run the install, it will detail any other bits of information (including any vulnerabilities), and you can then begin using the latest versions of each package within your project!

Whenever you install a new package, it installs the latest version by default anyway, and you can run any future updates using the above method if it's ever required.

---

## 05 - Conclusion
That concludes the basic overview for setting up and managing your build environment. I will most likely continue adding to this in the future as I learn new things, but currently, the above gives me a good foundation for setting up `dev` environments when working with preprocessors such as Sass (SCSS), live servers, and much more!

This includes _real_ examples for things such as setting up a `sass watcher` and `compiler` as well, so it will definitely be of some use.



**Curtis Lee, 2019**