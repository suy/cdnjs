[![Build Status](https://travis-ci.org/cdnjs/cdnjs.png?branch=master)](https://travis-ci.org/cdnjs/cdnjs) [![Dependency Status](https://david-dm.org/cdnjs/cdnjs.png?theme=shields.io)](https://david-dm.org/cdnjs/cdnjs) [![devDependency Status](https://david-dm.org/cdnjs/cdnjs/dev-status.png?theme=shields.io)](https://david-dm.org/cdnjs/cdnjs#info=devDependencies)

# cdnjs Library Repository

[cdnjs](http://github.com/cdnjs/cdnjs) is the repository mirroring all library assets on [cdnjs.cloudflare.com](http://cdnjs.cloudflare.com). [Thomas Davis](https://twitter.com/neutralthoughts) and [Ryan Kirkman](https://twitter.com/ryan_kirkman) created cdnjs, [Drew Freyling](http://decompile.it/blog/) and [Pete Cooper](http://petecooper.org) are maintainers.

We will host any production version of any library, subject to licence permissions.

  * Beta, release candidate and alpha releases are not usually considered ready for full production status. Requests for pre-release versions of libraries _may_ be declined after peer review.

Please raise a pull request for a _new_ library addition proposal. Likewise, please raise a pull request for an _older_ production version of a existing library if your site still uses it.

## Adding a new or updating an existing library

cdnjs relies on user-submitted pull requests and automatic updating via `npm` to populate and update libraries.

To add a new library, or update an existing library outside of `npm`, start by [forking the cdnjs repo](https://github.com/cdnjs/cdnjs/fork) to your own GitHub account.

If you're adding/modifying outside of the GitHub browser interface, for example on the command line or with the GitHub desktop application, you will need to additionally install `node` locally. For more information on installing `node`, please refer to [nodejs.org](http://nodejs.org).

When you have forked the cdnjs repo, add your library to it. Libraries are stored in the `ajax/libs` directory. Each library has its own subdirectory of `ajax/libs` and each version of the library has its own subdirectory of the library directory name, for example:

```
/ajax/libs/jquery/2.0.0/
```

## Conventions

You should consider the following when adding to or updating the library:

* Filenames should _not_ include a version number and be _lowercase_.
  * This is OK: `useful.min.js`, but this is not: `useful-2.0.1.min.js`.

* JavaScript & CSS files should be minified to reduce network and browser overhead.
  * If the library doesn't already provide a minified version, cdnjs's preferred JavaScript minifier is [UglifyJS](http://marijnhaverbeke.nl/uglifyjs "UglifyJS")

* If you are updating a library, please try to maintain consistency with the existing file and directory structure.
  * There will be occasions, particularly with major version increments, where this is not practical.

## Create or update `package.json`

Each library has a corresponding `package.json`, written in `npm` format (see `test/schemata/npm-package.json` for details or use another `package.json` to crib from - it's pretty self-explanatory).

When an existing library is updated, the details in `package.json` should be updated where required. For example, if a new version of the library is added, the version number may need changing.

## Install `npm test` dependencies

If you don't have vows installed do so by running `npm install -g vows`.


## Run `npm test` to check all is well

If you're updating the library outside of `npm` or the GitHub browser, you should run `npm test` from the library directory to ensure everything is OK.

If you run `npm test` and see no errors, all is well; resolve any errors before you raise your pull request and re-run `npm test` to ensure everything works.

If you see an error then run `npm install` before running `npm test` 
```
sh: vows: command not found
npm ERR! Test failed.  See above for more details.
npm ERR! not ok code 0
```

## Pull request pre-flight checks

* Have you followed the library directory structure?
* Does a valid and accurate `package.json` exist for the library?
* Have you minified JavaScript and CSS?
* Did `npm test` check out OK?

...if so, great! You're ready to raise a pull request.

## Raising a pull request

Please restrict your pull request to one library. You can include >1 version/release of a library in a single pull request.

From a maintenance standpoint, it's much more straightforward to process pull requests where there is one commit for one library.

In your pull request title, tell us what you're doing. If you are the author of the library, please add `[author]` to the pull request title.

__Please include the following in your pull request:__

* The origin of your new files
  * e.g., where you downloaded the version from

A URL is ideal. Providing the origin of your files is very helpful as the cdnjs project is peer-reviewed. Practically speaking, it also helps us process your pull request more efficiently, which means your files go live sooner. Help us and we'll help you back.

## Enabling `npm` auto update

__We are currently in the process of converting as many libraries to NPM auto update as possible. For a bit of fun, cdnjs will send you **$5 USD in Bitcoin for each library you convert to NPM auto-update**. If you could tag your pull request with [BC] and throw your Bitcoin address in the commit, we will send your payment through as soon as possible. If you would like to keep your BC address private, send an email to thomasalwyndavis@gmail.com with the details.__

cdnjs automatically updates libraries that are known to be hosted on `npm` e.g., Lodash. This auto-update script runs every 15 minutes.

To add an `npm` hook to a library, update the `package.json` with configuration details and submit your pull request. An example configuration:

```
  "npmName": "lodash",
  "npmFileMap": [{
    "basePath": "/dist/",
    "files": [
      "*.js"
    ]
  }],
```

* `npmName` should map to the name of the library on `npm`
* `npmFileMap` is a list of files to take from the `npm` tarball and host on cdnjs
* `basePath` will be ignored when copying over to the CDN
* `files` is a pattern matcher allowing selection of multiple files

The above example looks in the tarball whose structure might look like this:

```
|__dist
| |__lodash.compat.js
| |__lodash.compat.min.js
| |__lodash.js
| |__lodash.min.js
| |__lodash.underscore.js
| |__lodash.underscore.min.js
|__LICENSE.txt
|__lodash.js
|__package.json
|__README.md
```

The auto-update process will look for `dist` inside the named tarball and copy all the JavaScript file to cdnjs, minus the `dist` path. The resulting files in cdnjs will be: 

```
|__ajax
  |__libs
    |__lodash.js
      |__x.y.z
        |__lodash.compat.js
        |__lodash.compat.min.js
        |__lodash.js
        |__lodash.min.js
        |__lodash.underscore.js
        |__lodash.underscore.min.js
```

...where `x.y.z` is the version number, extracted from the `package.json` on npm.

## API

You can search cdnjs via our API:

```
http://api.cdnjs.com/libraries
```

Without any query parameters it will return the name and main file URL of every library on cdnjs. To search, use:

```
http://api.cdnjs.com/libraries?search=jquery
```

If you would like more data, use the fields parameter which takes comma-separated values:

```
http://api.cdnjs.com/libraries?search=jquery&fields=version,description
```

To get a list of all files for that library, use the assets field:

```
http://api.cdnjs.com/libraries?search=jquery&fields=assets
```

Other fields available are:

```
version
description
homepage
keywords
maintainers
assets
```

The API is served over Cloudflare with a six-hour expiry for requests.

## Extensions, Plugins, Resources

[Extensions, Plugins, Resources](https://github.com/cdnjs/cdnjs/wiki/Extensions%2C-Plugins%2C-Resources)
