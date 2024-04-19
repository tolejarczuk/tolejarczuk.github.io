---
created: <% tp.file.creation_date() %>
share: true
dg-publish: false
dg-home: false
author: Tomasz Olejarczuk
title: <% tp.file.title %>
date: <% tp.date.now(format = "DD-MM-YYYY") %>
subject: null
keywords: null
subtitle: null
titlepage: true
titlepage-text-color: '663366'
titlepage-rule-color: '663366'
titlepage-background: null
titlepage-rule-height: null
page-background: Bins/Pandoc-extras/backgrounds/background6.pdf
titlepage-logo: Bins/Pandoc-extras/fontys-logo.pdf
logo-width: 50mm
toc: true
toc-own-page: true
header-left: \hspace{1cm}
header-center: \leftmark
header-right: Page \thepage
footer-left: \thetitle
footer-center: Fontys
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
