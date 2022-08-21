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

Surge - to deploy static websites: <https://surge.sh/>

```sh
$ npm install --global surge
$ surge
  project: ~/Jane/Desktop/my-project/
   domain: my-project.surge.sh
   upload: [============]
Success! Published and running at my-project.surge.sh
```

Does not work:

```sh
gitpod /workspace/github-actions-demo-1/react-app (main) $ surge

   Welcome to surge! (surge.sh)
   Login (or create surge account) by entering email & password.

          email: miro.adamy@gmail.com
       password: 

   Running as miro.adamy@gmail.com (Student)

        project: /workspace/github-actions-demo-1/react-app/
         domain: instinctive-string.surge.sh
         upload: [====================] 100% eta: 0.0s (17 files, 1266103 bytes)
            CDN: [====================] 100%
     encryption: *.surge.sh, surge.sh (270 days)
             IP: 138.197.235.123

   Success! - Published to instinctive-string.surge.sh
```

Must run after `npm run build` `cd build; surge`

Use `https://instinctive-string.surge.sh/`

Must use same domain every time

## 05-33 Using Prettier to Check for Code Formatting Rules

Enforce fomatting rules - <https://prettier.io/>

`npm install --save-dev --save-exact prettier`

Add `.prettierrc` - from Web config

Add `.prettierignore`

Run locally:

```sh
gitpod /workspace/github-actions-demo-1/react-app (main) $ npx prettier --check "**/*.js" 
Checking formatting...
[warn] src/App.js
[warn] src/App.test.js
[warn] src/index.js
[warn] src/reportWebVitals.js
[warn] Code style issues found in 4 files. Forgot to run Prettier?
```

=> returns != 0 exit code

Format it:

```sh
gitpod /workspace/github-actions-demo-1/react-app (main) $ npx prettier --write "**/*.js" 
src/App.js 63ms
src/App.test.js 9ms
src/index.js 10ms
src/reportWebVitals.js 8ms
src/setupTests.js 3ms
---
gitpod /workspace/github-actions-demo-1/react-app (main) $ npx prettier --check "**/*.js" 
Checking formatting...
All matched files use Prettier code style!
```

Add custom script to `package.json`

```json
...
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "format:check": "prettier --check \"**/*.{js,jsx,yml,yaml,json,css,scss,md}\"",
    "format": "prettier --write \"**/*.{js,jsx,yml,yaml,json,css,scss,md}\""
  },
...
```

and use as `npm run format:check`

```sh
gitpod /workspace/github-actions-demo-1/react-app (main) $ npm run format:check

> react-app@0.1.0 format:check
> prettier --check "**/*.{js,jsx,yml,yaml,json,css,scss,md}"

Checking formatting...
All matched files use Prettier code style!

---
gitpod /workspace/github-actions-demo-1/react-app (main) $ npm run format

> react-app@0.1.0 format
> prettier --write "**/*.{js,jsx,yml,yaml,json,css,scss,md}"

public/manifest.json 52ms
README.md 57ms
src/App.css 43ms
src/App.js 18ms
src/App.test.js 9ms
src/index.css 6ms
src/index.js 7ms
src/reportWebVitals.js 9ms
src/setupTests.js 3ms
```

3 things for workflow:

* test
* format
* build
* deploy

## 05-34 Planning the workflow

Planning is about: Multiple team members - pushing code, which branches, which PRs

* artifacts => logs (can be downloaded)

master + develop => protected branches, no push, only PR => approved

`master` => deploy to production
`develop` => latest integrated develop code

Feature branch => PR => triggers WF that runs tests and formatting => approve => merge to develop

Merge to develop => WF => tests again, deploys to staging

Merge to master => PR from develop, WF => tests, formatting, review and approve => merge, triggers deployment to production

### Workflows

#### Steps Feature PR

* install dependencies
* check code fmt
* run automated tests
* upload code coverage as artifact
* cache dependecies

#### Steps merge to Develop

* install dependencies
* check code fmt
* run automated tests
* upload code coverage as artifact
* build project
* upload build as artifact
* deploy to staging
* cache dependencies

#### Steps Develop PR

* install dependencies
* check code fmt
* run automated tests
* upload code coverage as artifact
* cache dependecies

#### Steps merge to master

* install dependencies
* check code fmt
* run automated tests
* upload code coverage as artifact
* upload build as artifact
* create a release
* deploy to prod
* upload coverage to Codecov
* cache dependencies

Job failure: => create issue
Issue created => slack message
Release created => send slack message

### Creating release

* releases tab
* release has version, change log, assets

We will generate version number and release notes

## 05-35 Setting Up Our Repository

CODEOWNERS file: <https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners>

* create a new file called CODEOWNERS in the root, docs/, or .github/ directory of the repository, in the branch where you'd like to add the code owners.
* different types of files / folder can be owned by different owners
* code owners must have write permissions for the repository
* Code owners are automatically requested for review when someone opens a pull request that modifies code that they own.
* CODEOWNERS file uses a pattern that follows most of the same rules used in gitignore files, with some exceptions. The pattern is followed by one or more GitHub usernames or team names using the standard @username or @org/team-name format.

### Example of CODEOWNERS file

```sh
# This is a comment.
# Each line is a file pattern followed by one or more owners.

# These owners will be the default owners for everything in
# the repo. Unless a later match takes precedence,
# @global-owner1 and @global-owner2 will be requested for
# review when someone opens a pull request.
*       @global-owner1 @global-owner2

# Order is important; the last matching pattern takes the most
# precedence. When someone opens a pull request that only
# modifies JS files, only @js-owner and not the global
# owner(s) will be requested for a review.
*.js    @js-owner

# You can also use email addresses if you prefer. They'll be
# used to look up users just like we do for commit author
# emails.
*.go docs@example.com

# Teams can be specified as code owners as well. Teams should
# be identified in the format @org/team-name. Teams must have
# explicit write access to the repository. In this example,
# the octocats team in the octo-org organization owns all .txt files.
*.txt @octo-org/octocats

# In this example, @doctocat owns any files in the build/logs
# directory at the root of the repository and any of its
# subdirectories.
/build/logs/ @doctocat

# The `docs/*` pattern will match files like
# `docs/getting-started.md` but not further nested files like
# `docs/build-app/troubleshooting.md`.
docs/*  docs@example.com

# In this example, @octocat owns any file in an apps directory
# anywhere in your repository.
apps/ @octocat

# In this example, @doctocat owns any file in the `/docs`
# directory in the root of your repository and any of its
# subdirectories.
/docs/ @doctocat

# In this example, any change inside the `/scripts` directory
# will require approval from @doctocat or @octocat.
/scripts/ @doctocat @octocat

# In this example, @octocat owns any file in the `/apps`
# directory in the root of your repository except for the `/apps/github`
# subdirectory, as its owners are left empty.
/apps/ @octocat
/apps/github
```

I will clear all workflows and set up develop branch

### Branch protection rules

Go to <https://github.com/miroadamy-practice/github-actions-demo-1/settings/branches>

I cannot do this - only one user, cannot approve own PR

Added CODEOWNERS file, removed restrictions - can push and merge locally

## 05-36 Creating the Develop PR workflow

In workflow branch

=> set up that `build` is required for branch merging

I had to remove requirement on PR => cannot approve own PR.
Ali uses second identity, too much hassle

The `ci.yaml` so far:

```yaml
name: CI 
on:
  pull_request:
    branches:
      - develop

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: use Node 16
        uses: actions/setup-node@v3
        with: 
            node-version: "16"
      - name: Install dependencies
        run: |
          cd react-app
          npm ci 
      - name: Check format
        run: |
          cd react-app
          npm run format:check
      - name: Test
        run: |
          cd react-app
          npm test -- --coverage
        env:
          CI: true
        
          
```

Started to use local VS-Code to save gitpod minutes

## 05-37 Creating the Develop Merge workflow

Running when the feature branch is merged to develop

We can duplicate and modify => duplicate code, or use if conditions

Same branch, using IF statements

For surge, need to use the old domain. Use `surge list` to see the domains

```sh
➜  github-actions-demo-1 git:(workflow) ✗ npx surge list
env: node: No such file or directory
➜  github-actions-demo-1 git:(workflow) ✗ nvm use default
🚨 NVM not loaded! Loading now...
Now using node v16.13.1 (npm v8.3.1)
➜  github-actions-demo-1 git:(workflow) ✗ npx surge list 
Need to install the following packages:
  surge

...

➜  react-app git:(workflow) ✗ npx surge list

   Welcome to surge! (surge.sh)
   Login (or create surge account) by entering email & password.

          email: miro.adamy@gmail.com
       password: 

   1661011326958 instinctive-string.surge.sh   18 hours ago   surge   surge.sh   Standard 

```

Added CNAME file for surge

Consider: [Adding custom domain](https://surge.sh/help/adding-a-custom-domain)

We also need to generate surge token `npx surge token`

```sh
✗ npx surge whoami

   miro.adamy@gmail.com - Student
```

After pushing the workflow branch, the pull_request action runs (because we have the condition to update) - the `build project` and `deploy to staging` did NOT
run: <https://github.com/miroadamy-practice/github-actions-demo-1/runs/7938187576?check_suite_focus=true>

I had an error in build, pushed new commit on `workflow` after the PR was closed:

* The PR stays closed, no CI runs
* need to open new one => <https://github.com/miroadamy-practice/github-actions-demo-1/pull/15>
* triggers the PR action again => <https://github.com/miroadamy-practice/github-actions-demo-1/actions/runs/2898176741>
* merge again => <https://github.com/miroadamy-practice/github-actions-demo-1/actions/runs/2898183804>
* now it worked - <https://github.com/miroadamy-practice/github-actions-demo-1/runs/7938278832?check_suite_focus=true>

test - new branch - `testing-deploy-staging`, change test, push, merge PR

* second attempt (formatting) => <https://github.com/miroadamy-practice/github-actions-demo-1/actions/runs/2898317879>
* merged => <https://github.com/miroadamy-practice/github-actions-demo-1/actions/runs/2898322726>

Goto <https://instinctive-string.surge.sh/>

![changed](./img/changed-app-1.png)

The Pipeline so far:

```yaml
{% raw %}
name: CI 
on:
  pull_request:
    branches: [develop]
  push:
    branches: [develop]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: use Node 16
        uses: actions/setup-node@v3
        with: 
            node-version: "16"
      - name: Install dependencies
        run: |
          cd react-app
          npm ci 
      - name: Check format
        run: |
          cd react-app
          npm run format:check
      - name: Test
        run: |
          cd react-app
          npm test -- --coverage
        env:
          CI: true
      - name: Build project
        if: github.event_name == 'push'
        run: |
          cd react-app 
          npm run build
      - name: Deploy to Staging
        if: github.event_name == 'push'
        run: npx surge --project ./react-app/build --domain instinctive-string.surge.sh
        env:
          SURGE_LOGIN: ${{ secrets.SURGE_LOGIN }}
          SURGE_TOKEN: ${{ secrets.SURGE_TOKEN }}

{% endraw %}
```

## 05-38 Caching NPM Dependencies

See [docs](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows)

Use `actions/cache` => <https://github.com/actions/cache/blob/main/examples.md#node---npm>

Using key: if dependencies has changed. This is why we use `{% raw %}${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}{% endraw %}`

The restore-key: way how to locate older cache (partial)

First run: <https://github.com/miroadamy-practice/github-actions-demo-1/runs/7938937950?check_suite_focus=true>

```sh
Run actions/cache@v3
Cache not found for input keys: Linux-node-4db6cb19e3209c55595fa8d57848b0d562f090ffba954889c82d1fece29e8e81, Linux-node-

....

Post-cache action - added by GHA

Post job cleanup.
/usr/bin/tar --posix --use-compress-program zstdmt -cf cache.tzst --exclude cache.tzst -P -C /home/runner/work/github-actions-demo-1/github-actions-demo-1 --files-from manifest.txt
Cache Size: ~42 MB (44206882 B)
Cache saved successfully
Cache saved with key: Linux-node-4db6cb19e3209c55595fa8d57848b0d562f090ffba954889c82d1fece29e8e81
```

In the ^^^ - `npm ci` took 26 sec

Let's merge and push:

Did NOT find the cache => different branch !!

```sh
Run actions/cache@v3
Cache not found for input keys: Linux-node-4db6cb19e3209c55595fa8d57848b0d562f090ffba954889c82d1fece29e8e81, Linux-node-
```

Push agains develop: <https://github.com/miroadamy-practice/github-actions-demo-1/runs/7938988403?check_suite_focus=true>

```sh
Run actions/cache@v3
Received 37748736 of 50097829 (75.4%), 35.9 MBs/sec
Received 50097829 of 50097829 (100.0%), 42.5 MBs/sec
Cache Size: ~48 MB (50097829 B)
/usr/bin/tar --use-compress-program unzstd -xf /home/runner/work/_temp/ce4a29e2-864c-405b-897a-5a596fb2fead/cache.tzst -P -C /home/runner/work/github-actions-demo-1/github-actions-demo-1
Cache restored successfully

Cache restored from key: Linux-node-4db6cb19e3209c55595fa8d57848b0d562f090ffba954889c82d1fece29e8e81
```

In the ^^^ - `npm ci` took 13 sec (50%)

## 05-41 a

zz

## 05-42 a

zz

## 05-43 a

zz

## 05-44 a

zz

## 05-45 a

zz

## 05-46 a

zz
