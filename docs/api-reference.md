# RVTools API Reference

## Table of Contents
1. [Overview](#overview)
2. [Command Line Interface](#command-line-interface)
3. [PowerShell Integration](#powershell-integration)
4. [REST API Integration](#rest-api-integration)
5. [Configuration API](#configuration-api)
6. [Data Export API](#data-export-api)
7. [Automation API](#automation-api)
8. [Error Handling](#error-handling)
9. [Examples](#examples)
10. [Reference Tables](#reference-tables)

## Overview

RVTools provides multiple APIs for integration and automation. This reference covers all available APIs, their parameters, return values, and usage examples.

### API Types

- **Command Line Interface**: Direct command-line execution
- **PowerShell Integration**: PowerShell cmdlets and functions
- **REST API**: HTTP-based API for web integration
- **Configuration API**: Settings and configuration management
- **Data Export API**: Data export and reporting
- **Automation API**: Automated operations and scheduling

## Command Line Interface

### Basic Syntax

```bash
RVTools.exe [options]
```

### Command Line Options

#### Connection Options
```bash
-server <server>          # vCenter Server FQDN or IP
-user <username>          # Username for authentication
-password <password>      # Password for authentication
-domain <domain>          # Domain for authentication
-port <port>             # Port number (default: 443)
-protocol <protocol>     # Protocol (HTTP/HTTPS)
```

#### Output Options
```bash
-output <path>            # Output directory for reports
-format <format>          # Output format (Excel/CSV/XML/JSON)
-filename <filename>      # Custom filename for output
-template <template>      # Report template to use
```

#### Scan Options
```bash
-scope <scope>            # Scan scope (All/VM/Host/Datastore)
-filter <filter>          # Filter criteria
-include <include>        # Include specific objects
-exclude <exclude>        # Exclude specific objects
```

#### Performance Options
```bash
-timeout <seconds>        # Connection timeout
-threads <count>          # Number of concurrent threads
-memory <MB>              # Memory limit for scan
-cpu <percent>            # CPU usage limit
```

#### Advanced Options
```bash
-silent                   # Silent mode (no GUI)
-verbose                  # Verbose output
-debug                    # Debug mode
-log <path>               # Log file path
-config <path>            # Configuration file path
```

### Usage Examples

#### Basic Connection
```bash
RVTools.exe -server vcenter.company.com -user administrator -password password123
```

#### Custom Output
```bash
RVTools.exe -server vcenter.company.com -user administrator -password password123 -output C:\Reports -format Excel
```

#### Filtered Scan
```bash
RVTools.exe -server vcenter.company.com -user administrator -password password123 -scope VM -filter "PowerState=PoweredOn"
```

#### Silent Mode
```bash
RVTools.exe -server vcenter.company.com -user administrator -password password123 -silent -output C:\Reports
```

## PowerShell Integration

### RVTools PowerShell Module

#### Installation
```powershell
# Install RVTools PowerShell module
Install-Module -Name RVTools -Force

# Import module
Import-Module RVTools
```

#### Core Cmdlets

##### Connect-RVTools
```powershell
Connect-RVTools -Server "vcenter.company.com" -User "administrator" -Password "password123"
```

**Parameters:**
- `-Server`: vCenter Server FQDN or IP
- `-User`: Username for authentication
- `-Password`: Password for authentication
- `-Domain`: Domain for authentication
- `-Port`: Port number (default: 443)
- `-Protocol`: Protocol (HTTP/HTTPS)
- `-Timeout`: Connection timeout in seconds

**Return Value:**
- `RVToolsConnection` object with connection details

##### Start-RVToolsScan
```powershell
Start-RVToolsScan -Connection $connection -Scope "All" -OutputPath "C:\Reports"
```

**Parameters:**
- `-Connection`: RVTools connection object
- `-Scope`: Scan scope (All/VM/Host/Datastore/Network)
- `-OutputPath`: Output directory for reports
- `-Format`: Output format (Excel/CSV/XML/JSON)
- `-Filter`: Filter criteria
- `-Include`: Include specific objects
- `-Exclude`: Exclude specific objects

**Return Value:**
- `RVToolsScanResult` object with scan results

##### Get-RVToolsData
```powershell
Get-RVToolsData -Connection $connection -DataType "VM" -Filter "PowerState=PoweredOn"
```

**Parameters:**
- `-Connection`: RVTools connection object
- `-DataType`: Data type (VM/Host/Datastore/Network/Cluster)
- `-Filter`: Filter criteria
- `-SortBy`: Sort criteria
- `-Top`: Number of records to return

**Return Value:**
- Array of data objects

##### Export-RVToolsReport
```powershell
Export-RVToolsReport -Data $data -OutputPath "C:\Reports\VMReport.xlsx" -Format "Excel"
```

**Parameters:**
- `-Data`: Data to export
- `-OutputPath`: Output file path
- `-Format`: Output format (Excel/CSV/XML/JSON)
- `-Template`: Report template
- `-IncludeCharts`: Include charts in report

**Return Value:**
- Export result object

### Advanced PowerShell Functions

#### Get-RVToolsVMInfo
```powershell
Get-RVToolsVMInfo -Connection $connection -Name "VM-01" -Detailed
```

#### Get-RVToolsHostInfo
```powershell
Get-RVToolsHostInfo -Connection $connection -Name "esxi-01.company.com" -Detailed
```

#### Get-RVToolsDatastoreInfo
```powershell
Get-RVToolsDatastoreInfo -Connection $connection -Name "datastore-01" -Detailed
```

#### Compare-RVToolsData
```powershell
Compare-RVToolsData -Baseline $baselineData -Current $currentData -OutputPath "C:\Reports\Comparison.xlsx"
```

### PowerShell Examples

#### Complete VM Inventory
```powershell
# Connect to vCenter Server
$connection = Connect-RVTools -Server "vcenter.company.com" -User "administrator" -Password "password123"

# Get all VMs
$vms = Get-RVToolsData -Connection $connection -DataType "VM"

# Filter powered-on VMs
$poweredOnVMs = $vms | Where-Object {$_.PowerState -eq "PoweredOn"}

# Export to Excel
Export-RVToolsReport -Data $poweredOnVMs -OutputPath "C:\Reports\PoweredOnVMs.xlsx" -Format "Excel"
```

#### Host Performance Analysis
```powershell
# Connect to vCenter Server
$connection = Connect-RVTools -Server "vcenter.company.com" -User "administrator" -Password "password123"

# Get host performance data
$hosts = Get-RVToolsData -Connection $connection -DataType "Host"

# Filter high CPU usage hosts
$highCpuHosts = $hosts | Where-Object {$_.CpuUsage -gt 80}

# Export performance report
Export-RVToolsReport -Data $highCpuHosts -OutputPath "C:\Reports\HighCpuHosts.xlsx" -Format "Excel"
```

#### Automated Daily Report
```powershell
# Schedule daily report generation
$action = {
    $connection = Connect-RVTools -Server "vcenter.company.com" -User "administrator" -Password "password123"
    $data = Get-RVToolsData -Connection $connection -DataType "All"
    $reportPath = "C:\Reports\DailyReport_$(Get-Date -Format 'yyyyMMdd').xlsx"
    Export-RVToolsReport -Data $data -OutputPath $reportPath -Format "Excel"
}

# Create scheduled task
Register-ScheduledTask -TaskName "RVTools Daily Report" -Action $action -Trigger (New-ScheduledTaskTrigger -Daily -At "06:00")
```

## REST API Integration

### Base URL
```
https://rvtools.company.com/api/v1
```

### Authentication
```http
Authorization: Bearer <token>
Content-Type: application/json
```

### Endpoints

#### Connection Management

##### POST /connections
```http
POST /api/v1/connections
Content-Type: application/json

{
    "server": "vcenter.company.com",
    "user": "administrator",
    "password": "password123",
    "domain": "vsphere.local",
    "port": 443,
    "protocol": "https"
}
```

**Response:**
```json
{
    "connectionId": "conn-12345",
    "status": "connected",
    "server": "vcenter.company.com",
    "timestamp": "2024-01-15T10:30:00Z"
}
```

##### GET /connections/{connectionId}
```http
GET /api/v1/connections/conn-12345
```

**Response:**
```json
{
    "connectionId": "conn-12345",
    "status": "connected",
    "server": "vcenter.company.com",
    "timestamp": "2024-01-15T10:30:00Z",
    "lastScan": "2024-01-15T10:25:00Z"
}
```

#### Data Collection

##### POST /scans
```http
POST /api/v1/scans
Content-Type: application/json

{
    "connectionId": "conn-12345",
    "scope": "All",
    "filter": "PowerState=PoweredOn",
    "outputFormat": "JSON"
}
```

**Response:**
```json
{
    "scanId": "scan-67890",
    "status": "running",
    "progress": 0,
    "estimatedCompletion": "2024-01-15T10:35:00Z"
}
```

##### GET /scans/{scanId}
```http
GET /api/v1/scans/scan-67890
```

**Response:**
```json
{
    "scanId": "scan-67890",
    "status": "completed",
    "progress": 100,
    "startTime": "2024-01-15T10:30:00Z",
    "endTime": "2024-01-15T10:32:00Z",
    "records": 150,
    "outputPath": "/api/v1/scans/scan-67890/data"
}
```

#### Data Retrieval

##### GET /data/{connectionId}/{dataType}
```http
GET /api/v1/data/conn-12345/VM?filter=PowerState=PoweredOn&limit=50
```

**Response:**
```json
{
    "dataType": "VM",
    "count": 50,
    "total": 150,
    "data": [
        {
            "name": "VM-01",
            "powerState": "PoweredOn",
            "cpu": 2,
            "memory": 4096,
            "guestOS": "Windows Server 2019"
        }
    ]
}
```

### REST API Examples

#### Python Example
```python
import requests
import json

# Connect to RVTools API
def connect_rvtools(server, user, password):
    url = "https://rvtools.company.com/api/v1/connections"
    data = {
        "server": server,
        "user": user,
        "password": password
    }
    response = requests.post(url, json=data)
    return response.json()

# Get VM data
def get_vm_data(connection_id):
    url = f"https://rvtools.company.com/api/v1/data/{connection_id}/VM"
    response = requests.get(url)
    return response.json()

# Usage
connection = connect_rvtools("vcenter.company.com", "administrator", "password123")
vm_data = get_vm_data(connection["connectionId"])
print(f"Found {vm_data['count']} VMs")
```

#### JavaScript Example
```javascript
// Connect to RVTools API
async function connectRVTools(server, user, password) {
    const response = await fetch('https://rvtools.company.com/api/v1/connections', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            server: server,
            user: user,
            password: password
        })
    });
    return await response.json();
}

// Get VM data
async function getVMData(connectionId) {
    const response = await fetch(`https://rvtools.company.com/api/v1/data/${connectionId}/VM`);
    return await response.json();
}

// Usage
const connection = await connectRVTools("vcenter.company.com", "administrator", "password123");
const vmData = await getVMData(connection.connectionId);
console.log(`Found ${vmData.count} VMs`);
```

## Configuration API

### Configuration File Format
```xml
<?xml version="1.0" encoding="UTF-8"?>
<rvtools>
    <connection>
        <server>vcenter.company.com</server>
        <port>443</port>
        <protocol>https</protocol>
        <timeout>30</timeout>
    </connection>
    <scan>
        <scope>All</scope>
        <threads>4</threads>
        <memory>1024</memory>
        <timeout>300</timeout>
    </scan>
    <output>
        <format>Excel</format>
        <path>C:\Reports</path>
        <template>default</template>
    </output>
    <logging>
        <level>Info</level>
        <path>C:\Logs\RVTools</path>
        <maxSize>10MB</maxSize>
    </logging>
</rvtools>
```

### Configuration Management

#### Get Configuration
```powershell
Get-RVToolsConfig -Path "C:\Config\RVTools.xml"
```

#### Set Configuration
```powershell
Set-RVToolsConfig -Path "C:\Config\RVTools.xml" -ConnectionTimeout 60 -ScanThreads 8
```

#### Reset Configuration
```powershell
Reset-RVToolsConfig -Path "C:\Config\RVTools.xml"
```

## Data Export API

### Export Formats

#### Excel Export
```powershell
Export-RVToolsToExcel -Data $data -OutputPath "C:\Reports\Report.xlsx" -IncludeCharts
```

#### CSV Export
```powershell
Export-RVToolsToCSV -Data $data -OutputPath "C:\Reports\Report.csv" -Delimiter ","
```

#### XML Export
```powershell
Export-RVToolsToXML -Data $data -OutputPath "C:\Reports\Report.xml" -Schema "RVTools.xsd"
```

#### JSON Export
```powershell
Export-RVToolsToJSON -Data $data -OutputPath "C:\Reports\Report.json" -PrettyPrint
```

### Custom Export Templates

#### Template Definition
```xml
<?xml version="1.0" encoding="UTF-8"?>
<template>
    <name>VM Inventory</name>
    <description>Virtual machine inventory report</description>
    <sections>
        <section name="VMs">
            <columns>
                <column name="Name" source="VM.Name"/>
                <column name="Power State" source="VM.PowerState"/>
                <column name="CPU" source="VM.CPU"/>
                <column name="Memory" source="VM.Memory"/>
            </columns>
        </section>
    </sections>
</template>
```

#### Template Usage
```powershell
Export-RVToolsReport -Data $data -Template "VM Inventory" -OutputPath "C:\Reports\VMInventory.xlsx"
```

## Automation API

### Scheduled Operations

#### Create Schedule
```powershell
New-RVToolsSchedule -Name "Daily Report" -Frequency "Daily" -Time "06:00" -Action {
    $connection = Connect-RVTools -Server "vcenter.company.com" -User "administrator" -Password "password123"
    $data = Get-RVToolsData -Connection $connection -DataType "All"
    Export-RVToolsReport -Data $data -OutputPath "C:\Reports\DailyReport_$(Get-Date -Format 'yyyyMMdd').xlsx"
}
```

#### Manage Schedules
```powershell
Get-RVToolsSchedule -Name "Daily Report"
Set-RVToolsSchedule -Name "Daily Report" -Frequency "Weekly" -Time "06:00"
Remove-RVToolsSchedule -Name "Daily Report"
```

### Event-Driven Automation

#### Event Triggers
```powershell
Register-RVToolsEvent -Event "VM.PowerState.Changed" -Action {
    $vm = $Event.SourceEventArgs.VM
    if ($vm.PowerState -eq "PoweredOn") {
        Write-Host "VM $($vm.Name) has been powered on"
    }
}
```

#### Custom Events
```powershell
New-RVToolsEvent -Name "HighCPUUsage" -Condition "Host.CpuUsage -gt 80" -Action {
    $host = $Event.SourceEventArgs.Host
    Send-MailMessage -To "admin@company.com" -Subject "High CPU Usage Alert" -Body "Host $($host.Name) has high CPU usage"
}
```

## Error Handling

### Error Codes

#### Connection Errors
- `CONN_001`: Connection timeout
- `CONN_002`: Authentication failed
- `CONN_003`: Server not found
- `CONN_004`: SSL certificate error
- `CONN_005`: Network unreachable

#### Scan Errors
- `SCAN_001`: Scan timeout
- `SCAN_002`: Insufficient permissions
- `SCAN_003`: Object not found
- `SCAN_004`: Data collection failed
- `SCAN_005`: Memory limit exceeded

#### Export Errors
- `EXPT_001`: Export failed
- `EXPT_002`: Invalid format
- `EXPT_003`: File access denied
- `EXPT_004`: Template not found
- `EXPT_005`: Data validation failed

### Error Handling Examples

#### PowerShell Error Handling
```powershell
try {
    $connection = Connect-RVTools -Server "vcenter.company.com" -User "administrator" -Password "password123"
    $data = Get-RVToolsData -Connection $connection -DataType "VM"
    Export-RVToolsReport -Data $data -OutputPath "C:\Reports\Report.xlsx"
}
catch [RVTools.ConnectionException] {
    Write-Error "Connection failed: $($_.Exception.Message)"
}
catch [RVTools.ScanException] {
    Write-Error "Scan failed: $($_.Exception.Message)"
}
catch [RVTools.ExportException] {
    Write-Error "Export failed: $($_.Exception.Message)"
}
catch {
    Write-Error "Unexpected error: $($_.Exception.Message)"
}
```

#### REST API Error Handling
```python
import requests
from requests.exceptions import RequestException

try:
    response = requests.post('https://rvtools.company.com/api/v1/connections', json=data)
    response.raise_for_status()
    connection = response.json()
except requests.exceptions.ConnectionError:
    print("Connection error: Unable to connect to RVTools API")
except requests.exceptions.HTTPError as e:
    print(f"HTTP error: {e.response.status_code} - {e.response.text}")
except RequestException as e:
    print(f"Request error: {e}")
```

## Examples

### Complete Automation Script
```powershell
# RVTools Automation Script
param(
    [string]$vCenterServer = "vcenter.company.com",
    [string]$username = "administrator",
    [string]$password = "password123",
    [string]$outputPath = "C:\Reports"
)

try {
    # Connect to vCenter Server
    Write-Host "Connecting to vCenter Server: $vCenterServer"
    $connection = Connect-RVTools -Server $vCenterServer -User $username -Password $password
    
    # Get VM data
    Write-Host "Collecting VM data..."
    $vms = Get-RVToolsData -Connection $connection -DataType "VM"
    
    # Get host data
    Write-Host "Collecting host data..."
    $hosts = Get-RVToolsData -Connection $connection -DataType "Host"
    
    # Get datastore data
    Write-Host "Collecting datastore data..."
    $datastores = Get-RVToolsData -Connection $connection -DataType "Datastore"
    
    # Create comprehensive report
    Write-Host "Creating comprehensive report..."
    $reportData = @{
        VMs = $vms
        Hosts = $hosts
        Datastores = $datastores
        ReportDate = Get-Date
        vCenterServer = $vCenterServer
    }
    
    # Export to Excel
    $reportPath = Join-Path $outputPath "ComprehensiveReport_$(Get-Date -Format 'yyyyMMdd_HHmmss').xlsx"
    Export-RVToolsReport -Data $reportData -OutputPath $reportPath -Format "Excel"
    
    Write-Host "Report created successfully: $reportPath"
}
catch {
    Write-Error "Script failed: $($_.Exception.Message)"
    exit 1
}
```

### REST API Integration Example
```python
import requests
import json
from datetime import datetime

class RVToolsAPI:
    def __init__(self, base_url, username, password):
        self.base_url = base_url
        self.username = username
        self.password = password
        self.connection_id = None
    
    def connect(self, server):
        """Connect to vCenter Server"""
        url = f"{self.base_url}/api/v1/connections"
        data = {
            "server": server,
            "user": self.username,
            "password": self.password
        }
        response = requests.post(url, json=data)
        response.raise_for_status()
        self.connection_id = response.json()["connectionId"]
        return response.json()
    
    def start_scan(self, scope="All"):
        """Start a new scan"""
        url = f"{self.base_url}/api/v1/scans"
        data = {
            "connectionId": self.connection_id,
            "scope": scope
        }
        response = requests.post(url, json=data)
        response.raise_for_status()
        return response.json()
    
    def get_data(self, data_type, filter=None):
        """Get data from scan"""
        url = f"{self.base_url}/api/v1/data/{self.connection_id}/{data_type}"
        params = {}
        if filter:
            params["filter"] = filter
        response = requests.get(url, params=params)
        response.raise_for_status()
        return response.json()
    
    def export_report(self, data, output_path, format="Excel"):
        """Export data to file"""
        url = f"{self.base_url}/api/v1/export"
        data = {
            "connectionId": self.connection_id,
            "data": data,
            "outputPath": output_path,
            "format": format
        }
        response = requests.post(url, json=data)
        response.raise_for_status()
        return response.json()

# Usage example
if __name__ == "__main__":
    # Initialize API
    api = RVToolsAPI("https://rvtools.company.com", "administrator", "password123")
    
    # Connect to vCenter Server
    connection = api.connect("vcenter.company.com")
    print(f"Connected to vCenter Server: {connection['server']}")
    
    # Start scan
    scan = api.start_scan("All")
    print(f"Scan started: {scan['scanId']}")
    
    # Get VM data
    vm_data = api.get_data("VM", "PowerState=PoweredOn")
    print(f"Found {vm_data['count']} powered-on VMs")
    
    # Export report
    report_path = f"C:\\Reports\\VMReport_{datetime.now().strftime('%Y%m%d_%H%M%S')}.xlsx"
    export_result = api.export_report(vm_data, report_path, "Excel")
    print(f"Report exported to: {export_result['outputPath']}")
```

## Reference Tables

### Data Types

| Data Type | Description | Key Fields |
|-----------|-------------|------------|
| VM | Virtual Machine | Name, PowerState, CPU, Memory, GuestOS |
| Host | ESXi Host | Name, ConnectionState, CPU, Memory, Version |
| Datastore | Storage | Name, Type, Capacity, FreeSpace, UsedSpace |
| Network | Network | Name, Type, VLAN, Ports, Uplinks |
| Cluster | Cluster | Name, HA, DRS, DPM, ResourcePools |
| ResourcePool | Resource Pool | Name, CPU, Memory, Shares, Limits |

### Output Formats

| Format | Extension | Description |
|--------|-----------|-------------|
| Excel | .xlsx | Full functionality with formulas and charts |
| CSV | .csv | Comma-separated values |
| XML | .xml | Extensible markup language |
| JSON | .json | JavaScript object notation |
| PDF | .pdf | Portable document format |

### Error Codes

| Code | Category | Description |
|------|----------|-------------|
| CONN_001 | Connection | Connection timeout |
| CONN_002 | Connection | Authentication failed |
| SCAN_001 | Scan | Scan timeout |
| SCAN_002 | Scan | Insufficient permissions |
| EXPT_001 | Export | Export failed |
| EXPT_002 | Export | Invalid format |

---

**This API reference provides comprehensive documentation for all RVTools APIs. For additional support, refer to the official documentation or contact Dell Technologies support.**