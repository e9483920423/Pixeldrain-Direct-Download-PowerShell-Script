# Pixeldrain Direct Download - PowerShell Script

.SYNOPSIS
This PowerShell script converts a Pixeldrain download link into an API call (if available)
and directly downloads the file to your `C:\Users\` directory.

.DESCRIPTION
- Finds the file ID on a Pixeldrain page.
- Uses the Pixeldrain API to get the real filename.
- Downloads the file directly to your current directory (or `C:\Users\` if you are in that folder).
- Supports multiple downloads in one go.

.PARAMETER Slugs
Replace the example slug(s) with your Pixeldrain URL slug(s). Example:
https://pixeldrain.com/u/cW8FKre1 → slug = cW8FKre1



## SCRIPT (DON'T FORGET THE COMMAS AFTER EACH URL):

```
# 🔴 $urls = @(
    "https://pixeldrain.com/u/(EXAMPLE_SLUG)",
    "https://pixeldrain.com/u/(EXAMPLE_SLUG)",
    "https://pixeldrain.com/u/(EXAMPLE_SLUG)"
)

foreach ($url in $urls) {
    try {
        Write-Host "`nProcessing $url ..."

        $fileId = ([regex]::Matches((Invoke-WebRequest $url).Content,'/u/([A-Za-z0-9-_]+)') | Select-Object -First 1).Groups[1].Value
        $apiUrl = "https://pixeldrain.com/api/file/$fileId"

        $resp = Invoke-WebRequest $apiUrl -MaximumRedirection 0 -ErrorAction SilentlyContinue
        $filename = ($resp.Headers.'Content-Disposition' -replace '.*filename="(.+)".*','$1')

        $filename = ($filename -replace '[<>:"/\\|?*]', '_').Trim()

        [System.IO.File]::WriteAllBytes($filename, $resp.Content)

        Write-Host "Downloaded: $filename"
    }
    catch {
        Write-Warning ("Failed to download from " + $url + ": " + $_)
    }
}```
