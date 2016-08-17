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
    <article
      rss-item-channel="Blog Posts"
      rss-item-title="The Genius Loki of Utopia"
      rss-item-description="A sample blog post"
      rss-item-pubDate="12 July 2013, 11:13"
      rss-item-link="http://mysite.com/blog/the-genius-loki-of-utopia"
      rss-item-author="Joe West"
      rss-item-guid="http://mysite.com/blog/the-genius-loki-of-utopia"
      rss-item-category="random"
      rss-item-comments="http://mysite.com/blog/the-genius-loki-of-utopia/#/comments">
      ...
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

### Attributes

Attributes for a single item. Any custom item attribute will be mapped to a respective XML node (child of `<item>`) e.g. `rss-item-foo="bar"` becomes `<foo>bar</foo>`. Refer to [this explanation](http://www.landofcode.com/rss-reference/item-tag.php) for a list of each item property.

> `*` indicates a required attribute.

| Name                  | Description                           | Default |
| --------------------- | ------------------------------------- | ------- |
| rss-item-channel*     | Which channel this item belongs to.   |         |
| rss-item-title*       | The title for this item.              |         |
| rss-item-description* | The description for this item.        |         |
| rss-item-link*        | A URI to the HTML site for this item. |         |
