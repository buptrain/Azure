## 1st update

I started to use Azure Powershell CLI from years ago, even before AzureRM. 
Recently I finally decide to move all script to powershell core(I know it's late) but hitting so many obstacles. 

This note is to describe the best practice. 

### Procedure

# Step 1. Install .Net Core 
at [https://dotnet.microsoft.com/download]

# Step 2. Install Powershell core with preferably MSI Package*

Installing the MSI package
To install PowerShell on a Windows client or Windows Server (works on Windows 7 SP1, Server 2008 R2, and later), download the MSI package from our GitHub releases page. Scroll down to the Assets section of the Release you want to install. The Assets section may be collapsed, so you may need to click to expand it.
The MSI file looks like this - PowerShell-<version>-win-<os-arch>.msi

_I suggest not to install with dotnet command. Cuz assume you want to use Visual Studio Code with powershell extension as IDE. It has issue recognizing the latest version from dotnet tool directory. So you may end up with intellisense working on old powershell version but terminal running latest version._

# Step 3. Install Visual Studio Code with powershell extension

**Optional:**
Go to Code's settings.json, add the following settings to make the IDE more powershell friendly. Then reload.
```markdown
To make the IDE more powershell friendly

{
    
    "terminal.integrated.shell.windows": "C:\\Windows\\System32\\cmd.exe",
    "omnisharp.projectLoadTimeout": 120,
    "csharp.suppressDotnetRestoreNotification": true,
    // other settings...
    "[powershell]": {
        "editor.renderWhitespace": "all",
        "editor.renderControlCharacters": true,
        "files.trimTrailingWhitespace": true,
        "files.encoding": "utf8bom",
        "files.autoGuessEncoding": true
    },
    "workbench.colorTheme": "PowerShell ISE",
    "powershell.promptToUpdatePowerShell": true,
    "powershell.startAsLoginShell.osx": false,
    "powershell.startAutomatically": false
}


```
# Step 4. Configure Visual Studio Code to load powershell core

If you don't do it, you visual studio code may still load old powershell version, which won't have Azure SDK access you are about to install. After this step, intellisense should  work. 

	Configure default PowerShell version
	 
	Before configuring Shell Launcher, I want to set PowerShell Core as the default PowerShell version. To do so, open up the VS Code user settings.json file by clicking on file > preferences > settings, select ... and then Open settings.json.
	 
	Modify the settings.json file to include terminal.integrated.shell.windows. The update must be well formed json. If you have other settings in your settings.json file, you may need to adjust the following example.
	 
	{
    "terminal.integrated.shell.windows": "c:/Program Files/PowerShell/6/pwsh.exe"
}
	 
	Now, when opening up a terminal in Visual Studio Code, PowerShell core should be the default version.
	 
	PS C:\storage\code\azure-powershell-demos> $PSVersionTable
	Name                           Value
----                           -----
PSVersion                      6.1.0
PSEdition                      Core
GitCommitId                    6.1.0
OS                             Microsoft Windows 10.0.17134
Platform                       Win32NT
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
WSManStackVersion              3.0
	 

Reference
https://techcommunity.microsoft.com/t5/itops-talk-blog/configure-visual-studio-code-to-run-powershell-for-windows-and/ba-p/283258


# Step 5. Uninstall old Azure SDK

If you ever run earlier Azure SDK like AzureRM, please follow this step. Otherwise, skip to step 6.

The annoying part is, instead of upgrading, current Azure Powershell modules are deprecated and new modules, called AZ, is implemented. They are NOT COMPATIBLE. So I recommend cleaning up old environment and switching to new environment. 

Official Wiki explaining the decision is at:
	 https://docs.microsoft.com/en-us/powershell/azure/new-azureps-module-az?view=azps-3.5.0&viewFallbackFrom=azps-3.3.0
If you don't want to read, this is how to Uninstall Azure powershell and AzureRM module
	https://docs.microsoft.com/en-us/powershell/azure/uninstall-az-ps?view=azps-3.5.0#uninstall-the-azurerm-module
	
	In most of the case, there are garbage still left there. 
	The best way is to:
```markdown
foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique) { write-host "Removing Module $module" Uninstall-module $module }
```

# Step 5.  Install Az Modules
```markdown
Install-Module -Name Az -AllowClobber -Scope AllUsers
```
