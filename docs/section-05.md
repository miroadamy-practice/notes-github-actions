# 05 Creating CI/CD Workflow to automate test and deployment

We need something to deploy as part of the deployment app.

## 05-30 Creating a ReactJS Boilerplate Application


See <https://create-react-app.dev/docs/getting-started>

`npx create-react-app react-app --use-npm`

I will use same repo - in a Gitpod

inside the <https://github.com/miroadamy-practice/github-actions-demo-1>

```sh
gitpod /workspace/github-actions-demo-1 (main) $ npx create-react-app react-app --use-npm
Need to install the following packages:
  create-react-app
Ok to proceed? (y) y
npm WARN deprecated tar@2.2.2: This version of tar is no longer supported, and will not receive security updates. Please upgrade asap.

Creating a new React app in /workspace/github-actions-demo-1/react-app.

Installing packages. This might take a couple of minutes.
Installing react, react-dom, and react-scripts with cra-template...


added 1391 packages in 1m

207 packages are looking for funding
  run `npm fund` for details

Installing template dependencies using npm...

added 55 packages in 4s

207 packages are looking for funding
  run `npm fund` for details
Removing template package using npm...


removed 1 package, and audited 1446 packages in 2s

207 packages are looking for funding
  run `npm fund` for details

6 high severity vulnerabilities

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.

Success! Created react-app at /workspace/github-actions-demo-1/react-app
Inside that directory, you can run several commands:

  npm start
    Starts the development server.

  npm run build
    Bundles the app into static files for production.

  npm test
    Starts the test runner.

  npm run eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!

We suggest that you begin by typing:

  cd react-app
  npm start

Happy hacking!
npm notice 
npm notice New minor version of npm available! 8.11.0 -> 8.18.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v8.18.0
npm notice Run npm install -g npm@8.18.0 to update!
npm notice 

```


aa

## 05-31 Building & Testing the Application Locally

See package.json => defines scripts

```json
...
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  ...
```

See also the [README.md](https://github.com/miroadamy-practice/github-actions-demo-1/blob/main/react-app/README.md)

We care only about tests

`npm run test`

```sh
 PASS  src/App.test.js
  ✓ renders learn react link (22 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.331 s, estimated 1 s
Ran all test suites.

Watch Usage
 › Press f to run only failed tests.
 › Press o to only run tests related to changed files.
 › Press q to quit watch mode.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press Enter to trigger a test run.
```

Disable watch mode: `CI=true`

```sh
gitpod /workspace/github-actions-demo-1/react-app (main) $ CI=true npm run test

> react-app@0.1.0 test
> react-scripts test

PASS src/App.test.js
  ✓ renders learn react link (44 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.117 s
Ran all test suites.
```

Test checks if we have text `Learn React`

Generate coverage report:

```sh
gitpod /workspace/github-actions-demo-1/react-app (main) $ CI=true npm run test -- --coverage

> react-app@0.1.0 test
> react-scripts test "--coverage"

PASS src/App.test.js
  ✓ renders learn react link (33 ms)

--------------------|---------|----------|---------|---------|-------------------
File                | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s 
--------------------|---------|----------|---------|---------|-------------------
All files           |    8.33 |        0 |   33.33 |    8.33 |                   
 App.js             |     100 |      100 |     100 |     100 |                   
 index.js           |       0 |      100 |     100 |       0 | 7-17              
 reportWebVitals.js |       0 |        0 |       0 |       0 | 1-8               
--------------------|---------|----------|---------|---------|-------------------
Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.259 s
Ran all test suites.

```

It also generates file in lcov-report:

Preview on GitPod:

```sh
gitpod /workspace/github-actions-demo-1/react-app (main) $ pwd
/workspace/github-actions-demo-1/react-app
gitpod /workspace/github-actions-demo-1/react-app (main) $ cd coverage/lcov-report/
gitpod /workspace/github-actions-demo-1/react-app/coverage/lcov-report (main) $ python -m http.server 8000 
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```


![html](./img/html-preview.png)

Build for production => `npm run build` => `./build`

This is what needs to be deployed for production

## 05-32 Deploying app using Surge


## 05-33 Using Prettier to Check for Code Formatting Rules

aa

