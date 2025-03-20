---
title: "Level 1: The Basics"
weight: 2
prev: /docs/getting-started
---

Welcome to the first level of your DSC journey! You should have successfully installed DSC on your machine. It's now time to understand the basics. This level will guide you through the following basic concepts:

- The command line interface (CLI) and DSC
- Executing your first DSC resource
- Ask for help
- Lab exercise

After you've finished this level, you should have a basic understanding of the CLI and how you're able to execute a DSC resource.

## The Command Line Interface (CLI) and DSC

The DSC command line interface (CLI) is a tool that allows you to interact with a DSC resource and apply configurations to your system. To explain it in simple words: if there is a DSC resource available for the "thing" that you want to configure, whether it is a SQL Server you want to build from the ground up, or install a Visual Studio Code extension, the DSC CLI allows you to execute certain capabilities to perform such task for you. The DSC project is open source and can be installed cross-platform, as you've learned in earlier section.

The DSC CLI currently has the following commands available:

- `dsc completer`
- `dsc config`
- `dsc resource`
- `dsc schema`
- `dsc help`

Each command always has a set of default options that can be added before calling the actual command. The following table represent these three options:

| Option              | Short | Description                                |
| ------------------- | ----- | ------------------------------------------ |
| `--trace-level`     | `-l`  | Sets the level of tracing                  |
| `--trace-format`    | `-t`  | Specifies the format for trace output      |
| `--progress-format` | `-p`  | Defines the format for displaying progress |

To display the possible values for each option, you can simply type in: `dsc` in your command prompt or terminal.

{{< figure
      src="/images/options-possible-values.png"
      alt="Options possible values"
      caption="Figure 1: The commands and options available"
>}}

If you're unsure about a command, you can always ask the CLI for help. This applies to each subcommand. For example, to get help for the config command, type: `dsc config --help`.

```plaintext
Apply a configuration document

Usage: dsc.exe config [OPTIONS] <COMMAND>

Commands:
  get     Retrieve the current configuration
  set     Set the current configuration
  test    Test the current configuration
  export  Export the current configuration
  help    Print this message or the help of the given subcommand(s)

...
```

You now know how you can ask the CLI for help, but what about DSC resources in general? Before looking at a DSC resource and run it, it will be helpful to have a completion script registered. A completion script allows you to easily perform so called tab-completions when using the CLI from a command-line shell, like PowerShell. The following steps demonstrates how you can add a completion script in a PowerShell profile:

1. Open a PowerShell session.
2. Execute `dsc completer powershell | clip` to add the completion script to the clipboard.
3. Run `Invoke-Item $Profile` to open the PowerShell profile.
4. Paste the clipboard content into the PowerShell profile file and save it.

{{< callout icon="star" >}}
  Try out this exercise yourself by adding the completion script to your profile.
{{< /callout >}}

Depending on the version of PowerShell you are using, once you have added the completion script, you can start typing `dsc` and press the Tab key to cycle through the available commands.

## Executing Your First DSC Resource

To run your first DSC resource, you need to understand what a DSC resource is. A DSC resource allows you to define the desired state of a specific part of your system and ensures it remains in that state. The logic behind a DSC resource can be written in any programming language, such as a bash script, a .NET application, or a CLI written in Rust. As long as the tool you create follows certain schema semantics, it can be used as a DSC resource.

Now you know what a DSC resource is, you need to know how to find them. To do so, you can use the `dsc resource list` command. What the command will do in the background is the following:

1. It searches the `resourcePath` setting for additional directories.
2. It appends the known `PATH` environment variable to the `resourcePath`.
3. It detects if the `dsc.exe` is already in the `PATH`.
4. It searches for the following suffix files:
    - `.dsc.resource.json`
    - `.dsc.resource.yml`
    - `.dsc.resource.yaml`

If files with these suffixes are found in the mentioned directories, DSC will load them, search for the required fields, and display the results in the console. If the required fields are missing, DSC will provide a warning indicating which properties are absent. The following output shows what DSC has found. These are the builtin resources.

```plaintext
Type                                        Kind      Version  Capabilities  RequireAdapter  Description                                                                
------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Microsoft.DSC.Debug/Echo                    Resource  1.0.0    gs--t---                                                                                                 
Microsoft.DSC.Transitional/RunCommandOnSet  Resource  0.1.0    gs------                      Takes a single-command line to execute on DSC set operation                
Microsoft.DSC/Assertion                     Group     0.1.0    gs--t---                      `test` will be invoked for all resources in the supplied configuration.    
Microsoft.DSC/Group                         Group     0.1.0    gs--t---                      All resources in the supplied configuration is treated as a group.         
Microsoft.DSC/Include                       Importer  0.1.0    gs--t---                      Allows including a configuration file with optional parameter file.        
Microsoft.DSC/PowerShell                    Adapter   0.1.0    gs--t-e-                      Resource adapter to classic DSC Powershell resources.                      
Microsoft.Windows/RebootPending             Resource  0.1.0    g-------                      Returns info about pending reboot.                                         
Microsoft.Windows/Registry                  Resource  0.1.0    gs-w-d--                      Manage Windows Registry keys and values                                    
Microsoft.Windows/WMI                       Adapter   0.1.0    g-------                      Resource adapter to WMI resources.                                         
Microsoft.Windows/WindowsPowerShell         Adapter   0.1.0    gs--t---                      Resource adapter to classic DSC Powershell resources in Windows PowerShell.
Microsoft/OSInfo                            Resource  0.1.0    g-----e-                      Returns information about the operating system.
```

{{< callout type="info" >}}
  For now, don't worry about the suffix. You'll learn more about it in the next level.
{{< /callout >}}

It can be an excruciating list at first sight. That's why it can be filtered down to focus on the `Resource` kind for now. The following command demonstrates how to find all DSC resources of type `resource`.

```powershell
dsc resource list |
ConvertFrom-Json | 
Where-Object -Property Kind -EQ 'resource' | 
Select-Object -Property type, capabilities
```

```plaintext
type                                       capabilities
----                                       ------------
Microsoft.DSC.Debug/Echo                   {get, set, test}
Microsoft.DSC.Transitional/RunCommandOnSet {get, set}
Microsoft.Windows/RebootPending            {get}
Microsoft.Windows/Registry                 {get, set, whatIf, delete}
Microsoft/OSInfo                           {get, export}
```

You now have a filtered list of DSC resources that are builtin when you've installed DSC.

> [!IMPORTANT]
> The output shown is from a Windows machine. If you are using a different operating system, the output may be different.

In the output table when `dsc resource list` is run, flags such as `gs--t---` are displayed. When the output is piped (|) and converted with `ConvertFrom-Json`, the actual capability full names are shown. This demonstrates what the resource has implemented and what capabilities you can call using `dsc resource <capability>`.

You now have the first piece how you can call the DSC resource. Let's pick the `Microsoft/OSInfo` resource. If you want to use the `get` capability, the command would start with `dsc resource get --resource Microsoft/OSInfo`. There is only one piece missing to execute the DSC resource: what input _might_ be required to interact with it?

DSC resources follow a certain schema. You should see a schema somewhat as a contract when you purchase a house. In the contract it clearly states what will be _in_ the house and what you will get _out_ of it when you purchase it. To see such contract, or schema, you can use the `dsc resource schema` command. When you want to display it for the `Microsoft/OSInfo` resource, you can run: `dsc resource schema --resource Microsoft/OSInfo`.

```sh
dsc resource schema --resource Microsoft/OSInfo
```

```yaml
$schema: http://json-schema.org/draft-07/schema#
$id: https://raw.githubusercontent.com/PowerShell/DSC/main/schemas/resources/Microsoft/OSInfo/v0.1.0/schema.json
title: OsInfo
description: |
  Returns information about the operating system.

  https://learn.microsoft.com/powershell/dsc/reference/microsoft/osinfo/resource
markdownDescription: |
  The `Microsoft/OSInfo` resource enables you to assert whether a machine meets criteria related to
  the operating system. The resource is only capable of assertions. It doesn't implement the set
  operation and can't configure the operating system.

  [Online documentation][01]

  [01]: https://learn.microsoft.com/powershell/dsc/reference/microsoft/osinfo/resource
type: object
required: []
additionalProperties: false
properties:
  $id:
    type: string
    readOnly: true
    title: Data Type ID
    description: |
      Returns the unique ID for the OSInfo instance data type.

      https://learn.microsoft.com/powershell/dsc/reference/microsoft/osinfo/resource#id
    markdownDescription: |
      Returns the unique ID for the OSInfo instance data type.

      [Online documentation][01]

      [01]: https://learn.microsoft.com/powershell/dsc/reference/microsoft/osinfo/resource#id

# Limited
```

To make things easy to start from, you notice the `Microsoft/OSInfo` resource doesn't require any input properties to be passed along if you call the CLI. In this case, it would mean you can execute it directly by running: `dsc resource get --resource Microsoft/OSInfo` seen from the limited YAML snippet from above.

```sh
dsc resource get --resource Microsoft/OSInfo
```

```yaml
actualState:
  $id: https://developer.microsoft.com/json-schemas/dsc/os_info/20230303/Microsoft.Dsc.OS_Info.schema.json
  family: Windows
  version: <version>
  edition: Windows 11
  bitness: '<bitness>'
  architecture: x86_64
```

{{< callout icon="star" >}}
  It's now up to you! Try to run the above command. It's safe to run.
{{< /callout >}}

## Ask for Help

You should have successfully run the command through the CLI. If you need help further with DSC, there are several resources available.

The official documentation is published on Microsoft Learn, the platform from Microsoft for all learning materials. You can find it using the following link: <https://learn.microsoft.com/en-us/powershell/>. In the tiles you can search and find _PowerShell DSC_.

To stay up to date with the latest releases, you can check out the official repository at: <https://github.com/PowerShell/DSC/releases>. There is also a YouTube channel to follow, which is the official DSC community channel found at: <https://www.youtube.com/@dsccommunity2958>. The DSC community itself has:

- Twitter at @dsccommunity
- Discord in the following link: <https://discordapp.com/invite/AtzXnJM>

## Lab Exercise

You have now successfully learned about the basics and executed your first DSC resource. It's time to reinforce your understanding by completing the following lab exercise:

1. How can you trace _all_ information when `dsc resource get --resource Microsoft/OSInfo` executes?
2. What are the amount of possible values for the tracing?
3. The required input for the `Microsoft.DSC.Debug/Echo` resource is?
4. How can you check if there is a pending reboot using DSC?
5. To add a resource for discovery, you would create the following file(s): `<fill in the blank>`
