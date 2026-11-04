# osTicket - Prerequisites and Installation

## Project Overview

In this hands-on CourseCareers lab, I deployed **osTicket v1.15.8** on a Windows virtual machine in **Microsoft Azure**. I prepared IIS, PHP, and MySQL, configured the osTicket application files, and completed the web-based installation. This repository documents **Part 1 of a three-part osTicket help desk project** using my original lab screenshots.

## Environments and Technologies Used

- Microsoft Azure (Windows virtual machine)
- Windows, PowerShell, Internet Information Services (IIS), CGI
- PHP 7.3.8 and PHP Manager
- MySQL and HeidiSQL
- osTicket v1.15.8

## Operating System Used

- Windows virtual machine hosted in Azure (the screenshots do not establish a specific Windows release)

## List of Prerequisites

- Microsoft Azure virtual machine and Remote Desktop access
- Internet Information Services (IIS) with CGI enabled
- PHP 7.3.8 runtime and PHP Manager for IIS
- MySQL database service and HeidiSQL
- osTicket v1.15.8 application files
- Administrator access to configure IIS, PHP, and file permissions

## Installation Steps

### Step 1: Azure Virtual Machine

Created an Azure-hosted Windows virtual machine named `osticket-vm` to host the osTicket help desk environment.

![Step 1: Azure Virtual Machine](screenshots/step-01.png)

### Step 2: Administrative PowerShell Session

Verified the Windows account context using the `whoami` command in an elevated PowerShell window.

![Step 2: Administrative PowerShell Session](screenshots/step-02.png)

### Step 3: Enable IIS and CGI

Configured Windows Features for Internet Information Services (IIS), including the CGI feature required for the PHP setup used in this lab.

![Step 3: Enable IIS and CGI](screenshots/step-03.png)

### Step 4: Prepare PHP Files

Placed the PHP runtime files in `C:\PHP`, including the `php-cgi` executable.

![Step 4: Prepare PHP Files](screenshots/step-04.png)

### Step 5: Verify MySQL Service

Checked Windows Services and confirmed that the MySQL service was running with automatic startup.

![Step 5: Verify MySQL Service](screenshots/step-05.png)

### Step 6: Configure PHP in IIS

Used PHP Manager in IIS to verify PHP 7.3.8 was registered and mapped to `C:\PHP\php-cgi.exe`.

![Step 6: Configure PHP in IIS](screenshots/step-06.png)

### Step 7: Place osTicket in the Web Root

Placed osTicket application files under `C:\inetpub\wwwroot\osTicket`, including the `include`, `scp`, and `setup` folders.

![Step 7: Place osTicket in the Web Root](screenshots/step-07.png)

### Step 8: Check Installer Prerequisites

Opened the osTicket v1.15.8 installer and verified that the required PHP version and MySQLi extension passed. Some recommended extensions were still unavailable at this point.

![Step 8: Check Installer Prerequisites](screenshots/step-08.png)

### Step 9: Enable PHP Extensions

Reviewed the enabled extensions in IIS PHP Manager, including MySQLi and other PHP modules.

![Step 9: Enable PHP Extensions](screenshots/step-09.png)

### Step 10: Recheck Installer Readiness

Returned to the installer to confirm the required checks still passed and that the Intl extension was now enabled; some optional extensions remained unavailable.

![Step 10: Recheck Installer Readiness](screenshots/step-10.png)

### Step 11: Adjust Configuration File Permissions

Configured temporary Modify permission for the IIS user group on `include\ost-config.php` so installation could write the configuration file. This permission should be removed after installation.

![Step 11: Adjust Configuration File Permissions](screenshots/step-11.png)

### Step 12: Open Basic Installation Form

Reached the osTicket basic installation form for help desk settings, administrator account, and database connection details.

![Step 12: Open Basic Installation Form](screenshots/step-12.png)

### Step 13: Create the osTicket Database

Used HeidiSQL to create/select the `osticket` MySQL database before the installer populated its tables.

![Step 13: Create the osTicket Database](screenshots/step-13.png)

### Step 14: Confirm Successful Installation

Verified the osTicket installer displayed its successful installation confirmation, with instructions to remove configuration-file write access and complete post-installation setup.

![Step 14: Confirm Successful Installation](screenshots/step-14.png)

## Skills Demonstrated

- Cloud virtual machine provisioning and basic Windows administration
- IIS and PHP configuration for a web application
- Windows services and MySQL database preparation
- Web application prerequisite troubleshooting
- File permission management and installation verification

## Outcome

Successfully reached the osTicket installation confirmation screen. The subsequent post-installation configuration and ticket lifecycle exercises are documented separately in the `post-install-config` and `ticket-lifecycle` repositories.

> **Lab security note:** The screenshots show temporary installer permissions and a legacy software stack used for training. Remove write access to `ost-config.php` after installation, remove or restrict the setup directory, and use supported software versions for production deployments.

> **Privacy note:** Review images for account details and public IP addresses before publishing.
