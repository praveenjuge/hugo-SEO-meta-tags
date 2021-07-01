# Hugo SEO Meta Tags

Simple list of SEO tags to use on Hugo based websites.

### `config.toml`

An example config file to help with everything we use in the meta tags.

```toml
baseURL = "https://www.example.org/"
title = "Example Site"
languageCode = "en-us"

enableEmoji = true
enableGitInfo = true
enableRobotsTXT = true
canonifyURLs = true

[params]
  author = "Praveen Juge"
  github = "https://github.com/praveenjuge"
  facebook = "https://facebook.com/praveenjuge"
  og_image = "/images/og.png"
  sitename = "Site Name"
  twitter = "@praveenjuge"

[languages]
  [languages.en]
    languageName = "English"
    languageCode = "en-us"
    weight = 1

  [languages.ae]
    languageName = "Arabic"
    languageCode = "ae"
    weight = 2
```

### `baseof.html`

A default [`baseof.html` file](https://gohugo.io/templates/base/) with best SEO practices.

```html
<!DOCTYPE html>
<html prefix="og: http://ogp.me/ns#" lang="{{ .Language }}">
  <head itemscope itemtype="https://schema.org/WebPage">  <!-- Use the correct itemtype for your website https://schema.org/CreativeWork#subtypes -->
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />

    <!-- All the meta tags below will come under this meta partial -->
    {{ partial "meta" . }}
    {{ partial "links" . }}
  </head>

  <body>
    {{ partial "header" . }}
    <main id="main">
      {{ block "main" . }}
      {{ end }}
    </main>
    {{ partial "footer" . }}
    {{ partial "js" . }}
  </body>
</html>
```

### Title Tags

```html
<title itemprop="name">{{ .Title }} | {{ .Site.Title }}</title>
<meta property="og:title" content="{{ .Title }} | {{ .Site.Title }}" />
<meta name="twitter:title" content="{{ .Title }} | {{ .Site.Title }}" />
<meta itemprop="name" content="{{ .Title }} | {{ .Site.Title }}" />
<meta name="application-name" content="{{ .Title }} | {{ .Site.Title }}" />
<meta property="og:site_name" content="{{ .Site.Params.sitename }}" />
```

### Description Tags

```html
<meta name="description" content="{{ .Params.description }}" />
<meta itemprop="description" content="{{ .Params.description }}" />
<meta property="og:description" content="{{ .Params.description }}" />
<meta name="twitter:description" content="{{ .Params.description }}" />
```

### Link Tags

```html
<base href="{{ .Permalink }}" />
<link rel="canonical" href="{{ .Permalink }}" itemprop="url" />
<meta name="url" content="{{ .Permalink }}" />
<meta name="twitter:url" content="{{ .Permalink }}" />
<meta property="og:url" content="{{ .Permalink }}" />
```

### Language Tags

```html
<meta property="og:locale" content="{{ .Language.Lang }}" />
<meta name="language" content="{{ .Language.LanguageName }}" />
{{ range .AllTranslations }}
  <link rel="alternate" hreflang="{{ .Language.Lang }}" href="{{ .Permalink }}" title="{{ .Language.LanguageName }}" />
{{ end }}
```

### Image Tags

```html
{{ with .Params.image }}
  <meta itemprop="image" content="{{ . | absURL }}" />
  <meta property="og:image" content="{{ . | absURL }}" />
  <meta name="twitter:image" content="{{ . | absURL }}" />
  <meta name="twitter:image:src" content="{{ . | absURL }}" />
{{ else }}
  <meta itemprop="image" content="{{ .Site.Params.ogimage | absURL }}" />
  <meta property="og:image" content="{{ .Site.Params.ogimage | absURL }}" />
  <meta name="twitter:image" content="{{ .Site.Params.ogimage | absURL }}" />
  <meta name="twitter:image:src" content="{{ .Site.Params.ogimage | absURL }}" />
{{ end }}
```

### Date Tags

```html
<meta property="og:updated_time" content={{ .Lastmod.Format "2006-01-02T15:04:05Z0700" | safeHTML }} />
```

### Sitemap & RSS Feed Tags

```html
<link rel="sitemap" type="application/xml" title="Sitemap" href='{{ "sitemap.xml" | absURL }}' />

{{ with .OutputFormats.Get "RSS" }}
  <link href="{{ .Permalink }}" rel="alternate" type="application/rss+xml" title="{{ $.Site.Title }}" />
  <link href="{{ .Permalink }}" rel="feed" type="application/rss+xml" title="{{ $.Site.Title }}" />
{{ end }}
```

### Tags for Article Pages

These meta tags are specific for article or blog pages such as, `example.org/blog/example-blog/`.

```html
<!-- To make sure this renders only in the article page, we check the section -->
{{ if eq .Section "blog" }}

<!-- Pagination meta tags for list pages only -->
{{ $paginator := .Paginate (where .Pages "Section" "blog") }} 
{{ if $paginator }}
  <link rel="first" href="{{ $paginator.First.URL }}" />
  <link rel="last" href="{{ $paginator.Last.URL }}" />
  {{ if $paginator.HasPrev }}
    <link rel="prev" href="{{ $paginator.Prev.URL }}" />
  {{end }}
  {{ if $paginator.HasNext }}
    <link rel="next" href="{{ $paginator.Next.URL }}" />
  {{end }}
{{end }}

<meta property="og:type" content="article" />
<meta property="article:publisher" content="{{ .Site.Params.facebook }}" />
<meta property="og:article:published_time" content={{ .Date.Format "2006-01-02T15:04:05Z0700" | safeHTML }} />
<meta property="article:published_time" content={{ .Date.Format "2006-01-02T15:04:05Z0700" | safeHTML }} />

{{ with.Params.author }}
  <meta property="og:article:author" content="{{humanize . }}" />
  <meta property="article:author" content="{{humanize . }}" />
  <meta name="author" content="{{humanize . }}" />
{{ end }}

{{ with.Params.category }}
  <meta name="news_keywords" content="{{ index . 0 }}" />
  <meta property="article:section" content="{{ index . 0 }}" />
{{ end }}

<script defer type="application/ld+json">
  {
    "@context": "http://schema.org",
    "@type": "Article",
    "headline": {{ .Title }},
    "author": {
      "@type": "Person",
      "name": "{{ .Site.Params.github }}"
    },
    "datePublished": "{{ .Date.Format "2006-01-02" }}",
    "description": {{ .Description }},
    "wordCount": {{ .WordCount }},
    "mainEntityOfPage": "True",
    "dateModified": "{{ .Lastmod.Format "2006-01-02" }}",
    "image": {
      "@type": "imageObject",
      "url": "{{ with .Params.image }}{{ .Permalink }}{{ end }}"
    },
    "publisher": {
      "@type": "Organization",
      "name": "{{ .Site.Title }}",
      "logo": {
        "@type": "imageObject",
        "url": "https://www.example.com/images/brand/favicon.png"
      }
    }
  }
</script>
{{ end }}
```

### Meta Tags for Website Pages

For pages such as `/contact`, `/about` etc.. Don't include these tags on article or blog pages.

```html
<meta property="og:type" content="website" />
<meta name="author" content="{{ .Site.Params.author }}" />
<script defer type="application/ld+json">
  {
    "@context": "http://schema.org",
    "@type": "WebSite",
    "url": "{{ .Permalink }}",
    "sameAs": [
      "{{ .Site.Params.facebook }}",
      "{{ .Site.Params.github }}"
    ],
    "name": "{{ .Title }}",
    "logo": "https://www.example.com/images/brand/favicon.png"
  }
</script>
```

### Favicon Tags

```html
<link rel="shortcut icon" href="/favicon.ico" />
<link rel="icon" type="image/x-icon" sizes="16x16 32x32" href="/favicon.ico" />
<link rel="apple-touch-icon" sizes="152x152" href="/favicons/favicon-152-precomposed.png" />
<link rel="apple-touch-icon" sizes="144x144" href="/favicons/favicon-144-precomposed.png" />
<link rel="apple-touch-icon" sizes="120x120" href="/favicons/favicon-120-precomposed.png" />
<link rel="apple-touch-icon" sizes="114x114" href="/favicons/favicon-114-precomposed.png" />
<link rel="apple-touch-icon" sizes="180x180" href="/favicons/favicon-180-precomposed.png" />
<link rel="apple-touch-icon" sizes="72x72" href="/favicons/favicon-72-precomposed.png" />
<link rel="apple-touch-icon" href="/favicons/favicon-57.png" />
<link rel="icon" href="/favicons/favicon-32.png" sizes="32x32" />
<link rel="icon" sizes="192x192" href="/favicons/favicon-192.png" />
```

### Search Engine Crawler Tags

```html
<meta name="robots" content="index,follow" />
<meta name="googlebot" content="index,follow" />
```

### Social Media Tags

```html
<meta name="twitter:site" content="{{ .Site.Params.twitter }}" />
<meta name="twitter:creator" content="{{ .Site.Params.twitter }}" />
<meta property="fb:app_id" content="538089519640705" />
<meta property="fb:admins" content="100000686899395" />
```

### Other Tags

```html
<link rel="manifest" href='{{ "manifest.json" | absURL }}' />
<meta name="theme-color" content="#141414" />
<meta name="apple-mobile-web-app-title" content="{{ .Site.Params.sitename }}" />
<meta name="apple-mobile-web-app-capable" content="yes" />
<meta name="apple-mobile-web-app-status-bar-style" content="black">
```
