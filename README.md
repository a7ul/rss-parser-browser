# rss-parser-browser

[![Build Status](https://travis-ci.org/master-atul/rss-parser-browser.svg?branch=master)](https://travis-ci.org/master-atul/rss-parser-browser)

This is a direct clone from https://github.com/bobby-brennan/rss-parser

I have removed the node dependencies so that it works with webpack in browser.

All credits should go to bobby-brennan.

## Installation
You can install via npm:
```bash
npm install --save rss-parser-browser
# or
yarn add rss-parser-browser
```

## Usage
You can parse RSS from a URL or a string.
* `parseString(xml, [options,],  callback)`

* `parseURL(url, [options,] callback)`


## Output
Check out the full output format in [test/output/reddit.json](test/output/reddit.json)

##### Notes:
* The `dc:` prefix will be removed from all fields
* Both `dc:date` and `pubDate` will be available in ISO 8601 format as `isoDate`
* If `author` is specified, but not `dc:creator`, `creator` will be set to `author` ([see article](http://www.lowter.com/blogs/2008/2/9/rss-dccreator-author))

### Usage
```js
var parser = require('rss-parser-browser');

parser.parseURL('https://www.reddit.com/.rss', function(err, parsed) {
  console.log(parsed.feed.title);
  parsed.feed.entries.forEach(function(entry) {
    console.log(entry.title + ':' + entry.link);
  })
})
```

### Redirects
By default, `parseURL` will follow up to one redirect. You can change this
with `options.maxRedirects`.

```js
parser.parseURL('https://reddit.com/.rss', {maxRedirects: 3}, function(err, parsed) {
  console.log(parsed.feed.title);
});
```

### Custom Fields
If your RSS feed contains fields that aren't currently returned, you can access them using the `customFields` option.

```js
var options = {
  customFields: {
    feed: ['otherTitle', 'extendedDescription'],
    item: ['coAuthor','subtitle'],
  }
}
parser.parseURL('https://www.reddit.com/.rss', options, function(err, parsed) {
  console.log(parsed.feed.extendedDescription);

  parsed.feed.entries.forEach(function(entry) {
    console.log(entry.coAuthor + ':' + entry.subtitle);
  })
})
```

To rename fields, you can pass in an array with two items, in the format `[fromField, toField]`:

```js
var options = {
  customFields: {
    item: [
      ['dc:coAuthor', 'coAuthor'],
    ]
  }
}
```

## Contributing
Contributions welcome!

### Running Tests
The tests run the RSS parser for several sample RSS feeds in `test/input` and outputs the resulting JSON into `test/output`. If there are any changes to the output files the tests will fail.

To check if your changes affect the output of any test cases, run

`npm test`

To update the output files with your changes, run

`WRITE_GOLDEN=true npm test`

### Publishing Releases
```bash
# change version in package.json
grunt build
git commit -a -m "vX.X.X"
git tag vX.X.X
npm publish
git push --follow-tags
```
