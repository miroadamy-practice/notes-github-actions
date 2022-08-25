# 06 Creating own Github Actions

Instead of ugly long scripts we can use actions.

## 06-52 Github Actions Overview

Actions can:

* talk to 3rd party
* use GH API
* deploy code

If in public repo, can refer to them

Private actions: live in the same repo, use path

Actions can be written in Javascript (fastest)
or in any language inside Docker container => Linux only

Action can be referred:

* using branch name => actions/name@master => can break
* using commit sha => actions/name@7bf21e
* using tag (preferred) => actions/name@v1

We will re-create HelloJavascript action

## 06-53 Creating Simple JS Action

It will be private action => `./.github/actions/hello/`

Create the action.yml

```yml
name: Hello World
author: Miro
description: Following the course
inputs:
  whom-to-greet:
    description: "Whom to greet"
    required: true
    default: World
outputs:
  time:
    description: 'The greeting time'
runs:
  using: "node16"
  main: index.js

```

Create the `index.js`

```js
const core = require('@actions/core');
const githb = require('@actions/github');

// lets simulate an error => on GH action, it will seem to be successful
try {
    // throw( new Error('Some simulated error'));

    const name = core.getInput('whom-to-greet');
    console.log(`Hello, ${name}`);

    const time = new Date();
    core.setOutput("time", time.toTimeString());

    console.log.log(JSON.stringify(github, null, '\t'));
} catch (error) {}
    core.setFailed('I failed');
}
```

Need some packages:

```sh
➜  github-actions-demo-1 git:(custom-action-1) ✗ cd .github/actions/hello 


➜  hello git:(custom-action-1) ✗ npm list
env: node: No such file or directory

# need Node 16

➜  hello git:(custom-action-1) ✗ nvm use default
🚨 NVM not loaded! Loading now...
Now using node v16.13.1 (npm v8.3.1)

➜  hello git:(custom-action-1) ✗ npm list       
/Users/miroadamy/prj/MIRROR/github.com/miroadamy-practice/src/github-actions-demo-1/.github/actions/hello
└── (empty)

➜  hello git:(custom-action-1) ✗ npm install @actions/github --save

added 23 packages, and audited 24 packages in 6s

found 0 vulnerabilities

➜  hello git:(custom-action-1) ✗ npm install @actions/core --save  
⸨⠂⠂⠂⠂⠂⠂⠂⠂⠂⠂⠂⠂⠂⠂⠂⠂⠂⠂⸩ ⠴ idealTree:hello: sill idealTree buildDeps

added 2 packages, and audited 26 packages in 14s

found 0 vulnerabilities
➜  hello git:(custom-action-1) ✗ 

➜  hello git:(custom-action-1) ✗ npm list                        
hello@ /Users/miroadamy/prj/MIRROR/github.com/miroadamy-practice/src/github-actions-demo-1/.github/actions/hello
├── @actions/core@1.9.1
└── @actions/github@5.0.3

```

Adding test WF to invoke it:

```yaml
name: Test custom workflow
on:
  push:
    branches:
      - "custom-action*"

jobs:
  invoke:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: custom wf
        id: hello
        uses: ./.github/actions/hello
        with:
          whom-to-greet: 'Miro'
      - run: |
          echo "Time ${{ steps.hello.outputs.time}}"

```

When running these => does not find it, as they are not installed

```sh
node:internal/modules/cjs/loader:936
  throw err;
  ^

Error: Cannot find module '@actions/core'
Require stack:
- /home/runner/work/github-actions-demo-1/github-actions-demo-1/.github/actions/hello/index.js
    at Function.Module._resolveFilename (node:internal/modules/cjs/loader:933:15)
    at Function.Module._load (node:internal/modules/cjs/loader:778:27)
    at Module.require (node:internal/modules/cjs/loader:1005:19)
    at require (node:internal/modules/cjs/helpers:102:18)
    at Object.<anonymous> (/home/runner/work/github-actions-demo-1/github-actions-demo-1/.github/actions/hello/index.js:1:14)
    at Module._compile (node:internal/modules/cjs/loader:1101:14)
    at Object.Module._extensions..js (node:internal/modules/cjs/loader:1153:10)
    at Module.load (node:internal/modules/cjs/loader:981:32)
    at Function.Module._load (node:internal/modules/cjs/loader:822:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:81:12) {
  code: 'MODULE_NOT_FOUND',
  requireStack: [
    '/home/runner/work/github-actions-demo-1/github-actions-demo-1/.github/actions/hello/index.js'
  ]
}

```

We need to bundle

## 06-54 - Bundling js code to one file

aaa