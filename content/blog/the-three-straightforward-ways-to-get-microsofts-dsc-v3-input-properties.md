---
authors:
  - name: Gijs Reijn
    link: https://github.com/gijsreyn
    image: https://avatars.githubusercontent.com/gijsreyn
date: 2025-04-15
linktitle: The Three Straightforward Ways To Get Microsoft's DSC v3 Input Properties
title: The Three Straightforward Ways To Get Microsoft's DSC v3 Input Properties
---

Microsoft DSC v3 introduced command-based and non-command-based DSC resources (adapter).

This fundamental change has changed the way you can retrieve the input properties. Typically, you would use the Get-DscResource command to discover the available properties on script-based and class-based DSC resources.

Because Microsoft DSC v3 is a command-line interface (CLI) application that uses JSON and YAML, retrieving the input properties doesn’t work using the command mentioned earlier.

This blog post was posted on [Medium](https://medium.com/@gijsreijn/the-three-straightforward-ways-to-get-microsofts-dsc-v3-input-properties-771a5e3b02e8).
