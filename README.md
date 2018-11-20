# Relativity File Migration CLI

The Relativity File Migration CLI is an application that supports **_TBD_** Add introduction.

This page contains the following information:

* [Data flow overview](#data-flow-overview)
* [Before you begin](#before-you-begin)
* [Setting up the File Migration CLI](#setting-up-the-file-migration-cli)
* [Common workflows using PowerShell commands](#common-workflows-using-powershell-commands)
* [Command-line reference](#command-line-reference)
* [Server paths, commands, and other reference information](#server-paths-commands-and-other-reference-information)

## Data flow overview

**_TBD_** Add overview of Data flow and diagram

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

You can execute commands for File Migration CLI by running PowerShell scripts or through the command-line. The following instructions describe how to set up the PowerShell scripts.

**Note:** If you want to execute commands from the command-line, you only need to complete steps 1-3. For more information, see [Command-line reference](#command-line-reference).

Review the following best practices:

* Run the File Migration CLI from your local machine. While you can run it from a fileshare, the File Migration CLI performs better when it runs from a local machine.

* Create a new top-level folder for each set of workspaces that you want to migrate. The File Migration CLI builds separate databases for each migration when you create separate top-level folders for them. The SQLite databases persist the data for each run on your local machine. For example, you might create a series of top-level folders, such as Phase 1, Phase 2, and so on.


Complete the following steps to set up PowerShell scripts for the File Migration CLI:

1. Download the [Relativity.FileMigrator.zip](https://github.com/relativitydev/file-migrator-cli/releases) file.
1. Create a top-level folder for the current migration job, and add the zip file to it.
1. Extract the files from the Relativity.FileMigrator.zip file. Verify that  the following files and folder are were extracted:
     * Setup.ps1 file
     * Relativity.Migration.Console.exe 
     * Templates folder
     * Other miscellaneous files
1. In the Relativity.FileMigrator folder, locate the **Setup.ps1** script.
1. Right-click on the script, and select **Run with PowerShell**.
1. Enter the following information in the PowerShell console window:

      Entry|Description
   ------------|---------------
   SQL authentication| **I** indicates integrated authentication, and **S** indicates SQL authentication. 
   Relativity SQL instance| The DNS name of your SQL instance, such as _sqlinstance.mycompany.corp_.
   Relativity URL| The web URL for your destination Relativity instance, such as _<span>https:/</span>/hostname.mycompany.corp_.
   Target path| The UNC path for the destination fileshare, such as _\\\files\T002\files_.
   Workspaces| A semi-colon delimited list of workspaces to migrate, such as _580162;1580184;1580194_.
  
  1. After the setup completes, verify that the following scripts have been added to the top-level folder:
     * Migrate.ps1
     * Report.ps1
     * Sync.ps1
  
     For information about running the scripts, see [Common workflows using PowerShell commands](#common-workflows-using-powershell-commands).
 1. To migrate another set of workspaces, repeat steps 2 through 7. 
        
## Common workflows using PowerShell commands
You can use the PowerShell scripts provided with the File Migration CLI to synchronize data, migrate files, and generate reports. This section provides instructions for running the PowerShell command.

### Synchronizing a source instance with a local database

Before performing a migration, you must build local databases that store the metadata for the natives files in the source instance. The File Migration CLI builds these databases when you run a synchronization job for the first time. When you run subsequent jobs, the synch command updates your local databases with any changes made to the files in the source instance and continues to persist this information. For more information, see [Data flow overview](#data-flow-overview).

Use the following steps to synchronize your data:

1. If you are using integrated authentication, continue to step 2. If you are using SQL authentication, complete these steps:

   * Navigate to the **Sync.ps1** file in your top-level folder.
   * Open it with a text editor and add your SQL username and password to the following fields:
    
     ```
     $sql_username = ""
     $sql_password = "" 
     ```
   * Save your changes to the file.

 1. Right-click on the script, and select **Run with PowerShell**.
 1. After the scripted runs, verify that the following items were created:
    
    * LocalDB folder 
    * An SQLite database for each of the specified workspaces exists in folder

### Migrating native files
TBD

### Running reports

TBD


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

The File Migration CLI uses the same syntax followed by other Microsoft-based CLI tools. This list includes common CLI syntax conventions:

* Use a colon between a command and a parameter values use:
  ```
  /command:sync
  ```
* Enclose parameter values containing spaces with double quotation marks: 
  ```
  /command:sync /targetpath:"\\files\SomeFolder\Files"
  ```

* Use a plus (+) or minus (-) sign to flag parameters as enabled and disabled respectively:
  ```
  /command:sync  /skipinv+ (enable)
  /command:sync  /skipinv- (disable)
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
   /command:sync /sqlinstance:"sqlinstance.mycompany.corp" /sqlpwd:"P@ssw0rd@1" /sqluser:"sa" /url:"https://hostname.mycompany.corp" /login+ /enforcessl- /targetpath:"\\files\T005\FTA\ScottP\DataMigration" /sqlintegrated- /workspaces:"1027428"
    ``` 

* **Integrated login for SQL Server** - You can use integrated login to authenticate to the SQL Server by setting the /sqlintegrated parameter to True. In this case, you don't need to provide a SQL Server username and password.

     Integrated login for SQL Server has the following general command format:

     ```
     Relativity.Migration.Console.exe /command:<YourCommand> [ParametersForCommand] /sqlinstance:<"Value"> /url:<"Value"> /login+ /enforcessl- /sqlintegrated:+ 
     ```

    The following example illustrates how to use this login type in a command:

    ```
    /command:sync /sqlinstance:"sqlinstance.mycompany.corp" /url:"https://hostname.mycompany.corp" /login+ /enforcessl- /targetpath:"\\files\T005\FTA\ScottP\DataMigration" /sqlintegrated+ /workspaces:"1027428"
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
     Relativity.Migration.Console.exe /command:<YourCommand> [ParametersForCommand] /url:<"Value"> /login+ /oktaforce+
     ```
     The following command uses the /oktaforce+ parameter for an interactive login with Okta:

     ```
    Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /workspaces:"1171671;1171672;1171673"
     ```
     
<details><summary>View parameter descriptions for target login</summary>

The following table describes each of the parameters used to log in to a target instance. For more parameters, see [Commands](#commands) and [Authentication - source instance](#authentication-source-instance).

Parameter|Description
--------------|------------------
/interactive{+\|-}|Enables or disables interactive mode, which requires a user to press the ENTER key or provide some other response before terminating a command. <br>**Note:** The default value is True. 
/login{+\|-}|Enables or disables the launching of a login window. <br>**Note:** This parameter is required for interactive login, and for interactive login with Okta support. The default value is False.
/oktaforce{+\|-}|Enables or disables the requirement to use the Okta provider to login. Specify this parameter if you are running the File Migration CLI in an environment that doesn’t use the _HRDHint_ registry setting but requires an Okta login. <br>**Note:** To force an Okta login, you must also specify the /login+ parameter. The default value for /oktaforce is False.
/password:{value}|The Relativity login password. <br>**Note:** This parameter is required only for username and password login.
/url:{value}|The Relativity base URL for your environment. Don't enter the web service URL used by the Relativity Desktop Client, which has the format https://\<MyServiceName\>/RelativityWebAPI/.
/username:{value}|The Relativity login name. <br>**Note:** This parameter is required only for username and password login.

</details>


### Synchronizing workspaces

**_TBD_** Add introduction

#### Synchronizing local master and workspace databases

Setting up or updating local master or workspace databases has the following general command format:

``` 
Relativity.Migration.Console.exe /command:sync /sqlinstance:<"Value"> {/sqluser:<"Value"> /sqlpwd:<"Value"> /login+ /oktaforce+ | /login+ /enforcessl- /sqlintegrated:+} /url:<"Value"> {/targetpath:<"Value">|/fileshare:<"Value">} /sha1:<+ | -> /metadata:<+ | ->  /skipinv:<+ | ->  /skipnative:<+ | -> 
``` 

The following example will setup the initial local master/workspace databases or perform local database updates for active workspaces.

``` 
Relativity.Migration.Console.exe /command:sync /sqlinstance:"sqlinstance.mycompany.corp.mycompany.corp" /sqlpwd:"SomePassword!" /sqluser:"SomeUserName" /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /targetpath:"\\files\SomeFolder\Files" /sha1- /metadata- /skipinv- /skipnative-
``` 

#### Synchronizing specific workspaces

Synching a specific group of workspace databases has the following general command format:

``` 
Relativity.Migration.Console.exe /command:sync /sqlinstance:<"Value"> {/sqluser:<"Value"> /sqlpwd:<"Value"> /login+ /oktaforce+ | /login+ /enforcessl- /sqlintegrated:+} /url:<"Value"> {/targetpath:<"Value">|/fileshare:<"Value">} /sha1:<+ | -> /metadata:<+ | ->  /skipinv:<+ | ->  /skipnative:<+ | -> /workspaces:<"Value">
```

The following example is the same as above but limits the sync to specific workspaces. 

```
Relativity.Migration.Console.exe /command:sync /sqlinstance:"sqlinstance.mycompany.corp" /sqlpwd:"SomePassword!" /sqluser:"SomeUserName" /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /targetpath:"files\SomeFolder\Files" /sha1- /metadata- /skipinv- /skipnative- /workspaces:"1032984;1624500"
```

<details><summary>View parameter descriptions</summary>

The following table describes each of the parameters used in a migration command. For more parameters, see [Commands](#commands) and [Authentication](#authentication).


Parameter|Description
--------------|------------------
/dupfiles{+\|-}|Enables or disables the removal of duplicate files. During synchronization, this optimization increases the memory and CPU usage, and the time to completion, but it can significantly reduce the overall transfer time. <br>**Note:** The default value is True.
/fileshare:{value}|The name, number, artifact identifier, or UNC path for a file share. For more information, see [UNC file paths](#unc-file-paths) and [Remote server paths](#remote-server-paths). <br>**Note:** You can optionally use the /fileshare parameter instead of the /targetpath.
/maxsync:{value}|The maximum number of records to synchronize. Use this parameter to limit the number of records fetched with the sync command while debugging or troubleshooting. <br>**Note:** The default value is Int32.MaxValue.
/metadata:{value}|Enables or disables the retrieval of file metadata, such as the length in bytes, for all source files. Although an expensive operation, we highly recommend enabling this setting because it provides verifiable migration results and eliminates more expensive server-side validation. <br>**Note:** The default value is True.
/sha1{+\|-}|Enables or disables the calculation of SHA1 hashes for all source files. SHA1 hashes are used to validate the migration, so you should only modify this setting in a performance critical scenario. <br>**Note:** The default value is True.
/skipinv{+\|-}|Enables or disables skipping Invariant files when executing the sync or migrate commands. <br>**Note:** The default value is False.
/skipnative{+\|-}|Enables or disables skipping native files when executing the sync or migrate commands. <br>**Note:** The default value is False.
/skiplongpaths{+\|-}|Enables or disables skipping long paths found during search path enumeraion. When this parameter is set to False, the enumeration fails if a path exceeds the maximum length defined by the File Migration CLI. <br>**Note:** The default value is False.
/targetpath:{value}|The target path used for synching databases or workspaces, or for migrating native files. For more information, see [UNC file paths](#unc-file-paths) and [Remote server paths](#remote-server-paths). <br>**Note:** You can optionally use the /fileshare parameter instead of the /targetpath.
/workspaces:{value}|The artifact IDs for a list of Relativity workspace that you want to filter on when running a specific command. This parameter supports the following syntax:<ul><li>A list of semicolon delimited artifact IDs, such as _1171671;1171672;1171673_</li><li>The start and end of range separated by a dash, such as _1171671-1171673_ </li></ul>

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
/fileshare:{value}|The name, number, artifact identifier, or UNC path for a file share. For more information, see [UNC file paths](#unc-file-paths) and [Remote server paths](#remote-server-paths). <br>**Note:** You can optionally use the /fileshare parameter instead of the /targetpath.
/maxbytesperbatch:{value}|The maximum number of bytes per batch. <br>**Note:** The default value is 100GB.
/maxfilesperbatch:{value}|The maximum number of files per batch. <br>**Note:** The default value is 1M.
/maxhttpattempts:{value}|The maximum number of HTTP retry attempts. <br>**Note:** The default value is 5.
/reset{+\|-}|Indicates whether reset the migration job or re-migrate all files. For example, you previously migrated a workspace, and then re-execute the migrate command on the same workspace. The File Migration CLI checks the database, and then quickly terminates the command because it determined that the files have been migrated. If you execute the migrate command with the /reset+ parameter, then the workspace is re-migrated.<br>**Note:** The default value is False.
/skipinv{+\|-}|Enables or disables skipping Invariant files when executing the sync or migrate commands. <br>**Note:** The default value is False.
/skipnative{+\|-}|Enables or disables skipping native files when executing the sync or migrate commands. <br>**Note:** The default value is False.
/skiplongpaths{+\|-}|Enables or disables skipping long paths found during search path enumeration. When this parameter is set to False, the enumeration fails if a path exceeds the maximum length defined by the File Migration CLI. <br>**Note:** The default value is False.
/subjob:{value}|The subjob filter applied to certain migration commands. For example, if you set the value of this parameter to InvariantDbStorage, and executed the migrate command, only workspaces with files in the Invariant DB Storage table are migrated. Supported values include: <ul><li>None</li><li>WorkspaceDbFiles</li><li>InvariantDbStorage</li><li>InvariantDbPages</li><li>InvariantTemporaryNative</li></ul>
/targetpath:{value}|The target path used for synching databases or workspaces, or for migrating native files. For more information, see [UNC file paths](#unc-file-paths) and [Remote server paths](#remote-server-paths). <br>**Note:** You can optionally use the /fileshare parameter instead of the /targetpath.
/workspaces:{value}|The artifact IDs for a list of Relativity workspace that you want to filter on when running a specific command. This parameter supports the following syntax:<ul><li>A list of semicolon delimited artifact IDs, such as _1171671;1171672;1171673_</li><li>The start and end of range separated by a dash, such as _1171671-1171673_ </li></ul>

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
/jobstatus:{value}|Indicates the job status filter applied to certain migration commands. For example, to run a report on completed jobs, you would set this parameter to Completed when executing the report command. Supported values include: <ul><li>None</li><li>InProgress</li><li>Completed</li><li>Skipped</li></ul> 

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
/workspaces:{value}|The artifact IDs for a list of Relativity workspace that you want to filter on when running a specific command. This parameter supports the following syntax:<ul><li>A list of semicolon delimited Artifact IDs, such as _1171671;1171672;1171673_</li><li>The start and end of range separated by a dash, such as _1171671-1171673_ </li></ul>

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

The  File Migration CLI supports both absolute and relative paths. The File Migration CLI leverages path resolvers used by the underlying TAPI architecture to adapt paths from one format to another. It supports the following path architecture:

 * **On-premises Relativity** - supports UNC paths. For more information, see [Absolute paths used by on-premises Relativity](#absolute-paths-used-by-on-premises-Relativity).
* **RelativityOne** - supports only UNIX-style relative paths, because it is deployed on Aspera. By default, the File Migration CLI can resolve UNC paths to relative paths for backwards compatibility. See [UNIX-style relative paths used by RelativityOne](#unix-style-relative-paths-used-by-RelativityOne).

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

The following table lists the commands available in the File Migration CLI:

Commands|Version|Description
--------|----------|---------------
[configdocs](#tapi-client-configuration-values)|TBD|Displays general and plugin-specific TAPI client configuration documentation. It includes the following information for each entry:<ul><li>Key </li><li>Description</li><li>Default value</li><li>Choices</li></ul>No additional parameters are required for this command.
[csv](#generating-workspace-CSV-files)|TBD|Autogenerates a single CSV file per workspace. All files are stored in the CSV subfolder of the working directory. The file names have the following format: _EDDSxxxxxxx.csv_. When the migrate command is executed, the File Migration CLI uses the existing CSV file or autogenerates a file on the fly. You can optionally use this command to manually modify CSV files used for a workspace migration that fails due to unsupported migration rules or logic. The available parameters include:<ul><li>/workspaces (optional)</li><li>/jobstatus (optional)</li></ul>
[migrate](#migrating-workspaces)|TBD|Executes the file migration process on a list of workspaces that you must supply. We recommend that you list three to five workspaces. As each workspace is migrated, File Migration CLI automatically updates the local database with the proper results. The available parameters include:<ul><li>/url</li><li>/workspaces</li><li>/jobstatus (optional)</li><li>/login (optional)</li><li>/enforcessl (optional)</li><li>/configuration (optional)</li></ul>
[report](#generating-reports)|TBD|Displays migration results for each workspace to the standard output (stdout), including the workspace name and artifact ID, status, total migrated files and bytes, and the timestamps for the last synchronization and migration jobs. The available parameters include:<ul><li>/workspaces (optional)</li><li>/jobstatus (optional)</li></ul>
[sync](#synchronizing-workspaces)|TBD|Accesses the on-premise EDDS and workspace databases, retrieves data from them, and creates local SQLite databases. When this command is run for the first time, it performs a _baseline_ migration, which creates the local databases. It can be run multiple times for _delta_ migrations that update the databases with changes made to active workspaces. The available parameters include:<ul><li>/sqlinstance</li><li>/sqlpwd</li><li>/sqluser</li><li>/url</li><li>/targetpath</li><li>/workspaces (optional)</li><li>/login (optional)</li><li>/enforcessl (optional)</li><li>/reset (optional)</li></ul>

</details>


### Exit codes

<details><summary>View exit codes</summary>

The File Migration CLI supports standard exit codes for command-line tools. The following table list supported exist codes:

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
    <fileSink name="File1" logFileLocation="%temp%\Relativity-Migration\Migration-Cli\" maxFileSizeInMB ="10000" />
    <seqSink name="Seq1" serverUrl="http://localhost:5341" batchSizeLimit="50" waitPeriodSeconds="15" />
    <relativityHttpSink name="Http1" batchSizeLimit="50" waitPeriodSeconds="15" />
  </sinks>
</kCuraLogging>
```

### Rolling file sink path

The rolling file sink stores logs in _rolling_ log files in the user profile temp directory. The following path is used for the log directory:

```
%temp%\Relativity-Migration\Migration-Cli
```

</details>

### APM metrics

<details><summary>View APM metrics</summary>

The File Migration CLI collects APM metrics **_for TBD commands_**. This functionality is enabled by default. For information about the metrics that are collected, see the [README.md file](https://github.com/relativitydev/transfer-api-samples/blob/master/README.md) for the TAPI.

The following command performs a TBD and collects APM metrics. It includes the enabled /apm+ parameter:

```
Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /workspaces:"1171671;1171672;1171673" /apm+
```

</details>