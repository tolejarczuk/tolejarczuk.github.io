---
created: <% tp.file.creation_date() %>
share: false
dg-publish: false
dg-home: false
author: Tomasz Olejarczuk
title: <% tp.file.title %>
date: <% tp.date.now(format = "DD-MM-YYYY") %>
subtitle: null
titlepage: true
titlepage-text-color: 2a2f31
titlepage-rule-color: 2a2f31
titlepage-background: null
titlepage-rule-height: null
page-background: Bins/Pandoc-extras/backgrounds/background9.pdf
titlepage-logo: null
logo-width: null
toc: true
toc-own-page: true
header-left: \hspace{1cm}
header-center: \leftmark
header-right: Page \thepage
footer-left: \thetitle
footer-center: null
footer-right: \theauthor
tags: null
---

# \<% tp.file.title %>

---

Last Modified: `=dateformat(this.file.mtime, "DDDD, HH:mm")`

#### Tags:

`=this.file.tags`

````dataview
List FROM #kubernetes
````
