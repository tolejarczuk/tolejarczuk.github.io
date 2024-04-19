---
created: 2024-04-17 12:30
share: true
dg-publish: false
dg-home: false
author: Tomasz Olejarczuk
title: Remove duplicate entries
date: 17-04-2024
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
- database
- sql
- public
---

# Remove Duplicate Rows in PostgreSQL

The provided code snippet offers a way to remove duplicate rows in a PostgreSQL table (named 'enc_history_entries') based on the 'encrypted_data' column. Let's break down the steps:

````sql
insert into enc_history_entries_temp(encrypted_data, nonce, device_id, user_id, "date", encrypted_id, read_count)

select distinct on (encrypted_data) encrypted_data, nonce, device_id, user_id, "date", encrypted_id, read_count from enc_history_entries;

drop table enc_history_entries;

alter table enc_history_entries_temp rename to enc_history_entries;
````

1. **Temporary Table:**
   * `insert into enc_history_entries_temp...`: Creates a temporary table and inserts distinct rows from the original table. `distinct on (encrypted_data)` ensures only the first occurrence of each unique 'encrypted_data' value is included.
1. `select distinct` allows you to see the records that do not have any duplicates
1. **Replace Original:**
   * `drop table enc_history_entries;`: Deletes the original table.
   * `alter table enc_history_entries_temp rename to enc_history_entries;`: Renames the temporary table to replace the original.

**Key PostgreSQL Techniques:**

* **DISTINCT ON:** Used to select the first row within a group of duplicates as defined by the specified column(s).
* **Temporary Tables:** Useful for intermediate storage during complex data transformations.
* **DROP TABLE / ALTER TABLE RENAME TO:** Manage table deletion and renaming for restructuring.

**Additional Considerations**

* **Identifying Duplicates:** If you need to find the duplicates *before* removal, use a query like this:

````sql
   SELECT encrypted_data, COUNT(*) 
   FROM enc_history_entries
   GROUP BY encrypted_data
   HAVING COUNT(*) > 1;
````

---

Last Modified: `=dateformat(this.file.mtime, "DDDD, HH:mm")`

#### Tags:

`=this.file.tags`

````dataview
List FROM #database 
````
