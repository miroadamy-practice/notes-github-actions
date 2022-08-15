# Github Actions

Notes from the course: `The Complete GitHub Actions & Workflows Guide` - <https://www.udemy.com/course/github-actions/>

### Repositories with code

* <https://github.com/miroadamy-practice/github-actions-course>
* <https://github.com/miroadamy-practice/github-actions-course-react>
* This notes: <https://github.com/miroadamy-practice/notes-github-actions>

## TOC

### [Section-1 - Intro](./section-01.md)

In section 1 we will have a quick conceptual introduction to what workflows, actions, jobs, steps and other terms that we will see are. We will also learn about YAML which is the format used to write workflows in GitHub. Then we will start writing our workflow and take a look at basic things like writing commands, using different shells and using actions including the most common action which is the checkout action.

### [Section-2 - Events, Schedulers, Filters](./section-02.md)

In section 2 we will get a bit deeper and learn different ways that we can use in order to trigger a workflow to run. This includes GitHub events like push and pull_request, cron schedules and also external events. We will also see how to make a workflow only run for certain branches, tags and directory paths.

### [Section-3 - Environment variables, encryption, expressions and context](./section-03.md)

In this section we will see how can we use default environment variables and also how to add custom environment variables. We will also see how to encrypt environment variables that are sensitive and also how to encrypt and decrypt sensitive files that we don't won't to push to our repository. Moreover, we will see the GITHUB_TOKEN environment variable and how to use it for authentication. Also we will take a look at the context information that we can access in our workflows which contains a lot of information about our current workflow and virtual machine and more. Finally we will take a look at different functions that are available to use in our workflows.

### [Section-4 -](./section-04.md)

In section 4 we will see how can we setup matrices, a matrix is a way to run a job multiple times but using different environments. We will also learn how to use docker in our jobs and steps and how to run multiple docker services in our job.

### [Section-5 -](./section-05.md)

In section 5 we will finally use our knowledge to create a more real-world CI/CD example. We will set-up a repository where we have a web app and we need to have a flow for production and development deployment. So we will have 2 branches, 1 for production and another for development and we will write a workflow for each one of these branches to do certain things. These things will differ depending whether we are pushing on production or development branch. We will also have a workflow that will run if someone opened a pull request. The tasks we will perform includes: installing dependencies, caching, testing, checking code format, uploading artifacts, automatically generating a semantic version, deploying to production/staging, creating releases, opening issues, sending slack notifications and more.

### [Section-6 -](./section-06.md)

During the course we will use some actions that are available in the marketplace. In this section we will learn how to create our own actions. Actions can be created using JavaScript or using Docker. In this section we are going to learn about both ways and then we will create a JavaScript action that opens GitHub issues and a docker action that send a slack message which we will write using PHP.

## What you’ll learn

* Learn about writing serialized data in the YAML format
* Discover conceptually what workflows and GitHub actions are
* Learn what terms like action, step, job, event, workflow, etc.. mean
* Discover everything you can do in a github workflow (events, schedules, filtering, environment variables, context, encryption, matrix and more)
* Learn how docker can be used in a github workflow
* Create a real-life CI/CD workflow for code testing, formatting, versioning and more
* Learn how to use semantic versioning to automatically version your code in your workflow
* Upload code testing coverage reports in your workflows using codecov
* Use caching in workflows for faster performace
* Learn about uploading artifacts in workflows
* Learn about how to create your own custom GitHub Actions using Javascript or Docker
* Create an action for opening GitHub issues using JavaScript
* Create an action for sending slack messages using PHP
* Learn how to publish GitHub actions in the GitHub marketplace.

## Regarding MkDocs

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

## Running it

    docker run --rm -it -p 8000:8000 -v $(pwd):/docs miroadamy/mkdocs-material
