# Relativity File Migrator

**_TBD_** The tool provides an ability to perform baseline and differential workspace/processing native file migrations using our high-speed Transfer API tech.

This page contains the following information:


* [Before you begin](#before-you-begin)
* [Authentication](#authentication)
* [Common workflows](#common-workflows)
* [Command-line reference](#command-line-reference)
* [Additional reference information](additional-reference-information)

## Before you begin

* Make sure your environment meets these system requirements:

   * **_TBD_** release for RelativityOne or Relativity
   * .NET 4.6.2
   * Visual C++ 2010 x86 Runtime
   * Intel 2Ghz (2-4 cores are recommended)
   * 16GB RAM (32GB of RAM are recommended)

* Set up the Migrator CLI by downloading the [Relativity.FileMigrator.zip](https://github.com/relativitydev/file-migrator-cli/releases) file. Extract the zip file **_TBD_**.

* **_TBD - access rights that you need to have for running the tool_**

* Review the information provided about required file path formats for RelativityOne. See [Remote server paths](#remote-server-paths) and [UNC file paths](#unc-file-paths).

## Authentication

TBD

## Common workflows

TBD

## Command-line reference

**_TBD_**The Relativity.Migration.Console is a CLI (command-line interface) based application that provides workspace-based native file migrations through a standard set of command-line parameters. This page will outline the available commands and other optional parameters.

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


### Synchronizing workspaces

TBD

#### Local

The following example will setup the initial local master/workspace databases or perform local database updates for active workspaces.


Setting up or updating local master or workspace databases has the following general command format:

``` 
TBD
``` 

``` 
Relativity.Migration.Console.exe /command:sync /sqlinstance:"SomeInstance.mycompany.corp" /sqlpwd:"SomePassword!" /sqluser:"SomeUserName" /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /targetpath:"\\files\SomeFolder\Files" /sha1- /metadata- /skipinv- /skipnative-
``` 

#### Specific workspace

The following example is the same as above but limits the sync to specific workspaces.

Synching a specific group of workspace databases has the following general command format:

``` 
TBD
``` 

```
Relativity.Migration.Console.exe /command:sync /sqlinstance:"SomeInstance.mycompany.corp" /sqlpwd:"SomePassword!" /sqluser:"SomeUserName" /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /targetpath:"files\SomeFolder\Files" /sha1- /metadata- /skipinv- /skipnative- /workspaces:"1032984;1624500"
``` 

<details><summary>View parameter descriptions</summary>
The following table describes each of the parameters used in a migration command. For more parameters, see [Commands](#commands) and [Authentication](#authentication).



</details>




### Migrating workspaces

TBD

The following example migrates all native files contained within the specified workspaces to the target R1 instance.

Migrating native files has the following general command format:

```  
TBD
```

``` 
Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ workspaces:"1171671;1171672;1171673"
``` 

<details><summary>View parameter descriptions</summary>
The following table describes each of the parameters used in a migration command. For more parameters, see [Commands](#commands) and [Authentication](#authentication).



</details>

### Generating reports

tbd



The following example uses the local database to display a basic migration report.

``` 
Relativity.Migration.Console.exe /command:report
``` 

### Generating workspace CSV files

tbd

Generating CSV files has the following general command format:

``` 
TBD
``` 

The following example generates CSV files for the specified workspaces. This command is optional to provide operators an ability to "hand tune" CSV files when a workspace migration fails due to unsupported migration rules or logic.

``` 
Relativity.Migration.Console.exe /csv workspaces:"1171671;1171672;1171673"
``` 

<details><summary>View parameter descriptions</summary>
The following table describes each of the parameters used in a migration command. For more parameters, see [Commands](#commands) and [Authentication](#authentication).
``` 


</details>


### TAPI client configuration values

TBD
#### Displaying configuration values




This command is useful to view all available TAPI client configuration keys, descriptions, and default values.

``` 
Relativity.Migration.Console.exe /command:configdocs
``` 



#### Setting configuration values


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



</details>




## Additional reference information

This section includes the following additional information about the Migrator CLI:

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
The Migrator CLI supports the logging functionality available through Relativity. It supports three specialized desktop sinks:

* [Rolling file](https://github.com/serilog/serilog-sinks-rollingfile)
* [SEQ](https://getseq.net/)
* **Relativity HTTP** - this sink periodically sends batches of logs to Kepler API endpoints. This process is equivalent to writing logs from an agent, custom page, or event handler. In RelativityOne, this sink sends the logs to the the LogEntries tenant log, and eventually, to [Splunk](https://www.splunk.com/).

### Configuring logging
The Migrator  CLI uses standard Relativity logging configuration. **_A LogConfig.xml is located in the zip file with the Migrator CLI executable, and it is used to configure the sinks._** The following example illustrates a LogConfig.xml file:

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

The Migrator CLI collects APM metrics **_for TBD commands_**. This functionality is enabled by default. For information about the metrics that are collected, see the [README.md file](https://github.com/relativitydev/transfer-api-samples/blob/master/README.md) for the TAPI.


The following command performs a TBD and collects APM metrics. It includes the enabled  /apm+ parameter:

```
TBD - NEED SAMPLE COMMAND WITH APM COLLECTION
```

</details>



