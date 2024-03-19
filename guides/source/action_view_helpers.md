**DO NOT READ THIS FILE ON GITHUB, GUIDES ARE PUBLISHED ON https://guides.rubyonrails.org.**

Action View Helpers
====================

After reading this guide, you will know:

* How to format dates, strings, and numbers.
* How to link to images, videos, stylesheets, etc.
* How to work with Atom feeds and JavaScript in the views.
* How to cache, capture, debug and sanitize content.
* How to work with text and tags.

--------------------------------------------------------------------------------

Overview of Helpers Provided by Action View
-------------------------------------------

The following outlines **some of the most commonly used helpers** available in
Action View. It serves as a good starting point, but reviewing the full [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers.html) is
also recommented, as it covers all of the helpers in more detail.

### AssetTagHelper

This module provides methods for generating HTML that links views to assets such
as images, JavaScript files, stylesheets, and feeds.

By default, Rails links to these assets on the current host in the public
folder, but you can direct Rails to link to assets from a dedicated assets
server by setting [`config.asset_host`][] in the application configuration,
typically in `config/environments/production.rb`. For example, let's say your
asset host is `assets.example.com`:

```ruby
config.asset_host = "assets.example.com"
image_tag("rails.png")
# => <img src="http://assets.example.com/images/rails.png" />
```

[`config.asset_host`]: configuring.html#config-asset-host

#### audio_tag

Generate an HTML audio tag with source(s), either as a single tag for a string
source or nested source tags within an array for multiple sources. The `sources`
can be full paths, files in your public audios directory, or Active Storage
attachments.

```ruby
audio_tag("sound")
# => <audio src="/audios/sound"></audio>

audio_tag("sound.wav", "sound.mid")
# => <audio><source src="/audios/sound.wav" /><source src="/audios/sound.mid" /></audio>
```

You can modify the HTML attributes of the audio tag by passing a hash as the
last argument.

```ruby
audio_tag("sound", controls: true)
# => <audio controls="controls" src="/audios/sound"></audio>
```

`controls` is a boolean attribute that indicates whether the audio should have
controls.

INFO: Internally, `audio_tag` uses [`audio_path` from the
AssetUrlHelpers](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetUrlHelper.html#method-i-audio_path)
to build the audio path.

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-audio_tag)
for more information.

#### auto_discovery_link_tag

Returns a link tag that browsers and feed readers can use to auto-detect an RSS,
Atom, or JSON feed.

```ruby
auto_discovery_link_tag(:rss, "http://www.example.com/feed.rss", { title: "RSS Feed" })
# => <link rel="alternate" type="application/rss+xml" title="RSS Feed" href="http://www.example.com/feed.rss" />
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-auto_discovery_link_tag)
for more information.

#### favicon_link_tag

Returns a link tag for a favicon managed by the asset pipeline. The `source` can
be a full path or a file that exists in your assets directory.

```ruby
favicon_link_tag
# => <link href="/assets/favicon.ico" rel="icon" type="image/x-icon" />
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-favicon_link_tag)
for more information.

#### image_tag

Returns an HTML image tag for the source. The `source` can be a full path or a
file that exists in your `app/assets/images` directory.

```ruby
image_tag("icon.png")
# => <img src="/assets/icon.png" />
```

INFO: Internally, `image_tag` uses [`image_path` from the
AssetUrlHelpers](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetUrlHelper.html#method-i-image_path)
to build the image path.

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-image_tag)
for more information.

#### javascript_include_tag

Returns an HTML script tag for each of the sources provided. You can pass in the
filename (`.js` extension is optional) of JavaScript files that exist in your
`app/assets/javascripts` directory for inclusion into the current page, or you
can pass the full path relative to your document root.

```ruby
javascript_include_tag "common"
# => <script src="/assets/common.js"></script>
```

You can modify the HTML attributes of the script tag by passing a hash as the
last argument.

```ruby
javascript_include_tag "common", async: true
# => <script src="/assets/common.js" async="async"></script>
```

Some of the most common attributes are `async` and `defer`, where `async` will
allow the script to be loaded in parallel to be parsed and evaluated as soon as
possible, and `defer` will indicate that the script is meant to be executed
after the document has been parsed.

INFO: Internally, `javascript_include_tag` uses [`javascript_path` from the
AssetUrlHelpers](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetUrlHelper.html#method-i-javascript_path)
to build the script path.

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-javascript_include_tag)
for more information.

#### picture_tag

Returns an HTML picture tag for the source. It supports passing a String, an
Array, or a Block.

```ruby
picture_tag("icon.webp", "icon.png")
```

This generates the following HTML:

```html
<picture>
  <source srcset="/assets/icon.webp" type="image/webp" />
  <source srcset="/assets/icon.png" type="image/png" />
  <img src="/assets/icon.png" />
</picture>
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-picture_tag)
for more information.

#### preload_link_tag

Returns a link tag that browsers can use to preload the source. The source can
be the path of a resource managed by the asset pipeline, a full path, or a URI.

```ruby
preload_link_tag "application.css"
# => <link rel="preload" href="/assets/application.css" as="style" type="text/css" />
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-preload_link_tag)
for more information.

#### stylesheet_link_tag

Returns a stylesheet link tag for the sources specified as arguments. If you
don't specify an extension, `.css` will be appended automatically.

```ruby
stylesheet_link_tag "application"
# => <link href="/assets/application.css" rel="stylesheet" />
```

You can modify the HTML attributes of the stylesheet tag by passing a hash as
the last argument.

```ruby
stylesheet_link_tag "application", media: "all"
# => <link href="/assets/application.css" media="all" rel="stylesheet" />
```

`media` is used to specify the media type for the link. The most common media
types are `all`, `screen`, `print`, and `speech`.

INFO: Internally, `stylesheet_link_tag` uses [`stylesheet_path` from the
AssetUrlHelpers](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetUrlHelper.html#method-i-stylesheet_path)
to build the stylesheet path.

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-stylesheet_link_tag)
for more information.

#### video_tag

Generate an HTML video tag with source(s), either as a single tag for a string
source or nested source tags within an array for multiple sources. The `sources`
can be full paths, files in your public videos directory, or Active Storage
attachments.

```ruby
video_tag("trailer")
# => <video src="/videos/trailer"></video>

video_tag(["trailer.ogg", "trailer.flv"])
# => <video><source src="/videos/trailer.ogg" /><source src="/videos/trailer.flv" /></video>
```

You can modify the HTML attributes of the video tag by passing a hash as the
last argument.

```ruby
video_tag("trailer", controls: true)
```
`controls` is a boolean attribute that indicates whether the video should have
controls.

INFO: Internally, `video_tag` uses [`video_path` from the
AssetUrlHelpers](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetUrlHelper.html#method-i-video_path)
to build the video path.

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-video_tag)
for more information.

### AtomFeedHelper

Atom Feeds are XML-based file formats used to syndicate content and can be
used by users in feed readers to browse content or by search engines to help
discover additional information about your site.

`AtomFeedHelper` is mostly used in Builder templates for creating XML.

#### atom_feed

This helper makes building an Atom feed easy. Here's a full usage example:

**config/routes.rb**

```ruby
resources :articles
```

**app/controllers/articles_controller.rb**

```ruby
def index
  @articles = Article.all

  respond_to do |format|
    format.html
    format.atom
  end
end
```

**app/views/articles/index.atom.builder**

```ruby
atom_feed do |feed|
  feed.title("Articles Index")
  feed.updated(@articles.first.created_at)

  @articles.each do |article|
    feed.entry(article) do |entry|
      entry.title(article.title)
      entry.content(article.body, type: 'html')

      entry.author do |author|
        author.name(article.author_name)
      end
    end
  end
end
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/AtomFeedHelper.html#method-i-atom_feed)
for more information.

### CacheHelper

This helper exposes a method for caching fragments of a view rather than an
entire action or page. This technique is useful for caching pieces like menus,
lists of news topics, static HTML fragments, and so on. It allows a fragment of
view logic to be wrapped in a cache block and served out of the cache store when
the next request comes in.

#### cache

The `cache` method takes a block that contains the content you wish to cache.

For example, if you wanted to cache each article on a page, you could do so by
passing the `article` object to the `cache` method. This would cache each
article separately.

```erb
<% @articles.each do |article| %>
  <% cache article do %>
    <%= render article %>
  <% end %>
<% end %>
```

When your application receives its first request to this page, Rails will write
a new cache entry with a unique key. A key looks something like this:

```irb
views/articles/index:bea67108094918eeba32cd4a6f786301/articles/1
```

See [`Fragment Caching`](caching_with_rails.html#fragment-caching) and the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/CacheHelper.html#method-i-cache)
for more information.


### CaptureHelper

The CaptureHelper exposes methods to let you extract generated markup which can
be used in other parts of a template or layout file.

It provides a method to capture blocks into variables through `capture` and a
way to capture a block of markup for use in a layout through `content_for`.

#### capture

The `capture` method allows you to extract part of a template into a variable.

```html+erb
<% @greeting = capture do %>
  <p>Welcome! The date and time is <%= Time.now %></p>
<% end %>
```

You can then use this variable anywhere in your templates, layouts, or helpers.

```html+erb
<html>
  <head>
    <title>Welcome!</title>
  </head>
  <body>
    <%= @greeting %>
  </body>
</html>
```

The return of capture is the string generated by the block.

``` ruby
@greeting
# => "Welcome to my shiny new web page! The date and time is 2018-09-06 11:09:16 -0500"
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/CaptureHelper.html#method-i-capture)
for more information.


#### content_for

Calling `content_for` stores a block of markup in an identifier for later use.
You can make subsequent calls to the stored content in other templates, helper
modules or the layout by passing the identifier as an argument to `yield`.

For example, let's say you have a standard application layout, but also a
special page that requires some JavaScript that the rest of the site doesn't
need. You can use `content_for` to include this JavaScript on your special page
without fattening up or affecting the rest of the site's performance.

You define a `content_for` block in the special page's view, and then you
`yield` it within the layout. For other pages, where the `content_for` block
isn't utilized, it remains empty, resulting in nothing being yielded.

**app/views/users/special_page.html.erb**

```html+erb
<p>This is a special page.</p>

<% content_for :greeting_alert do %>
  <script>alert('Hello!')</script>
<% end %>
```

**app/views/layouts/application.html.erb**

```html+erb
<html>
  <head>
    <title>Welcome!</title>
  </head>
  <body class="<%= content_for?(:greeting_alert) ? 'bg-white' : 'bg-gray-400' %>">
    <%= yield :greeting_alert %>
    <p>Welcome! The date and time is <%= Time.now %></p>
  </body>
</html>
```

You'll notice that in the above example, we use the `content_for?` predicate
method to conditionally render a relevant class. This method checks whether any
content has been captured yet, enabling you to adjust parts of your layout based
on the content within your views.

Additionally, you can employ `content_for` within a helper module.

**app/helpers/greeting_helper.rb**

```ruby
module GreetingHelper
  def special_greeting_alert
    content_for(:greeting_alert) || "Bye!"
  end
end
```

Now, you can call `special_greeting_alert` in your layout to retrieve the
content stored in the `content_for` block. If a `content_for` block is set on
the page being rendered, such as in the case of the special page, it will
display the greeting alert. Otherwise, it will display the default text "Bye!"

WARNING: `content_for` is ignored in caches. So you shouldn’t use it for
elements that will be fragment cached.

NOTE: You may be thinking what's the difference between `capture` and
`content_for`? <br><br>
`capture` is used to capture a block of markup in a variable, while
`content_for` is used to store a block of markup in an identifier for later use.
Internally `content_for` actually calls `capture`. However, the key difference
lies in their behavior when invoked multiple times.<br><br>
`content_for` can be called repeatedly, concatenating the blocks it receives for
a specific identifier in the order they are provided. Each subsequent call
simply adds to what's already stored. In contrast, `capture` only remembers the
latest invocation; previous calls get overwritten.

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/CaptureHelper.html#method-i-content_for)
for more information.

### DateHelper

The Date Helper primarily creates select/option tags for different kinds of
dates and times or date and time elements.

#### distance_of_time_in_words

Reports the approximate distance in time between two Time or Date objects or
integers as seconds. Set `include_seconds` to true if you want more detailed
approximations.

```ruby
distance_of_time_in_words(Time.now, Time.now + 15.seconds)
# => less than a minute
distance_of_time_in_words(Time.now, Time.now + 15.seconds, include_seconds: true)
# => less than 20 seconds
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/DateHelper.html#method-i-distance_of_time_in_words)
for more information.

#### time_ago_in_words

It's similar to `distance_of_time_in_words`, but where `to_time` is fixed to
`Time.now`.

```ruby
time_ago_in_words(3.minutes.from_now) # => 3 minutes
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/DateHelper.html#method-i-time_ago_in_words)
for more information.

### DebugHelper

Provides a set of methods for making it easier to debug Rails objects.

#### debug

Returns a YAML representation of an object wrapped with a `pre` tag. This
creates a very readable way to inspect an object.

```ruby
my_hash = { 'first' => 1, 'second' => 'two', 'third' => [1, 2, 3] }
debug(my_hash)
```

```html
<pre class='debug_dump'>---
first: 1
second: two
third:
- 1
- 2
- 3
</pre>
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/DebugHelper.html#method-i-debug)
for more information.

### FormHelper

Form helpers simplify working with models compared to using standard HTML
elements alone. They offer a range of methods tailored to generating forms based
on your models. Each method corresponds to a specific type of input, such as
text fields, password fields, select dropdowns, and more. When a form is
submitted the inputs within the form are grouped into the params object and sent
back to the controller.

You can learn more about form helpers in the [Action View Form Helpers
Guide](form_helpers.html).

### JavaScriptHelper

Provides functionality for working with JavaScript in your views.

#### escape_javascript

Escapes carriage returns and single and double quotes for JavaScript segments.
You would use this method to take a string of text and make sure that it doesn’t
contain any invalid characters when the browser tries to parse it.

For example, if you have a partial with a greeting that contains double quotes,
you can escape the greeting to use in a JavaScript alert.

**app/views/users/greeting.html.rb**

```html+erb
my name is <%= current_user.name %>, and I'm here to say "Welcome to our website!"
```

```ruby
<script>
  var greeting = '<%= escape_javascript(render 'captured_pokemons/greeting') %>';
  alert(`Hello, ${greeting}`);
</script>
```

This will escape the double quotes correctly and display the greeting as in an
alert box.

#### javascript_tag

Returns a JavaScript tag wrapping the provided code. You can pass a hash of
options to control the behavior of the `<script>` tag.

```ruby
javascript_tag "alert('All is good')", type: 'application/javascript'
```

```html
<script type="application/javascript">
//<![CDATA[
alert('All is good')
//]]>
</script>
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/JavaScriptHelper.html#method-i-javascript_tag)
for more information.

### NumberHelper

Provides methods for converting numbers into formatted strings. Methods are
provided for phone numbers, currency, percentage, precision, positional
notation, and file size.

#### number_to_currency

Formats a number into a currency string (e.g., $13.65).

```ruby
number_to_currency(1234567890.50) # => $1,234,567,890.50
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/NumberHelper.html#method-i-number_to_currency)
for more information.

#### number_to_human

Pretty prints (formats and approximates) a number so it is more readable by
users; useful for numbers that can get very large.

```ruby
number_to_human(1234)    # => 1.23 Thousand
number_to_human(1234567) # => 1.23 Million
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/NumberHelper.html#method-i-number_to_human)
for more information.

#### number_to_human_size

Formats the bytes in size into a more understandable representation; useful for
reporting file sizes to users.

```ruby
number_to_human_size(1234)    # => 1.21 KB
number_to_human_size(1234567) # => 1.18 MB
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/NumberHelper.html#method-i-number_to_human_size)
for more information.

#### number_to_percentage

Formats a number as a percentage string.

```ruby
number_to_percentage(100, precision: 0) # => 100%
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/NumberHelper.html#method-i-number_to_percentage)
for more information.

#### number_to_phone

Formats a number into a phone number (US by default).

```ruby
number_to_phone(1235551234) # => 123-555-1234
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/NumberHelper.html#method-i-number_to_phone)
for more information.

#### number_with_delimiter

Formats a number with grouped thousands using a delimiter.

```ruby
number_with_delimiter(12345678) # => 12,345,678
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/NumberHelper.html#method-i-number_with_delimiter)
for more information.

#### number_with_precision

Formats a number with the specified level of `precision`, which defaults to 3.

```ruby
number_with_precision(111.2345)               # => 111.235
number_with_precision(111.2345, precision: 2) # => 111.23
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/NumberHelper.html#method-i-number_with_precision)
for more information.

### SanitizeHelper

The SanitizeHelper module provides a set of methods for scrubbing text of
undesired HTML elements. The helpers are particularly useful for helping to
ensure that only safe and valid HTML/CSS is rendered. It can also be useful to
prevent XSS attacks by escaping or removing potentially malicious content from
user input before rendering it in your views.

#### sanitize

The `sanitize` method will HTML encode all tags and strip all attributes that
aren't specifically allowed.

```ruby
sanitize @article.body
```

If either the `:attributes` or `:tags` options are passed, only the mentioned
attributes and tags are allowed and nothing else.

```ruby
sanitize @article.body, tags: %w(table tr td), attributes: %w(id class style)
```

To change defaults for multiple uses, for example, adding table tags to the
default:

```ruby
class Application < Rails::Application
  config.action_view.sanitized_allowed_tags = 'table', 'tr', 'td'
end
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/SanitizeHelper.html#method-i-sanitize)
for more information.

#### sanitize_css

Sanitizes a block of CSS code, particularly when it comes across a style
attribute in HTML content. `sanitize_css` is particularly useful when dealing
with user-generated content or dynamic content that includes style attributes.

The `sanitize_css` method below will remove the styles that are not allowed.

```ruby
sanitize_css("background-color: red; color: white; font-size: 16px;")
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/SanitizeHelper.html#method-i-sanitize_css)
for more information.

#### strip_links

Strips all link tags from text leaving just the link text.

```ruby
strip_links('<a href="https://rubyonrails.org">Ruby on Rails</a>')
# => Ruby on Rails

strip_links('emails to <a href="mailto:me@email.com">me@email.com</a>.')
# => emails to me@email.com.

strip_links('Blog: <a href="http://myblog.com/">Visit</a>.')
# => Blog: Visit.
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/SanitizeHelper.html#method-i-strip_links)
for more information.

#### strip_tags

Strips all HTML tags from the html, including comments. This functionality is
powered by the
[rails-html-sanitizer](https://github.com/rails/rails-html-sanitizer) gem.

```ruby
strip_tags("Strip <i>these</i> tags!")
# => Strip these tags!

strip_tags("<b>Bold</b> no more! <a href='more.html'>See more</a>")
# => Bold no more! See more
```

NOTE: The output may still contain unescaped `<`, `>`, `&` characters and
confuse browsers.

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/SanitizeHelper.html#method-i-strip_tags)
for more information.

### Text Helper

Provides a set of methods for filtering, formatting and transforming strings.

#### excerpt

Extracts the first occurrence of phrase plus surrounding text from `text`. An
omission marker is prepended/appended if the start/end of the result does not
coincide with the start/end of the text.

```ruby
excerpt('This is a very beautiful morning', 'very', separator: ' ', radius: 1)
# => ...a very beautiful...

excerpt('This is also an example', 'an', radius: 8, omission: '<chop> ')
#=> <chop> is also an example
```

where:

- `radius` is the number of characters to include on each side of the `phrase`.
  If the `phrase` isn't found, `nil` is returned.
- `ommision` is the string to be used to indicate omitted content.
- `separator` is the string to use to separate the `phrase` from the omitted
  content. Defaults to "", which treats each character as a token.

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/TextHelper.html#method-i-excerpt)
for more information.

#### pluralize

Returns the singular or plural form of a word based on the value of a number.

```ruby
pluralize(1, 'person') # => 1 person
pluralize(2, 'person') # => 2 people
pluralize(3, 'person', plural: 'users') # => 3 users
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/TextHelper.html#method-i-pluralize)
for more information.

#### truncate

Truncates a given text after a given `length`. If text is truncated, an omission
marker will be appended to the result for a total length not exceeding `length`.

```ruby
truncate("Once upon a time in a world far far away")
# => "Once upon a time in a world..."

truncate("Once upon a time in a world far far away", length: 17)
# => "Once upon a ti..."

truncate("Once upon a time in a world far far away", length: 17, separator: ' ')
# => "Once upon a..."

truncate("And they found that many people were sleeping better.", length: 25, omission: '... (continued)')
# => "And they f... (continued)"

truncate("<p>Once upon a time in a world far far away</p>", escape: false)
# => "<p>Once upon a time in a wo..."
```

where:

- `length`: The maximum number of characters to return.
- `separator`: A string or regexp used to find a breaking point at which to
  truncate. By default, truncation can occur at any character in text.
- `omission`: The string to append after truncating. Defaults to "...".
- `escape`: Whether to escape the result. Defaults to true.

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/TextHelper.html#method-i-truncate)
for more information.

#### word_wrap

Wraps the text into lines no longer than `line_width` width.

```ruby
word_wrap('Once upon a time', line_width: 8)
# => "Once\nupon a\ntime"
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/TextHelper.html#method-i-word_wrap)
for more information.

### Tag Helper

Provides methods to generate HTML tags programmatically.

#### content_tag

Generates an HTML block tag of the specified type to surround the content. You
can add HTML attributes by passing an `attributes` hash to options.

```ruby
content_tag(:div, "Hello world!", class: ["strong", { highlight: current_user.admin? }])
# => <div class="strong highlight">Hello world!</div>
 ```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/TagHelper.html#method-i-content_tag)
for more information.

#### tag

Generates a standalone HTML tag with the given `name` and `options`.

```ruby
tag.h1 'All titles fit to print'
# => <h1>All titles fit to print</h1>

tag.section class: %w( kitties puppies )
# => <section class="kitties puppies"></section>
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/TagHelper.html#method-i-tag)
for more information.

### UrlHelper

Provides methods to make links and get URLs that depend on the routing
subsystem.

#### button_to

Generates a form that submits to the passed URL. The form has a submit button
with the value of the `name`.

```html+erb
<%= button_to "Sign in", sign_in_path %>
```

would output the following HTML:

```html
<form method="post" action="/sessions" class="button_to">
  <input type="submit" value="Sign in" />
</form>
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-button_to)
for more information.

#### current_page?

Returns true if the current request URI matches the given `options`.

```html+erb
<% if current_page?(controller: 'profiles', action: 'show') %>
  <strong>Currently on the profile page</strong>
<% end %>
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-current_page-3F)
for more information.

#### link_to

Links to a URL derived from `url_for` under the hood. It's commonly used to
create links for RESTful resources, especially when passing models as arguments
to `link_to`.

```ruby
link_to "Profile", @profile
# => <a href="/profiles/1">Profile</a>

link_to "Book", @book # given a composite primary key [:author_id, :id]
# => <a href="/books/2_1">Book</a>
```

You can use a block if your link target can't fit in the name parameter.

```html+erb
<%= link_to @profile do %>
  <strong><%= @profile.name %></strong> -- <span>Check it out!</span>
<% end %>
```

would output the following HTML:

```html
<a href="/profiles/1">
  <strong>David</strong> -- <span>Check it out!</span>
</a>
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-link_to)
for more information.

#### mail_to

Generates a `mailto` link tag to the specified email address. You can also
specify the link text, additional HTML options, and whether to encode the email
address.

```html+erb
<%= mail_to "john_doe@gmail.com" %>
```

would output the following HTML:

```html
<a href="mailto:john_doe@gmail.com">john_doe@gmail.com</a>
```

See the [API
Documentation](https://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-mail_to)
for more information.

#### url_for

Returns the URL for the set of `options` provided.

```ruby
url_for @profile
# => /profiles/1

url_for [ @hotel, @booking, page: 2, line: 3 ]
# => /hotels/1/bookings/1?line=3&page=2

url_for @post # given a composite primary key [:blog_id, :id]
# => /posts/1_2
```
