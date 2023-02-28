---
layout: post
title: "Monorepo with NX"
description: "A summary about monorepo and simple step to use Nx for Express"
date: 2023-02-28 15:28:18
comments: true
keywords: "monorepo, gitlab, github, nx"
category: [Tech]
tags:
- nx
---

This post is to summary <a href="https://f.hubspotusercontent20.net/hubfs/2757427/effective-react-with-nx-2022.pdf" target="_top"> a book about monorepo with Nx </a> that I recently finished. But I took an example for only setting up monorepo for backend, especially using express with a guide from <a href="https://nx.dev/" target="_top"> the official Nx website </a>

### Monorepo
As quoted from the book, `A monorepo is a single repository containing multiple distinct projects, where we don't just have code collocation, but well defined relationships among these projects.` The keyword is `defined relationship`. If we have some services that somehow are related to each other, monorepo is very useful to use.
