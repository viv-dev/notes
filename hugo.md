# hugo

## Terminology

**frontmatter** - metadata placed at the top of a content file that can be used by the templating logic to adjust the view/data for the page.

## Directory Structure

- **archetypes**: templates for certain pages that have specific frontmatter
- **assets**: stores static files that will be processed by `Hugo Pipes`. Oly files whose `.Permalink` or `.RelPermalink` are actually used will be published to the `public` directory. This directory is not created by default.
- **config**: environment specific config can be organised in this directory. Simple websites can simply use a `config.toml|.yaml|.json` file in the project root
- **content**: Content, in the form of markdown files with front matter, live inside this director. The directory structure in the folder should match the content sections.
- **data**: this holds data that can be referenced during the generation step of your website. You can also create datatemplates that pull from dynamic content.
- **layout**: stores the `.html` page templates that specify how your views will look. These include:
  - homepage
  - listpage
  - taxonomy templates
  - partials
  - single page templates
  - ... and more
- **static**: stores all static content: images, css, javascript etc. These are copied over as is during site generation.
