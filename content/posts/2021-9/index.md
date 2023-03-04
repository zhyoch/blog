---
title: "设置CMD和PowerShell中的Alias"
date: 2021-03-21T20:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [CMD,PowerShell,Windows]
featuredImagePreview: ""
summary: 使用`DOSKEY`设置CMD中的Alias, 使用`New-Alias`设置PowerShell中的Alias, 将设置Alias的命令保存在预加载文件中来达到别名持久化。
---

## CMD

### DOSKEY

使用`DOSKEY`命令, 比如: 

```shell
DOSKEY ls=dir /p $*
```

{{< admonition type=warning title="注意" open=true >}}
1. `$*`是需要的, 用来传递参数。
2. 在当前窗口设置的别名只在当前窗口有效。
{{< /admonition >}}

### 持久化

参考[这里](https://zhyoch.netlify.app/2020-1/#永久代理), 将自定义的`DOSKEY`命令写入`cmd_init.cmd`文件中, 比如: 

```shell
@echo off

:: Commands Aliases

DOSKEY aria2c=aria2c -c -s16 -x16 -k1M  $*
DOSKEY cd=cd /d $*
DOSKEY clear=cls
DOSKEY cp=xcopy /e /h /k $*
DOSKEY ls=dir /p $*
DOSKEY mv=move $*
```

## PowerShell

### Set-Alias

参考微软官方文档[Set-Alias](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/set-alias), 使用`Set-Alias`命令设置新的别名或修改已有别名: 

```shell
Set-Alias -Name 别名 -Value 原命令
```

比如: 

```shell
Set-Alias -Name aria2c -Value "aria2c -c -s16 -x16 -k1M"
```

> 也可以直接使用`Set-Alias`的别名`sal`。

### Get-Alias

使用`Get-Alias`命令查看所有别名, 也可以直接使用`Get-Alias`的别名`gal`。

### New-Alias

参考微软官方文档[about_Aliases](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_aliases#creating-an-alias), 使用`New-Alias`命令设置新的别名, 如果别名已存在会提示重复: 

```shell
New-Alias -Name 别名 -Value 原命令
```

> 也可以直接使用`Newt-Alias`的别名`nal`。

{{< admonition type=warning title="注意" open=true >}}
在当前窗口设置的别名只在当前窗口有效。
{{< /admonition >}}

### 持久化

不推荐使用[Export-Alias](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/export-alias)和[Import-Alias](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/import-alias), 太不方便了, 直接参考[这里](https://zhyoch.netlify.app/2020-1/#永久代理-1), 将自定义的`Set-Alias`命令写入`PROFILE`文件中, 比如: 

```shell
Set-Alias -Name aria2c -Value "aria2c -c -s16 -x16 -k1M"
```

其他别名也没什么好写的, 毕竟`PowerShell`中已存在的别名都是挺常用的了: 

{{< admonition type=note title="Get-Alias的输出" open=false >}}
% -> ForEach-Object                     
? -> Where-Object                       
ac -> Add-Content                       
asnp -> Add-PSSnapin                    
cat -> Get-Content                      
cd -> Set-Location                      
CFS -> ConvertFrom-String               
chdir -> Set-Location                   
clc -> Clear-Content                    
clear -> Clear-Host                     
clhy -> Clear-History                   
cli -> Clear-Item                       
clp -> Clear-ItemProperty               
cls -> Clear-Host                       
clv -> Clear-Variable                   
cnsn -> Connect-PSSession               
compare -> Compare-Object               
copy -> Copy-Item                       
cp -> Copy-Item                         
cpi -> Copy-Item                        
cpp -> Copy-ItemProperty                
curl -> Invoke-WebRequest               
cvpa -> Convert-Path                    
dbp -> Disable-PSBreakpoint             
del -> Remove-Item                      
diff -> Compare-Object                  
dir -> Get-ChildItem                    
dnsn -> Disconnect-PSSession            
ebp -> Enable-PSBreakpoint              
echo -> Write-Output                    
epal -> Export-Alias                    
epcsv -> Export-Csv                     
epsn -> Export-PSSession                
erase -> Remove-Item                    
etsn -> Enter-PSSession                 
exsn -> Exit-PSSession                  
fc -> Format-Custom                     
fhx -> Format-Hex                       
fl -> Format-List                       
foreach -> ForEach-Object               
ft -> Format-Table                      
fw -> Format-Wide                       
gal -> Get-Alias                        
gbp -> Get-PSBreakpoint                 
gc -> Get-Content                       
gcb -> Get-Clipboard                    
gci -> Get-ChildItem                    
gcm -> Get-Command                      
gcs -> Get-PSCallStack                  
gdr -> Get-PSDrive                      
ghy -> Get-History                      
gi -> Get-Item                          
gin -> Get-ComputerInfo                 
gjb -> Get-Job                          
gl -> Get-Location                      
gm -> Get-Member                        
gmo -> Get-Module                       
gp -> Get-ItemProperty                  
gps -> Get-Process                      
gpv -> Get-ItemPropertyValue            
group -> Group-Object                   
gsn -> Get-PSSession                    
gsnp -> Get-PSSnapin                    
gsv -> Get-Service                      
gtz -> Get-TimeZone                     
gu -> Get-Unique                        
gv -> Get-Variable                      
gwmi -> Get-WmiObject                   
h -> Get-History                        
history -> Get-History                  
icm -> Invoke-Command                   
iex -> Invoke-Expression                
ihy -> Invoke-History                   
ii -> Invoke-Item                       
ipal -> Import-Alias                    
ipcsv -> Import-Csv                     
ipmo -> Import-Module                   
ipsn -> Import-PSSession                
irm -> Invoke-RestMethod                
ise -> powershell_ise.exe               
iwmi -> Invoke-WmiMethod                
iwr -> Invoke-WebRequest                
kill -> Stop-Process                    
lp -> Out-Printer                       
ls -> Get-ChildItem                     
man -> help                             
md -> mkdir                             
measure -> Measure-Object               
mi -> Move-Item                         
mount -> New-PSDrive                    
move -> Move-Item                       
mp -> Move-ItemProperty                 
mv -> Move-Item                         
nal -> New-Alias                        
ndr -> New-PSDrive                      
ni -> New-Item                          
nmo -> New-Module                       
npssc -> New-PSSessionConfigurationFile 
nsn -> New-PSSession                    
nv -> New-Variable                      
ogv -> Out-GridView                     
oh -> Out-Host                          
popd -> Pop-Location                    
ps -> Get-Process                       
pushd -> Push-Location                  
pwd -> Get-Location                     
r -> Invoke-History                     
rbp -> Remove-PSBreakpoint              
rcjb -> Receive-Job                     
rcsn -> Receive-PSSession               
rd -> Remove-Item                       
rdr -> Remove-PSDrive                   
ren -> Rename-Item                      
ri -> Remove-Item                       
rjb -> Remove-Job                       
rm -> Remove-Item                       
rmdir -> Remove-Item                    
rmo -> Remove-Module                    
rni -> Rename-Item                      
rnp -> Rename-ItemProperty              
rp -> Remove-ItemProperty               
rsn -> Remove-PSSession                 
rsnp -> Remove-PSSnapin                 
rujb -> Resume-Job                      
rv -> Remove-Variable                   
rvpa -> Resolve-Path                    
rwmi -> Remove-WmiObject                
sajb -> Start-Job                       
sal -> Set-Alias                        
saps -> Start-Process                   
sasv -> Start-Service                   
sbp -> Set-PSBreakpoint                 
sc -> Set-Content                       
scb -> Set-Clipboard                    
select -> Select-Object                 
set -> Set-Variable                     
shcm -> Show-Command                    
si -> Set-Item                          
sl -> Set-Location                      
sleep -> Start-Sleep                    
sls -> Select-String                    
sort -> Sort-Object                     
sp -> Set-ItemProperty                  
spjb -> Stop-Job                        
spps -> Stop-Process                    
spsv -> Stop-Service                    
start -> Start-Process                  
stz -> Set-TimeZone                     
sujb -> Suspend-Job                     
sv -> Set-Variable                      
swmi -> Set-WmiInstance                 
tee -> Tee-Object                       
trcm -> Trace-Command                   
type -> Get-Content                     
wget -> Invoke-WebRequest               
where -> Where-Object                   
wjb -> Wait-Job                         
write -> Write-Output                   
{{< /admonition >}}
