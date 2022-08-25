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

aa


