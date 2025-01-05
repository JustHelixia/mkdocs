# MkDocs  
  
## Introduction
What is MkDocs? Funny you asked.  
You are looking at it right now.  
With MkDocs you can start documentation site and/or a blog.  
This site is using the material design for MkDocs, but there are other choices available.  
  
## Deploying MkDocs  
I spun up the the LXC container with Ubuntu 22.04 LTS and installed Docker and Docker Compose.

1. After updating and upgrading the packages I created a MkDocs folder.
2. I created a new docker-compose.yml file with the configuration below:
```` yaml linenums="1" title="MkDocs Config"
services:
  mkdocs:
    image: squidfunk/mkdocs-material
    ports:
      - "8005:8000"
    volumes:
      - ./:/docs
    stdin_open: true
    tty: true
    restart: unless-stopped
````
3. After that I created the site structure like below.
`````
- mkdocs.yml (this files will be filled in step 4)
- docs
    - assets
        - images
            *.png
            *.jpg
  index.md
  *.md (site pages)
- docker-compose.yml (created in step 2)
`````
4. Configuration for your website.
```` yaml linenums="1" title="Website Config"
site_name: name-your-website

nav: # Your website navigation
  - Home: index.md
  - Docker containers: # Category this cannot connected to a md file
    - MkDocs: mkdocs.md # Add the page under the category and link the md file

theme:
   name: 'material'
   favicon: assets/images/*.png # This needs to be located at assets/images
   logo: assets/images/*.png # This needs to be located at assets/images
   features: # In this section you can add/enable features
    - content.code.copy
   palette:
    # Palette toggle for automatic mode     
    - media: "(prefers-color-scheme)"
      toggle:
        icon: material/brightness-auto
        name: Switch to light mode

    - media: "(prefers-color-scheme: light)"
      scheme: default
      primary: grey
      accent: blue
      toggle:
        icon: material/weather-night
        name: Switch to dark mode

    - media: "(prefers-color-scheme: dark)"
      scheme: slate
      primary: blue grey
      accent: cyan
      toggle:
        icon: material/weather-sunny
        name: Switch to light mode

markdown_extensions: # Configure the markdown extension.
  - pymdownx.highlight:
      anchor_linenums: true
      line_spans: __span
      pygments_lang_class: true
  - pymdownx.inlinehilite
  - pymdownx.snippets
  - pymdownx.superfences
````
More information about creating the configuration file.  
  - [Creating](https://squidfunk.github.io/mkdocs-material/creating-your-site/)
  - [Customization](https://squidfunk.github.io/mkdocs-material/customization/)
  - [Theming](https://squidfunk.github.io/mkdocs-material/setup/changing-the-colors/)
  - [Markdown extension](https://squidfunk.github.io/mkdocs-material/setup/extensions/python-markdown-extensions/)
  - [Extension list](https://squidfunk.github.io/mkdocs-material/setup/extensions/)

5. After configuring your initial website you can start your Docker container by running `docker compose up`
   
## Rescources  
- [MkDocs website](https://www.mkdocs.org/)
- [MkDocs documentation](https://squidfunk.github.io/mkdocs-material/getting-started/)
- [Official MkDocs themes](https://www.mkdocs.org/user-guide/choosing-your-theme/)
- [Third-party MkDocs themes](https://github.com/mkdocs/mkdocs/wiki/MkDocs-Themes)
- [MkDocs Material theme](https://squidfunk.github.io/mkdocs-material/)
- [MkDocs Material Documentation](https://squidfunk.github.io/mkdocs-material/getting-started/)
- [MkDocs Material Extensions](https://squidfunk.github.io/mkdocs-material/setup/extensions/)
- [MkDocs Github](https://github.com/mkdocs/mkdocs)
- [MkDocs Material Github](https://github.com/squidfunk/mkdocs-material)