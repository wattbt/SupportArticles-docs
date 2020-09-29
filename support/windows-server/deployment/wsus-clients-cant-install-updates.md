---
title: WSUS clients can't install updates
description: Provides a solution to an issue where Windows Server Update Services (WSUS) clients can't install updates when Symantec Endpoint Protection is installed on the same Web site.
ms.date: 09/14/2020
author: Deland-Han 
ms.author: delhan
manager: dscontentpm
audience: itpro
ms.topic: troubleshooting
ms.prod: windows-server
localization_priority: medium
ms.reviewer: kaushika
ms.prod-support-area-path: Setup
ms.technology: Deployment
---
# WSUS (Windows Server Update Services) clients cannot install updates when Symantec Endpoint Protection is installed on the same Web site with WSUS

This article provides a solution to an issue where Windows Server Update Services (WSUS) clients can't install updates when Symantec Endpoint Protection is installed on the same Web site.

_Original product version:_ &nbsp; Windows Server 2012 R2  
_Original KB number:_ &nbsp; 968248

## Symptom

WSUS clients do not install approved updates from WSUS.

The Symantec Endpoint Protection Manager is able to download current definitions to clients.

The WSUS client computers are configured correctly to connect to WSUS and appear on the WSUS Administration console. The WSUS server is functional in every other respect.

## Cause

WSUS and the Symantec Endpoint Protection Manager both utilize a virtual directory called "Content." If WSUS and Symantec are both installed onto the same default Web site using Port 80, only the last one installed will be able to serve updates to clients.

## Resolution

Choose one of the following methods to resolve this problem.

### Method 1

Move the WSUS content directory to a new location by using the Wsusutil utility.

To move the WSUS Content directory, follow these steps:

1. Log on to the WSUS server by using an account that is a member of the local Administrators group.

2. Create a new destination folder for the WSUS content directory on an NTFS partition. The new destination folder must have the same permissions that were set on the original folder.

3. Click **Start**, right-click Command Prompt, and then click **Run as administrator**.

4. Navigate to the directory that contains the Wsusutil.exe utility:

    `cd WSUSInstallDir\Tools`

5. Type the following command:

    ```console
    wsusutil movecontent *contentpath* logfile -skipcopy
    ```

    > [!NOTE]
    > The *contentpath* placeholder is the new root for content files (the path must exist) and logfile is the path and file name of the log file to create.

6. Press ENTER. Wsusutil does the following:

    a. Updates the WSUS database to refer to the new location of the update files.

    b. Ensures that the content and metadata are synchronized.

    c. Retains the old content directory. It remains intact and is not deleted by the utility.

    d. Using the `-skipcopy` parameter ensures that the files in the existing content directory are not copied to the new WSUS destination folder.

7. Symantec Endpoint Protection Manager can now use the default content directory.

    > [!NOTE]
    > You may need to repair or reinstall Symantec Endpoint Protection Manager.

### Method 2

Move WSUS to port 8530 by using the Wsusutil utility. This option will cause IIS to use the alternate port, but all of the files will remain in place and will still be used. You may need to reconfigure the WSUS client's Group Policy setting(s) for the new port.

To move WSUS to port 8530, follow these steps:

1. Log on to the WSUS server by using an account that is a member of the local Administrators group.

2. On the WSUS server, click Start, right-click Command Prompt, and then click Run as administrator.

3. Navigate to the directory that contains the Wsusutil.exe utility:

    `cd WSUSInstallDir\Tools`

4. Type the following command:

    ```console
    wsusutil usecustomwebsite true
    ```

5. Press ENTER. WSUS will now run on port 8530.

6. As applicable, reconfigure the WSUS client's Group Policy setting for port 8530.

7. Symantec Endpoint Protection Manager can now use the default content directory and port 80.

    > [!NOTE]
    > You may need to repair or reinstall Symantec Endpoint Protection Manager.

### Method 3

Move Symantec Endpoint Protection Manager to an alternate port.

For more information, see the Symantec documentation for the supported alternate port number and procedures.