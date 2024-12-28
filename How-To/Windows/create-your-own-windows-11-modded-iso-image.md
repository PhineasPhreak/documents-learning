# Create your own Windows 11 modded ISO Image
Deployment Image Servicing and Management [DISM.exe](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/what-is-dism?view=windows-11) is a command-line tool that can be used to service and prepare Windows images, including those used for [Windows PE](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/winpe-intro?view=windows-11), [Windows Recovery Environment (Windows RE)](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-recovery-environment--windows-re--technical-reference?view=windows-11) and [Windows Setup](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-setup-technical-reference?view=windows-11). DISM can be used to service a Windows image (.wim) or a virtual hard disk (.vhd or .vhdx).

DISM comes built into Windows and is available through the command line or from Windows PowerShell. To learn more about using DISM with PowerShell, see [Deployment Imaging Servicing Management (DISM) Cmdlets in Windows PowerShell](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/use-dism-in-windows-powershell-s14?view=windows-11).

---

For a GUI :
- [NTLite](https://www.ntlite.com/) - Local control for updating and editing Windows images and deployments. For IT professionals and enthusiasts

Reference :
- [xdaforums](https://xdaforums.com/t/create-your-own-windows-11-modded-iso-image.4405885/)
- [Video Youtube](https://www.youtube.com/watch?v=_H9kiTCDmeQ)
- [Create Windows 10 ISO image from Existing Installation](https://www.tenforums.com/tutorials/72031-create-windows-10-iso-image-existing-installation.html)

Reference Microsoft :
- [Add or Remove Packages Offline Using DISM](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/add-or-remove-packages-offline-using-dism?view=windows-11)

## 1. `/Get-ImageInfo` Displays information about the images that are contained in a .wim, .ffu, .vhd or .vhdx file
Displays information about the images that are contained in a .wim, .ffu, .vhd or .vhdx file. When used with the /Index or /Name argument, information about the specified image is displayed, which includes if an image is a WIMBoot image, if the image is Windows 8.1, see [Take Inventory of an Image or Component Using DISM](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/take-inventory-of-an-image-or-component-using-dism?view=windows-11). The /Name argument does not apply to VHD files. You must specify /Index:1 for FFU and VHDX files.

```console
dism /Get-ImageInfo /ImageFile:C:\test\images\install.wim
```

## 2. `/Mount-Image` Mounts an image from a .ffu, .wim, .vhd or .vhdx file
Mounts an image from a .ffu, .wim, .vhd or .vhdx file to the specified directory so that it is available for servicing.

When mounting an image, note the following:

- The mount directory must be created, but empty.
- An index or name value is required for all image types. WIMs can contain more than image. For FFU and VHD, use `index:6`.

```console
dism /Mount-Image /ImageFile:C:\test\images\install.wim /Index:6 /MountDir:C:\test\offline /CheckIntegrity
```

## 3. (Optional) `/Set-Product-Key` Sets the product key for the Windows image
[Sets the product key](https://learn.microsoft.com/en-us/powershell/module/dism/set-windowsproductkey?view=windowsserver2025-ps) for the Windows image. (Optional)

- Using Dism command :
```console
dism /Image:C:\test\offline /Set-ProductKey:<COPY_KEY_HERE>
```

- Using Powershell command :
```powershell
Set-WindowsProductKey -Path "C:\test\offline" -ProductKey "12345-12345-12345-12345-12345"
```
## 4. `/Get-Packages` DISM App Package

Displays basic information about all packages in the image. Use the `/Format:Table` or `/Format:List` argument to display the output as a table or a list.

```console
dism /Image:C:\test\offline /Get-Packages
dism /Image:C:\test\offline /Get-Packages > get-packages.txt
```

Adds one or more app packages to the image.

The app will be added to the Windows image and registered for each existing or new user profile the next time the user logs in. If the app is added to an online image, the app will not be registered for the current user until the next time the user logs in.

Provision apps on an online operating system in Audit mode so that appropriate hard links can be created for apps that contain the exact same files (to minimize disk space usage) while also ensuring no apps are running for a successful installation.

```console
dism /Image:C:\test\offline /Get-ProvisionedAppxPackages
dism /Image:C:\test\offline /Get-ProvisionedAppxPackages > get-provisionedappxpackages.txt
```

## 5. `/Add-Package` Installs a specified .cab or .msu package in the image.
Installs a specified .cab or .msu package in the image.

> [!NOTE]
> You can use `/Add-Package` to add an .msu package to an online or offline Windows 11, version 21H2, or later image. If you're working with a Windows image prior to Windows 11, version 21H2, you can only add .msu packages on offline target images.

- `/PackagePath` can point to:
 - A single .cab or .msu file.
 - A folder that contains a single expanded .cab file.
 - A folder that contains a single .msu file.
 - A folder that contains multiple .cab or .msu files.


- If `/PackagePath` points to a folder that contains a .cab or .msu files at its root, any subfolders will also be recursively checked for .cab and .msu files.

> [!NOTE]
> `/Add-Package` doesn't run a full check for a package's applicability and dependencies:
>
> - If you're adding a package with dependencies, make sure that all dependencies are installed when you add the package.
> - If you're adding an .msu, be sure to check the associated KB for any package-specific installation instructions.

```
dism /Image:C:\test\offline /Add-Package /PackagePath:C:\packages\package.msu
```

## 6. `/Remove-Package` Removes a specified .cab file package from the image
Removes a specified .cab file package from the image. Only .cab files can be specified. You cannot use this command to remove .msu files.

> [!NOTE]
> Using this command to remove a package from an offline image will not reduce the image size.

1. Search Windows App Store : [Windows_App_Store](https://apps.microsoft.com/)
2. Find the ID
   1. [Store_rg_adguard.net](https://store.rg-adguard.net/)
   2. Paste the link you copy from App Store in `store.rg-adguard.net` link bar, select `Retail` and search links.

```console
dism /Image:C:\test\offline /Remove-Package /PackageName:Microsoft.Windows.Calc.Demo~6595b6144ccf1df~x86~en~1.0.0.0 /PackageName:Microsoft-Windows-MediaPlayer-Package~31bf3856ad364e35~x86~~6.1.6801.0
```

```console
DISM /Image:C:\test\offline /Remove-provisionedappxpackage /PackageName:Microsoft.Windows.Calc.Demo~6595b6144ccf1df~x86~en~1.0.0.0 /PackageName:Microsoft-Windows-MediaPlayer-Package~31bf3856ad364e35~x86~~6.1.6801.0
```

## 7. Removing Microsoft Edge
To remove Microsoft Edge, we need to run Powershell in Administrator mode.

```powershell
Remove-Item -Path "C:\win-offline\Program Files (x86)\Microsoft\Edge" -Recurse -Force >null
Remove-Item -Path "C:\win-offline\Program Files (x86)\Microsoft\EdgeUpdate" -Recurse -Force >null
Remove-Item -Path "C:\win-offline\Program Files (x86)\Microsoft\EdgeCore" -Recurse -Force >null
Remove-Item -Path "C:\win-offline\Windows\System32\Microsoft-Edge-Webview" -Recurse -Force >null
```

## 8. Removing OneDrive
Same as of Microsoft Edge, change Administrator rights to delete the file.

```powershell
Remove-Item -Path "C:\win-offline\Windows\System32\OneDriveSetup.exe" -Force >null
```

## 9. (Optional) `/Cleanup-Image` or `/Cleanup-Mountpoints`
- `/Cleanup-Image`, See [Learn /Cleanup-Image](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-operating-system-package-servicing-command-line-options?view=windows-11#cleanup-image)

    Performs cleanup or recovery operations on the image. `/AnalyzeComponentStore` and `/ResetBase` can be used with Windows 10, Windows 8.1, and Windows PE images above 5.0. Beginning with Windows 10, version 1607, you can specify `/Defer` with `/ResetBase`, but you should only use `/Defer` as an option in the factory where DISM `/Resetbase` requires more than 30 minutes to complete.

- `/Cleanup-Mountpoints`, See [Learn /Cleanup-Mountpoints](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-image-management-command-line-options-s14?view=windows-11#cleanup-mountpoints)

  Deletes all of the resources associated with a mounted image that has been corrupted. This command will not unmount images that are already mounted, nor will it delete images that can be recovered using the `/Remount-Image` command.
  ```console
  dism /Cleanup-Mountpoints
  ```
  To learn more, see [Repair a Windows Image](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/repair-a-windows-image?view=windows-11)

## 10. (Optional) `/Commit-Image` Applies the changes that you have made to the mounted image
Applies the changes that you have made to the mounted image. The image remains mounted until the /Unmount-Image option is used.

```console
dism /Commit-Image /MountDir:C:\test\offline /CheckIntegrity
```
> [!NOTE]
> - /ChackIntegrity
>
>   Detects and tracks .wim file corruption when used with capture, unmount, export, and commit operations. /CheckIntegrity stops the operation if DISM detects that the .wim file is corrupted when used with apply and mount operations.

## 11. `/Unmount-Image` Unmounts the .ffu, .wim, .vhd or .vhdx file
Unmounts the .ffu, .wim, .vhd or .vhdx file and either commits or discards the changes that were made when the image was mounted.

You must use either the `/commit` or `/discard` argument when you use the `/Unmount-Image` option.

```console
dism /Unmount-Image /MountDir:C:\test\offline /CheckIntegrity /Commit
```

You can also use powershell to unmount the image.
```powershell
powershell Dismount-WindowsImage -CheckIntegrity -Path "C:\test\offline" -Save
```

## 12. (Optional) `/Export-Image` Exports a copy of the specified image to another file
[Exports a copy](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-image-management-command-line-options-s14?view=windows-11#export-image) of the specified image to another file. The source and destination files must use the same compression type. You can also optimize an image by exporting to a new image file. When you modify an image, DISM stores additional resource files that increase the overall size of the image. Exporting the image will remove unnecessary resource files.

This command-line option does not apply to virtual hard disk (VHD) files.

```console
dism /Export-Image /SourceImageFile:C:\test\offline\sources\install.wim /SourceIndex:6 /DestinationImageFile:C:\test\offline\sources\install2.wim '/compress:recovery'
```

## 13. `The Windows Assessement and Deployment Kit` Download and install the Windows ADK
The Windows Assessment and Deployment Kit (Windows ADK) and Windows PE add-on has the tools you need to customize Windows images for large-scale deployment, and to test the quality and performance of your system, its added components, and the applications running on it.

Download this tool, extract and execute : [adksetup.zip](https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install)

**You need to install The Windows Assessement and Deployment Kit for using the tool oscdimg.exe**

[Oscdimg is a command-line tool](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/oscdimg-command-line-options?view=windows-11) that you can use to create an image (.iso) file of a customized 32-bit or 64-bit version of Windows Preinstallation Environment (Windows PE). You can then burn the .iso file to a CD or DVD. Oscdimg supports ISO 9660, Joliet, and Universal Disk Format (UDF) file systems.

After you execute it, in selection window of tools, only select Deployment Tools like image bellow and install.

## 14. Build ISO `oscdimg.exe`
```console
oscdimg.exe -m -o -u2 -udfver102 -bootdata:2#p0,e,bd:\iso_files\boot\etfsboot.com#pEF,e,bd:\iso_files\efi\microsoft\boot\efisys.bin d:\iso_files d:\Win11-PRO-x64_NoApps_NoEdge_NoOneDrive.iso
```

**Is Done! Your Image is ready.**
