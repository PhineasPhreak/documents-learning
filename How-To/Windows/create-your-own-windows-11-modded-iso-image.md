# Create your own Windows 11 modded ISO Image
Build your own Windows 11 ISO copy image with [DISM](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-operating-system-package-servicing-command-line-options?view=windows-11) command tool from Windows.

For a GUI :
- [NTLite](https://www.ntlite.com/) - Local control for updating and editing Windows images and deployments. For IT professionals and enthusiasts

Reference :
- [xdaforums](https://xdaforums.com/t/create-your-own-windows-11-modded-iso-image.4405885/)
- [Video Youtube](https://www.youtube.com/watch?v=_H9kiTCDmeQ)
- [Create Windows 10 ISO image from Existing Installation](https://www.tenforums.com/tutorials/72031-create-windows-10-iso-image-existing-installation.html)

Reference Microsoft :
- [Add or Remove Packages Offline Using DISM](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/add-or-remove-packages-offline-using-dism?view=windows-11)

## 1. `Preparing_and_extracting_img`
First one it show the images inside install.esd file and the option the select and work on one. **(Copy to inside the work folder)**

```batch
@echo off & @echo.

:: BatchGotAdmin
:-------------------------------------
REM  --> Check for permissions
    IF "%PROCESSOR_ARCHITECTURE%" EQU "amd64" (
>nul 2>&1 "%SYSTEMROOT%\SysWOW64\cacls.exe" "%SYSTEMROOT%\SysWOW64\config\system"
) ELSE (
>nul 2>&1 "%SYSTEMROOT%\system32\cacls.exe" "%SYSTEMROOT%\system32\config\system"
)

REM --> If error flag set, we do not have admin.
if '%errorlevel%' NEQ '0' (
    @echo. & @echo [43m[31mRequesting administrative privileges...[0m
    goto UACPrompt
    goto UACPrompt
) else ( goto gotAdmin )

:UACPrompt
    echo Set UAC = CreateObject^("Shell.Application"^) > "%temp%\getadmin.vbs"
    set params= %*
    echo UAC.ShellExecute "cmd.exe", "/c ""%~s0"" %params:"=""%", "", "runas", 1 >> "%temp%\getadmin.vbs"

    "%temp%\getadmin.vbs"
    del "%temp%\getadmin.vbs"
    exit /B

:gotAdmin
rem :To CD to the location of the batch script file (%0)
CD /d "%~dp0"

mkdir %~dp0ISO
powershell.exe Dism /Get-WimInfo /WimFile:"%~dp0sources\install.esd"
@echo. & set /p index=" Type Image Index number to extract image: "
powershell.exe Dism /export-image /SourceImageFile:"%~dp0sources\install.esd" /SourceIndex:%index% /DestinationImageFile:"%~dp0ISO\install.esd" /Compress:max /CheckIntegrity
pause
```

## 2. `Mount_Windows_image`
Second one will mount the image and ask if you want to add a windows key to image. (Copy to inside the work folder)

```batch
@echo off & @echo.

:: BatchGotAdmin
:-------------------------------------
REM  --> Check for permissions
    IF "%PROCESSOR_ARCHITECTURE%" EQU "amd64" (
>nul 2>&1 "%SYSTEMROOT%\SysWOW64\cacls.exe" "%SYSTEMROOT%\SysWOW64\config\system"
) ELSE (
>nul 2>&1 "%SYSTEMROOT%\system32\cacls.exe" "%SYSTEMROOT%\system32\config\system"
)

REM --> If error flag set, we do not have admin.
if '%errorlevel%' NEQ '0' (
    @echo. & @echo [43m[31mRequesting administrative privileges...[0m
    goto UACPrompt
    goto UACPrompt
) else ( goto gotAdmin )

:UACPrompt
    echo Set UAC = CreateObject^("Shell.Application"^) > "%temp%\getadmin.vbs"
    set params= %*
    echo UAC.ShellExecute "cmd.exe", "/c ""%~s0"" %params:"=""%", "", "runas", 1 >> "%temp%\getadmin.vbs"

    "%temp%\getadmin.vbs"
    del "%temp%\getadmin.vbs"
    exit /B

:gotAdmin
rem :To CD to the location of the batch script file (%0)
CD /d "%~dp0"
@echo. & @echo Doing backup from stock install.esd windows image to desktop.
@echo To rollbackp delete install.esd file from sources folder and paste stock install.esd file. & @echo.
mkdir %userprofile%\Desktop\install.esd_backup
move /y "%~dp0sources\install.esd" "%userprofile%\Desktop\install.esd_backup\install.esd"
move /y "%~dp0ISO\install.esd"  "%~dp0sources\install.esd"
mkdir "%~dp0PATH"
dism.exe /mount-wim /wimfile:"%~dp0sources\install.esd" /mountdir:"%~dp0PATH" /index:1 /CheckIntegrity
@echo. & @echo.

:repeat
set /p ask= " type 1 to add a key, type 0 to exite: "
if /i %ask% EQU 0 (goto exit)
if /i %ask% EQU 1 (goto key) else (goto repeat)

:key
set /p key=" Insert Windows Key here if you have it: "
dism.exe /Image:%~dp0PATH /Set-ProductKey:%key%

:exit
@echo. & @echo Press any key to exit
pause >nul
exit
```

## 3. `Enable_Disable_options`
In this one you can check the Optional Features windows state then use batch to enable or disable them, but some of them aren't possible to change state, it gives error. **(Copy to inside the work folder)**

```batch
@echo off & @echo.

:: BatchGotAdmin
:-------------------------------------
REM  --> Check for permissions
    IF "%PROCESSOR_ARCHITECTURE%" EQU "amd64" (
>nul 2>&1 "%SYSTEMROOT%\SysWOW64\cacls.exe" "%SYSTEMROOT%\SysWOW64\config\system"
) ELSE (
>nul 2>&1 "%SYSTEMROOT%\system32\cacls.exe" "%SYSTEMROOT%\system32\config\system"
)

REM --> If error flag set, we do not have admin.
if '%errorlevel%' NEQ '0' (
    @echo. & @echo [43m[31mRequesting administrative privileges...[0m
    goto UACPrompt
    goto UACPrompt
) else ( goto gotAdmin )

:UACPrompt
    echo Set UAC = CreateObject^("Shell.Application"^) > "%temp%\getadmin.vbs"
    set params= %*
    echo UAC.ShellExecute "cmd.exe", "/c ""%~s0"" %params:"=""%", "", "runas", 1 >> "%temp%\getadmin.vbs"

    "%temp%\getadmin.vbs"
    del "%temp%\getadmin.vbs"
    exit /B

:gotAdmin
rem :To CD to the location of the batch script file (%0)
CD /d "%~dp0"

:start
cls
@echo. & @echo Open WindowsOptionalFeature.txt to check Optional Feature state & @echo.
Timeout /t 3 >nul
powershell.exe Get-WindowsOptionalFeature -Path "%~dp0PATH" > WindowsOptionalFeature.txt
type WindowsOptionalFeature.txt
@echo.
set /p Feature=" Optional Feature name:-"
set /p state=" Enable or disable ? -"
powershell.exe %state%-WindowsOptionalFeature -Path "%~dp0PATH" -FeatureName "%Feature%"
@echo. & @echo Press any key to continue. & pause >nul
goto start
```

## 3.1 `Add_or_remove_Apps_to_Offline_Windows_Image`
This one Will Install or remove apps from Windows image, you can install apps from windows app Store in Windows Offline image. **(Copy to inside the work folder)**

```batch
@echo off

:: BatchGotAdmin
:-------------------------------------
REM  --> Check for permissions
    IF "%PROCESSOR_ARCHITECTURE%" EQU "amd64" (
>nul 2>&1 "%SYSTEMROOT%\SysWOW64\cacls.exe" "%SYSTEMROOT%\SysWOW64\config\system"
) ELSE (
>nul 2>&1 "%SYSTEMROOT%\system32\cacls.exe" "%SYSTEMROOT%\system32\config\system"
)

REM --> If error flag set, we do not have admin.
if '%errorlevel%' NEQ '0' (
    @echo. & @echo [43m[31mRequesting administrative privileges...[0m
    goto UACPrompt
    goto UACPrompt
) else ( goto gotAdmin )

:UACPrompt
    echo Set UAC = CreateObject^("Shell.Application"^) > "%temp%\getadmin.vbs"
    set params= %*
    echo UAC.ShellExecute "cmd.exe", "/c ""%~s0"" %params:"=""%", "", "runas", 1 >> "%temp%\getadmin.vbs"

    "%temp%\getadmin.vbs"
    del "%temp%\getadmin.vbs"
    exit /B

:gotAdmin
rem :To CD to the location of the batch script file (%0)
CD /d "%~dp0"
@echo. & @echo [43m[31mAdd apps to offline Windows image. [4m[1mImage must be mounted![0m
@echo. & @echo [41mOpenning Windows App Store...[0m & @echo Press any key to coninue... & pause >nul
start "windows_app_store" https://www.microsoft.com/pt-pt/store/apps/windows
@echo. & @echo [1mFind your app and copy link.[0m
@echo. & @echo [41mOppening https://store.rg-adguard.net/ to [1m[4mget app link to download without install it.[0m & @echo Press any key to coninue... & pause >nul
start "store_rg-adguard.net/" https://store.rg-adguard.net/
@echo. & @echo [1m[31 PPaste the link you copy from App Store in store.rg-adguard.net link bar, select Retail and search links.[0m
@echo. & @echo [1mDownload to desktop [1m[4m[31mthe right [103mappx or appxbundle[0m [31mversion that match your windows version[0m, example for Windows 64 bits download x64 [31m[103mappx or appxbundle[0m version.
@echo [33m Note: * If download doesn t show the extencion just paste the name, to check extencion do right click and check app propertys.
@echo * Some apps have more than 1 package, The app package and its dependencies like Frameworks or VClibs as example.[0m
rem Lets start...
@echo.
dism.exe /Image:%~dp0PATH /Get-ProvisionedAppxPackages > ProvisionedAppxPackages.txt
@echo. & @echo Installed packages:
type %~dp0ProvisionedAppxPackages.txt

:repeat
@echo.
Set /p option=" Type 1 to remove Appx Packages, type 2 to add Appx Packages: "
if /i %option% EQU 1 (goto remove)
if /i %option% EQU 2 (goto add) else (goto repeat)

:remove
@echo. [31m
set /p remove=" Type/paste Appx Package name here: "
Dism /Image:%~dp0PATH /Remove-ProvisionedAppxPackage /PackageName:%remove%
@echo. [0m & @echo Press any key to remove more apps!
pause >nul
goto repeat

:add
@echo.
@echo [44mAdding the Apps...[0m
@echo. [33m
set /p app=" Type/Paste the downloaded app name and extension here and press ENTER: "
@echo.
Dism /Image:%~dp0PATH /Add-ProvisionedAppxPackage /PackagePath:%userprofile%\Desktop\%app% /SkipLicense /Region:"all"
@echo. [0m & @echo Press any key to add more apps!
pause >nul
goto repeat
```

## 4. `Dismount_Windows_Image`
This one dismount image. **(Copy to inside the work folder)**

```batch
@echo off & @echo.

:: BatchGotAdmin
:-------------------------------------
REM  --> Check for permissions
    IF "%PROCESSOR_ARCHITECTURE%" EQU "amd64" (
>nul 2>&1 "%SYSTEMROOT%\SysWOW64\cacls.exe" "%SYSTEMROOT%\SysWOW64\config\system"
) ELSE (
>nul 2>&1 "%SYSTEMROOT%\system32\cacls.exe" "%SYSTEMROOT%\system32\config\system"
)

REM --> If error flag set, we do not have admin.
if '%errorlevel%' NEQ '0' (
    @echo. & @echo [43m[31mRequesting administrative privileges...[0m
    goto UACPrompt
    goto UACPrompt
) else ( goto gotAdmin )

:UACPrompt
    echo Set UAC = CreateObject^("Shell.Application"^) > "%temp%\getadmin.vbs"
    set params= %*
    echo UAC.ShellExecute "cmd.exe", "/c ""%~s0"" %params:"=""%", "", "runas", 1 >> "%temp%\getadmin.vbs"

    "%temp%\getadmin.vbs"
    del "%temp%\getadmin.vbs"
    exit /B

:gotAdmin
rem :To CD to the location of the batch script file (%0)
CD /d "%~dp0"
powershell Dismount-WindowsImage -CheckIntegrity -Path "%~dp0PATH" -Save
RD /S /Q ISO >NUL
RD /S /Q PATH >NUL
move WindowsOptionalFeature.txt %userprofile%\Desktop
move ProvisionedAppxPackages.txt %userprofile%\Desktop
pause
```

## 5. `Selection Window from Windows Assessement and Deployment Kit`
This one and the last one create a ISO image from Your Windows modded image but first you **need to Install Windows_Kits10ADK**.

Download this tool, extract and execute : [adksetup.zip](https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install)

After you execute it, in selection window of tools, only select Deployment Tools like image bellow and install.


### `Build_ISO`
```batch
@echo off
setlocal
CD /d "%~dp0"
cls
@echo. & @echo Building ISO image... & @echo.
Set /p dir=" Type here the Work dir name: " & @echo.

echo oscdimg.exe -m -oc -u2 -udfver102 -bootdata:2#p0,e,bC:\Users\perso\Desktop\%dir%\boot\etfsboot.com#pEF,e,bC:\Users\perso\Desktop\%dir%\efi\microsoft\boot\efisys.bin "C:\Users\perso\Desktop\%dir%" "C:\Users\perso\Desktop\%dir%.iso"|"%systemroot%\system32\cmd.exe" /k "%systemdrive%\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools\DandISetEnv.bat"

@echo. & @echo DONE!!!
pause
exit /b
```

**Is Done! Your Image is ready.**
