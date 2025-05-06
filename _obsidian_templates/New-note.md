---
title: Proj_Name
excerpt: 
date: <% tp.date.now() %>
categories:
  - <% tp.file.folder(true).split("/").slice(-1)[0] %>
tags:
  - Blog
---

<% tp.file.rename(tp.date.now()) %>
