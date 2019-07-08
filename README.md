# Stanford Research Computing Center Documentation Portal

This is a [mkdocs-jekyll](https://github.com/vsoch/mkdocs-jekll) template
that provides a user portal for Stanford Research Computing Documentation
bases.


## Usage

### 1. Get the code

You can clone the repository:

```bash
git clone https://github.com/stanford-rc/stanford-rc.github.io
cd docs
```

### 2. Customize

To edit configuration values, customize the [_config.yml](_config.yml).
To add pages, write them into the [pages](pages) folder. 
You define urls based on the `permalink` attribute in your pages,
Most of the configuration values in the [_config.yml](_config.yml) are self explanatory,
and for more details, see the [about page](https://vsoch.github.io/mkdocs-jekyll/about/)
rendered on the site.

#### Navigation

The [_data/toc.myl](_data/toc.yml) controls navigation of the site.

#### Links

By way of being a portal, this site's main page is a series of cards linking
to other sites. Importantly, the metadata (URL, logo, name) comes from 
[_data/metadata.yml](_data/metadata.yml). If you want to change a value,
that's the place to do it. Logos (including raw files) can be found in
[stanford-rc/logos](https://github.com/stanford-rc/logos).
Take a look at the rendered metadata (available to other sites) at
[http://localhost:4000/data.json](http://localhost:4000/data.json).

### 4. Serve

Depending on how you installed jekyll:

```bash
jekyll serve
# or
bundle exec jekyll serve
```
