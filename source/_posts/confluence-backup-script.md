---
title: Confluence备份脚本
date: 2017-12-25 14:18:46
tags: [confluence, backup, powershell, git]
---

# Powershell脚本
start.ps1:
``` powershell
$script = "backup_script.ps1"
[string]$dir = Get-Location
$logfolder = $dir + '\logs\'
$logtime = Get-Date -Format yyyyMMdd-HHmmss
$logfile = $logfolder + $logtime + ".log"

if (!(Test-Path $logfolder)) {
    New-Item -ItemType Directory -Path $logfolder
}
powershell -f $script $logtime | Out-File $logfile
```

backup_script.ps1:
``` powershell 
$logtime = $args
[string]$dir = Get-Location
$logfolder = $dir + '\logs\'
$logfile = $logfolder + $logtime + '-git' + '.log'
Write-output $logfile
Write-Output "### Backup Start Up ####"
Write-Output "### Init Parameters ####"
Write-Output "### Excute time: $(Get-Date) ####"
Write-Output ""

$app_location = 'C:\Program Files\Atlassian\Confluence\'
$data_location = 'D:\CONFLUENCE_DATA\'
$dbdump_location = 'D:\postgresql_dump\'

Write-Output "Confluence app localtion: $app_location"
Write-Output "Confluence data location: $data_location"
Write-Output "Confluence dbdump location: $dbdump_location"
Write-Output ""

$app_repos = @{
    local     = 'D:\GitRepo\Confluence\application\';
    bitbucket = 'git@bitbucket.org:bjebc/confluence-app.git'
}
$data_repos = @{
    local     = 'D:\GitRepo\Confluence\data\';
    bitbucket = 'git@bitbucket.org:bjebc/confluence-data.git'
}
$dbdump_repos = @{
    local     = 'D:\GitRepo\Confluence\database\';
    bitbucket = 'git@bitbucket.org:bjebc/confluence-database.git'
}
Write-Output "Conflunece app repos:"
Write-Output $app_repos
Write-Output "Confluence data repo:"
Write-Output $data_repos
Write-Output "Confluence dbdump repo:"
Write-Output $dbdump_repos
Write-Output ""

# check if confluence app folder has got a .git folder, init git if not
if (!(Test-Path -PATH ($app_location + '.git'))) {
    Write-Output "### Init Git Forlders $app_location ####"
    Set-Location $app_location
    git init >> $logfile
    foreach ($remote in $app_repos.Keys) {
        if ($remote -eq "local") {
            if (!(Test-Path ($app_repos["local"] + 'HEAD'))) {
                if (!(Test-Path $app_repos["local"])) {
                    New-Item -Type Directory -Path $app_repos["local"]
                }
                git init --bare $app_repos["local"] >> $logfile
            }
        }
        git remote add $remote $app_repos[$remote] >> $logfile
    }
}

# check if confluence data folder has got a .git folder, init git if not
if (!(Test-Path -Path ($data_location + '.git'))) {
    Write-Output "### Init Git Forlders $data_location ####"
    Set-Location $data_location
    git init >> $logfile
    foreach ($remote in $data_repos.Keys) {
        if ($remote -eq "local") {
            if (!(Test-Path ($data_repos["local"] + 'HEAD'))) {
                if (!(Test-Path $data_repos["local"])) {
                    New-Item -Type Directory -Path $data_repos["local"]
                }
                git init --bare $data_repos["local"] >> $logfile
            }
        }
        git remote add $remote $data_repos[$remote] >> $logfile
    }
}

# check if confluence dbdump folder has got a .git folder, init git if not
if (!(Test-Path -Path ($dbdump_location + '.git'))) {
    if (!(Test-Path -Path $dbdump_location)) {
        New-Item -Type Directory -Path $dbdump_location
    }
    Write-Output "### Init Git Forlders $dbdump_location ####"
    Set-Location $dbdump_location
    git init $dbdump_location >> $logfile
    foreach ($remote in $dbdump_repos.Keys) {
        if ($remote -eq "local") {
            if (!(Test-Path ($dbdump_repos["local"] + 'HEAD'))) {
                if (!(Test-Path $dbdump_repos["local"])) {
                    New-Item -Type Directory -Path $dbdump_repos["local"]
                }
                git init --bare $dbdump_repos["local"] >> $logfile
            }
        }
        git remote add $remote $dbdump_repos[$remote] >> $logfile
    }
}
Write-Output "### Stop Confluence Service ####"
Stop-Service Confluence121217132300
Write-Output ""

# dump database
$time = Get-Date -Format yyyyMMdd-HHmmss
pg_dump -d CONFLUENCE -U postgres -F t -f "d:\postgresql_dump\$time.tar"

# commit and push conflunece app changes
Set-Location $app_location
git add . >> $logfile
git commit -m 'Timed script commit.' >> $logfile
foreach ($remote in $app_repos.Keys) {
    git push $remote master >> $logfile
}

# commit and push confluence data changes
Set-Location $data_location
git add . >> $logfile
git commit  -m 'Timed script commit.' >> $logfile
foreach ($remote in $data_repos.Keys) {
    git push $remote master >> $logfile
}

# commit and push dbdump changes
Set-Location $dbdump_location
git add . >> $logfile
git commit -a -m 'Timed script commit.' >> $logfile
foreach ($remote in $dbdump_repos.Keys) {
    git push $remote master >> $logfile
}

# start confluence service 
Start-service Confluence121217132300

Write-Output '### Backup Complete ####'
```

# .gitignore
## Confluence应用目录
``` .gitignore
*temp/
*logs/
```
## Confluence_Data目录
``` .gitignore
*temp/
*logs/
*cache/
*.logs
```

# Postgresql数据库导出与导入
## plain text
``` bash
pg_dump -U CONFLUENCE -f /$time.sql -d CONFLUENCE
psql -U CONFLUENCE -f /20171225-120003.sql CONFLUENCE
```
## *.tar
``` bash
pg_dump -U postgres -F t -f /$time.tar -d CONFLUENCE
pg_restore -U postgres -d CONFLUENCE /20171225-120003.tar
```
# Windows计划任务配置
``` XML
<?xml version="1.0" encoding="UTF-16"?>
<Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
  <RegistrationInfo>
    <Date>2017-12-24T15:38:54.5916575</Date>
    <Author>HF-DEV01\Administrator</Author>
    <URI>\Backup\Conflunece Backup</URI>
  </RegistrationInfo>
  <Triggers>
    <CalendarTrigger>
      <StartBoundary>2017-12-24T12:00:00+08:00</StartBoundary>
      <Enabled>true</Enabled>
      <ScheduleByDay>
        <DaysInterval>1</DaysInterval>
      </ScheduleByDay>
    </CalendarTrigger>
  </Triggers>
  <Principals>
    <Principal id="Author">
      <UserId>S-1-5-21-938857123-2463988143-3464861971-500</UserId>
      <LogonType>InteractiveToken</LogonType>
      <RunLevel>LeastPrivilege</RunLevel>
    </Principal>
  </Principals>
  <Settings>
    <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
    <DisallowStartIfOnBatteries>true</DisallowStartIfOnBatteries>
    <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
    <AllowHardTerminate>true</AllowHardTerminate>
    <StartWhenAvailable>false</StartWhenAvailable>
    <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
    <IdleSettings>
      <StopOnIdleEnd>true</StopOnIdleEnd>
      <RestartOnIdle>false</RestartOnIdle>
    </IdleSettings>
    <AllowStartOnDemand>true</AllowStartOnDemand>
    <Enabled>true</Enabled>
    <Hidden>false</Hidden>
    <RunOnlyIfIdle>false</RunOnlyIfIdle>
    <WakeToRun>false</WakeToRun>
    <ExecutionTimeLimit>PT72H</ExecutionTimeLimit>
    <Priority>7</Priority>
  </Settings>
  <Actions Context="Author">
    <Exec>
      <Command>powershell</Command>
      <Arguments>-f start.ps1</Arguments>
      <WorkingDirectory>D:\CONFLUENCE_DATA\confluence_backup_script</WorkingDirectory>
    </Exec>
  </Actions>
</Task>
```