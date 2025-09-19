```
$urls = @(
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
