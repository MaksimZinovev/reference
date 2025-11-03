
title: PowerShell
date: 2025-11-03 18:20:00
background: bg-[#397fe4]
tags:

- script
- windows
categories:
- Programming
- Operating System
intro: This is a quick reference cheat sheet covering PowerShell basics.
plugins:
- copyCode

## PowerShell Basics {.cols-2}

### Check PowerShell Version

```powershell
$PSVersionTable.PSVersion
# Output:
Major  Minor  Patch  PreReleaseLabel BuildLabel
--  --  --   -
7      5      2
```

"C:\Users\maksim.zinovev\AppData\Local\Microsoft\WindowsApps\Microsoft.PowerShell_8wekyb3d8bbwe\pwsh.exe"

### Command Structure

```powershell
# verb-noun pattern
Get-Command
Get-Date
Get-Service
```

### Filter Commands by Noun

```powershell
Get-Command -noun service
# Lists all commands with 'service' noun
```

### Get Help

```powershell
Get-Help get-service         # Short help
Get-Help get-service -Full  # Full help
```

### Aliases & Host

```powershell
Get-Alias   # List all aliases
cls         # Clear host (screen)
```

### Script Editing & Execution

```powershell
psedit .\test.ps1   # Open file in editor
.\test.ps1          # Run script
```

### Execution Policy

```powershell
Get-ExecutionPolicy
# => RemoteSigned (can execute code written by you or trusted author)
```

## Different ways to copy

### Copying Directories: Cheatsheet

#### Initial folder structure

```powershell
source/
  file1.txt
  file2.txt
dist/   # may or may not exist
```

#### 1. Copy folder to new location (target does not exist)

```powershell
# dist does NOT exist
Copy-Item source dist
# Result:
dist/
  file1.txt
  file2.txt
```

#### 2. Copy folder into existing folder

```powershell
# dist exists
Copy-Item source dist
# Result:
dist/
  source/
    file1.txt
    file2.txt
```

#### 3. Trailing slash behavior

```powershell
Copy-Item source dist/   # same as Copy-Item source dist
Copy-Item source/ dist   # same as Copy-Item source dist
# On Windows, trailing slashes are ignored
```

#### 4. Copy only contents (not folder itself)

```powershell
Copy-Item source\* dist -Recurse
# Result: dist/ contains files from source/, not source/ itself
dist/
  file1.txt
  file2.txt
```

**Summary:**

- Trailing slashes on paths are ignored on Windows.
- If target exists, source is copied inside it; if not, source is copied and renamed to target.
- Use `source\*` to copy only contents, not the folder itself.

## Copying Directories with PowerShell {.cols-2}

### Initial folder structure

```powershell
source/
  file1.txt
  file2.txt
dist/   # may or may not exist
```

### Copy folder to new location (target does not exist)

```powershell
# dist does NOT exist
Copy-Item source dist
# Result:
dist/
  file1.txt
  file2.txt
```

### Copy folder into existing folder

```powershell
# dist exists
Copy-Item source dist
# Result:
dist/
  source/
    file1.txt
    file2.txt
```

### Trailing slash behavior

```powershell
Copy-Item source dist/   # same as Copy-Item source dist
Copy-Item source/ dist   # same as Copy-Item source dist
# On Windows, trailing slashes are ignored
```

### Copy only contents (not folder itself)

```powershell
Copy-Item source\* dist -Recurse
# Result: dist/ contains files from source/, not source/ itself
dist/
  file1.txt
  file2.txt
```

### Summary {.secondary}

- Trailing slashes on paths are ignored on Windows.
- If target exists, source is copied inside it; if not, source is copied and renamed to target.
- Use `source\*` to copy only contents, not the folder itself.
