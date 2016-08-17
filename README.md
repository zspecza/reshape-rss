# reshape-rss

[Reshape](https://github.com/reshape/reshape) plugin for generating RSS feeds.

> **Note**: this project is still in early development and not all features may be implemented yet.

### Example Usage

`app.js`

```js
const reshape = require('reshape')
const rss = require('reshape-rss')

reshape({
  plugins: [
    rss({
      output: 'rss.xml',
      channels: {
        "Blog Posts": {
          description: "Read the latest posts from my blog.",
          copyright: "© mysite.com 2016",
          link: "http://mysite.com/blog"
        }
      }
    })
  ]
})
  .process(`
	<article rss-channel="Blog Posts" rss-category="random">
      <h2 rss-title>The Genius Loki of Utopia</h2>
      <time rss-pubDate>12 July 2013, 11:13</time>
      <p rss-description>A sample blog post</p>
      <footer rss-author>Joe West</footer>
      <a href="http://mysite.com/blog/the-genius-loki-of-utopia/#/comments"
         rss-comments>31 Comments</a>
      <a href="http://mysite.com/blog/the-genius-loki-of-utopia" rss-link rss-guid>Read More</a>
	</article>
  `)
  .then((result) => result.output())
```

![arrow pointing down](http://imgh.us/downarrow_1.png)

`rss.xml`

```xml
<!doctype xml>
<rss
  version="2.0"
  xmlns:content="http://purl.org/rss/1.0/modules/content/"
  xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    <title>Blog Posts</title>
    <description>Read the latest posts from my blog</description>
    <copyright>© mysite.com 2016</copyright>
    <generator>reshape-rss</generator>
    <link>http://mysite.com/blog</link>
    <lastBuildDate>Sat, 13 Jul 2013 13:12:00 EST</lastBuildDate>
    <pubDate>Fri, 12 Jul 2013 11:13:00 EST</pubDate>
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

### Options

Options passed to the plugin function. Any custom channel property will be mapped to a respective XML node (child of `<channel>`) e.g. `channel.foo: 'bar'` becomes `<foo>bar</foo>`.  Refer to [this explanation](http://www.landofcode.com/rss-reference/channel-elements.php) of each channel property.

> `*` indicates a required option.

| Name                 | Description                         | Default     |
| -------------------- | ----------------------------------- | ----------- |
| output               | Output path for the RSS xml file.   | `./rss.xml` |
| channels*            | Object containing channel metadata. |             |
| channel.title*       | This channel's title                |             |
| channel.link*        | A link to this channel's HTML site  |             |
| channel.description* | A description for this channel      |             |

### Item Attributes

Attributes for a single item. Any custom item attribute will be mapped to a respective XML node (child of `<item>`) e.g. `rss-foo="bar"` becomes `<foo>bar</foo>`. Refer to [this explanation](http://www.landofcode.com/rss-reference/item-tag.php) for a list of each item property. If an attribute has no value, it will be inferred from the `innerText` of the node. `rss-link`, `rss-comments` and `rss-guid` will grab the value from the `href` attribute if present.

> `*` indicates a required attribute.

| Name             | Description                           | Default |
| ---------------- | ------------------------------------- | ------- |
| rss-channel*     | Which channel this item belongs to.   |         |
| rss-title*       | The title for this item.              |         |
| rss-description* | The description for this item.        |         |
| rss-link*        | A URI to the HTML site for this item. |         |
