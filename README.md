<p align="center">
  <img src="https://i.imgur.com/Clzj7Xs.png" alt="osTicket logo" width="300">
</p>

# osTicket — Prerequisites and Installation

## Project Overview

I deployed osTicket on a Windows virtual machine in Microsoft Azure. This lab covers preparing IIS, configuring PHP and MySQL, resolving installer prerequisites, and completing the initial installation. It is Part 1 of a three-part osTicket project.

## Environments and Technologies

* Microsoft Azure (Windows 10 virtual machine and Remote Desktop)
* Internet Information Services (IIS) and PHP Manager for IIS
* PHP (FastCGI)
* MySQL Server and HeidiSQL
* osTicket

## Prerequisites

* An Azure Windows VM with administrator access and Remote Desktop connectivity
* Compatible installation packages for PHP, PHP Manager, MySQL Server, HeidiSQL, and osTicket
* An IIS-compatible **Non Thread Safe (NTS)** PHP build with the extensions required by the selected osTicket version

Use PHP and MySQL versions supported by your chosen osTicket release. Installation screens and extension requirements may differ by version.

## Installation and Configuration

### Step 1 — Prepare the Azure VM

Create or start a Windows VM in the **Azure portal**. Confirm it is running and that Remote Desktop access is configured.

!\[Azure VM in the Azure portal](step-01.png)

### Step 2 — Connect to Windows

Connect to the VM with **Remote Desktop** using an administrator account. Open Command Prompt and run `whoami` to confirm the signed-in account.

!\[Command Prompt on the VM](step-02.png)

### Step 3 — Enable IIS and CGI

1. Open **Control Panel → Programs → Turn Windows features on or off**.
2. Enable **Internet Information Services → Web Management Tools → IIS Management Console**.
3. Under **World Wide Web Services → Application Development Features**, enable **CGI** (which provides FastCGI support).
4. Apply the changes. Open `http://localhost` on the VM to confirm IIS responds.

!\[Windows Features with IIS components](step-03.png)

### Step 4 — Prepare PHP and the Supporting Software

1. Install **PHP Manager for IIS**, **MySQL Server**, and **HeidiSQL**.
2. Extract the compatible **PHP NTS** package to a permanent folder, such as `C:\\PHP`.
3. Keep the extracted osTicket package available for deployment in Step 7.

Keep your MySQL administrator credentials secure; they will be used to create the application database.

!\[PHP installation files in File Explorer](step-04.png)

### Step 5 — Verify the MySQL Service

Open **Services** (`services.msc`), locate the MySQL service, and confirm its status is **Running**. Start the service if necessary.

!\[MySQL service in Windows Services](step-05.png)

### Step 6 — Register PHP with IIS

1. Open **IIS Manager → PHP Manager → Register new PHP version**.
2. Select `C:\\PHP\\php-cgi.exe` (or the actual location of `php-cgi.exe`).
3. Select the **Default Web Site** in IIS Manager and open **Handler Mappings**. Look for a PHP mapping that uses **FastCgiModule** and points to `C:\\PHP\\php-cgi.exe` (or your actual PHP path).
4. **Only if that mapping is missing:** Select **Add Module Mapping** in the Actions pane and enter:

   * **Request path:** `\*.php`
   * **Module:** `FastCgiModule`
   * **Executable:** `C:\\PHP\\php-cgi.exe` (adjust to your PHP location)
   * **Name:** `PHP via FastCGI`
Select **OK**, then **Yes** if IIS prompts you to create the FastCGI application.
5. In **PHP Manager**, confirm the registered PHP version appears without configuration warnings. Browse to `http://localhost/osTicket` after deploying the files in Step 7; the PHP-based installer should load instead of downloading a `.php` file or returning a handler error.

!\[PHP Manager in IIS](step-06.png)

### Step 7 — Copy osTicket into the IIS Web Directory

1. Create `C:\\inetpub\\wwwroot\\osTicket`.
2. Copy the **contents** of the osTicket package's `upload` folder into that directory. The directory should contain `index.php` and the `include` folder, not another nested `upload` folder.
3. Open `http://localhost/osTicket` in the VM browser.

!\[osTicket files in the IIS web directory](step-07.png)

### Step 8 — Check Installer Requirements

On the osTicket installer page, review the prerequisite checks. Identify any required PHP extensions that are missing or disabled.

!\[Initial osTicket prerequisite check](step-08.png)

### Step 9 — Enable Required PHP Extensions

1. In **IIS Manager → PHP Manager**, open **Enable or disable an extension**.
2. Enable the extensions required by the installer, including **mysqli**, **mbstring**, and **intl** where supported and required by your osTicket version. Address any additional items marked as required on the installer page.
3. In PHP Manager, open **Check phpinfo()** or inspect PHP configuration to verify that the active `php.ini` and extension directory are correct. If an extension cannot be enabled, confirm its DLL is present in the PHP `ext` folder.
4. Recycle the site's application pool in IIS Manager to reload PHP.

!\[PHP extensions in PHP Manager](step-09.png)

### Step 10 — Confirm the Requirements Pass

Refresh `http://localhost/osTicket`. Confirm the required checks pass, then select **Continue**.

!\[Updated osTicket prerequisite check](step-10.png)

### Step 11 — Create and Temporarily Unlock the Configuration File

1. Open `C:\\inetpub\\wwwroot\\osTicket\\include`.
2. **Copy** `ost-sampleconfig.php` and rename the copy to `ost-config.php`. Keep the sample file unchanged.
3. In **IIS Manager → Application Pools**, identify the application pool serving the osTicket site. For the default IIS site, this is typically `DefaultAppPool`.
4. Right-click `ost-config.php` → **Properties → Security → Edit → Add**. Enter `IIS AppPool\\<PoolName>` (for example, `IIS AppPool\\DefaultAppPool`), select **Check Names**, and grant **Modify** permission temporarily. This assumes the pool uses its default **ApplicationPoolIdentity**; otherwise grant access to the identity actually configured for that pool.
5. After successful installation, remove the temporary Modify permission. Do not remove access needed for IIS to read the file.

!\[Configuration-file security permissions](step-11.png)

### Step 12 — Review the osTicket Installation Form

Open **osTicket Basic Installation**. The form requests help desk details, an administrator account, and MySQL connection information. Prepare the database in Step 13 before submitting the form.

!\[osTicket Basic Installation form](step-12.png)

### Step 13 — Create the Database and Install osTicket

1. Open **HeidiSQL** and connect to the local MySQL server using an account permitted to create databases and users.
2. Open a query tab and execute the SQL below, replacing `REPLACE\_WITH\_STRONG\_PASSWORD` with a unique password:

```sql
   CREATE DATABASE osticket CHARACTER SET utf8mb4 COLLATE utf8mb4\_unicode\_ci;
   CREATE USER 'osticket\_user'@'localhost'
     IDENTIFIED BY 'REPLACE\_WITH\_STRONG\_PASSWORD';
   GRANT ALL PRIVILEGES ON osticket.\* TO 'osticket\_user'@'localhost';
   ```

3. In HeidiSQL, refresh the database list and confirm that `osticket` appears.

!\[Database created in HeidiSQL](step-13.png)

4. Return to the osTicket installation form. Enter the help desk name and email address, and create the administrator account.
5. Enter **MySQL Hostname:** `localhost`, **MySQL Database:** `osticket`, **MySQL Username:** `osticket\_user`, and the password from the SQL command.
6. Select **Install Now**. If the database connection fails, check the MySQL service, username, password, and host permissions before retrying.

*These commands assume MySQL and osTicket run on the same VM. If you have already created the database or user, use the existing values rather than running the creation commands again.*

### Step 14 — Verify Installation and Secure the Configuration

Confirm the installer displays **Congratulations**. Then delete the `setup` directory from the osTicket web folder and remove the temporary **Modify** permission from `include/ost-config.php`, retaining the read access required by IIS. Test the help desk and staff sign-in pages.

!\[Successful osTicket installation](step-14.png)

## Project Outcome

Installed osTicket on an Azure-hosted Windows VM, configured IIS and PHP, resolved installer prerequisites, and connected the application to MySQL.

## Next Lab

**Part 2 — osTicket Post-Installation Configuration:** Configure help desk settings, agents, departments, teams, help topics, and SLA plans.

