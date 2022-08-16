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

## 04-24 a

xx

## 04-25 a

xx

## 04-26 a

xx

## 04-27 a

xx

## 04-28 a

xx

## 04-29 a

xx
