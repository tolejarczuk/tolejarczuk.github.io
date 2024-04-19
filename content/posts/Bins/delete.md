---
created: 2023-12-15 12:38
share: true
dg-publish: false
dg-home: false
author: Tomasz Olejarczuk
title: delete
date: 15-12-2023
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

# Create Multiline File

````markdown
# Creating a Multiple-Line File with `cat` and Here Document

In Unix-like systems, you can use the `cat` command along with a Here Document (<<) to create a multiple-line file efficiently. This is particularly useful when you need to generate configuration files, such as an `application.properties` file.

## Syntax:

```bash
cat <<EOF >application.properties
<CONTENT>
EOF
````

Replace `<CONTENT>` with the actual content you want in the file.

## Example:

Suppose you want to create an `application.properties` file with the following content:

````properties
FOO=Bar
````

You can achieve this using the following command:

````bash
cat <<EOF >application.properties
FOO=Bar
EOF
````

This command redirects the content between `<<EOF` and `EOF` into the `application.properties` file.

# Database Designs

## Light Controller

````mermaid
---
title: Lights Controller
---
%%{
  init: {
    'theme': 'light'
    }
  }
}%%
erDiagram
    Location {
        int LocationID
        string LocationName
        string Description
    }
    Light {
        int LightID
        int LocationID
        string LightName
        bool Status
        int Brightness
        string Color
    }
    ActionType {
        int ActionTypeID
        string ActionTypeName
    }
    AuditLog {
        int LogID
        int LightID
        int UserID
        int ActionTypeID
        string OldValue
        string NewValue
        DateTime Timestamp
    }

    Location ||--o{ Light : "Contains"
    AuditLog ||--o| Light : "Logs"
    AuditLog ||--o| ActionType : "Contains"
````

## Blinds Controller

````mermaid
---
title: Blinds Controller
---
erDiagram
    Location {
        int LocationID
        string LocationName
        string Description
    }
    Blind {
        int BlindID
        int LocationID
        string BlindName
        bool Status
        int Position
    }
    BlindActionType {
        int ActionTypeID
        string ActionTypeName
        string Description
    }
    BlindAuditLog {
        int LogID
        int BlindID
        int UserID
        int ActionTypeID
        string OldValue
        string NewValue
        DateTime Timestamp
    }

    Location ||--o{ Blind : "Contains"
    Blind ||--o{ BlindAuditLog : "Logs"
    BlindAuditLog ||--o| BlindActionType : "ActionType"

````

---

Last Modified: `=dateformat(this.file.mtime, "DDDD, HH:mm")`

#### Tags:

`=this.file.tags`

````dataview
List FROM #kubernetes
````
