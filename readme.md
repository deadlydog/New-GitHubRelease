# New-GitHubRelease PowerShell Module

A PowerShell module to make automating the creation of new GitHub releases easy.

## Getting Started

You can either download the `New-GitHubRelease.psm1` file from the [Releases page][GitHubRepoReleasesPage] directly, or [install the module from the PowerShell Gallery][PowerShellGalleryNuGetPackagePage].

Here is an example of how to import the New-GitHubRelease module into your powershell session and call it:

```PowerShell
Import-Module -Name "C:\PathTo\New-GitHubRelease.psm1"
New-GitHubRelease -GitHubUsername 'deadlydog' -GitHubRepositoryName 'New-GitHubRelease' -GitHubAccessToken 'SomeLongHexidecimalString' -TagName 'v1.0.0'
```

A hash table with the following properties is returned:

* Succeeded = $true if the Release was created successfully and all assets were uploaded to it, $false if some part of the process failed.
* ReleaseCreationSucceeded = $true if the Release was created successfully (does not include asset uploads), $false if the Release was not created.
* AllAssetUploadsSucceeded = $true if all assets were uploaded to the Release successfully, $false if one of them failed, $null if there were no assets to upload.
* ReleaseUrl = The URL of the new Release that was created.
* ErrorMessage = A message describing what went wrong in the case that Succeeded is $false.

## Examples

```PowerShell
$gitHubReleaseParameters =
@{
    GitHubUsername = 'deadlydog'
    GitHubRepositoryName = 'New-GitHubRelease'
    GitHubAccessToken = 'SomeLongHexidecimalString'
    ReleaseName = "New-GitHubRelease v1.0.0"
    TagName = "v1.0.0"
    ReleaseNotes = "This release contains the following changes: ..."
    AssetFilePaths = @('C:\MyProject\Installer.exe','C:\MyProject\Documentation.md')
    IsPreRelease = $false
    IsDraft = $true	# Set to true when testing so we don't publish a real release (visible to everyone) by accident.
}
$result = New-GitHubRelease @gitHubReleaseParameters

if ($result.Succeeded -eq $true)
{
    Write-Host "Release published successfully! View it at $($result.ReleaseUrl)"
}
elseif ($result.ReleaseCreationSucceeded -eq $false)
{
    Write-Host "The release was not created. Error message is: $($result.ErrorMessage)"
}
elseif ($result.AllAssetUploadsSucceeded -eq $null)
{
    Write-Host "The release was created, but not all of the assets were uploaded to it. View it at $($result.ReleaseUrl). Error message is: $($result.ErrorMessage)"
}
```

Attempts to create a new Release, and returns a hash table containing the results.
The PowerShell script will halt execution until the Release creation succeeds or fails.

---


## Full Documentation

Once the module has been imported, you can access the the latest documention in PowerShell by using `Get-Help New-GitHubRelease -Full`, or just [look at the file in source control here][DocumentationInSourceControlFile].

[PowerShellGalleryNuGetPackagePage]:https://www.powershellgallery.com/packages/New-GitHubRelease/
[GitHubRepoReleasesPage]:https://github.com/deadlydog/New-GitHubRelease/releases
[DocumentationInSourceControlFile]:https://github.com/deadlydog/New-GitHubRelease/blob/master/src/New-GitHubRelease/New-GitHubRelease.psm1#L6