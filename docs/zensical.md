---
icon: lucide/package-open
---
# Zensical in 5min

This is intended to be brief and to the point. More information can be found [here](https://zensical.org/docs/get-started/).

---

## Installation

!!! example

    ```Shell
    python3 -m venv .venv
    source .venv/bin/activate
    pip install zensical
    ```

---

## Create your site

After installing, you can bootstrap documentation using the `zensical` executable. In the project directory you are working in:

!!! example

    ```Shell
    zensical new .
    ```

This creates the following structure:

!!! info "zensical project structure"

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

!!! example "Zensical only requires site_name"

    ```TOML
    [project]
    site_name = "My site"
    ```

Zensical strongly recommends using `site_url` as well unless building for offline usage. `site_url` is a prerequisite for the following features:

* Instant navigation
* Instant previews
* Custom error pages

!!! note

    `zensical.toml` is automatically created with generic information filled in for you if using `zensical new`.

---

## Publish your site

Zensical makes it super easy to deploy changes as they are pushed with `git`. We will only cover usage for GitLab.

!!! example ".gitlab-ci.yml"

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

---

## Preview your site

Zensical includes a web server, so you can preview as you write. The server will automatically rebuild the site when changes are made.

Start with:

!!! example "Start localhost"

    ```Shell
    zensical serve
    ```

---

## Customization

### Icons

#### Included icon sets

The following icon sets are bundled with Zensical (links lead to search page for each):

* [Lucide](https://lucide.dev/)
* [Material Design](https://pictogrammers.com/library/mdi/)
* [FontAwesome](https://fontawesome.com/search?m=free)
* [Octicons](https://octicons.github.com/)
* [Simple Icons](https://simpleicons.org/)

**Note:** You are not limited to these icons as you can add your own icons.

### Emojis

Emojis can be integrated in Markdown by putting the shortcode of the emoji between two colons. If you're using [Twemoji](https://github.com/jdecked/twemoji) (recommended), you can look up the shortcodes at [Emojipedia](https://emojipedia.org/twitter/):

!!! quote "Emoji"

    ```markdown
    :smile:
    ```

    :smile:

### Fonts

You can change the fonts with zensical using the project documentation. Zensical integrates with [Google Fonts](https://fonts.google.com/). Alternatively, fonts can be custom-loaded if self-hosting is preferred.

See for more [information](https://zensical.org/docs/setup/fonts/?h=project.theme.font).

#### Regular font

The regular font is used for all content that does not need to be monospaced. It can be set to any valid Google Font via configuration:

!!! example

    ```TOML
    [project.theme]
    font.text = "Inter"
    ```

#### Monospaced font

The monospaced font is used for code blocks and can be configured separately:

!!! example

    ```TOML
    [project.theme]
    font.code = "JetBrains Mono"
    ```

---

## General Usage

### Admonitions

A.K.A *call-outs.* These allow you to include side content without significantly interrupting the document flow. Zensical provides several different types.

#### Supported Types

* `note`
!!! note
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Proin mattis fringilla faucibus. Fusce ex augue, varius at tempus eget, fringilla.

* `abstract`
!!! abstract
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec ultrices diam tortor, a auctor mauris efficitur dignissim. Lorem ipsum dolor.

* `info`
!!! info
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Praesent at vehicula magna. Proin pretium varius nisi vel sodales. Fusce magna.

* `tip`
!!! tip
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec eros odio, bibendum ullamcorper libero id, ornare interdum nibh. Etiam et.

* `success`
!!! success
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Ut a justo vitae nisl dapibus tempor sit amet et magna. Morbi.

* `question`
!!! question
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Suspendisse ut congue tellus, vel facilisis ex. Quisque neque risus, tempus at.

* `warning`
!!! warning
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nullam scelerisque magna quis lobortis maximus. Sed non purus viverra, consequat erat.

* `failure`
!!! failure
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam blandit commodo justo quis placerat. Class aptent taciti sociosqu ad litora.

* `danger`
!!! danger
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer vehicula rhoncus maximus. Nulla molestie, erat vel egestas gravida, mauris erat.

* `bug`
!!! bug
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Maecenas ultricies euismod est eu egestas. Sed vel est gravida lorem congue.

* `example`
!!! example
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Vestibulum viverra ipsum sapien, vitae vestibulum nibh condimentum et. Duis ullamcorper in.

* `quote`
!!! quote
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque feugiat lacus eu tortor rutrum imperdiet. Etiam venenatis est vel malesuada.

#### Admonition Configuration

!!! example

    This is the configuration does the following:

    * Enables admonitions
    * Allows to make admonitions collapsible
    * Allows nested admonitions

    ```TOML
    [project.markdown_extensions]
    admonition = {}
    pymdownx.details = {}
    pymdownx.superfences = {}
    ```

##### Admonition Icons

Each of the supported types have distinct icons, which can be changed to any icon bundled with the theme, or a custom icon. Add the following lines to your configuration:

!!! example

    ```TOML
    [project.theme.icon.admonition]
    <type> = "<icon>"
    ```

#### Usage

Admonition blocks start with `!!!`, followed by a single keyword.

!!! example

    ```Markdown
    !!! note

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
        nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
        massa, nec semper lorem quam in massa.
    ```
