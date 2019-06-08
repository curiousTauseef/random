---
title: "git prune"
description: "git commands"
date: "2019-06-08T17:32:57.143Z"
categories:
  - git
  - misnomers
published: true
canonical_link: https://jsj14.withknown.com/2019/git-prune
---
Read slowly

This runs git fsck --unreachable using all the refs available in refs/, optionally with additional set of objects specified on the command line, and prunes all unpacked objects unreachable from any of these head objects from the object database.