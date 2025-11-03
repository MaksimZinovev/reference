---
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
---


## Table of Contents

- Version & Host
- Command Structure
- Help & Aliases
- Scripts & Policy
- Copying Directories

### Version & Host {.cols-2}

#### Check Version

```powershell
$PSVersionTable.PSVersion
# Output:
# Major  Minor  Patch
# 7      5      2
```

#### Executable Path

```powershell
"C:\Users\<user>\AppData\Local\Microsoft\WindowsApps\pwsh.exe"
```

### Command Structure {.cols-3}

#### Verb-Noun Pattern

```powershell
Get-Command
Get-Date
Get-Service
```

#### Filter by Noun

```powershell
Get-Command -Noun Service  # Lists all commands with 'Service' noun
```

### Help & Aliases {.cols-3}

#### Get Help

```powershell
Get-Help Get-Service        # Short help
Get-Help Get-Service -Full  # Detailed help
```

#### Aliases & Clear Screen

```powershell
Get-Alias   # List all aliases
cls         # Clear host screen
```

### Scripts & Policy {.cols-3}

#### Edit & Run Script

```powershell
psedit .\test.ps1   # Open in editor
.\test.ps1          # Execute script
```

#### Execution Policy

```powershell
Get-ExecutionPolicy
# => RemoteSigned (local scripts allowed)
```

## Copying Directories {.cols-2}

#### Initial Structure

```powershell
source/
  file1.txt
  file2.txt
dist/   # may or may not exist
```

#### 1. Copy folder (target does NOT exist)

```powershell
Copy-Item source dist
# Result:
dist/
  file1.txt
  file2.txt
```

#### 2. Copy into existing folder

```powershell
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
# Windows ignores trailing slashes
```

#### 4. Copy only contents

```powershell
Copy-Item source\* dist -Recurse
# Result:
dist/
  file1.txt
  file2.txt
```

### Summary {.secondary}

- Trailing slashes are ignored on Windows.
- If target exists → source copied inside it.
- If target does NOT exist → source renamed to target.
- Use `source\*` to copy contents only.
