# Github Actions

Notes from the course: `The Complete GitHub Actions & Workflows Guide` - https://www.udemy.com/course/github-actions/



## Regarding MkDocs:

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

```bash
docker run --rm -it -p 8000:8000 -v $(pwd):/docs miroadamy/mkdocs-material
```
