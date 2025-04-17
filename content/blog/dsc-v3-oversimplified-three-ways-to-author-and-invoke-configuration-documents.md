---
authors:
  - name: Gijs Reijn
    link: https://github.com/gijsreyn
    image: https://avatars.githubusercontent.com/gijsreyn
date: 2025-04-04
linktitle: 'DSC v3 Oversimplified: Three Ways to Author and Invoke Configuration Documents'
title: 'DSC v3 Oversimplified: Three Ways to Author and Invoke Configuration Documents'
---

DSC's v3 engine isn't the only way to invoke a configuration document. Nor does the authoring have to happen purely in DSC v3 format.

Configuration documents for DSC v3 are written in YAML and JSON. If you come from the old days, that can surprise you. Configuration documents were originally solely written in PowerShell.

DSC v3, or now changed to Microsoft DSC 3.0, does it differently. And for several reasons. One of those is the fact that DSC in itself is an executable that can be executed from orchestration tools. Think of Azure Machine Configuration, which, quite frankly, was also available for DSC v2.

This blog post was posted on [Medium](https://medium.com/@gijsreijn/dsc-v3-oversimplified-three-ways-to-author-and-invoke-configuration-documents-639a138e3e5d).
