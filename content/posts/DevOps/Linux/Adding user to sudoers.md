---
created: 2024-01-09 11:49
share: true
dg-publish: false
dg-home: false
author: Tomasz Olejarczuk
title: Adding user to sudoers
date: 09-01-2024
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
tags:
- terminal
- linux
- devops
- public
---

# Adding a User to Sudoers in /etc/sudoers.d/

This guide provides a one-liner to add a user to the sudoers file in a secure and organized way. Instead of modifying the main `/etc/sudoers` file directly, we'll create a new file in the `/etc/sudoers.d/` directory.

## One-Liner Command

````bash
echo "$USER ALL=(ALL:ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/$USER
````

This command creates a new file with the same name as the current user in the `/etc/sudoers.d/` directory, specifying the sudo configuration for that user.

## Explanation

* `echo "$USER ALL=(ALL:ALL) ALL"`: Generates the sudo configuration line for the current user.
* `| sudo tee /etc/sudoers.d/$USER`: Writes the generated line to a new file in the `/etc/sudoers.d/` directory. The filename is based on the current user's name.

### Important Notes:

* This approach is safer than directly editing the main `/etc/sudoers` file.
* Always replace `$USER` with the actual username if you are using the command manually.

## Verification

You can verify that the configuration is correct by checking the content of the created file:

````bash
cat /etc/sudoers.d/$USER
````

Remember to replace `$USER` with the actual username.

## Removal

If you need to remove sudo privileges for a user added using this method, simply delete the corresponding file in `/etc/sudoers.d/`:

````bash
sudo rm /etc/sudoers.d/$USER
````

Again, replace `$USER` with the actual username.

---

Last Modified: `=dateformat(this.file.mtime, "DDDD, HH:mm")`

#### Tags:

`=this.file.tags`

````dataview
List FROM #terminal 
````
