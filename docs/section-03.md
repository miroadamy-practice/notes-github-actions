# Section 03 - Env variables, Encryption

Environment variables, encryption, expressions and context

## 03-13 Default and custom environment variables

See <https://github.com/miroadamy-practice/github-actions-course/tree/default-and-custom-environment-variables>

Docs: <https://docs.github.com/en/actions/learn-github-actions/environment-variables>

```yaml
name: ENV Variables 
on: push 
env: 
  WF_ENV: Available to all jobs 

jobs: 
  log-env:
    runs-on: ubuntu-latest
    env:
      JOB_ENV: Available to all steps in log-env jobs
    steps:
      - name: Log ENV Variables 
        env: 
          STEP_ENV: Available to only this step 
        run: |
          echo "WF_ENV: ${WF_ENV}"
          echo "JOB_ENV: ${JOB_ENV}"
          echo "STEP_ENV: ${STEP_ENV}"
  log-default-env: 
    runs-on: ubuntu-latest
    steps:
      - name: Default ENV Variables 
        run: |
          echo "HOME: ${HOME}"
          echo "GITHUB_WORKFLOW: ${GITHUB_WORKFLOW}"
          echo "GITHUB_ACTION: ${GITHUB_ACTION}"
          echo "GITHUB_ACTIONS: ${GITHUB_ACTIONS}"
          echo "GITHUB_ACTOR: ${GITHUB_ACTOR}"
          echo "GITHUB_REPOSITORY: ${GITHUB_REPOSITORY}"
          echo "GITHUB_EVENT_NAME: ${GITHUB_EVENT_NAME}"
          echo "GITHUB_WORKSPACE: ${GITHUB_WORKSPACE}"
          echo "GITHUB_SHA: ${GITHUB_SHA}"
          echo "GITHUB_REF: ${GITHUB_REF}"
          echo "WF_ENV: ${WF_ENV}"
          echo "JOB_ENV: ${JOB_ENV}"
          echo "STEP_ENV: ${STEP_ENV}"
```

Each level can have `env:` key

Output:

```sh
WF_ENV: Available to all jobs
JOB_ENV: Available to all steps in log-env jobs
STEP_ENV: Available to only this step
---
HOME: /home/runner
GITHUB_WORKFLOW: ENV Variables
GITHUB_ACTION: __run
GITHUB_ACTIONS: true
GITHUB_ACTOR: miroadamy
GITHUB_REPOSITORY: miroadamy-practice/github-actions-demo-1
GITHUB_EVENT_NAME: push
GITHUB_WORKSPACE: /home/runner/work/github-actions-demo-1/github-actions-demo-1
GITHUB_SHA: ec152435ccb1d4b3cb52c14fcb4f5ebb4688e587
GITHUB_REF: refs/heads/chapter/03-13
WF_ENV: Available to all jobs
JOB_ENV: 
STEP_ENV: 
```

GITHUB action - name of the step, if multiple - uses run1, run2 etc
## 03-14 Encrypting variables

Go to User Profile settings, secrets 

I have defined secret at repo level but any attempt to print it fails (feature)

![secret](./img/secrets.png)

See the hacks around it: https://github.com/miroadamy-practice/github-actions-demo-1/runs/7838712995?check_suite_focus=true

Special secret - can be used directly without creating it

```sh
{% raw %}
${{ secrets.GITHUB_TOKEN }}
{% endraw %}

```


## 03-15 Using GITHUB_TOKEN 

## 03-16 Encrypting and decrypting files


## 03-17 Expressions and contexts


## 03-18 Using Functions in Expressions

## 03-19 The If key && job status function



