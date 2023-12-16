---
layout: post
title: data engineering overview
---

Row based vs Column based

Rows on disk stored in ---- line by line,
new rows are written efficiently
but for select statement that target just a subset of columns (or fields), reading is slow because you have to scan all then filter.
compression is inefficient because data type are scattered all around the files.
OLTP, avro

Column on disk stored in | | | by column
new row are written slowly, because one record need to update different parts of file.
but for select statement that target just a subset of columns (or fields), read are faster b/c avoid scan the entire file
OLAP, Parquet, ORC











