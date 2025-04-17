---
title: "Level 1: The Basics"
weight: 2
prev: /docs/getting-started
---

{{< hextra/hero-badge >}}
  <!-- markdownlint-disable-next-line MD033 -->
  <div class="hx-w-2 hx-h-2 hx-rounded-full hx-bg-primary-400"></div>
   <!-- markdownlint-disable-next-line MD033 -->
    <span>Author: Gijs Reijn</span>  
{{< /hextra/hero-badge >}}

Welcome to your first step in the temple of your DSC journey! You should have successfully installed DSC on your machine. It's now time to understand the basics. This level will guide you through the following basic concepts:

- DSC and the command line interface (CLI)
- Executing your first DSC resource
- Ask for help
- Lab exercise

After you've finished this level, you should have a basic understanding of the CLI and how you're able to execute a DSC resource.

## DSC and the Command Line Interface (CLI)

The DSC command line interface (CLI) is a tool that allows you to interact with a DSC resource and apply configurations to your system. To explain it in simple words: if there is a DSC resource available for the "thing" that you want to configure, whether it is a SQL Server you want to build from the ground up, or install a Visual Studio Code extension, the DSC CLI allows you to execute certain capabilities to perform such task for you.

As you've learned whilst installing DSC, the CLI can be installed on multiple operating systems (OS). The DSC project is also open-source and you can visit it on [GitHub](https://github.com/PowerShell/DSC/tree/main).

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

If you're unsure about a command, you can always ask the CLI for help. This applies to each subcommand. For example, to get help for the `config` command, type: `dsc config --help`.

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

You now know how you can ask the CLI for help, but what about DSC resources in general? Before looking at a DSC resource and running it, it will be helpful to have a completion script registered. A completion script allows you to easily perform so-called tab-completions when using the CLI from a command-line shell, like PowerShell or the Bash terminal. The following steps demonstrate how you can add a completion script in a PowerShell profile:

1. Open a PowerShell session.
2. Execute `dsc completer powershell | clip` to add the completion script to the clipboard.
3. Run `Invoke-Item $Profile` to open the PowerShell profile.
4. Paste the clipboard content into the PowerShell profile file and save it.

{{< callout icon="star" >}}
  Try out this exercise yourself by adding the completion script to your profile.
{{< /callout >}}

Depending on the version of PowerShell you are using, once you have added the completion script, you can start typing `dsc` and press the Tab key to cycle through the available commands.

## Executing Your First DSC Resource

To run your first DSC resource, it's important to understand what a DSC resource actually is. At its core, a DSC resource is a package of code that lets you define the desired state for a specific part of your system — whether that's a Windows Server, an iMac, or Linux machine — and ensures it stays in that state when enforced by orchestration tools. What makes DSC resources flexible is that they can be written in virtually any programming language. You could use bash scripts, create a .NET executable, or even CLIs written in Rust. The only requirement is that your code adheres to the schema semantics of `dsc`, allowing it to participate and be utilized as a _DSC resource_.

Now you know what a DSC resource is, you need to know how to find them. To do so, you can use the `dsc resource list` command. What the command will do in the background is the following:

1. It searches the `resourcePath` setting for directories containing files with a certain suffix (see step 4).
2. It appends the known `PATH` environment variable to the `resourcePath`.
3. It detects if the `dsc.exe` is already in the `PATH`.
4. It searches for the following suffix files:
    - `.dsc.resource.json`
    - `.dsc.resource.yml`
    - `.dsc.resource.yaml`

If files with these suffixes are found in the mentioned directories, DSC will load them, search for the required elements, and display the results in the console. If the required elements are missing, DSC will provide a warning indicating which properties are absent. The following output shows what DSC has found. These are the builtin resources when you've installed DSC on a Windows OS.

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

> [!NOTE]
> For now, don't worry about the suffix. You'll learn more about it in the next level.

To filter down the list, you can focus on the `resource` kind for now. The following command demonstrates how to find all DSC resources of type `resource`.

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

You now have a filtered list of _command-based_ DSC resources that are builtin.

> [!IMPORTANT]
> The output shown is from a Windows machine. If you are using a different operating system, the output may be different.

In the output table when `dsc resource list` is run, flags such as `gs--t---` are displayed. When the output is piped (|) and converted with `ConvertFrom-Json`, the actual capability full names are shown. This demonstrates what the resource has implemented and what capabilities you can call using `dsc resource <capability>`.

You got the first piece how you can call the DSC resource. Let's pick the `Microsoft/OSInfo` resource to experiment with. If you want to use the `get` capability, the command would start with `dsc resource get --resource Microsoft/OSInfo`. There is only one piece missing to execute the DSC resource: what input _might_ be required to interact with it?

DSC resources follow a certain schema. You should see a schema somewhat as a contract when you purchase a house. In the contract it clearly states what will be _in_ the house and what you will get _out_ of it when you purchase it. To see such contract, or schema, you can use the `dsc resource schema` command. When you want to display it for the `Microsoft/OSInfo` resource, you can run:

```sh
dsc resource schema --resource Microsoft/OSInfo
```

```yaml
# Output
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

To make things easy to start from, you notice the `Microsoft/OSInfo` resource doesn't require any input properties seen from the `required: []` element to be passed along if you call the CLI. In this case, it would mean you can execute it directly by running:

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

You have successfully ran your first DSC resource. Before making things to complex from the start, let's look how you can ask for help.

## Ask for Help

You've already learned the `--help` option for each subcommand. Make this a habit if you aren't certain about a command. Each time you append the `--help` option, it will reveal the help information.

The official documentation is published on Microsoft Learn, the platform from Microsoft for all learning materials. You can find it using the following link: <https://learn.microsoft.com/en-us/powershell/>. In the tiles you can search and find _Microsoft DSC_.

To stay up to date with the latest releases, you can check out the official repository at: <https://github.com/PowerShell/DSC/releases>. Always keep your CLI version up to date to get the latest functionality and bug fixes!

## Lab Questions

You have now successfully learned about the basics and executed your first DSC resource. It's time to reinforce your understanding by completing the following lab questions:

1. How can you trace _all_ information when `dsc resource get --resource Microsoft/OSInfo` executes?
2. What are the amount of possible values for the tracing?
3. The required input for the `Microsoft.DSC.Debug/Echo` resource is?
4. How can you check if there is a pending reboot using DSC?
5. To add a resource for discovery, you would create the following file(s): `<fill in the blank>`
