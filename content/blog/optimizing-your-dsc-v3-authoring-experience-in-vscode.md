---
authors:
  - name: Gijs Reijn
    link: https://github.com/gijsreyn
    image: https://avatars.githubusercontent.com/gijsreyn
date: 2024-06-01
linktitle: Optimizing Your DSC V3 Authoring Experience in VSCode
title: Optimizing Your DSC V3 Authoring Experience in VSCode
---

The world around DSC has changed significantly; only some of the components to talk to the DSC engine are written in PowerShell. Instead, the authoring of configurations is written in YAML or JSON.

Previous versions of DSC were closed and you couldn’t have a look under the hood of the engine. Now, DSC V3 is totally rewritten and open-sourced on GitHub. The engine itself doesn’t require a Local Configuration Manager (LCM) anymore.

Instead, you become responsible for the orchestration of configurations to machines. Also, the team working behind DSC V3 has promised that any language can be used to write DSC resources.

To prevent a bit of misunderstanding, let’s clarify: configuration documents outline the desired state of a machine, and the resource executes the necessary tasks with some required information in DSC V3.

This blog post was posted on [Medium](https://medium.com/@gijsreijn/optimizing-your-dsc-v3-authoring-experience-in-vscode-bd8e90c52312).
