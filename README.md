[![Build Status](https://travis-ci.org/declandewet/reshape-rss.svg?branch=master)](https://travis-ci.org/declandewet/reshape-rss)
[![codecov](https://codecov.io/gh/declandewet/reshape-rss/branch/master/graph/badge.svg)](https://codecov.io/gh/declandewet/reshape-rss)
[![dependencies Status](https://david-dm.org/declandewet/reshape-rss/status.svg)](https://david-dm.org/declandewet/reshape-rss)
[![devDependencies Status](https://david-dm.org/declandewet/reshape-rss/dev-status.svg)](https://david-dm.org/declandewet/reshape-rss?type=dev)

# reshape-rss

[Reshape](https://github.com/reshape/reshape) plugin for generating RSS feeds.

> **Note**: this project is still in early development and not all features may be implemented yet.

### Example Usage

```js
const reshape = require('reshape')
const rss = require('reshape-rss')

let feed = {}

reshape({
  plugins: [
    rss(feed)
  ]
})
  .process(`
    <main role="main" rss-channel rss-language="en-US">
      <a href="http://mysite.com/blog" rss-link>
        <h1 rss-title>Blog Posts</h1>
      </a>
      <p rss-description>Read the latest posts from my blog</p>
      <article rss-item rss-category="random">
        <h2 rss-title>The Genius Loki of Utopia</a></h2>
        <time rss-pubDate>12 July 2013, 11:13</time>
        <p rss-description>A sample blog post</p>
        <footer rss-author>Joe West</footer>
        <a href="http://mysite.com/blog/the-genius-loki-of-utopia/#/comments"
           rss-comments>31 Comments</a>
        <a href="http://mysite.com/blog/the-genius-loki-of-utopia"
           rss-link
           rss-guid>Read More</a>
      </article>
      <footer rss-copyright>© mysite.com 2013</footer>
    </main>
  `)
  .then((result) => {
    console.log(rss.convertToXML(feed))
  })
```

![arrow pointing down](http://imgh.us/downarrow_1.png)

```xml
<!doctype xml>
<rss
  version="2.0"
  xmlns:content="http://purl.org/rss/1.0/modules/content/"
  xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    <title>Blog Posts</title>
    <description>Read the latest posts from my blog</description>
    <copyright>© mysite.com 2013</copyright>
    <generator>reshape-rss</generator>
    <link>http://mysite.com/blog</link>
    <lastBuildDate>Sat, 13 Jul 2013 13:12:00 EST</lastBuildDate>
    <pubDate>Fri, 12 Jul 2013 11:13:00 EST</pubDate>
    <language>en-US</language>
    <item>
      <title>The Genius Loki of Utopia</title>
      <description>A sample blog post</description>
      <author>Joe West</author>
      <pubDate>Fri, 12 Jul 2013 11:13:00 EST</pubDate>
      <link>http://mysite.com/blog/the-genius-loki-of-utopia</link>
      <guid isPermaLink="true">http://mysite.com/blog/the-genius-loki-of-utopia</guid>
      <category>random</category>
      <comments>http://mysite.com/blog/the-genius-loki-of-utopia/#/comments</comments>
    </item>
  </channel>
</rss>
```

### Installation

```sh
$ npm install reshape-rss --save
```



### Pass an empty object to `rss`

`reshape-rss` takes one option: an empty object. As your HTML file is parsed for `rss-*` attributes, this object will be populated with information about your RSS feed. For the example above, it would look like this once parsing is done:

```js
{
  channels: [{
    title: 'Blog Posts',
    description: 'Read the latest posts from my blog',
    link: 'http://mysite.com/blog',
    copyright: '© mysite.com 2013',
    language: 'en-US',
    lastBuildDate: 'Sat, 13 Jul 2013 13:12:00 EST',
    pubDate: 'Fri, 12 Jul 2013 11:13:00 EST'
    items: [{
      title: 'The Genius Loki of Utopia',
      description: 'A sample blog post',
      author: 'Joe West',
      pubDate: 'Fri, 12 Jul 2013 11:13:00 EST',
      link: 'http://mysite.com/blog/the-genius-loki-of-utopia',
      guid: {
        isPermaLink: true,
        value: 'http://mysite.com/blog/the-genius-loki-of-utopia'
      },
      categories: ['random'],
      comments: 'http://mysite.com/blog/the-genius-loki-of-utopia/#/comments'
    }]
  }]
}
```

### Converting to XML

You can use the `rss.convertToXML()` method to convert your feed object to a string of XML. You can then do whatever you like with this string, like write it out to a file.

```js
const rss = require('reshape-rss')
rss.convertToXML(feed) // => "<!doctype xml><rss>...</rss>"
```



### Merging Many Feeds

`reshape-rss` exposes one more method: `rss.mergeFeeds()`. Pass this method two or more feed objects, and it will merge them together. It's useful if you are working with multiple files that produce multiple RSS feeds but you only want to render them out to one XML file.

```js
const rss = require('reshape-rss')
rss.mergeFeeds({
  channels: [{
    title: 'Foo',
    description: 'Bar',
    link: 'http://baz.com'
  }]
}, {
  channels: [{
    title: 'Fizz',
    description: 'Buzz',
    link: 'http://fizzbuzz.com'
  }]
})
/* => {
  channels: [{
    title: 'Foo',
    description: 'Bar',
    link: 'http://baz.com'
  }, {
    title: 'Fizz',
    description: 'Buzz',
    link: 'http://fizzbuzz.com'
  }]
} */
```



### Specifying Attributes

In your HTML file, you can specify an `rss-channel` attribute on any element. An element with an `rss-channel` attribute can have other `rss-*` attributes directly on or as a descendant of itself. These attributes will become properties of the `rss-channel`. One HTML file can have many `rss-channel` attributes. A channel requires an `rss-title`, `rss-description` and `rss-link` attribute.

```html
<main rss-channel>
  <h1 rss-title>Foo</h1> <!-- becomes "title" of channel -->
  <p rss-description>bar</p> <!-- becomes "description" of channel -->
  <a href="https://site.com/foo" rss-link>Go to foo</a> <!-- becomes "link" of channel -->
  ...
</main>
```

An `rss-channel` attribute must have at least one child with an `rss-item` attribute. Any `rss-*` attributes directly on or descendant of an `rss-item` attribute will become properties of that `rss-item`. An item requires an `rss-title`, `rss-description` and `rss-link` attribute. An `rss-channel` can have many descendant `rss-item`s.

```html
<main rss-channel>
  <h1 rss-title>Foo</h1>
  <p rss-description>bar</p>
  <a href="https://site.com/foo" rss-link>Go to foo</a>
  <article rss-item> <!-- this item belongs to it's parent channel -->
    <h2 rss-title>Fizz</h2> <!-- becomes "title" of item -->
    <p rss-description>Bazz</p> <!-- becomes "description" of item -->
    <a href="https://site.com/foo/fizz" rss-link>Read More</a> <!-- becomes "link" of item -->
  </article>
</main>
```



The attributes can be specified anywhere in the markup **as long as** the properties of `rss-channel` are placed on or within `rss-channel`, an `rss-item` occurs only within an `rss-channel`, and properties of `rss-item` are placed on or within `rss-item`.

Most `rss-*` attributes will attempt to infer their value from the `.innerText` property of the HTML node they are placed on, however, special case is given to `rss-guid`, `rss-link` and `rss-comments`, which will grab their value from their elements' `href` attribute if present. You may override this behavior by passing in a value directly, like so:

```html
<main rss-channel rss-title="Custom Title">
  <h1>Foo</h1>
  <p rss-description="Wow such doge">bar</p>
  <a href="https://site.com/foo" rss-link>Go to foo</a>
  <article rss-item rss-link="http://google.com">
    <h2 rss-title="Custom Title">Fizz</h2>
    <p rss-description>Bazz</p>
    <a href="https://site.com/foo/fizz">Read More</a>
  </article>
</main>
```
