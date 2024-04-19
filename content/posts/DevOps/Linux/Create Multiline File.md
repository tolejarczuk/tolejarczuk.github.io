---
created: 2023-12-06 09:56
modified: Wednesday, 6th December 2023, 09:56:05
share: true
tags:
- devops
- linux
- public
title: Create Multiline File
date: 19-04-2024
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

---

Last Modified: `=dateformat(this.file.mtime, "DDDD, HH:mm")`

#### Tags:

`=this.file.tags`

````dataview
List FROM #linux
````
