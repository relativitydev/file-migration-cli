# Relativity File Migration CLI

The Relativity File Migration CLI is an application that supports **_TBD_** Add introduction.

This page contains the following information:


* [Before you begin](#before-you-begin)
* [Setting up the File Migration CLI](#setting-up-the-file-migration-cli)
* [Data flow overview](#data-flow-overview)
* [Common workflows using PowerShell commands](#common-workflows-using-powershell-commands)
* [Command-line reference](#command-line-reference)
* [Server paths, commands, and other reference information](#server-paths-commands-and-other-reference-information)

## Before you begin

* Make sure your environment meets these system requirements:

   * **_TBD_** release for RelativityOne or Relativity
   * .NET 4.6.2
   * Visual C++ 2010 x86 Runtime
   * Intel 2Ghz (2-4 cores are recommended)
   * 16GB RAM (32GB of RAM are recommended)

* Make sure that you have the following permissions:

  * **_TBD_** Add required permissions
  * **_TBD_** Add required permissions

## Setting up the File Migration CLI

**_TBD_** Add steps for extracting to top level folder and other information


## Data flow overview

**_TBD_** Add overview of Data flow and diagram

## Common workflows using PowerShell commands

**_TBD_** Add PowerShell command instructions

## Command-line reference

**_TBD_** Add introduction

##  Command-line syntax

Review the following table to familiarize yourself with the command-line syntax used in this documentation. Each section includes a generic representation of a command with required and optional parameters, as well as an example using actual values. The following table outlines the notation used in the generic commands:

Notation|Description
------------|---------------
Text without brackets or braces|Indicates you must type the text as shown.
\<Text inside angle brackets\>|A placeholder for a value that you provide.
[Text inside square brackets]|Any optional parameters.
{Text inside braces}|A list of required parameters. You must choose one of the items.
Vertical bar (\|)|A separator for mutually exclusive items. You must choose one item.
Ellipsis (…)|Items that can be repeated.  
 <br/>

<details><summary>View additional syntax conventions</summary>

The Transfer CLI uses the same syntax followed by other Microsoft-based CLI tools. This list includes common CLI syntax conventions:

* Use a colon between a command and a parameter values use:
  ```
  /command:transfer
  ```
* Enclose parameter values containing spaces with double quotation marks: 
  ```
  /command:transfer /sourcepaths:"C:\Windows"
  ```

* Use a plus (+) or minus (-) sign to flag parameters as enabled and disabled respectively:
  ```
  /command:transfer  /interactive+ (enable)
  /command:transfer  /interactive- (disable)
  ```
* Use a semicolon to delimited two or more parameters consisting of name-value pairs:
  ```
  /configuration:"target-data-rate-mbps=100;file-not-found-errors-disabled=true"
  ```

</details>

## Authentication

**_TBD_** Add introduction.

### Authentication - source instance

When you execute the sync command, you must authenticate to the source, such as an SQL Server. The following options are available for authenticating to a source:

* **Username and password for  SQL Server** - You can enter your username and password for the SQL Server by setting the /sqluser and /sqlpwd parameters respectively.

     Username and password login has the following general command format:

     ```
     Relativity.Migration.Console.exe /command:<YourCommand> [ParametersForCommand] /sqlinstance:<"Value"> /sqlpwd:<"Value"> /sqluser:<"Value"> /url:<"Value"> /login+ /enforcessl- /sqlintegrated-
     ```

    The following example illustrates how to use this login type in a command:

    ``` 
   /command:sync /sqlinstance:"SomeInstance.mycompany.corp" /sqlpwd:"P@ssw0rd@1" /sqluser:"sa" /url:"https://hostname.mycompany.corp" /login+ /enforcessl- /targetpath:"\\files\T005\FTA\ScottP\DataMigration" /sqlintegrated- /workspaces:"1027428"
    ``` 

* **Integrated login for SQL Server** - You can use integrated login to authenticate to the SQL Server by setting the /sqlintegrated parameter to True. In this case, you don't need to provide a SQL Server username and password.

     Integrated login for SQL Server has the following general command format:

     ```
     Relativity.Migration.Console.exe /command:<YourCommand> [ParametersForCommand] /sqlinstance:<"Value"> /url:<"Value"> /login+ /enforcessl- /sqlintegrated:+ 
     ```

    The following example illustrates how to use this login type in a command:

    ```
    /command:sync /sqlinstance:"SomeInstance.mycompany.corp" /url:"https://hostname.mycompany.corp" /login+ /enforcessl- /targetpath:"\\files\T005\FTA\ScottP\DataMigration" /sqlintegrated+ /workspaces:"1027428"
    ```
<details><summary>View parameter descriptions for source login</summary>

The following table describes each of the parameters used to log in to a source instance. For more parameters, see [Commands](#commands) and [Authentication - target instance](#authentication-target-instance).

Parameter|Description
--------------|------------------
/enforcessl{+\|-}|Enables or disables secure sockets layer (SSL) validation when executing a diagnostic check command. <br>**Note:** By default, this parameter is set to True. It ensures that SSL is well-tested across all environments. If the File Migration CLI reports SSL errors, we recommend that you configure your environment with a certificate, which doesn’t include wildcards. We also recommend that you don’t suppress SSL validation.
/sqlinstance:{value}|The instance name for the source or on-premise SQL Server.
/sqlintegrated:{value}|Enables or disables integrated authentication for the SQL Server. If this param is set to False, then you must specify values for the /sqlpwd and /sqluser parameters. <br>**Note:** The default value is False. 
/sqlpwd:{value}|The password for the source or on-premise SQL Server.
/sqluser:{value}|The username for the source or on-premise SQL Server.
/url:{value}|The Relativity base URL for your environment. Don't enter the web service URL used by the Relativity Desktop Client, which has the format https://\<MyServiceName\>/RelativityWebAPI/.

</details>


### Authentication - target instance

You must provide credentials for authentication, when you make certain commands through the File Migration CLI to a Relativity or RelativityOne target environment. 

**Note:** The File Migration CLI doesn’t persist your username or password. This information isn’t stored or cached by the tool, so you must reenter it for each login. 

This list describes the methods available for authenticating to a target through the File Migration CLI: 

* **Username and password login** - You can enter your username and password in the command-line using the /username and /password parameters respectively.

     Username and password login has the following general command format:

     ```
     Relativity.Migration.Console.exe /command:<YourCommand> [ParametersForCommand] /url:<"Value">  /username:"email@company.com" /password:"SomePassword!"
     ```

    The following example illustrates how to use this login type in a command:

    ```
    Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /username:<"Value"> /password:<"Value"> /workspaces:"1171671;1171672;1171673"
    ```
* **Interactive login** - You can use interactive login when a Relativity login is required for the migrate command. For example, use interactive login for a migrating data to an environment, where you haven't been assigned an Okta provider for authentication. The following command displays the Relativity login dialog where you can enter your credentials:

     Interactive login has the following general command format:

     ```
     Relativity.Migration.Console.exe /command:<YourCommand> [ParametersForCommand] /url:<"Value"> /login+     
     ```

    The following example illustrates how to use this login type in a command:

    ```
    Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /login+  /workspaces:"1171671;1171672;1171673"
    ```

* **Interactive login with Okta support** - Depending on the configuration of your Relativity environment, an Okta provider may be assigned to your user profile for authentication. For example, RelativityOne environments commonly require that you sign in with Okta.

     Interactive login with Okta support has the following general command format:

     ```
     Relativity.Transfer.Console.exe /command:<YourCommand> [ParametersForCommand] /url:<"Value"> /login+ /oktaforce+
     ```
     The following command uses the /oktaforce+ parameter for an interactive login with Okta:

     ```
    Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /workspaces:"1171671;1171672;1171673"
     ```
     
<details><summary>View parameter descriptions for target login</summary>

The following table describes each of the parameters used to log in to a target instance. For more parameters, see [Commands](#commands) and [Authentication - source instance](#authentication-source-instance).

Parameter|Description
--------------|------------------
/login{+\|-}|Enables or disables the launching of a login window. <br>**Note:** This parameter is required for interactive login, and for interactive login with Okta support. The default value is False.
/oktaforce{+\|-}|Enables or disables the requirement to use the Okta provider to login. <br>**Note:** This parameter is required only for login with an Okta provider.
/password:{value}|The Relativity login password. <br>**Note:** This parameter is required only for username and password login.
/url:{value}|The Relativity base URL for your environment. Don't enter the web service URL used by the Relativity Desktop Client, which has the format https://\<MyServiceName\>/RelativityWebAPI/.
/username:{value}|The Relativity login name. <br>**Note:** This parameter is required only for username and password login.

</details>


### Synchronizing workspaces

**_TBD_** Add introduction

#### Synchronizing local master and workspace databases

Setting up or updating local master or workspace databases has the following general command format:

``` 
Relativity.Migration.Console.exe /command:sync /sqlinstance:<"Value"> {/sqluser:<"Value"> /sqlpwd:<"Value"> /login+ /oktaforce+ | /login+ /enforcessl- /sqlintegrated:+} /url:<"Value"> /targetpath:<"Value"> /sha1:<+ | -> /metadata:<+ | ->  /skipinv:<+ | ->  /skipnative:<+ | -> 
``` 

The following example will setup the initial local master/workspace databases or perform local database updates for active workspaces.

``` 
Relativity.Migration.Console.exe /command:sync /sqlinstance:"SomeInstance.mycompany.corp" /sqlpwd:"SomePassword!" /sqluser:"SomeUserName" /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /targetpath:"\\files\SomeFolder\Files" /sha1- /metadata- /skipinv- /skipnative-
``` 

#### Synchronizing specific workspaces

Synching a specific group of workspace databases has the following general command format:

``` 
Relativity.Migration.Console.exe /command:sync /sqlinstance:<"Value"> {/sqluser:<"Value"> /sqlpwd:<"Value"> /login+ /oktaforce+ | /login+ /enforcessl- /sqlintegrated:+} /url:<"Value"> /targetpath:<"Value"> /sha1:<+ | -> /metadata:<+ | ->  /skipinv:<+ | ->  /skipnative:<+ | -> /workspaces:<"Value">
```

The following example is the same as above but limits the sync to specific workspaces. 

```
Relativity.Migration.Console.exe /command:sync /sqlinstance:"SomeInstance.mycompany.corp" /sqlpwd:"SomePassword!" /sqluser:"SomeUserName" /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /targetpath:"files\SomeFolder\Files" /sha1- /metadata- /skipinv- /skipnative- /workspaces:"1032984;1624500"
```

<details><summary>View parameter descriptions</summary>

The following table describes each of the parameters used in a migration command. For more parameters, see [Commands](#commands) and [Authentication](#authentication).


Parameter|Description
--------------|------------------
/dupfiles{+\|-}|Enables or disables the removal of duplicate files. During synchronization, this optimization increases the memory and CPU usage, and the time to completion, but it can significantly reduce the overall transfer time. <br>**Note:** The default value is True.
/maxsync:{value}|The maximum number of records to synchronize. Use this parameter to limit the number of records fetched with the sync command while debugging or troubleshooting. <br>**Note:** The default value is Int32.MaxValue.
/metadata:{value}|Enables or disables the retrieval of file metadata, such as the length in bytes, for all source files. Although an expensive operation, we highly recommend enabling this setting because it provides verifiable migration results and eliminates more expensive server-side validation. <br>**Note:** The default value is True.
/sha1{+\|-}|Enables or disables the calculation of SHA1 hashes for all source files. SHA1 hashes are used to validate the migration, so you should only modify this setting in a performance critical scenario. <br>**Note:** The default value is True.
/skipinv{+\|-}|Enables or disables skipping Invariant files when executing the sync or migrate commands. <br>**Note:** The default value is False.
/skipnative{+\|-}|Enables or disables skipping native files when executing the sync or migrate commands. <br>**Note:** The default value is False.
/skiplongpaths{+\|-}|Enables or disables skipping long paths found during search path enumeraion. When this parameter is set to False, the enumeration fails if a path exceeds the maximum length defined by the File Migration CLI. <br>**Note:** The default value is False.
/targetpath:{value}|The target path used for synching databases or workspaces, or for migrating native files. For more information, see [UNC file paths](#unc-file-paths) and [Remote server paths](#remote-server-paths). <br>**Note:** This parameter is required.
/workspaces:{value}|The Artifact IDs for a list of Relativity workspace that you want to filter when running a specific command. This parameter supports a list of semicolon delimited Artifact IDs, such as _1171671;1171672;1171673_, or the start and end of range separated by a dash, such as _1171671-1171673_.

</details>


### Migrating workspaces

**_TBD_** Add introduction

Migrating native files has the following general command format:

```  
Relativity.Migration.Console.exe /command:migrate /url:<"Value"> {/username:<"Value"> /password:<"Value"> | /login+ | /login+ /oktaforce+} /workspaces:<"Value"> 
```

The following example migrates all native files contained within the specified workspaces to the target R1 instance.

``` 
Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /workspaces:"1171671;1171672;1171673"
``` 

<details><summary>View parameter descriptions</summary>

The following table describes each of the parameters used in a migration command. For more parameters, see [Commands](#commands) and [Authentication](#authentication).

Parameter|Description
--------------|--------------
/maxbytesperbatch:{value}|The maximum number of bytes per batch. <br>**Note:** The default value is 100GB.
/maxfilesperbatch:{value}|The maximum number of files per batch. <br>**Note:** The default value is 1M.
/maxhttpattempts:{value}|The maximum number of HTTP retry attempts. <br>**Note:** The default value is 5.
/skipinv{+\|-}|Enables or disables skipping Invariant files when executing the sync or migrate commands. <br>**Note:** The default value is False.
/skipnative{+\|-}|Enables or disables skipping native files when executing the sync or migrate commands. <br>**Note:** The default value is False.
/skiplongpaths{+\|-}|Enables or disables skipping long paths found during search path enumeraion. When this parameter is set to False, the enumeration fails if a path exceeds the maximum length defined by the File Migration CLI. <br>**Note:** The default value is False.
/subjob:{value}|The subjob filter applied to certain migration commands. For example, if you set the value of this parameter to InvariantDbStorage, and executed the migrate command, only workspaces with files in the Invariant DB Storage table are migrated. Supported values include: None, WorkspaceDbFiles, InvariantDbStorage, InvariantDbPages, and InvariantTemporaryNative.
/targetpath:{value}|The target path used for synching databases or workspaces, or for migrating native files. For more information, see [UNC file paths](#unc-file-paths) and [Remote server paths](#remote-server-paths). <br>**Note:** This parameter is required.
/workspaces:{value}|The Artifact IDs for a list of Relativity workspace that you want to filter when running a specific command. This parameter supports a list of semicolon delimited Artifact IDs, such as _1171671;1171672;1171673_, or the start and end of range separated by a dash, such _1171671-1171673_.

</details>

### Generating reports

**_TBD_** Add introduction

The following example generates a basic migration report for the local database:

``` 
Relativity.Migration.Console.exe /command:report
``` 

<details><summary>View parameter descriptions</summary>

The following table describes each of the parameters used in a report command. For more parameters, see [Commands](#commands).

Parameter|Description
--------------|--------------
/jobstatus:{value}|Indicates the job status filter applied to certain migration commands. For example, to run a report on completed jobs, you would set this parameter to Completed when executing the report command. Supported values include: None, InProgress, Completed, and Skipped. 

</details>

### Generating workspace CSV files

**_TBD_** Add introduction

Generating CSV files has the following general command format:

``` 
Relativity.Migration.Console.exe /csv workspaces:<"Value">
``` 

The following example generates CSV files for the specified workspaces. This command is optional to provide operators an ability to "hand tune" CSV files when a workspace migration fails due to unsupported migration rules or logic.

``` 
Relativity.Migration.Console.exe /csv workspaces:"1171671;1171672;1171673"
``` 

<details><summary>View parameter descriptions</summary>

The following table describes each of the parameters used in a migration command. For more parameters, see [Commands](#commands).

Parameter|Description
---------|--------------
/workspaces:{value}|The Artifact IDs for a list of Relativity workspace that you want to filter when running a specific command. This parameter supports a list of semicolon delimited Artifact IDs, such as _1171671;1171672;1171673_, or the start and end of range separated by a dash, such as _1171671-1171673_.

</details>


### TAPI client configuration values

**_TBD_** Add more information

#### Displaying configuration values

**_TBD_** Add more information

This command is useful to view all available TAPI client configuration keys, descriptions, and default values.

``` 
Relativity.Migration.Console.exe /command:configdocs
``` 

#### Setting configuration values

**_TBD_** Add more information

Setting configuration values has the following general command format:

``` 
TBD
``` 

Any of the supported TAPI client configuration settings can be set by assigning name value pairs to the configuration parameter. The following example forces the Aspera client and sets the target data rate to 250 Mbps.

``` 
Relativity.Migration.Console.exe /configuration:"client=Aspera;target-data-rate-mbps=250"
``` 

<details><summary>View parameter descriptions</summary>

The following table describes each of the parameters used in a migration command. For more parameters, see [Commands](#commands) and [Authentication](#authentication).

Parameter|Description
--------------|--------------
/configuration|A semi-colon delimited list of name-value pairs for items, such as max-target data rate, pre-calculate, and so on. 

</details>

## Server paths, commands, and other reference information

This section includes the following additional information about the File Migration CLI:

### Remote server paths

<details><summary>View remote server paths</summary>

The Transfer CLI supports both absolute and relative paths. The Transfer CLI leverages path resolvers used by the underlying TAPI architecture to adapt paths from one format to another. It supports the following path architecture:

 * **On-premises Relativity** - supports UNC paths. For more information, see [Absolute paths used by on-premises Relativity](#absolute-paths-used-by-on-premises-Relativity).
* **RelativityOne** - supports only UNIX-style relative paths, because it is deployed on Aspera. By default, the Transfer CLI can resolve UNC paths to relative paths for backwards compatibility. See [UNIX-style relative paths used by RelativityOne](#unix-style-relative-paths-used-by-RelativityOne).

### Absolute paths used by on-premises Relativity
An absolute UNC path is a fully qualified domain name (FQDN). The following absolute path represents a _Temp_ folder in a workspace with the identifier 1027541.

```
\\files.txxx.ctus010000.relativity.one\Txxx\Files\EDDS1027541\Temp
```

### UNIX-style relative paths used by RelativityOne 
RelativityOne is deployed on Aspera, so it supports only UNIX-style relative paths. The following screen shot illustrates the root of a RelativityOne fileshare, when browsed through File Explorer:

![relativityonepaths](https://user-images.githubusercontent.com/43040844/45386621-66add880-b5d9-11e8-9aee-a1e2c8c485f3.png)

When you browse RelativityOne file shares, you see the following default structure, where the _xxx_ represents a tenant identifier:

```
\\files.txxx.ctus010000.relativity.one\Txxx\ARM
\\files.txxx.ctus010000.relativity.one\Txxx\BCPPath
\\files.txxx.ctus010000.relativity.one\Txxx\Cache
\\files.txxx.ctus010000.relativity.one\Txxx\dtSearch
\\files.txxx.ctus010000.relativity.one\Txxx\EDDS
\\files.txxx.ctus010000.relativity.one\Txxx\Files
\\files.txxx.ctus010000.relativity.one\Txxx\FTA
\\files.txxx.ctus010000.relativity.one\Txxx\ProcessingSource
\\files.txxx.ctus010000.relativity.one\Txxx\Temp
```

For RelativityOne, all Aspera servers are configured at the root of each supported file share. Each file share has an equivalent absolute and relative path as illustrated here: 

* Aspera file share path

  ```
  \\files.txxx.ctus010000.relativity.one\Txxx\
  ```

* Absolute path

  ```
  \\files.txxx.ctus010000.relativity.one\Txxx\Files\EDDS1027541\Temp
  ```

* Relative path – In Aspera, relative paths use the UNIX-style forward slashes:

  ```
  /Files/EDDS1027541/Temp
  ```
</details>

### Commands

<details><summary>View available commands</summary>

The following table lists the commands available in the Transfer CLI:

Commands|Version|Description
--------|----------|---------------
[configdocs](#tapi-client-configuration-values)|TBD|TBD
[csv](#generating-workspace-CSV-files)|TBD|TBD
[migrate](#migrating-workspaces)|TBD|TBD
[report](#generating-reports)|TBD|TBD
[sync](#synchronizing-workspaces)|TBD|TBD

</details>


### Exit codes

<details><summary>View exit codes</summary>

The Transfer CLI supports standard exit codes for command-line tools. The following table list supported exist codes:

Exit code|Description
-----------|--------------
0|Successful
1|Canceled
2|Failed
3|Fatal
100|Unsupported

**Note:** The Unsupported exit code indicates that you attempted to perform an unsupported operation, such as using a remote path search for a client that doesn't provide an implementation.

</details>

### Logging

<details><summary>View logging information</summary>
The File Migration CLI supports the logging functionality available through Relativity. It supports three specialized desktop sinks:

* [Rolling file](https://github.com/serilog/serilog-sinks-rollingfile)
* [SEQ](https://getseq.net/)
* **Relativity HTTP** - this sink periodically sends batches of logs to Kepler API endpoints. This process is equivalent to writing logs from an agent, custom page, or event handler. In RelativityOne, this sink sends the logs to the the LogEntries tenant log, and eventually, to [Splunk](https://www.splunk.com/).

### Configuring logging
The File Migration CLI uses standard Relativity logging configuration. **_A LogConfig.xml is located in the zip file with the File Migration CLI executable, and it is used to configure the sinks._** The following example illustrates a LogConfig.xml file:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<kCuraLogging masterLoggingConfiguration="EDDS">
  <rules enabled="true">
    <rule system="*" loggingLevel="Information" sink="File1;Seq1;Http1"/>
  </rules>
  <sinks>
    <fileSink name="File1" logFileLocation="%temp%\Relativity-Transfer\Transfer-Cli\" maxFileSizeInMB ="10000" />
    <seqSink name="Seq1" serverUrl="http://localhost:5341" batchSizeLimit="50" waitPeriodSeconds="15" />
    <relativityHttpSink name="Http1" batchSizeLimit="50" waitPeriodSeconds="15" />
  </sinks>
</kCuraLogging>
```

### Rolling file sink path

The rolling file sink stores logs in _rolling_ log files in the user profile temp directory. The following path is used for the log directory:

```
%temp%\Relativity-Transfer\Migration-Cli
```

</details>

### APM metrics

<details><summary>View APM metrics</summary>

The File Migration CLI collects APM metrics **_for TBD commands_**. This functionality is enabled by default. For information about the metrics that are collected, see the [README.md file](https://github.com/relativitydev/transfer-api-samples/blob/master/README.md) for the TAPI.

The following command performs a TBD and collects APM metrics. It includes the enabled  /apm+ parameter:

```
Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /workspaces:"1171671;1171672;1171673" /apm+
```

</details>