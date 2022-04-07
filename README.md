# DFD Hugo module for metadata for `.Page`

Basic documentation for metadata-mod-hugo-dfd, a Hugo module by [Daniel F. Dickinson](https://github.com/danielfdickinson) for handling page metadata intended to be used in generating the 'head' section, as well as used in page elements.

## Status

[![build-and-verify](https://github.com/danielfdickinson/metadata-mod-hugo-dfd/actions/workflows/build-and-verify.yml/badge.svg)](https://github.com/danielfdickinson/metadata-mod-hugo-dfd/actions/workflows/build-and-verify.yml)

## GitHub repository

<https://github.com/danielfdickinson/metadata-mod-hugo-dfd>

## Features

* Provides a common method of accessing page metadata for use in layouts and shortcodes.
* The following metadata is currently available:
  * date-created — timestamp of page creation (UTC)
  * date-expired — timestamp of page expiry (UTC)
  * date-published — timestamp of published page (UTC)
  * date-modified — timestamp of last time page was modified (UTC)
  * description — A page frontmatter defined description of the page with a fallback to the page summary (below)
  * description-no-fallback — Page frontmatter description of the page with no fallback
  * keywords — Keywords for the page (generated from all terms from all taxonomies)
  * locale — Language/locale in/for which page is written
  * locale-alternate — Other available languages/locales for the page
  * media-audio-file — The first (if any) audio file associated with the page _[Note 1](#note-1)_
  * media-audio-files — All (if any) audio files associated with the page _[Note 1](#note-1)_
  * media-image — The first (if any) featured image, cover image, or thumbnail image associated with the page _[Note 2](#note-2)_
  * media-images — All (if any) featured images, cover images, or thumbnail images associated with the page _[Note 2](#note-2)_
  * media-video — The first (if any) video file associated with the page _[Note 1](#note-1)_
  * media-videos — All (if any) video files associated with the page _[Note 1](#note-1)_
  * opengraph-type — Open Graph Protocol type _[Note 3](#note-3)_
  * reading-time — Approximate time in seconds to read page
  * section — Top-level section in which page resides
  * secure-url — Secure (HTTPS) permanent URL for page _[Note 4](#note-4)_
  * see-also — Automatically generated list of likely similar pages
  * summary — A summary of the page, may be manually created or automatic _[Note 5](#note-5)_
  * tags — A list of tags associated with the page _[Note 1](#note-1)_
  * title-page — The title of the page (see below)
  * title-site — The title of the entire site (see below)
  * url — Permanent URL for the page (aka permalink)
  * word-count — Estimated number of words in the main page content
* Has a unified and comprehensive means of finding page and site titles.
* For \<head> metadata, supports either self-closing or 'void style' empty tags _[Note 6](#note-6)_
* Partials are 'shortcode safe' (which means they can work from layouts or shortcodes).
* Logic for finding images for use by Open Graph and Twitter cards
* Featured/cover images discovery/selection (combines with Open Graph/Twitter cards above)
* Supports adding microformats to the page's \<head> section _[Note 7](#note-7)_
  * [Open Graph Protocol](https://ogp.me/)
  * [Twitter Cards](https://developer.twitter.com/en/docs/twitter-for-websites/cards/overview/abouts-cards)
  * [Schema (Website)](https://schema.org/)
  * [Facebook Admin ID for Domain Insights](https://www.facebook.com/insights/)

## Basic usage

### Importing the module

1. The first step to making use of this module is to add it to your site or theme.  In your configuration file:

   ``config.toml``

   ```toml
   [module]
       [[module.imports]]
           path = "github.com/danielfdickinson/metadata-mod-hugo-dfd"
   ```

   OR
   ``config.yaml``

   ```yaml
   module:
     imports:
       - path: github.com/danielfdickinson/metadata-mod-hugo-dfd
   ```

2. Execute

   ```bash
   hugo mod get github.com/danielfdickinson/metadata-mod-hugo-dfd
   hugo mod tidy
   ```

## Summary of configurable params

Unless otherwise noted, params may be per-page (in frontmatter), or
in the site 'params' section in you site configuration file(s). Page params
override site-wide params.

| Param | Default | Description |
|-------|---------|-------------|
| internalTemplatesOverrideRobotsTXT | _true_ | Site-level params only. When true overrides the internal template for robots.txt (`robots.txt`) with one from this module _[Note 8]\(#note-8)_ |
| internalTemplatesOverrideRSS | _true_ | Site-level params only. When true overrides the internal template for RSS feeds (`rss.xml`) with one from this module |
| internalTemplatesOverrideSitemap | _true_ | Site-level params only. When true overrides the internal template for Sitemap protocol file (`sitemap.xml`) with one from this module |
| pageCanonical | _true_ | Page-level params only. When false omits this page from the `sitemap.xml`, if applicable |
| rssIncludeMainArticle | _false_ | When true include the pages `.Content` (that is the rendered content from the page's file such as `/content/posts/a-post.md`) directly in the RSS feed instead of only a summary or description |
| taxCanonical | _false_ | When true includes this taxonomy or term page in the `sitemap.xml`, if applicable |

### \<head> metadata configuration

* If ``emptyElementStyle`` is set to ``self-close`` in params (site or per-page), then empty tags produced by this module use 'self-closing' form (see above), otherwise 'void style' (see above). _([Note 6](#note-6))_
  For example, with ``toml`` for the site as

  ```toml
  [params]
       emptyElementStyle = "self-close"
  ```

  you get

  ```html
      <meta name="generator" content="Hugo 0.91.2" />
      <meta name="keywords" content="Placeholder" />
  ```

  while not configuring ``emptyElementStyle`` produces

  ```html
      <meta name="generator" content="Hugo 0.91.2">
      <meta name="keywords" content="Placeholder">
  ```

  in the 'Test of metadata for \<head>' section of the page from ``exampleSite/docs/placeholder.md`` as the generated [/docs/placeholder/](/docs/placeholder/#test-of-metadata-for-head).

### 'summary' metadata configuration

* If ``summaryRenderMarkdown`` is true in Params (site or per-page) then we allow setting a summary in the frontmatter that contains markdown, which we render.
* If ``summaryPreserveHtml`` is true in Params (site or per-page) then we return a summary that will render any contained HTML. Note that HTML is only preserved using the manual split method of generating a summary (that is using ``<!--more-->`` as described in [the Hugo docs for Content Summaries](https://gohugo.io/content-management/summaries)), or we rendered Markdown (above).
* Also note that if you render markdown but don't preserve HTML the generated HTML is converted to plain text (i.e. will have no formatting).

### Notes on 'description' metadata item

* For a page (including sections, taxonomies, and terms, but not the home page): Uses ``.Description`` if present, otherwise defaults to the summary (if present).
  * For the found description: Turn into plain text, replace newlines and carriage-returns with spaces, and condense all multiple space sequences to single spaces.
* For the home page: defaults to ``.Description`` if present, otherwise the home page frontmatter description, otherwise the site ``.Description``, otherwise summary from ``.Site.Params``
  * For the found description: Render markdown if present, turn into plain text, replace newlines and carriage-returns with spaces, then condense all multiple space sequences to single spaces.

### Notes on Date, Expiry, PublishDate, Lastmod

(dateCreated, dateExpired, datePublished, and dateModified) metadata items

* For pages that do no exist in the ``content`` directory (e.g. taxonomies, terms, subdirectories/sections with no '_index.md' or 'index.md' and/or a homepage with no '_index.md' in 'content'), then Hugo's ``.PublishDate`` (Open Graph name 'article:published_time') is .IsZero, treated as false or not present depending on the method of use. This is true even with the ``config.toml`` ``[frontmatter]`` ``publishDate`` configured.
* With this exception, [the preferences for automatic values for the various date .Page variables can be configured using the ``frontmatter`` section on your Hugo config](https://gohugo.io/getting-started/configuration/#configure-dates).
* In this module, if ``datesPreserveTimezone`` is ``true`` in page or site ``Params``, then the timezone of the date is not adjusted. If this is not the case, all dates are converted to UTC. You can of course use ``.Local`` or other date functions to adjust the output of the date variables.
* Not all themes are consistent in treating ``.Date`` as 'dateCreated' even though Hugo's internal templates (like RSS feed generator) have that expectation. I'd recommend modifying the theme's date handling if this is the case.

### Taxonomies and 'keywords' \<head> metadata

* For all pages except the homepage, any taxonomy terms (but not taxonomies themselves; so not 'tags' and 'categories' for the default taxonomies configuration, but the terms you specify under 'tags' and/or under 'categories' in page frontmatter) in the page frontmatter are used to fill in the 'keywords' \<meta> tag.
* For the homepage all taxonomy terms (but not taxonomies) are used to fill in the 'keywords' \<meta> tag.
* The 'keywords' meta tag uses a comma separated list of terms, with no space added before or after the comma, as a flat string as [defined in the HTML5 spec](https://html.spec.whatwg.org/multipage/semantics.html#standard-metadata-names).
* Note that the 'keywords' meta tag will likely not be used by search engines due to historical misuse of this tag to mislead naive processors.

### 'type' metadata

* Refers to the item type for feeds and Open Graph Protocol. We only use 'website' and 'article'. The homepage is of 'type' 'website' and other pages with a 'publishDate' (that is, all pages which exist under ``content`` and have frontmatter) are considered of type 'article'.
* This can by overridden by a frontmatter ``openGraphType`` in the page-level ``Params``. See [Object Types in the Open Graph Protocol](https://ogp.me/#types).

### 'name'/title discovery and use

* Uses the following order of preference for discovering title:
  1. ``title`` frontmatter (that is, ``.Page.Title``)
  2. The contents of the first H1 element (if there is at least one)
  3. Either:
     1. For the home page the Site Title
     2. For any other page which has a content file, the ``ContentBaseName``
  4. If not title has been found to this point, the title is 'Untitled'.
* If a non-content title was found and there are one or more H1 elements in the content, use the fix, warn, or error logic (below).
* If a content title was found and there are more than one H1 elements in the content, use fix, warn, or error logic (below).
* In the event of H1 issues:
  1. If site or per-page ``fixMultipleH1Error`` is not false do nothing; indicates that the content handling logic fixes up the H1 issues.
  2. Otherwise, if ``warnMultipleH1Error``, emit a build warning if there are H1 issues.
  3. Finally, if ``ignoreMultipleH1Error`` is not true, fatally error the build on H1 issues.

### 'siteName'/site title discovery and use

* Uses the following order of preference for discovering site title:
  1. ``title`` configuration item for the site (or language-specific site)
  2. ``.Site.Params.Title``
  3. Try steps 1 and 2 for the 'name'/Title of the homepage
  4. If not title found in 1-3, use a title of 'Untitled Site'

## End notes

### Note 1

From frontmatter

### Note 2

See [DFDs Hugo image handling module](https://github.com/danielfdickinson/image-handling-mod-hugo-dfd)

### Note 3

 Article or website, depending on frontmatter

### Note 4

Other URLs such as those for media-image may have their own secure URL as a part of the microformat

### Note 5

Depending on frontmatter and site config

### Note 6

* That is a choice of either \<meta name="some-meta" content="some-content" /> or \<meta name="some-meta" content="some-content">
* **Only for \<head> tags generated by this module**; you'll need to implement the same logic for the rest of the items in your \<head> section if you need consistency

### Note 7

Microformats are used by search engines and other automated page handling software including for displaying website 'cards' (image and summary of a page/site in a box on other sites, such as Discourse forum).

## Contributions Welcome

If [your issue can't be found when searching both open and closed issues](https://github.com/danielfdickinson/metadata-mod-hugo-dfd/issues?q=is%3Aissue), please add it!

Please [check open issues on danielfdickinson/metadata-mod-hugo-dfd](https://github.com/danielfdickinson/metadata-mod-hugo-dfd/issues)
for enhancements and bugs that you would like resolved, write the fix, and submit a PR!

Add and improving documention is always handy as well.

Thank you, and I hope you find this repository useful.
