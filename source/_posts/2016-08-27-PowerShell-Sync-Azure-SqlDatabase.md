---
title: 使用PowerShell同步Azure数据库
date: 2016-08-27 14:44:28
tags:
	- PowerShell
	- Azure
categories:
	- PowerShell
	- Azure
---
##使用PowerShell 同步Azure数据库

### 1. Azure PublishSettings文件
1. 使用Get-AzurePublishSettingsFile 获取Azure设置文件
2. 使用Import-AzurePublishSettingsFile 导入Azure设置文件
>Import-AzurePublishSettingsFile .\XXX.publishsettings

3. 选择SubScription
>Select-AzureSubscription -Name $SubscriptionName

### 2. 在当前数据库服务器拷贝一个数据库

```powershell
Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $SourceDatabaseName -PartnerDatabase $PartnerDatabaseName(copy库的名称)

#查看拷贝状态
$CheckDBStateIndex = 0
do 
{
	$CheckDBStateResult =  Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $SourceDatabaseName
	$CheckDBStateIndex++
	if($CheckDBStateIndex -gt 100)
	{
		exit 0
	}

	Start-Sleep -s 10
}
while($CheckDBStateResult.State -ne "COMPLETED")

```

### 3. 将数据库导出到Storage

```powershell
$StorageName = "StorageName"
$StorageAccessKey = Get-AzureStorageKey -StorageAccountName $StorageName | Select -ExpandProperty Primary
$StorageContext = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageAccessKey
$Container = Get-AzureStorageContainer -Name $StorageContainerForDatabase -Context $StorageContext

# Export copy database from Export
$AzureUsername = "xxxxxx"
$AzurePassword = "xxxxxx" | ConvertTo-SecureString -AsPlainText -Force
$ExportCred = New-Object System.Management.Automation.PSCredential($ExportUsername,$ExportPassword)
$ExportSqlContext = New-AzureSqlDatabaseServerContext -ServerName $ExportServerName -Credential $ExportCred

$ExportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $ExportSqlContext -DatabaseName $ExportPartnerDatabaseName -StorageContainer $Container -BlobName $BlobName

# Check export status
$CheckDBStateIndex = 0
do 
{
	$CheckExportResult = Get-AzureSqlDatabaseImportExportStatus -Request $ExportRequest 
	$CheckDBStateIndex++
	if($CheckDBStateIndex -gt 100)
	{
		exit 0
	}

	Start-Sleep -s 10
}
while($CheckExportResult.Status -ne "Completed")
```

>$StorageName 要导出的Storage的名称
>$StorageContainerForDatabase 在Storage中存储导出数据库的Container的名称，放在Blob中
>$AzureUsername Azure管理要账号用户名
>$AzurePassword Azure管理要账号密码

### 4. 从Storage中导入数据库到新的数据库服务器

```powershell
$ImportSqlContext = New-AzureSqlDatabaseServerContext -ServerName $ImportDbServerName -Credential $ExportCred
$ImportRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $ImportSqlContext -StorageContainer $Container -DatabaseName $ImportDatabaseName -BlobName $BlobName

# Check import status
$CheckDBStateIndex = 0
do 
{
	$CheckImportResult = Get-AzureSqlDatabaseImportExportStatus -Request $ImportRequest 
	$CheckDBStateIndex++
	if($CheckDBStateIndex -gt 100)
	{
		exit 0
	}

	Start-Sleep -s 10
}
while($CheckImportResult.Status -ne "Completed")
```

### 5. 重命名数据库和删除数据库

1. 重命名数据库
```powershell
Set-AzureSqlDatabase -ServerName $ServerName -DatabaseName $OriginDatabaseName -NewDatabaseName $TempDatabaseName
```

2. 删除数据库
```powershell
Remove-AzureSqlDatabase -ServerName $ServerName -DatabaseName $DatabaseName -Force
```

### 6. 执行Sql脚本

```powershell
Function ExecuteNonQuery
{
    Param ([string]$SqlText)

    try
    {
        $SQLConnection = New-Object System.Data.SqlClient.SqlConnection;
        $SQLConnection.ConnectionString = $ImportConnectionString;
        $SQLCommand = $SQLConnection.CreateCommand()
        $SQLCommand.CommandText = $SqlText
        $SQLConnection.Open();
        $ExecuteResult = $SQLCommand.ExecuteNonQuery()
        $SQLCommand.Dispose()
        $SQLConnection.Close()
        return $ExecuteResult
    }
    catch
    {
        $ErrorMessage = $_.Exception.Message
        LogWrite $ErrorMessage
        exit 0
    }
}
```

### 7. 写日志到文件中

```powershell
Function LogWrite
{
   Param ([string]$logstring)

   $LogFolder = Get-Item -Path $LogFolderPath
   if(!$LogFolder) # Has no folder
   {
       $LogFolder = New-Item Logs -type Directory
   }

   $Today = (Get-Date).ToString('yyyy-MM-dd') + ".log"
   $LogFile = Get-ChildItem $LogFolder.FullName -Depth 0 -Recurse | ?{ $_.Name -eq $Today }
   if(!$LogFile) # Has no file
   {
     $LogFileName = $LogFolder.FullName + "\" + $Today.ToString()
     $LogFile = New-Item $LogFileName -type file -Force
   }

   $logstring = (Get-Date).ToString('yyyy-MM-dd HH:mm') + ": " + $logstring
   $logstring | Out-File -Append -Encoding utf8 $Logfile.FullName 
   Write-Host $logstring
}
```


