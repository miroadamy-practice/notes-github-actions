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

See the hacks around it: <https://github.com/miroadamy-practice/github-actions-demo-1/runs/7838712995?check_suite_focus=true>

Special secret - can be used directly without creating it

```sh
{% raw %}
${{ secrets.GITHUB_TOKEN }}
{% endraw %}

```

## 03-15 Using GITHUB_TOKEN

At the start of each workflow run, GitHub automatically creates a unique GITHUB_TOKEN secret to use in your workflow. You can use the GITHUB_TOKEN to authenticate in a workflow run.

When you enable GitHub Actions, GitHub installs a GitHub App on your repository. The GITHUB_TOKEN secret is a GitHub App installation access token. You can use the installation access token to authenticate on behalf of the GitHub App installed on your repository. The token's permissions are limited to the repository that contains your workflow.

Use case: Use Github action that does something with repo and needs authentication

Docs: <https://docs.github.com/en/actions/security-guides/automatic-token-authentication>

```yaml
{% raw %}
name: Pull request labeler
on: [ pull_request_target ]

permissions:
  contents: read
  pull-requests: write

jobs:
  triage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/labeler@v4
        with:
          repo-token: ${{ secrets.GITHUB_TOKEN }}
{% endraw %}          
```

```yaml
{% raw %}
name: Create issue on commit

on: [ push ]

jobs:
  create_issue:
    runs-on: ubuntu-latest
    permissions:
      issues: write 
    steps:
      - name: Create issue using REST API
        run: |
          curl --request POST \
          --url https://api.github.com/repos/${{ github.repository }}/issues \
          --header 'authorization: Bearer ${{ secrets.GITHUB_TOKEN }}' \
          --header 'content-type: application/json' \
          --data '{
            "title": "Automated issue for commit: ${{ github.sha }}",
            "body": "This issue was automatically created by the GitHub Action workflow **${{ github.workflow }}**. \n\n The commit hash was: _${{ github.sha }}_."
            }' \
          --fail
{% endraw %}
```

This is in <https://github.com/miroadamy-practice/github-actions-demo-1/commit/1f68498caa44ccffd12c8d205d4061b3b5ca14dc> (must be in main branch to be target of the dispatch)

Note - <https://github.com/miroadamy-practice/github-actions-demo-1/issues/4>

Also - used to modify repo

Should use https based URLS - see <https://github.com/miroadamy-practice/github-actions-course/tree/using-the-github-token-secret-for-authentication>

```yaml
{% raw %}
jobs: 
  create_issue:
    runs-on: ubuntu-latest
    steps:
      - name: Push a random file
        run: |
          pwd 
          ls -a 
          git init
          git remote add origin "https://$GITHUB_ACTOR:${{ secrets.GITHUB_TOKEN }}@github.com/$GITHUB_REPOSITORY.git"
          git config --global user.email "my-bot@bot.com"
          git config --global user.name "my-bot"
          git fetch
          git checkout master
          git branch --set-upstream-to=origin/master
          git pull
          ls -a
          echo $RANDOM >> random.txt
          ls -a 
          git add -A
          git commit -m"Random file"
          git push

{% endraw %}
```

Test in <https://github.com/miroadamy-practice/github-actions-demo-1/tree/d26aad05dbdb91a4669167f43d322f08ae9f1f83>

```sh
### Update repo

POST https://api.github.com/repos/miroadamy-practice/github-actions-demo-1/dispatches
Content-Type:  application/vnd.github+json" \ 
Authorization: token {{ghtoken}}

{"event_type":"modify_repo","client_payload":{"unit":false,"integration":true, "env": "production"}}

```

See the commit: <https://github.com/miroadamy-practice/github-actions-demo-1/commit/1b7a3a91fcd6b371d4f8edb460472acac57f2ee3>

There is most likely ready to use action for this

## 03-16 Encrypting and decrypting files

Limitation:

* Secrets in GH - 64K max
* using CLI that works with files => cannot use GH stored secrets

Use - GPG - <https://www.gnupg.org/>

```sh
 gpg --version
gpg (GnuPG) 2.3.7
libgcrypt 1.10.1
Copyright (C) 2021 Free Software Foundation, Inc.
License GNU GPL-3.0-or-later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Home: /Users/miroadamy/.gnupg
Supported algorithms:
Pubkey: RSA, ELG, DSA, ECDH, ECDSA, EDDSA
Cipher: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
        CAMELLIA128, CAMELLIA192, CAMELLIA256
AEAD: EAX, OCB
Hash: SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
Compression: Uncompressed, ZIP, ZLIB, BZIP2
```

Running it:

```sh
gpg --symmetric --cipher-algo AES256 my_secret.json
```

Save passphrase, will need to be stored in GH Secret

Add encrypted file

Create decrypt script, add both encrypted and decryptor to repo

```sh
#!/bin/sh

# Decrypt the file
mkdir $HOME/secrets
# --batch to prevent interactive command
# --yes to assume "yes" for questions
gpg --quiet --batch --yes --decrypt --passphrase="$LARGE_SECRET_PASSPHRASE" \
--output $HOME/secrets/my_secret.json my_secret.json.gpg

---

git add my_secret.json.gpg
git commit -m "Add new encrypted secret JSON file"
chmod +x decrypt_secret.sh
git add decrypt_secret.sh
git commit -m "Add new decryption script"
git push
```

Workflow: see <https://github.com/miroadamy-practice/github-actions-course/blob/encrypting-and-decrypting-files/.github/workflows/env.yml>

```yaml
jobs:
  decrypt:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - name: Decrypt
        run: gpg --quiet --batch --yes --decrypt --passphrase="$PASSPHRASE" --output $HOME/secret.json secret.json.gpg
        env: 
          PASSPHRASE: ${{ secrets.PASSPHRASE }}
      - name: Print our file content 
        run: cat $HOME/secret.json
```

## 03-17 Expressions and contexts

See <https://docs.github.com/en/actions/learn-github-actions/contexts>

The syntax of expressions:

```sh
{% raw %}
${{ context.expression }}
{% endraw %}
```

Inside can be

* literals
* expressions
* functions

Top level objects (== contexts)

* secrets
* github

All: <https://docs.github.com/en/actions/learn-github-actions/contexts>

* github: Information about the workflow run. For more information, see github context.
* env: Contains environment variables set in a workflow, job, or step.
* job: Information about the currently running job.
* steps: Information about the steps that have been run in the current job.
* runner: Information about the runner that is running the current job.
* secrets: Contains the names and values of secrets that are available to a workflow run.
* strategy: Information about the matrix execution strategy for the current job.
* matrix: Contains the matrix properties defined in the workflow that apply to the current job.
* needs:  Contains the outputs of all jobs that are defined as a dependency of the current job.
* inputs: Contains the inputs of a reusable or manually triggered workflow.

See log-contexts.yml - <https://github.com/miroadamy-practice/github-actions-demo-1/runs/7842217621?check_suite_focus=true>

```yml
name: Context testing
on: 
  push:
    branches:
      - chapter/03-17

jobs:
  dump_contexts_to_log:
    runs-on: ubuntu-latest
    steps:
      - name: Dump GitHub context
        id: github_context_step
        run: echo '${{ toJSON(github) }}'
      - name: Dump job context
        run: echo '${{ toJSON(job) }}'
      - name: Dump steps context
        run: echo '${{ toJSON(steps) }}'
      - name: Dump runner context
        run: echo '${{ toJSON(runner) }}'
      - name: Dump strategy context
        run: echo '${{ toJSON(strategy) }}'
      - name: Dump matrix context
        run: echo '${{ toJSON(matrix) }}'
```

You can access context information using one of two syntaxes.

* Index syntax: `github['sha']`
* Property dereference syntax: `github.sha`

## 03-18 Using Functions in Expressions

..

## 03-19 The If key && job status function

..
