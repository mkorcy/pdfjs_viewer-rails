# pdf.js update guide

This guide describes how to update the pdf.js javascript library.

1. Download the latest pdf.js stable release from https://mozilla.github.io/pdf.js/getting_started/#download

2. Extract the `.zip` file

3. Replace all the files and folders marked with a `*` with the corresponding ones from  the new release
```
├── app
│   ├── assets
│   │   ├── javascripts
│   │   │   └── pdfjs_viewer
│   │   │       ├── application.js
│   │   │       ├── pdfjs
│   │   │       │   └── pdf.js *
│   │   │       ├── viewer.js *
│   │   └── stylesheets
│   │       └── pdfjs_viewer
│   │           ├── application.css
│   │           ├── full.scss
│   │           ├── minimal.scss
│   │           ├── pdfjs
│   │           │   └── viewer.css *
│   │           └── reduced.scss
├── public
│   └── pdfjs
│       └── web
│           ├── cmaps *
│           ├── images *
│           ├── locale *
│           ├── standard_fonts *
│           └── pdf.sandbox.js *
│           └── pdf.worker.js *
```

4. Apply the patches

## `app/assets/stylesheets/pdfjs_viewer/pdfjs/viewer.css`

Replace all `url(images/` with `url(/pdfjs/web/images/`

Take extra care here as there are a few instances of inconsistent naming across pdf.js releases! For example the upgrade to 1.10.100 was not a trivial find and replace.

## `app/assets/javascripts/pdfjs_viewer/viewer.js.erb`

Add to defaultOptions:

```javascript
  withCredentials: {
    value: false,
    kind: OptionKind.API
  },
```

Replace:

```javascript
var HOSTED_VIEWER_ORIGINS = ["null", "http://mozilla.github.io", "https://mozilla.github.io"]; 
```

with:

```javascript
var HOSTED_VIEWER_ORIGINS = <%= ENV.fetch("PDFJS_VIEWER_ORIGINS", "").split(",").inspect %>;
```

## `app/views/pdfjs_viewer/viewer/_viewer.html.erb`

Replace the whole content of `app/views/pdfjs_viewer/viewer/_viewer.html.erb` with `web/viewer.html` from the new pdf.js release.

##

Insert
```html
<% title = local_assigns[:title] || "PDF.js viewer" %>
<% pdf_url = local_assigns[:pdf_url] %>
```
at the top of the file before `<!DOCTYPE html>`

##

Replace all children of `<head>` except the `<meta>` tags with
```html
<%= render "pdfjs_viewer/viewer/head", title: title, pdf_url: pdf_url %>
```

##
Inject the correct styling class into the body classes.
```html
<body tabindex="1" class="loadingInProgress" id="pdfjs_viewer-<%= style %>">
```
##

At the bottom of the file insert `<%= render "pdfjs_viewer/viewer/printcontainer" %>` after `<div id="printContainer"></div>`
