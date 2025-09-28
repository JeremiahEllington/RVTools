# RVTools Installation Guide

## Table of Contents
1. [Overview](#overview)
2. [System Requirements](#system-requirements)
3. [Pre-Installation Checklist](#pre-installation-checklist)
4. [Download and Verification](#download-and-verification)
5. [Installation Methods](#installation-methods)
6. [Post-Installation Configuration](#post-installation-configuration)
7. [Verification and Testing](#verification-and-testing)
8. [Troubleshooting Installation](#troubleshooting-installation)
9. [Uninstallation](#uninstallation)
10. [Upgrade Procedures](#upgrade-procedures)

## Overview

This guide provides step-by-step instructions for installing RVTools 4.7.1 in various environments. RVTools is a VMware vSphere management tool that requires proper installation and configuration to function effectively.

### What You'll Learn

- System requirements and compatibility
- Download and verification procedures
- Multiple installation methods
- Configuration and testing
- Troubleshooting common installation issues

## System Requirements

### Minimum Requirements

#### Hardware Requirements
- **CPU**: 2.0 GHz processor (32-bit or 64-bit)
- **Memory**: 2GB RAM minimum
- **Disk Space**: 100MB for installation
- **Network**: Network adapter for vCenter Server connectivity

#### Software Requirements
- **Operating System**: Windows 7 SP1 or later
- **.NET Framework**: 4.0 or later
- **Excel**: Microsoft Excel 2007 or later (for full functionality)
- **PowerShell**: PowerShell 2.0 or later (for automation)

### Recommended Requirements

#### Hardware Requirements
- **CPU**: 4-core processor (64-bit)
- **Memory**: 8GB RAM or more
- **Disk Space**: 1GB for installation and data
- **Network**: Gigabit network adapter

#### Software Requirements
- **Operating System**: Windows 10/11 or Windows Server 2019/2022
- **.NET Framework**: 4.8 or later
- **Excel**: Microsoft Excel 2016 or later
- **PowerShell**: PowerShell 5.1 or later

### VMware Environment Requirements

#### vCenter Server Requirements
- **vCenter Server**: 4.0 or later
- **ESXi Hosts**: 4.0 or later
- **vSphere**: 4.0, 5.0, 5.1, 5.5, 6.0, 6.5, 6.7, 7.0, 8.0
- **Network**: Access to vCenter Server on port 443 (HTTPS)

#### User Permissions
- **Read-only access** to vCenter Server
- **View permissions** for all objects
- **No administrative privileges** required

## Pre-Installation Checklist

### System Preparation

#### 1. Verify System Requirements
```powershell
# Check Windows version
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion

# Check .NET Framework
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full\" -Name Release

# Check available memory
Get-WmiObject -Class Win32_ComputerSystem | Select-Object TotalPhysicalMemory

# Check disk space
Get-WmiObject -Class Win32_LogicalDisk | Select-Object DeviceID, Size, FreeSpace
```

#### 2. Network Connectivity
```powershell
# Test connectivity to vCenter Server
Test-NetConnection -ComputerName "vcenter.company.com" -Port 443

# Test DNS resolution
Resolve-DnsName -Name "vcenter.company.com"

# Check firewall rules
Get-NetFirewallRule -DisplayName "*RVTools*"
```

#### 3. User Account Preparation
```powershell
# Create dedicated service account (recommended)
New-ADUser -Name "RVTools-Service" -UserPrincipalName "rvtools-svc@company.com" -AccountPassword (ConvertTo-SecureString "ComplexPassword123!" -AsPlainText -Force) -Enabled $true

# Grant minimum required permissions
# - Read-only access to vCenter Server
# - No administrative privileges
# - Domain user account for security
```

#### 4. Antivirus Configuration
```powershell
# Add RVTools to antivirus exclusions
# Common antivirus software exclusions:
# - Windows Defender: Add to exclusions
# - Symantec: Add to exclusions
# - McAfee: Add to exclusions
# - Trend Micro: Add to exclusions
```

## Download and Verification

### Official Download Sources

#### Primary Download
- **Dell Technologies**: [RVTools 4.7.1 MSI](https://downloads.dell.com/rvtools/rvtools4.7.1.msi)
- **Checksum**: [Verify Download Integrity](https://downloads.dell.com/rvtools/checksum.txt)
- **Documentation**: [RVTools User Guide](https://downloads.dell.com/rvtools/rvtools.pdf)

#### Download Verification
```powershell
# Download RVTools
Invoke-WebRequest -Uri "https://downloads.dell.com/rvtools/rvtools4.7.1.msi" -OutFile "RVTools4.7.1.msi"

# Download checksum
Invoke-WebRequest -Uri "https://downloads.dell.com/rvtools/checksum.txt" -OutFile "checksum.txt"

# Verify checksum
$expectedChecksum = Get-Content "checksum.txt"
$actualChecksum = (Get-FileHash "RVTools4.7.1.msi" -Algorithm SHA256).Hash
if ($expectedChecksum -eq $actualChecksum) {
    Write-Host "Checksum verification successful"
} else {
    Write-Error "Checksum verification failed"
}
```

### Security Considerations

#### Certificate Validation
```powershell
# Verify SSL certificate
$request = [System.Net.WebRequest]::Create("https://downloads.dell.com/rvtools/rvtools4.7.1.msi")
$request.GetResponse()

# Check certificate details
$cert = [System.Net.ServicePointManager]::ServerCertificateValidationCallback
```

#### File Integrity
```powershell
# Verify file size
$fileInfo = Get-Item "RVTools4.7.1.msi"
$expectedSize = 50MB  # Approximate size
if ($fileInfo.Length -gt $expectedSize) {
    Write-Host "File size verification successful"
} else {
    Write-Error "File size verification failed"
}
```

## Installation Methods

### Method 1: Interactive Installation

#### Standard Installation
1. **Download the MSI file** from Dell Technologies
2. **Right-click on the MSI file** and select "Run as administrator"
3. **Follow the installation wizard**:
   - Welcome screen
   - License agreement
   - Installation location
   - Installation options
   - Ready to install
4. **Complete the installation**

#### Installation Steps
```
1. Welcome Screen
   - Click "Next" to continue

2. License Agreement
   - Read the license agreement
   - Check "I accept the terms in the License Agreement"
   - Click "Next"

3. Installation Location
   - Default: C:\Program Files\RVTools
   - Click "Next" to use default location
   - Or click "Change" to specify custom location

4. Installation Options
   - Choose installation type (Typical, Custom, Complete)
   - Select components to install
   - Click "Next"

5. Ready to Install
   - Review installation settings
   - Click "Install" to begin installation

6. Installation Progress
   - Wait for installation to complete
   - Do not interrupt the installation process

7. Installation Complete
   - Click "Finish" to complete installation
   - Optionally launch RVTools immediately
```

### Method 2: Silent Installation

#### Command Line Installation
```powershell
# Basic silent installation
msiexec /i RVTools4.7.1.msi /quiet

# Silent installation with logging
msiexec /i RVTools4.7.1.msi /quiet /log "C:\Logs\RVTools_Install.log"

# Silent installation with custom location
msiexec /i RVTools4.7.1.msi /quiet INSTALLDIR="D:\RVTools"

# Silent installation with specific options
msiexec /i RVTools4.7.1.msi /quiet ADDLOCAL=ALL
```

#### PowerShell Installation
```powershell
# Download and install silently
function Install-RVTools {
    param(
        [string]$DownloadUrl = "https://downloads.dell.com/rvtools/rvtools4.7.1.msi",
        [string]$InstallPath = "C:\Program Files\RVTools",
        [string]$LogPath = "C:\Logs\RVTools_Install.log"
    )
    
    try {
        # Download RVTools
        Write-Host "Downloading RVTools..."
        Invoke-WebRequest -Uri $DownloadUrl -OutFile "RVTools4.7.1.msi"
        
        # Install RVTools
        Write-Host "Installing RVTools..."
        $arguments = @(
            "/i", "RVTools4.7.1.msi",
            "/quiet",
            "/log", $LogPath,
            "INSTALLDIR=`"$InstallPath`""
        )
        
        Start-Process -FilePath "msiexec" -ArgumentList $arguments -Wait
        
        # Verify installation
        if (Test-Path $InstallPath) {
            Write-Host "RVTools installed successfully"
        } else {
            Write-Error "RVTools installation failed"
        }
    }
    catch {
        Write-Error "Installation failed: $($_.Exception.Message)"
    }
}

# Usage
Install-RVTools -InstallPath "D:\RVTools" -LogPath "D:\Logs\RVTools_Install.log"
```

### Method 3: Enterprise Deployment

#### Group Policy Deployment
```powershell
# Create Group Policy for RVTools deployment
# 1. Create software installation policy
# 2. Assign RVTools MSI to computers
# 3. Configure installation options
# 4. Deploy to target computers
```

#### SCCM Deployment
```powershell
# Create SCCM application package
# 1. Create application in SCCM
# 2. Add RVTools MSI as deployment type
# 3. Configure installation command
# 4. Deploy to target collections
```

#### PowerShell DSC Deployment
```powershell
# PowerShell DSC configuration
Configuration RVToolsInstallation {
    Node "RVToolsServer" {
        Package RVTools {
            Ensure = "Present"
            Name = "RVTools"
            Path = "C:\Software\RVTools4.7.1.msi"
            ProductId = "{GUID}"
            Arguments = "/quiet"
        }
    }
}

# Apply configuration
RVToolsInstallation -OutputPath "C:\DSC\RVTools"
Start-DscConfiguration -Path "C:\DSC\RVTools" -Wait -Verbose
```

## Post-Installation Configuration

### Initial Configuration

#### 1. Launch RVTools
```powershell
# Launch RVTools from Start Menu
Start-Process "C:\Program Files\RVTools\RVTools.exe"

# Or from command line
& "C:\Program Files\RVTools\RVTools.exe"
```

#### 2. Configure Connection Settings
```
vCenter Server: vcenter.company.com
Port: 443
Protocol: HTTPS
Username: administrator@vsphere.local
Password: [your password]
Domain: vsphere.local
```

#### 3. Test Connection
```powershell
# Test connection to vCenter Server
Test-NetConnection -ComputerName "vcenter.company.com" -Port 443

# Test with RVTools
# 1. Launch RVTools
# 2. Enter connection details
# 3. Click "Test Connection"
# 4. Verify successful connection
```

### Advanced Configuration

#### 1. SSL Certificate Configuration
```powershell
# Accept SSL certificate
# In RVTools, go to Settings > SSL Certificate
# Click "Accept Certificate" or "Trust Certificate"

# Or configure programmatically
$cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cert.Import("C:\Certificates\vcenter.cer")
$store = New-Object System.Security.Cryptography.X509Certificates.X509Store("TrustedPublisher", "LocalMachine")
$store.Open("ReadWrite")
$store.Add($cert)
$store.Close()
```

#### 2. Network Configuration
```powershell
# Configure proxy settings
$proxyConfig = @{
    Enabled = $true
    Server = "proxy.company.com"
    Port = 8080
    Username = "proxy-user"
    Password = "proxy-password"
    Bypass = @("localhost", "*.company.com")
}

# Configure firewall rules
New-NetFirewallRule -DisplayName "RVTools vCenter HTTPS" -Direction Outbound -Protocol TCP -LocalPort Any -RemotePort 443 -RemoteAddress "vcenter.company.com" -Action Allow
```

#### 3. Performance Configuration
```powershell
# Configure scan settings
$scanConfig = @{
    MaxThreads = 8
    Timeout = 300
    MemoryLimit = 4GB
    BatchSize = 100
}

# Configure logging
$loggingConfig = @{
    Level = "Info"
    Path = "D:\RVTools\Logs"
    MaxSize = "100MB"
    Retention = "30 days"
}
```

## Verification and Testing

### Installation Verification

#### 1. Check Installation Files
```powershell
# Verify installation directory
Get-ChildItem "C:\Program Files\RVTools"

# Check for required files
$requiredFiles = @(
    "RVTools.exe",
    "RVTools.exe.config",
    "RVTools.dll",
    "RVTools.pdb"
)

foreach ($file in $requiredFiles) {
    if (Test-Path "C:\Program Files\RVTools\$file") {
        Write-Host "✓ $file found"
    } else {
        Write-Error "✗ $file missing"
    }
}
```

#### 2. Check Registry Entries
```powershell
# Verify registry entries
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | Where-Object {$_.DisplayName -like "*RVTools*"}

# Check installation path
Get-ItemProperty "HKLM:\SOFTWARE\RVTools" -ErrorAction SilentlyContinue
```

#### 3. Check Windows Services
```powershell
# Check if RVTools service is installed
Get-Service -Name "*RVTools*" -ErrorAction SilentlyContinue

# Check service status
Get-Service -Name "*RVTools*" | Select-Object Name, Status, StartType
```

### Functionality Testing

#### 1. Basic Functionality Test
```powershell
# Test RVTools launch
Start-Process -FilePath "C:\Program Files\RVTools\RVTools.exe" -PassThru

# Check if process is running
Get-Process -Name "RVTools" -ErrorAction SilentlyContinue
```

#### 2. Connection Test
```powershell
# Test connection to vCenter Server
Test-NetConnection -ComputerName "vcenter.company.com" -Port 443

# Test with RVTools
# 1. Launch RVTools
# 2. Enter connection details
# 3. Click "Connect"
# 4. Verify successful connection
```

#### 3. Data Collection Test
```powershell
# Test data collection
# 1. Connect to vCenter Server
# 2. Start a scan
# 3. Verify data collection
# 4. Check for errors
```

### Performance Testing

#### 1. System Resource Usage
```powershell
# Monitor CPU usage
Get-Counter "\Process(RVTools)\% Processor Time" -SampleInterval 1 -MaxSamples 10

# Monitor memory usage
Get-Counter "\Process(RVTools)\Working Set" -SampleInterval 1 -MaxSamples 10

# Monitor disk usage
Get-Counter "\LogicalDisk(_Total)\Disk Read Bytes/sec" -SampleInterval 1 -MaxSamples 10
```

#### 2. Network Performance
```powershell
# Test network latency
Test-NetConnection -ComputerName "vcenter.company.com" -Port 443 -InformationLevel Detailed

# Monitor network usage
Get-Counter "\Network Interface(*)\Bytes Total/sec" -SampleInterval 1 -MaxSamples 10
```

## Troubleshooting Installation

### Common Installation Issues

#### Issue 1: Installation Fails
```powershell
# Check Windows Event Log
Get-WinEvent -LogName Application -MaxEvents 10 | Where-Object {$_.LevelDisplayName -eq "Error"}

# Check installation logs
Get-Content "C:\Windows\Temp\RVTools_Install.log" -ErrorAction SilentlyContinue

# Verify system requirements
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion
```

#### Issue 2: Permission Denied
```powershell
# Check user permissions
whoami /priv

# Run as administrator
Start-Process -FilePath "RVTools4.7.1.msi" -Verb RunAs

# Check file permissions
Get-Acl "RVTools4.7.1.msi" | Format-List
```

#### Issue 3: Antivirus Blocking
```powershell
# Temporarily disable antivirus
# Add RVTools to exclusions
# Check for false positives
```

### Installation Logs

#### Windows Installer Logs
```powershell
# Enable verbose logging
msiexec /i RVTools4.7.1.msi /l*v "C:\Logs\RVTools_Install.log"

# Check installation log
Get-Content "C:\Logs\RVTools_Install.log" | Select-String "Error"
```

#### RVTools Logs
```powershell
# Check RVTools logs
Get-ChildItem "$env:TEMP\RVTools" -Recurse

# Analyze log files
Get-Content "$env:TEMP\RVTools\RVTools.log" | Select-String "Error"
```

## Uninstallation

### Standard Uninstallation

#### Method 1: Control Panel
1. **Open Control Panel**
2. **Go to Programs and Features**
3. **Find RVTools in the list**
4. **Right-click and select Uninstall**
5. **Follow the uninstallation wizard**

#### Method 2: PowerShell
```powershell
# Uninstall RVTools
Get-WmiObject -Class Win32_Product | Where-Object {$_.Name -like "*RVTools*"} | ForEach-Object {$_.Uninstall()}

# Or use MSI
msiexec /x RVTools4.7.1.msi /quiet
```

#### Method 3: Command Line
```powershell
# Silent uninstallation
msiexec /x RVTools4.7.1.msi /quiet

# Uninstallation with logging
msiexec /x RVTools4.7.1.msi /quiet /log "C:\Logs\RVTools_Uninstall.log"
```

### Complete Removal

#### 1. Remove Installation Files
```powershell
# Remove installation directory
Remove-Item "C:\Program Files\RVTools" -Recurse -Force

# Remove data directory
Remove-Item "D:\RVTools\Data" -Recurse -Force -ErrorAction SilentlyContinue

# Remove logs directory
Remove-Item "D:\RVTools\Logs" -Recurse -Force -ErrorAction SilentlyContinue
```

#### 2. Remove Registry Entries
```powershell
# Remove registry entries
Remove-Item "HKLM:\SOFTWARE\RVTools" -Recurse -Force -ErrorAction SilentlyContinue

# Remove from Add/Remove Programs
Get-WmiObject -Class Win32_Product | Where-Object {$_.Name -like "*RVTools*"} | ForEach-Object {$_.Uninstall()}
```

#### 3. Remove User Data
```powershell
# Remove user configuration
Remove-Item "$env:APPDATA\RVTools" -Recurse -Force -ErrorAction SilentlyContinue

# Remove user data
Remove-Item "$env:USERPROFILE\Documents\RVTools" -Recurse -Force -ErrorAction SilentlyContinue
```

## Upgrade Procedures

### In-Place Upgrade

#### Method 1: Standard Upgrade
1. **Download new version**
2. **Run new MSI installer**
3. **Follow upgrade wizard**
4. **Verify installation**

#### Method 2: Silent Upgrade
```powershell
# Download new version
Invoke-WebRequest -Uri "https://downloads.dell.com/rvtools/rvtools4.7.1.msi" -OutFile "RVTools4.7.1.msi"

# Upgrade silently
msiexec /i RVTools4.7.1.msi /quiet /upgrade
```

### Migration Procedures

#### 1. Backup Current Installation
```powershell
# Backup configuration
Copy-Item "$env:APPDATA\RVTools" -Destination "D:\Backup\RVTools\Config_$(Get-Date -Format 'yyyyMMdd')" -Recurse

# Backup data
Copy-Item "D:\RVTools\Data" -Destination "D:\Backup\RVTools\Data_$(Get-Date -Format 'yyyyMMdd')" -Recurse
```

#### 2. Uninstall Old Version
```powershell
# Uninstall old version
msiexec /x RVTools4.6.1.msi /quiet
```

#### 3. Install New Version
```powershell
# Install new version
msiexec /i RVTools4.7.1.msi /quiet
```

#### 4. Restore Configuration
```powershell
# Restore configuration
Copy-Item "D:\Backup\RVTools\Config_20240115" -Destination "$env:APPDATA\RVTools" -Recurse

# Restore data
Copy-Item "D:\Backup\RVTools\Data_20240115" -Destination "D:\RVTools\Data" -Recurse
```

### Verification After Upgrade

#### 1. Check Version
```powershell
# Check RVTools version
Get-ItemProperty "C:\Program Files\RVTools\RVTools.exe" | Select-Object VersionInfo
```

#### 2. Test Functionality
```powershell
# Test RVTools functionality
Start-Process -FilePath "C:\Program Files\RVTools\RVTools.exe" -PassThru

# Test connection
# Test data collection
# Test reporting
```

#### 3. Verify Configuration
```powershell
# Check configuration files
Get-ChildItem "$env:APPDATA\RVTools"

# Verify settings
# Test connections
# Verify data access
```

---

**This installation guide provides comprehensive instructions for installing RVTools in various environments. For additional support, refer to the official documentation or contact Dell Technologies support.**