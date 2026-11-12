<p align="center">
  <img src="https://i.imgur.com/Clzj7Xs.png" alt="osTicket logo" width="300">
</p>

# osTicket — Prerequisites and Installation

## Project Overview

In this lab, I installed osTicket on a Windows virtual machine in Microsoft Azure. I configured IIS, PHP, and MySQL, addressed installer prerequisites, and completed the initial setup. This is Part 1 of a three-part osTicket project.

## Environment and Software

* Microsoft Azure virtual machine (Windows 10; lab specification: 4 vCPUs)
* Remote Desktop Protocol (RDP)
* Internet Information Services (IIS), CGI/FastCGI, and IIS URL Rewrite
* PHP Manager for IIS 1.5.0 and PHP 7.3.8
* Microsoft Visual C++ Redistributable (x86)
* MySQL Server 5.5.62 and HeidiSQL
* osTicket 1.15.8

**Version note:** These are the historical versions used in the original training lab, not a recommended production software stack.

## Prerequisites

* Azure access and permission to create a Windows virtual machine
* Administrator access to the VM through RDP
* The `osTicket-Installation-Files.zip` package containing the lab software
* Credentials for the local MySQL administrator account

## Installation Steps

### Step 1 — Create the Azure VM

In the Azure portal, create a Windows 10 virtual machine named `osticket-vm` with **4 vCPUs**. Create a local administrator account (the lab uses `labuser`), configure RDP access, and start the VM. Use your own secure password rather than publishing credentials.

!\[Azure virtual machine in the Azure portal](step-01.png)

### Step 2 — Connect to the VM

Connect through Remote Desktop using the VM's administrator credentials. Open Command Prompt and run `whoami` to verify the signed-in account.

!\[Command Prompt on the Windows VM](step-02.png)

### Step 3 — Enable IIS and CGI

1. Open **Control Panel → Programs → Turn Windows features on or off**.
2. Enable **Internet Information Services**, including **Web Management Tools → IIS Management Console**.
3. Under **World Wide Web Services → Application Development Features**, enable **CGI** (FastCGI support).
4. Apply the changes and browse to `http://localhost` to confirm IIS is running.

!\[Windows Features showing IIS configuration](step-03.png)

### Step 4 — Install the Supporting Components

1. Download and extract the lab’s [osTicket installation package](https://drive.google.com/uc?export=download&id=1b3RBkXTLNGXbibeMuAynkfzdBC1NnqaD) onto the VM desktop. Open `osTicket-Installation-Files`.
2. Install **PHP Manager for IIS 1.5.0** (`PHPManagerForIIS\_V1.5.0.msi`), **IIS URL Rewrite** (`rewrite\_amd64\_en-US.msi`), and **Visual C++ Redistributable (x86)** (`VC\_redist.x86.exe`).
3. Create `C:\\PHP` and extract **PHP 7.3.8** (`php-7.3.8-nts-Win32-VC15-x86.zip`) into it.
4. Install **MySQL Server 5.5.62** (`mysql-5.5.62-win32.msi`) using **Typical Setup → Launch Configuration Wizard → Standard Configuration**. Configure the `root` account with a local lab password. The original checklist uses `root` as both username and password; do not reuse that password outside an isolated training environment. HeidiSQL will be installed in Step 13.
5. Keep the included **osTicket 1.15.8** ZIP available for Step 7.

!\[PHP files in File Explorer](step-04.png)

### Step 5 — Verify MySQL Is Running

Open **Services** (`services.msc`). Find the MySQL service and confirm its status is **Running**. Start it if necessary.

!\[MySQL service in Windows Services](step-05.png)

### Step 6 — Register PHP with IIS

1. Open **IIS Manager → PHP Manager → Register new PHP version**.
2. Select `C:\\PHP\\php-cgi.exe`.
3. In **Default Web Site → Handler Mappings**, verify that requests for `\*.php` use **FastCgiModule** with `C:\\PHP\\php-cgi.exe`.
4. If the mapping is missing, choose **Add Module Mapping** and enter:

   * **Request path:** `\*.php`
   * **Module:** `FastCgiModule`
   * **Executable:** `C:\\PHP\\php-cgi.exe`
   * **Name:** `PHP via FastCGI`
5. Select **OK** and approve creation of the FastCGI application if prompted.

After registering PHP, **stop and start IIS** from IIS Manager as directed in the lab.

!\[PHP Manager in IIS](step-06.png)

### Step 7 — Deploy osTicket to IIS

1. Extract `osTicket-v1.15.8.zip` from the installation package.
2. Copy its **`upload` folder** into `C:\\inetpub\\wwwroot` and rename that folder **`osTicket`**.
3. Stop and start IIS again. In IIS Manager, open **Sites → Default Web Site → osTicket** and choose \**Browse :80*. Alternatively, open `http://localhost/osTicket`.

!\[osTicket files in the IIS web directory](step-07.png)

### Step 8 — Review Installer Prerequisites

Review the osTicket installer requirements page. Note any missing or disabled PHP extensions before continuing.

!\[Initial osTicket prerequisite checks](step-08.png)

### Step 9 — Enable PHP Extensions

In **IIS Manager → PHP Manager → Enable or disable an extension**, enable **`php\_imap.dll`**, **`php\_intl.dll`**, and **`php\_opcache.dll`** as specified in the checklist. Enable any other required extensions flagged by the installer. Refresh the osTicket browser page to confirm the checks update.

!\[PHP extensions in PHP Manager](step-09.png)

### Step 10 — Confirm Installer Readiness

Refresh `http://localhost/osTicket` and confirm the required checks now pass. Prepare the configuration file in Step 11 before selecting **Continue**.

!\[Updated osTicket prerequisite checks](step-10.png)

### Step 11 — Prepare the Configuration File

1. Open `C:\\inetpub\\wwwroot\\osTicket\\include`.
2. **Rename** `ost-sampleconfig.php` to `ost-config.php`, as specified in the lab.
3. Open **Properties → Security → Advanced** for `ost-config.php`. The checklist disables inheritance, removes existing permissions, and temporarily grants **Everyone → Full control**. This is an insecure lab-only permission setting; do not use it on a production server.
4. After installation, first ensure the IIS application pool identity has the required **Read** access to the file (and administrators retain appropriate management access). Then remove the temporary **Everyone → Full control** entry. Do not leave the file writable by Everyone.

!\[osTicket configuration-file permissions](step-11.png)

### Step 12 — Open the Installation Form

After preparing `ost-config.php`, select **Continue** to open the **osTicket Basic Installation** form. Enter the help desk name, default email address, and administrator details. Create the database in Step 13 before selecting **Install Now!**.

!\[osTicket Basic Installation form](step-12.png)

### Step 13 — Create the MySQL Database

1. Install **HeidiSQL** from the installation package, then open it.
2. Create a session to the local MySQL server and sign in with the lab’s MySQL administrator account (`root`; the original checklist uses `root` as the password).
3. Right-click the server connection and choose **Create new → Database**. Name it **`osTicket`**.
4. Refresh the database list and confirm that `osTicket` appears.

!\[Database visible in HeidiSQL](step-13.png)

Return to the osTicket installation form and enter the help desk and administrator details. For the database fields, use **MySQL Database:** `osTicket`, **MySQL Username:** `root`, and the MySQL administrator password set earlier (the checklist uses `root`). Use `localhost` for the MySQL host if requested. Select **Install Now!**.

### Step 14 — Verify the Installation

Confirm the **Congratulations** page appears. Remove the `setup` directory from the deployed osTicket folder and remove temporary write access to `include\\ost-config.php`. Confirm the **staff sign-in** page at `http://localhost/osTicket/scp/login.php` and the **end-user help desk** at `http://localhost/osTicket/` load.

!\[Successful osTicket installation](step-14.png)

## Project Outcome

Installed osTicket in an Azure-hosted Windows environment, configured IIS and PHP, resolved installer prerequisites, and connected the application to MySQL.

## Next Lab

**Part 2 — osTicket Post-Installation Configuration:** Configure help desk settings, agents, departments, teams, help topics, and SLA plans.

