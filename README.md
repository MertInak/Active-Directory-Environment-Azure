# Active-Directory-Environment-Azure
This project demonstrates how to set up a home lab with Active Directory using Oracle VirtualBox and manage users with PowerShell. This environment provides a practical platform for learning and testing AD configurations, user management, and PowerShell scripting. 

Before I began this Home Lab Project, I made sure to download the following:

[Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads)


[Server 2019 ISO](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019)


[Windows 10 ISO](https://www.microsoft.com/en-us/software-download/windows10ISO)


PowerShell Script (Folder within Repository)


Project Overview
This project outlines the steps to set up a basic home lab running Active Directory (AD) using Oracle VirtualBox. It includes installing Windows Server, configuring Active Directory Domain Services (AD DS), and adding users using a PowerShell script. This setup is ideal for IT professionals, students, or anyone looking to gain hands-on experience with Active Directory in a controlled environment.

Steps to Set Up the Home Lab
Step 1: Create a Virtual Machine in VirtualBox
Open VirtualBox: Launch Oracle VirtualBox on your host machine.
Create a New Virtual Machine:
Click on "New".
Name your VM (e.g., "AD-Server").
Choose "Microsoft Windows" and select the version as "Windows Server".
Allocate at least 2 GB of RAM.
Create a new virtual hard disk (dynamically allocated, at least 20 GB).
Configure VM Settings:
Go to "Settings" > "System" > "Processor" and allocate at least 2 CPUs.
Go to "Storage" and add the Windows Server ISO file as a virtual optical disk.


Step 2: Install Windows Server
Start the VM: Select the newly created VM and click "Start".
Install Windows Server:
Follow the on-screen prompts to install Windows Server.
Set up the administrator account and password.


Step 3: Configure Network Settings
Network Adapter: Ensure the network adapter is set to "Bridged Adapter" for the VM to connect to the same network as your host machine.
IP Configuration: Set a static IP address for the server to ensure consistent network access.


Step 4: Install Active Directory Domain Services
Open Server Manager: After logging in, open Server Manager.
Add Roles and Features:
Click on "Add roles and features".
Select "Role-based or feature-based installation".
Choose the server from the server pool.
Select "Active Directory Domain Services" and follow the prompts to install.
Promote Server to Domain Controller:
After installation, click on the notification flag and select "Promote this server to a domain controller".
Add a new forest (e.g., "example.com").
Set the DSRM password and complete the configuration.


Step 5: Configure Active Directory
Open Active Directory Users and Computers: From the Server Manager, go to Tools > Active Directory Users and Computers.
Create Organizational Units (OUs):
Right-click on your domain and select New > Organizational Unit.
Name your OU (e.g., "Users").


Step 6: Add Users with PowerShell Script

# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS = "Password1"
$NUMBER_OF_ACCOUNTS_TO_CREATE = 10000
# ------------------------------------------------------ #

Function generate-random-name() {
    $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
    $vowels = @('a','e','i','o','u','y')
    $nameLength = Get-Random -Minimum 3 -Maximum 7
    $count = 0
    $name = ""

    while ($count -lt $nameLength) {
        if ($($count % 2) -eq 0) {
            $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
        }
        else {
            $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
        }
        $count++
    }

    return $name
}

$count = 1
while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
    $firstName = generate-random-name
    $lastName = generate-random-name
    $username = $firstName + '.' + $lastName
    $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $firstName `
               -Surname $lastName `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
    $count++
}


Conclusion


Setting up a home lab with Active Directory using Oracle VirtualBox provides a practical platform for learning and testing AD configurations, user management, and PowerShell scripting. This project not only helps in understanding the core concepts of Active Directory but also provides hands-on experience with automating user creation using PowerShell. Ensure to follow best practices for security and regularly back up your configurations. Feel free to clone or fork this project to get started with your own home lab setup.
