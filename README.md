# Getting Started on Travis CI

This is an example application written in Node.js for
introducing users to basic features of Travis CI. It's based
off of the self-driven demo, [
travis-ci/travis-intro-node](https://github.com/travis-ci/travis-intro-node).

## Welcome to Travis CI!
We're going to learn how to create a project on Travis CI, 
add testing and deployment configurations, and then try out 
some new Travis CI features. 

### Step 1: Clone this Project and Look Around
You'll need a working [Node.js](https://nodejs.org/en/) and
[npm](https://www.npmjs.com/) setup to run local tests. You'll also want to
[clone the project](https://help.github.com/articles/cloning-a-repository/). 

You can start the app with:
```sh-session
$ node -e 'app = require("./lib/app"); app.start()'
```
...and then stop the app and run tests with:

```sh-session
$ npm install
$ ./node_modules/mocha/bin/mocha
```

### Step 2: Sign-Up for Travis CI

Head over to [travis-ci.com](https://travis-ci.com).
If you don't yet have a Travis CI account, you would be asked to
authorize Travis CI to access user data.


You can allow Travis CI to access all your repositories, or select few.
For the purpose of this demo, we'll allow Travis CI to access only this repository.
Click on the radio button "Only Select Repositories", and search for this repo. Once you find it, click on "Approve and Install".

### Step 3: Create a `.travis.yml`
The `.travis.yml` is Travis CI's configuration file. It lives in
the root of your project directory structure, and it's what Travis CI uses to generate your automated build script. Travis CI builds in reasonable defaults, so you can start building this project with very little setup: 

**Add a Basic `.travis.yml`**: 
 1. Create a `.travis.yml` file in the root of your project
 1. Add the following to the new file:
 ```yml
language: node_js
node_js: '8'
 ```

**Commit the `.travis.yml`**: It is time to trigger our first build! To do so, we need to push a new commit: 
```sh-session
$ git commit -m "add travis ci"
$ git push origin master
```
yay! The build passes! 🎉

**Test More Runtimes**: Now that we have a basic `travis.yml` 
configured, we can do some more testing. The [Build Matrix](https://docs.travis-ci.com/user/customizing-the-build/#build-matrix) feature lets quickly add testing against multiple language runtimes, other dependencies, or environment variables. To add one for multiple node runtimes, update the `.travis.yml` to look like the following:

```
language: node_js
node_js:
 - '8'
 - '9'
 - '10'
```
This is a great thing to do via a pull request, so we'll do the following:

```sh-session
$ git checkout -b add-more-nodejs
$ git add .travis.yml
$ git commit -m "add testing for node 9 and 10"
$ git push origin add-more-nodejs
```
Navigate to the repository, and open up a pull request. You'll see
the Check Runs (CI Tests) happen, and once everything passes, it's time to merge the PR! 

### Step 4: Deploy to GitHub Releases
Now that we have a build that we're confident of, we're going to deploy it as a GitHub Release. Read up in the [notes in this project](/GITHUB_RELEASES_NOTES.md) for more info about GitHub releases. For the sakes of this demo, we'll start with a `public_repo` scoped GitHub API token, and add it as an environment variable to Travis CI like so:

1. Go to Travis CI → this project → 'Settings' → Environment Variables
1. Add a new Environment Variable and name it `GITHUB_OAUTH_TOKEN`
1. Paste the token into the Value field
1. Leave the "Display value in build log" in the "OFF" position.
1. Click on the "Add" button

Or, 

Using the [cli](https://github.com/travis-ci/travis.rb), run:
```sh-session
$ travis encrypt <repo_token>
```
... and add the output to your `.travis.yml`.

Next, we'll need to update our `.travis.yml` to add a deploy step: 

```yml
language: node_js
node_js:
 - '8'
 - '9'
 - '10'
deploy:
  provider: releases
  api_key: $GITHUB_OAUTH_TOKEN
  file: lib/app.js
  on:
    tags: true
```
and, we'll need to tag this for release:

```sh-session
$ git commit -m "Deploy to GitHub Releases"
$ git tag v0.0.1
$ git push origin --tags
```

### Step 5: Test More Things! 
Now that we have a working GitHub release, what if we expanded our testing configuration to cover some more environments?

Let's update our `.travis.yml` to make use of the new Windows build environment, and use Build Stages to make this a bit more organized: 

```yml
language: node_js
os:
 - windows
 - linux
node_js:
 - '8'
 - '9'
 - '10'
jobs:
  include: 
   - stage: "Deploy to GitHub Releases"
     os: linux
     node_js: '9'
     deploy:
      provider: releases
      api_key: $GITHUB_OAUTH_TOKEN
      file: lib/app.js
      on:
       tags: true
```

Let's create this as a pull request, so we can see what happens. Once it all looks good, we'd tag it for release like before. 

## Next Steps...

Now that you're all setup on Travis CI, there's tons more you can do. Check out some of these resources for ideas on where to go next, and where you can find us on the interwebz:

* [Travis CI (main site)](https://travis-ci.com/)
* [Travis CI Enterprise](https://enterprise.travis-ci.com)
* [Travis CI for Education](http://education.travis-ci.com/)
* [Community Forum](http://travis-ci.community/)
* [Documentation](https://docs.travis-ci.com)
* [Travis CI Blog](https://blog.travis-ci.com/)
* [Twitter - @travisci](https://twitter.com/travisci)
* [Changelog](https://changelog.travis-ci.com/)
* [Enterprise Changelog](https://enterprise-changelog.travis-ci.com/)
* [Furher Reading on CI](https://github.com/acnagy/travis-intro-node/tree/06.deployment-pt2#further-reading)
