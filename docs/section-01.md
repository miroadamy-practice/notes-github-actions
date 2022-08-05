# Section 01 - Intro


## Basics

* reacts to events on repository or to reposotory (push, pull-request-open, pull-request-merged. schedule, external event)
* perform actions on events

Workflows runs in containers

Workflows - 1 or more jobs, each job - virtual machine

* VM can be Linux, Windows or Mac
* Job consists of steps (action or shell)
* jobs can be run in parallel
* can wait on finish TEST => (Build Linux, Build Mac)

Runner - any machine with GH App runner installed. It runs your jobs. It can be hosted on GH or by you

* GH hosted - maintained by GH, cannot customize HW [More information](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners#supported-software)
* Self hosted - can control HW, must maintain

Types:

* ubuntu-latest or ubuntu-20.04, ubuntu-18.04, macos-12, macos-11 or macos-lastest, macos-10.15, windows-2019, windows-2022 or windows-latest 

**Hardware specification for Windows and Linux virtual machines:**

* 2-core CPU (x86_64)
* 7 GB of RAM
* 14 GB of SSD space

**Hardware specification for macOS virtual machines:**

* 3-core CPU (x86_64)
* 14 GB of RAM
* 14 GB of SSD space

Preinstalled software:

* curl, git, npm, yarn, pip
* python, ruby, nodeJS
* Android SDK and XCode
* see [ubuntu-20.04](https://github.com/actions/virtual-environments/blob/main/images/linux/Ubuntu2004-Readme.md)


## YAML refresher

