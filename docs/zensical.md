# Get started

## Installation

```Shell
python3 -m venv .venv
source .venv/bin/activate
pip install zensical
```

---

## Create your site

After installing, you can bootstrap documentation using the `zensical` executable. In the project directory you are working in:

```Shell
zensical new .
```

This creates the following structure:

```
your-project-repo
├─ .github/workflows
│  └─ docs.yml
├─ docs/
│  ├─ index.md
│  └─ markdown.md
└─ zensical.toml
```

---

## Configuration

Zensical only requires `site_name`:

```TOML
[project]
site_name = "My site"
```

Zensical strongly recommends using `site_url` as well unless building for offline usage. `site_url` is a prerequisite for the following features:

* Instant navigation
* Instant previews
* Custom error pages

**Note:** `zensical.toml` is automatically created with generic information filled in for you if using `zensical new`.

---

## Publish your site

Zensical makes it super easy to deploy changes as they are pushed with `git`.

### GitLab Pages

Free of charge, easy to setup. Hosted by GitLab.

```YAML
pages:
  stage: deploy
  image: python:latest
  script:
    - pip install zensical
    - zensical build --clean 
  pages:
    publish: site 
  rules:
    - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
```

When a new commit is pushed to the default branch, the static site is automatically built and deployed.

**Note:** By default, Gitlab Pages publishes to a domain that includes a random string. Untick the `Use unique domain` box in your Gitlab Pages settings for your production deployment. Also make sure to set the visibility for Pages under `Settings > General > Visibility` if you want a public site.

### GitHub Pages

Free of charge, easy to set up. Hosted by GitHub.

### GitHub Actions

You can automate the deployment of your project on pushes to specific branches (GitHub Pages for your repo must be configured).

If you created the site with `zensical new`, a publishing workflow was already created in `.github/workflows/docs.yml`.

**Note:** If using GitLab, can delete this folder.

Contents:

```YAML
name: Documentation
on:
  push:
    branches:
      - master
      - main
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/configure-pages@v6
      - uses: actions/checkout@v7
      - uses: actions/setup-python@v6
        with:
          python-version: 3.x
      - run: pip install zensical
      - run: zensical build --clean 
      - uses: actions/upload-pages-artifact@v5
        with:
          path: site
      - uses: actions/deploy-pages@v5
        id: deployment
```

---

## Preview your site

Zensical includes a web server, so you can preview as you write. The server will automatically rebuild the site when changes are made.

Start with:

```Shell
zensical serve
```
