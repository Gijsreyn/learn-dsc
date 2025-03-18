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

The DSC command line interface (CLI) is a tool that allows you to interact with a DSC resource and apply configurations to your system. To explain it in simple words: if there is a DSC resource available for the "thing" that you want to configure, whether it is a SQL Server you want to build from the ground up, or install a Visual Studio Code extension, DSC's CLI allows you to execute certain capabilities to perform the task for you. DSC's project is open source and can be installed cross-platform, as you've learned in the getting started article.

The current available commands are:

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
      caption="Figure 1: The possible values for the options"
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

Before moving on to execute our first DSC resource, it's helpful to have a completion script registered. Having a completion script in your profile allows you to easily perform tab-completions when using the CLI from a command-line shell, like PowerShell. The following example illustrates how you can add a completion script in a PowerShell profile:

1. Open a PowerShell session.
2. Execute `dsc completer powershell | clip` to add the completion script to the clipboard.
3. Run `Invoke-Item $Profile` to open the PowerShell profile.
4. Paste the clipboard content into the PowerShell profile file and save it.

Depending on the PowerShell version that you've added the completion script, whenever you start typing in `dsc`, you can press tab to display the available commands to go through.

## Executing Your First DSC Resource

To execute your first DSC resource, you first need to know what resources are currently available. To do so, you can use the `dsc resource list` command. What this command will do in the background, is the following:

1. It searches the `resourcePath` setting for additional directories.
2. It appends the known `PATH` environment variable to the `resourcePath`.
3. It detects if the `dsc.exe` is already in the `PATH`.
4. It searches for the following suffix files:
    - `.dsc.resource.json`
    - `.dsc.resource.yml`
    - `.dsc.resource.yaml`

If DSC finds files with these suffixes, it loads it in, searches if the required fields are present, and output the results. If the required fields aren't present, it will warn you what is missing. The following output demonstrates what DSC has found.

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
> The output above comes from a Windows machine. If you're running a different OS, the output can be different.

In the output table when `dsc resource list` is run, flags such as `gs--t---` are displayed. When the output is piped (|) and converted with `ConvertFrom-Json`, the actual capability full names are shown. This demonstrates what the resource has implemented and what capabilities you can call using `dsc resource <capability>`.

You know have the first piece how you can call the DSC resource. Let's pick the `Microsoft/OSInfo` resource. If you want to use the `get` capability, the command would start with `dsc resource get --resource Microsoft/OSInfo`. There is only one piece missing to execute the DSC resource: what input _might_ be required to interact with it?

DSC resources follow a certain schema. Or to make it a more clearer, a contract. The contract states all kind of things like the description and title. But it also specifies the _required_ input and what it outputs. To get this contract (or schema), you can use the `dsc resource schema` command. In the above example, it would be `dsc resource schema --resource Microsoft/OSInfo`

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

The `Microsoft/OSInfo` resource does not require any input parameters. Therefore, you can execute it directly using the command `dsc resource get --resource Microsoft/OSInfo` seen from above limited YAML snippet.

```yaml
actualState:
  $id: https://developer.microsoft.com/json-schemas/dsc/os_info/20230303/Microsoft.Dsc.OS_Info.schema.json
  family: Windows
  version: <version>
  edition: Windows 11
  bitness: '64'
  architecture: x86_64
```

To interact with a DSC resource, you need to provide the necessary parameters and values that the resource expects. For example, if you are using the `File` resource, you need to specify parameters like `DestinationPath` and `Contents`. Each resource has its own set of required and optional parameters, which you can find in the resource documentation or by using the `dsc resource <resource_name> --help` command.

{{< callout emoji="❗" >}}
  It's now up to you! Try to run the above command. It's safe to run.
{{< /callout >}}

## Ask for Help

You should have successfully run the command through the CLI. If you need help further with DSC, there are several resources available.

The official documentation is published on Microsoft Learn, the platform from Microsoft for all learning materials. You can find it using the following link: <https://learn.microsoft.com/en-us/powershell/>. You can search in the tiles and find _PowerShell DSC_.

To stay up to date with the latest releases, you can check out the official repository at: <https://github.com/PowerShell/DSC/releases>. There is also a YouTube channel to follow, which is the official DSC community channel found at: <https://www.youtube.com/@dsccommunity2958>. The DSC community also have a:

- Twitter at @dsccommunity
- Discord using the following link: <https://discordapp.com/invite/AtzXnJM>

## Lab Exercise

To reinforce your understanding of the basic concepts, complete the following lab exercise:

1. Initialize a new DSC configuration.
2. Define a configuration that creates a directory and a file within that directory.
3. Apply the configuration to your system.
4. Verify that the directory and file were created successfully.

By completing this exercise, you will gain hands-on experience with the DSC CLI and executing DSC resources.
