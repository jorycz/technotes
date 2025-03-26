# Create

```
mkdocs new .
mv docs source
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

