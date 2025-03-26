# Create

```
mkdocs new .
```

# Customize mkdocs.yml

```
site_name: ...

docs_dir: source
site_dir: docs

theme:
  name: material
plugins:
  - search
  - ...
```

# Build site

```
mkdocs build
```

# Build site by github workflow

To deploy by GitHub Action read [GitHub Pages on Material for MkDocs](https://squidfunk.github.io/mkdocs-material/publishing-your-site/)

* Setup in repository `Settings` > `Pages` > `Build and deployment`
* * Source > `Deploy from a branch`
* * Branch > `gh-pages` and `/ (root)`
