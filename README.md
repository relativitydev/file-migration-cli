# Relativity File Migration CLI

The Relativity File Migration CLI is an application used for moving native files from a source to a target Relativity instance. During setup, it automatically creates local databases that it uses to persist file metadata retrieved from a source instance. Since the File Migration CLI persists this data, it can also provide information about deltas that occur on the source instance, such as the addition of new native files.

The File Migration CLI supports the following commands for tracking changes on the source instance used when migrating workspaces and processing native files to the target instance:

* **Sync** - use this command to initially populate the local databases that persist file information, and to identify changes to the files stored in the source instance. The File Migration CLI persists the following information for workspace and processing native files: the UNC path, size, name, SHA-1 hash for processing files, and other fields. It also updates the local databases with any new file information. For example, if new files are added to the source instance, this command updates the local databases with their details and persists it.
* **Migrate** - use this command to migrate native files from a source instance to the file share on a target Relativity instance. The File Migration CLI migrates the physical files to the specified target file share. The migration doesn't make any updates to the target workspace or Invariant databases.
* **Report** - use this command to obtain the status information, such as the number of files pending. migration or the number of them already migrated. Run this report after synchronizing or migrating files for comparison with expected values.

This page contains the following information:

* [Data flow overview](#data-flow-overview)
* [Before you begin](#before-you-begin)
* [Setting up the File Migration CLI](#setting-up-the-file-migration-cli)
* [Common workflows using PowerShell commands](#common-workflows-using-powershell-commands)
* [Command-line reference](#command-line-reference)
* [Server paths, commands, and other reference information](#server-paths-commands-and-other-reference-information)

## Data flow overview

The File Transfer CLI retrieves file metadata on a source instance, persists this data in local databases, and migrates native files to a target instance. The following diagram provides a high-level overview of this data workflow:

**_INSERT DIAGRAM_**

<details><summary>View data workflow description</summary>

Based on this diagram, key features of the data workflow used by the File Migration CLI include:

* **Accesses the source databases** - The File Migration CLI connects directly to the databases on the source instance, so you must have SQL access to the source instance when executing a sync command. You can use integrated or SQL authentication for this access. For more information, see [Before you begin](#before-you-begin).
* **Retrieves metadata from source databases** - When you execute a sync command, the CLI retrieves the metadata for native files obtained from master, workspace, and Invariant databases on the source instance.
* **Populates and updates local databases** - The CLI uses the metadata for native files obtained from the first execution of the sync command to set a baseline. It inserts new records with this metadata to the local database and persists this metadata for future comparisons. On subsequent executions of the sync command, the CLI updates the local database records with metadata about new files or changes to existing ones to maintain a series of deltas.
* **Sets up migration requests** - The deltas between the local databases and the source instance are used to determine which files have changed and may need to be migrated to a target instance. When you execute a migrate command, the CLI uses the Transfer API as the underlying technology for migration requests.
* **Accesses source and target file shares** - The CLI migrates the physical files from a file share on the source instance to a file share on the target instance. For this process, you must have direct access to the file shares on the source file servers. Depending on whether the file share is accessible, the Transfer API automatically chooses the _client_ for the target, such as direct, Aspera, or web. For this process, you must have system admin permissions to the target instance.
* **Migrates files** - When you execute the migrate command, the CLI migrates native files from the source to target instance based on the file information persisted in the local databases, which identify deltas between the initial or later sync operations, and the source instance. Using the deltas ensures that only new or modified files from the source instance are migrated, eliminating the need to migrate all files when a change occurs. The CLI migrates the physical files to the target instance. The migration doesn't make any updates to the target workspace or Invariant databases.

</details>

## Before you begin

* Make sure your environment meets these system requirements:

  * Source instance - Version 9.0 or higher for Relativity
  * Target instance - Bluestem (9.7) release for RelativityOne or Relativity
  * .NET 4.6.2
  * Visual C++ 2010 x86 Runtime
  * Intel 2Ghz (2-4 cores are recommended)
  * 16GB RAM (32GB of RAM are recommended)

* Make sure that you have the following permissions:

  * **Source instance** - You must have at least read-only permissions to all files in the source instance repository. These permissions are required to synchronize files in the source instance with the local databases built by the Migration CLI.

    Additionally, you must have read-only access to the following databases:

    * EDDSDBO
    * Invariant
    * InvariantStore

     **Note:** The File Migration CLI must have physical access to the UNC paths where the native files are stored on the source instance to migrate them to the target instance.

  * **Target instance** - You must be a member of the System Administrators group in Relativity. These permissions are required to migrate native files to the file share for the target Relativity instance. For more information, see [System groups](https://help.relativity.com/9.6/Content/Relativity/Groups.htm#System) on the Relativity 9.6 Documentation site.

## Setting up the File Migration CLI

You can execute commands for File Migration CLI by running PowerShell scripts or through the command-line. The following instructions describe how to set up the PowerShell scripts.

**Note:** If you want to execute commands from the command-line, you only need to complete steps 1-3. For more information, see [Command-line reference](#command-line-reference).

Review the following best practices:

* Run the File Migration CLI from your local machine. While you can run it from a file share, the File Migration CLI performs better when it runs from a local machine.

* Create a new top-level folder for each set of workspaces that you want to migrate. The File Migration CLI builds separate databases for each migration when you create separate top-level folders for them. The local databases persist the data for each run on your local machine. For example, you might create a series of top-level folders, such as Phase 1, Phase 2, and so on.

Complete the following steps to set up PowerShell scripts for the File Migration CLI:

1. Download the [Relativity.FileMigrator.zip](https://github.com/relativitydev/file-migrator-cli/releases) file.
1. Create a top-level folder for the current migration job and copy the zip file to it.
1. Extract the files from the Relativity.FileMigrator.zip file. Verify that  the following files and folder are were extracted:
    * Setup.ps1 file
    * Relativity.Migration.Console.exe 
    * Templates folder
1. In the Relativity.FileMigrator folder, locate the **Setup.ps1** script.
1. Right-click on the script and select **Run with PowerShell**.
1. Enter the following information in the PowerShell console window:
   
   ![powershellsetupscript](https://user-images.githubusercontent.com/43040844/49112085-755e7000-f257-11e8-9d14-9d60b9bacdb3.png)
    * Enter **Y** when asked if you want to continue.
    * For the source instance, enter the SQL authentication as Integrated or SQL. For example, enter **I** for Integrated authentication or **S** for SQL authentication. See [Before you begin](#before-you-begin).
    * Enter the domain name of the SQL instance. For example, it should have this general format: _sqlhost.domain.local_.
    * For the target instance, enter the Relativity URL.
    * Enter the path for the target file share. For example, it should have this general format: _\\\files\T002\files_.
    * Enter a semi-colon delimited list of workspaces to migrate. For example, the list has this format: _1580162;1580184;1580194_.

1. After the setup completes, verify that the following scripts have been added to the top-level folder:
     * Migrate.ps1
     * Report.ps1
     * Sync.ps1
  
     For information about running the scripts, see [Common workflows using PowerShell commands](#common-workflows-using-powershell-commands).

1. If you are using SQL authentication, complete these steps:

     * Navigate to the **Sync.ps1** file in your top-level folder.
     * Open it with a text editor and add your SQL username and password to the following fields:

       ```
       $sql_username = ""
       $sql_password = ""
       ```
     * Save your changes to the file.

1. To setup another set of workspaces, repeat steps 2 through 8.

## Common workflows using PowerShell commands

You can use the PowerShell scripts provided with the File Migration CLI to synchronize data, migrate files, and generate reports. This section provides instructions for running the PowerShell command.

### Synchronizing a source instance with a local database

Before performing a migration, you must build local databases that store the metadata for the native files in the source instance. The File Migration CLI builds these databases when you run a sync job for the first time. When you run subsequent jobs, the synch command updates your local databases with any changes made to the files in the source instance and continues to persist this information. For more information, see [Data flow overview](#data-flow-overview).

Use the following steps to synchronize your data:

1. In the top-level folder, right-click on the **Sync.ps1** script, and select **Run with PowerShell**.
1. After the scripted runs, verify that the following items were created:

    * LocalDB folder 
    * An local database for each of the specified workspaces exists in folder

### Running reports

You can generate a report containing information about the progress of each workspace. Additionally, you can use it to verify that the workspaces were actually migrated as expected. For more information, see [Data flow overview](#data-flow-overview).

The File Migration CLI generates a report provides the following information:

* After you synchronize files, the report lists the number of workspaces with an InProgress status, indicating that they haven’t been migrated yet.
* After you migrate files, the report lists the number of files and bytes migrated, and the date of the operation. The status of these files is Completed.

The following screen shot illustrates a sample report run after a sync job executed:

![syncreportv2](https://user-images.githubusercontent.com/43040844/49035764-5be7f600-f17b-11e8-9879-0ed21e71b5b6.png)

Use the following steps to generate a report:

1. In the top-level folder, right-click on the **Report.ps1** script, and select **Run with PowerShell**.
1. Review the report displayed in the console window.

### Migrating native files

You can migrate native files to your target Relativity instance after you have run the sync job. For more information, see [Data flow overview](#data-flow-overview).

**Note:** Don't attempt to run a migration job unless you have already run the Sync.ps1 script, and verified that it completed successfully. See [Synchronizing a source instance with a local database](#synchronizing-a-source-instance-with-a-local-database).

Use the following steps to migrate native files:

1. In the top-level folder, right-click on the **Migrate.ps1** script, and select **Run with PowerShell**.
2. Monitor the migration progress to ensure that all files transfer successfully. For information on performance, see [Telemetry and metrics](https://help.relativity.com/9.6/Content/Relativity/Metrics/Telemetry.htm) on the Relativity 9.6 Documentation site.

## Command-line reference

You can optionally run all commands supported by the File Migration CLI through a command-line interface rather than with PowerShell scripts. The File Migration CLI uses standard CLI commands and parameters, which provide additional options for fine-tuning how the supported operations are run. For example, you can use specific parameters to set the number of files or bytes per batch when running a migration job.

### Command-line syntax

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

### Authentication

The File Migration CLI provides parameters used for authenticating to the source instance and the target instance.

<details><summary>Source instance - SQL authentication</summary>

When you execute the sync command, you must authenticate to the source, such as an SQL Server. The following options are available for authenticating to a source:

* **Username and password for  SQL Server** - You can enter your username and password for the SQL Server by setting the /sqluser and /sqlpwd parameters respectively.

     Username and password login has the following general command format:

     ```
     Relativity.Migration.Console.exe /command:sync [ParametersForCommand] /sqlinstance:<"Value"> /sqlpwd:<"Value"> /sqluser:<"Value"> /url:<"Value"> /login+ /enforcessl-
     ```

    The following example illustrates how to use this authentication type in a command:

    ``` 
   /command:sync /sqlinstance:"sqlinstance.mycompany.corp" /sqlpwd:"P@ssw0rd@1" /sqluser:"sa" /url:"https://hostname.mycompany.corp" /login+ /enforcessl- /targetpath:"\\files\SomeFolder\Files" /sqlintegrated- /workspaces:"1027428"
    ```

* **Integrated login for SQL Server** - You can use integrated login to authenticate to the SQL Server by enabling the /sqlintegrated parameter. In this case, you don't need to provide a username and password for the SQL Server.

     Integrated login for SQL Server has the following general command format:

     ```
     Relativity.Migration.Console.exe /command:<YourCommand> [ParametersForCommand] /sqlinstance:<"Value"> /url:<"Value"> /login+ /enforcessl- /sqlintegrated:+
     ```

    The following example illustrates how to use this authentication type in a command:

    ```
    /command:sync /sqlinstance:"sqlinstance.mycompany.corp" /url:"https://hostname.mycompany.corp" /login+ /enforcessl- /targetpath:"\\files\SomeFolder\Files" /sqlintegrated+ /workspaces:"1027428"
    ```
### Parameter descriptions for source login

The following table describes each of the parameters used to log in to a source instance. For more parameters, see [Commands](#commands).

Parameter|Description
--------------|------------------
/enforcessl{+\|-}|Enables or disables secure sockets layer (SSL) validation when executing a command. <br>**Note:** By default, this parameter is enabled. It ensures that SSL is well-tested across all environments. If the File Migration CLI reports SSL errors, we recommend that you configure your environment with a certificate, which doesn’t include wildcards. We also recommend that you don’t suppress SSL validation.
/sqlinstance:{value}|The instance name for the source SQL Server.
/sqlintegrated:{+\|-}|Enables or disables integrated authentication for the SQL Server. If this param is disabled, then you must specify values for the /sqlpwd and /sqluser parameters. <br>**Note:** By default, the parameter is disabled.
/sqlpwd:{value}|The password for the source SQL Server. Only required for SQL authentication.
/sqluser:{value}|The username for the source SQL Server. Only required for SQL authentication.
/url:{value}|The Relativity base URL for the target instance. Don't enter the web service URL used by the Relativity Desktop Client, which has the format https://\<MyServiceName\>/RelativityWebAPI/.

</details>

<br>

<details><summary>Target instance - Relativity authentication</summary>

You must provide credentials for authentication, when you make certain commands through the File Migration CLI to a Relativity or RelativityOne target environment.

**Note:** The File Migration CLI doesn’t persist your username or password. This information isn’t stored or cached by the tool, so you must reenter it. However, if you specify credentials, you don't need to reenter them.

This list describes the methods available for authenticating to a target through the File Migration CLI:

* **Username and password login** - You can enter your username and password in the command-line using the /username and /password parameters respectively.

     Username and password login has the following general command format:

     ```
     Relativity.Migration.Console.exe /command:<YourCommand> [ParametersForCommand] /url:<"Value"> /username:"email@company.com" /password:"SomePassword!"
     ```

    The following example illustrates how to use this authentication type in a command:

    ```
    Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /username:<"Value"> /password:<"Value"> /workspaces:"1171671;1171672;1171673"
    ```
* **Interactive login** - You can use interactive login when a Relativity login is required for the migrate command. For example, use interactive login for a migrating data to an environment, where you haven't been assigned an Okta provider for authentication. The following command displays the Relativity login dialog where you can enter your credentials:

     Interactive login has the following general command format:

     ```
     Relativity.Migration.Console.exe /command:<YourCommand> [ParametersForCommand] /url:<"Value"> /login+
     ```

    The following example illustrates how to use this authentication type in a command:

    ```
    Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /login+ /workspaces:"1171671;1171672;1171673"
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
     
### Parameter descriptions for target login

The following table describes each of the parameters used to log in to a target instance. For more parameters, see [Commands](#commands).

Parameter|Description
--------------|------------------
/interactive{+\|-}|Enables or disables interactive mode, which requires a user to press the ENTER key or provide some other response before terminating a command. <br>**Note:** By default, the parameter is enabled.
/login{+\|-}|Enables or disables the launching of a login window. <br>**Note:** This parameter is required for interactive login, and for interactive login with Okta support. By default, the parameter is disabled.
/oktaforce{+\|-}|Enables or disables the requirement to use the Okta provider to login. Specify this parameter if you are running the File Migration CLI in an environment that doesn’t use the _HRDHint_ registry setting but requires an Okta login. <br>**Note:** To force an Okta login, you must also specify the /login+ parameter. The default value for /oktaforce is disabled.
/password:{value}|The Relativity login password for the target instance. <br>**Note:** This parameter is required only for username and password login.
/url:{value}|The Relativity base URL for the target instance. Don't enter the web service URL used by the Relativity Desktop Client, which has the format https://\<MyServiceName\>/RelativityWebAPI/.
/username:{value}|The Relativity login name for the target instance. <br>**Note:** This parameter is required only for username and password login.

</details>

### Running the sync command

The sync command builds and populates local databases with information about the native files obtained from the source instance. The File Migration CLI persists the following information for workspace and processing native files: the UNC path, size, name, SHA-1 hash for processing files, and other fields. You must run this command before you attempt to migrate any native files.

After the File Migration CLI builds the local databases, you can use the sync command to update them when changes have occurred in the source instance. You have the option to sync all the workspace databases from the source, or a specific group of them. For more information, see [Data flow overview](#data-flow-overview).

#### First-time setup or delta sync of local databases

The first-time setup or the delta sync of the local master or workspace databases has the following general command format:

```
Relativity.Migration.Console.exe /command:sync /sqlinstance:<"Value"> {/sqluser:<"Value"> /sqlpwd:<"Value"> /login+ /oktaforce+ | /login+ /enforcessl- /sqlintegrated:+} /url:<"Value"> {/targetpath:<"Value">|/fileshare:<"Value">} /sha1:<+ | -> /metadata:<+ | -> /skipinv:<+ | -> /skipnative:<+ | ->
```

The following example illustrates how to perform a first-time setup of the local master and workspace databases. It can also be used to update the local databases for active workspaces.

```
Relativity.Migration.Console.exe /command:sync /sqlinstance:"sqlinstance.mycompany.corp.mycompany.corp" /sqlpwd:"SomePassword!" /sqluser:"SomeUserName" /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /targetpath:"\\files\SomeFolder\Files" /sha1- /metadata- /skipinv- /skipnative-
```

#### Filter on workspace databases

You can filter on a limited number of workspace databases when executing the sync command. Use the /workspaces parameter to filter on specific workspaces. See the following example:

```
Relativity.Migration.Console.exe /command:sync /sqlinstance:"sqlinstance.mycompany.corp" /sqlpwd:"SomePassword!" /sqluser:"SomeUserName" /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /targetpath:"\\files\SomeFolder\Files" /sha1- /metadata- /skipinv- /skipnative- /workspaces:"1032984;1624500"
```

<details><summary>View parameter descriptions</summary>

The following table describes each of the parameters used in a migration command. For more parameters, see [Commands](#commands) and [Authentication](#authentication).

Parameter|Description
--------------|------------------
/dupfiles{+\|-}|Enables or disables the removal of duplicate files. During synchronization, this optimization increases the memory and CPU usage, and the time to completion, but it can significantly reduce the overall transfer time. <br>**Note:** By default, the parameter is enabled.
/fileshare:{value}|The name, number, artifact identifier, or UNC path for a file share. For more information, see [UNC file paths](#unc-file-paths) and [Remote server paths](#remote-server-paths). <br>**Note:** You can optionally use the /fileshare parameter instead of the /targetpath.
/maxsync:{value}|The maximum number of records to synchronize. Use this parameter to limit the number of records fetched with the sync command while debugging or troubleshooting. <br>**Note:** The default value is Int32.MaxValue.
/metadata:{+\|-}|Enables or disables the retrieval of file metadata, such as the length in bytes, for all source files. It provides verifiable migration results and eliminates more expensive server-side validation. We recommend enabling this setting only when running the File Migration CLI from from a Windows-based server where the files are local. <br>**Note:** By default, the parameter is disabled.
/sha1{+\|-}|Enables or disables the calculation of SHA1 hashes for all source files. SHA1 hashes are used to validate the migration. We recommend enabling this parameter only when running the File Migration CLI from from a Windows-based server where the files are local.<br>**Note:** By default, the parameter is disabled.
/skipinv{+\|-}|Enables or disables skipping Invariant files. <br>**Note:** By default, the parameter is disabled.
/skipnative{+\|-}|Enables or disables skipping native files. <br>**Note:** By default, the parameter is disabled.
/targetpath:{value}|The target path used for synching databases or workspaces. This parameter is only used by the sync command. The File Migration CLI migrates files to this location specified by this parameter. For more information, see [UNC file paths](#unc-file-paths) and [Remote server paths](#remote-server-paths). <br>**Note:** You can optionally use the /fileshare parameter instead of the /targetpath.
/workspaces:{value}|The artifact IDs for a list of Relativity workspaces that you want to filter on when running a specific command. This parameter supports the following syntax:<ul><li>A list of semicolon delimited artifact IDs, such as _1171671;1171672;1171673_</li><li>The start and end of range separated by a dash, such as _1171671-1171673_ </li></ul>

</details>

### Generating reports

When generating a report through the command-line, you can use additional filter options, such as the /jobstatus parameter. For example, you can a run a report on only completed or in progress jobs. For more information, see [Running reports](#running-reports).

The following example generates a basic migration report for the local database:

``` 
Relativity.Migration.Console.exe /command:report
``` 

<details><summary>View parameter descriptions</summary>

The following table describes each of the parameters used in a report command. For more parameters, see [Commands](#commands).

Parameter|Description
--------------|--------------
/jobstatus:{value}|Indicates the job status filter applied to certain migration commands. For example, to run a report on completed jobs, you would set this parameter to Completed when executing the report command. Supported values include: <ul><li>None</li><li>InProgress - indicates that the file migration hasn't yet completed.</li><li>Completed - indicates that the file migration has finished. </li><li>Skipped - indicates there wasn't any files to migrate.</li></ul>

</details>

### Migrating native files 

Before you can migrate native files through the command-line, you must run the sync command, and verify that it completed successfully. You need to set the authentication parameters required by the target instance for the migration job. For more information, see [Data flow overview](#data-flow-overview).

Migrating native files has the following general command format:

```  
Relativity.Migration.Console.exe /command:migrate /url:<"Value"> {/username:<"Value"> /password:<"Value"> | /login+ | /login+ /oktaforce+} /workspaces:<"Value"> 
```

The following example illustrates how to migrate all native files in the specified workspaces to the target instance:

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
/reset{+\|-}|Forces the re-migration of all files. For example, you previously migrated a workspace, and then re-execute the migrate command on the same workspace. The File Migration CLI checks the database, and then quickly terminates the command because it determined that the files have been migrated. If you execute the migrate command with the /reset+ parameter, then the workspace is re-migrated.<br>**Note:** By default, the parameter is disabled.
/skipinv{+\|-}|Enables or disables skipping Invariant files. <br>**Note:** By default, the parameter is disabled.
/skipnative{+\|-}|Enables or disables skipping native files. <br>**Note:** By default, the parameter is disabled.
/subjob:{value}|The subjob filter applied to certain migration commands. For example, if you set the value of this parameter to InvariantDbStorage, and executed the migrate command, only workspaces with files in the Invariant DB Storage table are migrated. Supported values include: <ul><li>None</li><li>WorkspaceDbFiles</li><li>InvariantDbStorage </li><li>InvariantDbPages</li><li>InvariantTemporaryNative</li></ul>
/workspaces:{value}|The artifact IDs for a list of Relativity workspace that you want to filter on when running a specific command. This parameter supports the following syntax:<ul><li>A list of semicolon delimited artifact IDs, such as _1171671;1171672;1171673_</li><li>The start and end of range separated by a dash, such as _1171671-1171673_ </li></ul>

</details>

## Server paths, commands, and other reference information

This section includes the following additional information about the File Migration CLI:

### Remote server paths

<details><summary>View remote server paths</summary>

The File Migration CLI supports both absolute and relative paths. The File Migration CLI leverages path resolvers used by the underlying TAPI architecture to adapt paths from one format to another. It supports the following path architecture:

* **On-premises Relativity** - supports UNC paths. For more information, see [Absolute paths used by on-premises Relativity](#absolute-paths-used-by-on-premises-Relativity).
* **RelativityOne** - supports only UNIX-style relative paths, because it is deployed on Aspera. By default, the File Migration CLI can resolve UNC paths to relative paths for backwards compatibility. See [UNIX-style relative paths used by RelativityOne](#unix-style-relative-paths-used-by-RelativityOne).

### Absolute paths used by on-premises Relativity
An absolute UNC path is a fully qualified domain name (FQDN). The following absolute path represents a _Temp_ folder in a workspace with the identifier 1027541.

```
\\files.txxx.ctus010000.relativity.one\Txxx\Files\EDDS1027541\Temp
```

### UNIX-style relative paths used by RelativityOne 
RelativityOne is deployed on Aspera, so it supports only UNIX-style relative paths. The following screen shot illustrates the root of a RelativityOne file share, when browsed through File Explorer:

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

### UNC file paths

<details><summary>View guidelines for UNC file paths</summary>

The File Migration CLI requires you to specify file paths in most of the commands that its provides. Relativity supports UNC paths, while Aspera supports only relative paths. To avoid migration failures, use the following guidelines when using remote UNC file paths:

Make sure that the remote UNC path in the /targetpath parameter has the same base path as specified in the /fileshare parameter. For example, the following /targetpath and /fileshare parameter illustrate matching values for a request:

   * /fileshare:"\\files2\T002\Files"
   * /targetpath:"\\files2\T002\Files\EDDS1111111"

**Note:** These examples illustrate file path conventions used in RelativityOne. For example, the paths all point to the Files directory, and  T002 indicates a tenant ID.

</details>

### Configuration settings

<details><summary>View configuration settings</summary>
The File Migration CLI supports several commands that you can use to set or view client configuration settings.

### Set client configuration values
To update client configuration settings, assign name-value pairs to the /configuration parameter. The following table lists the most commonly used configuration settings:

Key                           |Description
------------------------------|------------------
client|The migration client used for a command. The File Migration CLI automatically chooses the best-fit client, but you can also manually set this configuration value to Aspera, Fileshare, HTTP, or other values.
file-not-found-errors-disabled|Enables or disables the treatment of missing files as warnings or errors. The default value is False.
file-not-found-errors-retry|Enables or disables retrying missing file errors. The default value is True.
http-timeout-seconds|The timeout, in seconds, for an HTTP or REST service call. The default value is 300.
max-http-retry-attempts|The maximum number of retry attempts for an HTTP or REST service call. The default value is 5.
max-job-retry-attempts|The maximum number of job retry attempts. The default value is 3.
min-data-rate-mbps|The minimum data rate in Mbps unit. This option isn't supported by all clients. It acts as a hint to clients that support configurable data rates. The default value is 0.
overwrite-files|Enables or disables overwriting files at the target path. The job fails when this option is disabled, and the target paths already exist. The default value is True.
permission-errors-retry|Enables or disables retrying file migrations that fail due to permission or file access errors. The default value is False.
preserve-dates|Enables or disables preserving file created, modified, and access times. The default value is True.
target-data-rate-mbps|The target data rate in Mbps unit. This option isn't supported by all clients. It acts as a hint to clients that support configurable data rates. The default value is 100.
transfer-empty-directories|Enables or disables transferring empty directories. The default value is False.

In this example, the command forces the Aspera client and sets the target data rate to 250 Mbps.

```
Relativity.Migration.Console.exe /configuration:"client=Aspera;target-data-rate-mbps=250"
```
**Note:** If the name-value pair itself contains a semi-colon, then replace it with the %3B encoded character. For example, the _azure-storage-account-connection-string_ contains semi-colons to separate each setting within a single connection string parameter.

### Display a list of all client configuration keys
You can display a list of the all client configuration keys, descriptions, and default values by executing the following command:

```
Relativity.Migration.Console.exe /command:configdocs
```

</details>

### Commands

<details><summary>View available commands</summary>

The following table lists the commands available in the File Migration CLI:

Commands|Version|Description
--------|----------|---------------
[configdocs](#configuration-settings)|Bluestem (9.7)|Displays general and plugin-specific TAPI client configuration documentation. It includes the following information for each entry:<ul><li>Key </li><li>Description</li><li>Default value</li><li>Choices</li></ul>No additional parameters are required for this command.
[migrate](#migrating-workspaces)|Bluestem (9.7)|Executes the file migration process on a list of workspaces that you must supply. We recommend that you list three to five workspaces. As each workspace is migrated, File Migration CLI automatically updates the local database with the proper results. The available parameters include:<ul><li>/url</li><li>/workspaces</li><li>/jobstatus (optional)</li><li>/login (optional)</li><li>/enforcessl (optional)</li><li>/configuration (optional)</li></ul>
[report](#generating-reports)|Bluestem (9.7)|Displays migration results for each workspace to the standard output (stdout), including the workspace name and artifact ID, status, total migrated files and bytes, and the timestamps for the last synchronization and migration jobs. The available parameters include:<ul><li>/workspaces (optional)</li><li>/jobstatus (optional)</li></ul>
[sync](#synchronizing-workspaces)|Bluestem (9.7)|Accesses the on-premise EDDS and workspace databases, retrieves data from them, and creates local databases. When this command is run for the first time, it performs a _baseline_ migration, which creates the local databases. It can be run multiple times for _delta_ migrations that update the databases with changes made to active workspaces. The available parameters include:<ul><li>/sqlinstance</li><li>/sqlpwd</li><li>/sqluser</li><li>/url</li><li>/targetpath</li><li>/workspaces (optional)</li><li>/login (optional)</li><li>/enforcessl (optional)</li><li>/reset (optional)</li></ul>

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
    <fileSink name="File1" logFileLocation="%temp%\Relativity-Transfer\Migration-Cli\" maxFileSizeInMB ="10000" />
    <seqSink name="Seq1" serverUrl="http://localhost:5341" batchSizeLimit="50" waitPeriodSeconds="15" />
    <relativityHttpSink name="Http1" batchSizeLimit="50" waitPeriodSeconds="15" />
  </sinks>
</kCuraLogging>
```

### Rolling file sink path

The rolling file sink stores logs in _rolling_ log files in the user profile temp directory. The following path is used for the log directory:

```
%temp%\Relativity-Transfer\Migration-Cli\
```

</details>

### APM metrics

<details><summary>View APM metrics</summary>
 
The File Migration CLI collects APM metrics for only the migrate command. This functionality is enabled by default. For information about the metrics that are collected, see the [README.md file](https://github.com/relativitydev/transfer-api-samples/blob/master/README.md) for the TAPI.

The following command performs a migration and collects APM metrics. It includes the enabled /apm+ parameter:

```
Relativity.Migration.Console.exe /command:migrate /url:"https://hostname.mycompany.corp" /login+ /oktaforce+ /workspaces:"1171671;1171672;1171673" /apm+
```

</details>