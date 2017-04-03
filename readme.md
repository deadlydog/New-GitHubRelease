# New-GitHubRelease PowerShell Module

A PowerShell v3 compatible module to make automating the creation of new GitHub releases easy.

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

## Getting a GitHub Access Token

You can generate a GitHub Access Token on [your settings page][GitHubPersonalAccessTokensUrl] by following [these instructions][GitHubTutorialOnCreatingAnAccessTokenUrl].
Be sure to give the Access Token `repo`/`public_repo` permissions so that it will be allowed to create a new Release.

**Note:** You should _never_ hard-code your Access Token in your script (and source control). Instead prompt the user for it using something like `Read-Host`, and store it in an environmental variable for next time.

## Examples

```PowerShell
# Import the module dynamically from the PowerShell Gallery. Use CurrentUser scope to avoid having to run as admin.
Import-Module -Name New-GitHubRelease -Scope CurrentUser

# Specify the parameters required to create the release. Do it as a hash table for easier readability.
$newGitHubReleaseParameters =
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

# Try to create the Release on GitHub and save the results.
$result = New-GitHubRelease @newGitHubReleaseParameters

# Provide some feedback to the user based on the results.
if ($result.Succeeded -eq $true)
{
    Write-Output "Release published successfully! View it at $($result.ReleaseUrl)"
}
elseif ($result.ReleaseCreationSucceeded -eq $false)
{
    Write-Error "The release was not created. Error message is: $($result.ErrorMessage)"
}
elseif ($result.AllAssetUploadsSucceeded -eq $false)
{
    Write-Error "The release was created, but not all of the assets were uploaded to it. View it at $($result.ReleaseUrl). Error message is: $($result.ErrorMessage)"
}
```

Attempt to create a new Release on GitHub, and provide feedback to the user indicating if it succeeded or not.

---

## Full Documentation

Once the module has been imported, you can access the the latest documention in PowerShell by using `Get-Help New-GitHubRelease -Full`, or just [look at the file in source control here][DocumentationInSourceControlFile].

[PowerShellGalleryNuGetPackagePage]:https://www.powershellgallery.com/packages/New-GitHubRelease/
[GitHubRepoReleasesPage]:https://github.com/deadlydog/New-GitHubRelease/releases
[DocumentationInSourceControlFile]:https://github.com/deadlydog/New-GitHubRelease/blob/master/src/New-GitHubRelease/New-GitHubRelease.psm1#L6
[GitHubPersonalAccessTokensUrl]:https://github.com/settings/tokens
[GitHubTutorialOnCreatingAnAccessTokenUrl]:https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/