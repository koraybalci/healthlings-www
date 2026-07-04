Start the Jekyll local development server (Windows/PowerShell).

Run this PowerShell command to launch Jekyll with live reload in the background:

```powershell
Start-Process -NoNewWindow -FilePath "bundle" -ArgumentList "exec jekyll serve --livereload"
```

Then verify it's up:

```powershell
Start-Sleep 8; Invoke-WebRequest -Uri "http://localhost:4000/" -UseBasicParsing | Select-Object StatusCode
```

The site will be available at **http://localhost:4000/**. LiveReload runs on port 35729.

To stop the server, find and kill the Ruby process:

```powershell
Get-Process ruby | Stop-Process
```
