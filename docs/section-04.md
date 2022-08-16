# 04 Using Strategy, Matrix and Docker

How to use Strategy, Matrix and Docker Container in jobs

## 04-20 Continue on error + timeout

Keyword `[needs]` => waits for results of other job

Run even if all fails `continue_on_error`

`timeout-minutes`: 360 (default),

No demo. Check [docs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstepscontinue-on-error)

## 04-21 Using setup-node action

See [docs](https://github.com/actions/setup-node)

Demo repo: <https://github.com/miroadamy-practice/github-actions-course/blob/using-the-setup-node-action/.github/workflows/matrix.yml>

My test repo: <https://github.com/miroadamy-practice/github-actions-demo-1/blob/chapter/04-20/.github/workflows/matrix.yml>

Use case: running with different Node JS versions.

```yml
name: Matrix 
on: 
  push:
    branches:
      - chapter/04-20

jobs: 
  node-version:
    runs-on: ubuntu-latest
    steps: 
      - name: Log node version 
        run: node -v
      - uses: actions/setup-node@v3
        with:
          node-version: 14
      - name: Log node version 
        run: node -v
```

See <https://github.com/miroadamy-practice/github-actions-demo-1/runs/7865521546?check_suite_focus=true>

## 04-22 Creating a Matrix for Running a Job with Different Environments

Use case - needs to test multiple versions

Demo repo: <https://github.com/miroadamy-practice/github-actions-course/blob/creating-a-matrix-for-running-a-job-with-different-environments/.github/workflows/matrix.yml>

Use new job-level markup:

```yaml
{% raw %}
name: Matrix 
on: push 

jobs: 
  node-version:
    strategy: 
      matrix:
        os: [ubuntu-latest, windows-latest] 
        node_version: [14, 16, 18]
    runs-on: ${{ matrix.os }}
    steps: 
      - name: Log node version 
        run: node -v
      - uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node_version }}
      - name: Log node version 
        run: node -v

{% endraw %}
```

Optional -

* `fail-fast: true`
* `max-parallel: 2` (by default maximizes)

![matrix](./img/matrix.png)

## 04-23 Including & Excluding Matrix Configurations

Exclude certain configs

```yaml
        exclude:
          - os: windows-latest
            node_version:  16
          - os: windows-latest
            node_version: 14
```

NOTE: Include is different, it adds extra variable to a config

```yaml
{% raw %}
name: Matrix 
on: 
  push:
    branches:
      - chapter/04-20

jobs: 
  node-version:
    strategy: 
      matrix:
        os: [ubuntu-latest, windows-latest] 
        node_version: [14, 16, 18]
        exclude:
          - os: windows-latest
            node_version:  16
          - os: windows-latest
            node_version: 14
        include:
          - os: ubuntu-latest
            node_version: 16
            is_ubuntu_16: "yesss!"

    runs-on: ${{ matrix.os }}
    env: 
      IS_UBUNTU_16: ${{ matrix.is_ubuntu_16}}
    steps: 
      - name: Log node version 
        run: node -v
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node_version }}
      - name: Log node version 
        run: |
          node -v
          echo "Included = $IS_UBUNTU_16"
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
{% endraw %}
```

See <https://github.com/miroadamy-practice/github-actions-demo-1/actions/runs/2870690213>

Matrix context for 14 and 16:

```json
v14.20.0
Included = 
...
{
  "os": "ubuntu-latest",
  "node_version": 14
}
---
v16.16.0
Included = yesss!
...
{
  "os": "ubuntu-latest",
  "node_version": 16,
  "is_ubuntu_16": "yesss!"
}
```

## 04-24 Using Docker Containers in Jobs

See <https://github.com/miroadamy-practice/github-actions-course/blob/using-docker-containers-in-jobs/.github/workflows/container.yml>

In addition to ubuntu-latest, specify container

```yaml
name: Container

on:
  push:
    branches:
      - chapter/04-24

jobs:
  node-docker:
    runs-on: ubuntu-latest
    container: 
      image: node:16.16.0-alpine3.16
    steps:
      - name: Log node version  
        run: |
          node -v
          cat /etc/os-release

```

There is new section: initialize-container - <https://github.com/miroadamy-practice/github-actions-demo-1/runs/7866390271?check_suite_focus=true>

```txt
2s
Checking docker version
  /usr/bin/docker version --format '{{.Server.APIVersion}}'
  '1.41'
  Docker daemon API version: '1.41'
  /usr/bin/docker version --format '{{.Client.APIVersion}}'
  '1.41'
  Docker client API version: '1.41'
Clean up resources from previous jobs
  /usr/bin/docker ps --all --quiet --no-trunc --filter "label=94859b"
  /usr/bin/docker network prune --force --filter "label=94859b"
Create local container network
  /usr/bin/docker network create --label 94859b github_network_1f31a49f8c984efa9b3ec4f19b812cf3
  ee4f49c990ea66488b4f28bd9cd2118b9452f19b78b74dfb8da98628a83ab1a2
Starting job container
  /usr/bin/docker pull node:16.16.0-alpine3.16
  16.16.0-alpine3.16: Pulling from library/node
  Digest: sha256:1c8769a8c9ed57817ef07162744a3722421333a438185c560aa42a9a1fc6ea23
  Status: Downloaded newer image for node:16.16.0-alpine3.16
  docker.io/library/node:16.16.0-alpine3.16
  /usr/bin/docker create --name 17c4560715154bcf82673ce5dd48b260_node16160alpine316_5b0744 --label 94859b --workdir /__w/github-actions-demo-1/github-actions-demo-1 --network github_network_1f31a49f8c984efa9b3ec4f19b812cf3  -e "HOME=/github/home" -e GITHUB_ACTIONS=true -e CI=true -v "/var/run/docker.sock":"/var/run/docker.sock" -v "/home/runner/work":"/__w" -v "/home/runner/runners/2.295.0/externals":"/__e":ro -v "/home/runner/work/_temp":"/__w/_temp" -v "/home/runner/work/_actions":"/__w/_actions" -v "/opt/hostedtoolcache":"/__t" -v "/home/runner/work/_temp/_github_home":"/github/home" -v "/home/runner/work/_temp/_github_workflow":"/github/workflow" --entrypoint "tail" node:16.16.0-alpine3.16 "-f" "/dev/null"
  334f6baaa36386c02a1383533ac9a27f52515d5f16f1245157c14a0a9df95bb3
  /usr/bin/docker start 334f6baaa36386c02a1383533ac9a27f52515d5f16f1245157c14a0a9df95bb3
  334f6baaa36386c02a1383533ac9a27f52515d5f16f1245157c14a0a9df95bb3
  /usr/bin/docker ps --all --filter id=334f6baaa36386c02a1383533ac9a27f52515d5f16f1245157c14a0a9df95bb3 --filter status=running --no-trunc --format "{{.ID}} {{.Status}}"
  334f6baaa36386c02a1383533ac9a27f52515d5f16f1245157c14a0a9df95bb3 Up Less than a second
  /usr/bin/docker inspect --format "{{range .Config.Env}}{{println .}}{{end}}" 334f6baaa36386c02a1383533ac9a27f52515d5f16f1245157c14a0a9df95bb3
  HOME=/github/home
  GITHUB_ACTIONS=true
  CI=true
  PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
  NODE_VERSION=16.16.0
  YARN_VERSION=1.22.19
Waiting for all services to be ready
```

There can be more options - `env`, `ports`, `volumes`

Every step now runs inside VM

Double - compare with run without: <https://github.com/miroadamy-practice/github-actions-demo-1/actions/runs/2870878602>

```txt
v16.16.0
NAME="Alpine Linux"
ID=alpine
VERSION_ID=3.16.2
PRETTY_NAME="Alpine Linux v3.16"
HOME_URL="https://alpinelinux.org/"
BUG_REPORT_URL="https://gitlab.alpinelinux.org/alpine/aports/-/issues"
---
v16.16.0
NAME="Ubuntu"
VERSION="20.04.4 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.4 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal

```

It happens to use the same node version ...

## 04-25 An Overview of a Simple Dockerized NodeJS API

Using simple Node app - <https://github.com/alialaa/simple-docker-nodejs-api>

Simple api, connects to MongoDB table users

I cloned it, does not work - <https://github.com/miroadamy-practice/simple-docker-nodejs-api>

## 04-26 Running Multiple Docker Services in our Workflows

See <https://github.com/miroadamy-practice/github-actions-course/blob/running-multiple-docker-services-in-our-workflow/.github/workflows/container.yml>

My: <https://github.com/miroadamy-practice/github-actions-demo-1/runs/7867200466?check_suite_focus=true>

His version works

Instead of `containers:` we have `services:`

```yaml
name: Container
on: 
  push:
    branches:
      - chapter/04-26

jobs: 
  node-docker:
    runs-on: ubuntu-latest
    services:
      app:
        image: alialaa17/node-api
        ports:
          - 3001:3000
      mongo:
        image: mongo
        ports:
          - "27017:27017"
    steps:
      - name: Post a user
        run: 'curl -X POST http://localhost:3001/api/user -H ''Content-Type: application/json'' -d ''{"username": "hello","address": "dwded"}'''
      - name: Get Users
        run: curl http://localhost:3001/api/users
```

Test: <https://github.com/miroadamy-practice/github-actions-demo-1/runs/7867200466?check_suite_focus=true>



## 04-27 a

xx

## 04-28 a

xx

## 04-29 a

xx
