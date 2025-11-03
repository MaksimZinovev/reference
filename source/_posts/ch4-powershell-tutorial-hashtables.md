---

title: PowerShell Hashtables
date: 2025-11-03 19:18:50
background: bg-[#397fe4]
tags:

- powershell
- cheatsheet
categories:
- Scripting
- Windows
intro: Quick reference for creating, accessing, modifying, and using hashtables (@{}, [ordered]) in PowerShell.
plugins:
- copyCode

---

## PowerShell Hashtables — Quick Reference

### Table of Contents

- Basics
- Access & Inspect
- Modify
- Enumerate & Sort
- Ordered & Nested
- Splatting & Persist
- Tips & Pitfalls

### Basics {.cols-3}

#### Create & Type {.secondary}

```powershell
$ht = @{
  key1     = 100
  apple    = 2.34
  name     = 'Joe Doe'
  isActive = $true
}
$ht.GetType().FullName   # => System.Collections.Hashtable
$ht.Count                # => 4
```

#### Empty & New

```powershell
@{}                        # empty hashtable
$ht = [hashtable]::new()   # .NET ctor (empty)
$cap = [hashtable]::new()  # capacity auto-grows
```

#### Keys & Values

```powershell
$ht.Keys    # => key1, apple, name, isActive
$ht.Values  # => 100, 2.34, 'Joe Doe', True
$ht.Keys -is [System.Collections.ICollection] # True
```

### Access & Inspect {.cols-3}

#### Get by Key

```powershell
$ht['key1']   # => 100
$ht.key1      # => 100 (valid id only)
$ht['missing'] -eq $null   # => True
```

#### Existence & Null-Coalesce

```powershell
$ht.ContainsKey('key1')    # => True
'key1' -in $ht.Keys        # => True
$value = $ht['missing'] ?? 'N/A'  # PS 7+
```

#### Case-Insensitivity

```powershell
$ht['NAME'] = 1
$ht['name']            # => 1
$ht.ContainsKey('NaMe')# => True (default behavior)
```

### Modify {.cols-3}

#### Add / Update / Remove

```powershell
$ht.Add('new', 42)     # add
$ht['key1'] = 101      # update
$ht.Remove('apple')    # remove key
$ht.Clear()            # empty all entries
```

#### Merge & Clone

```powershell
$ht1 = @{ a=1; b=2 }
$ht2 = @{ b='x'; c=3 }
$merged = $ht1.Clone()     # shallow copy
foreach($k in $ht2.Keys){ $merged[$k] = $ht2[$k] }
$merged['b']    # => 'x' (ht2 overrides)
```

#### Default on Read

```powershell
$def = if($ht.ContainsKey('x')){$ht['x']}else{'default'}
$def # => 'default'
```

### Enumerate & Sort {.cols-3}

#### Iterate Keys

```powershell
foreach($k in $ht.Keys){
  "$k=$($ht[$k])"
}
```

#### GetEnumerator (pairs)

```powershell
foreach($e in $ht.GetEnumerator()){
  "$($e.Key): $($e.Value)"
}
```

#### Sort by Key / Value

```powershell
$ht.GetEnumerator() | Sort-Object Key
$ht.GetEnumerator() | Sort-Object Value
# Pipe to Format-Table for display
```

### Ordered & Nested {.cols-3}

#### Ordered Hashtable

```powershell
$ord = [ordered]@{ a=1; b=2; c=3 }
$ord.GetType().FullName
# => System.Collections.Specialized.OrderedDictionary
$ord.GetEnumerator() | % Key  # preserves order
```

#### Nested Hashtables

```powershell
$conf = @{
  db    = @{ host='localhost'; port=5432 }
  flags = @{ verbose=$true }
}
$conf['db']['host']  # => 'localhost'
```

#### To PSCustomObject

```powershell
[pscustomobject]$ord  # properties keep insertion order
```

### Splatting & Persist {.cols-3}

#### Splat Parameters

```powershell
$params = @{
  Path   = '.'
  Filter = '*.ps1'
  Recurse= $true
}
Get-ChildItem @params
```

#### Switches in Splat

```powershell
$rm = @{ Recurse=$true; Force=$true; WhatIf=$true }
Remove-Item @rm -Path 'temp'
```

#### Save / Load (CLIXML)

```powershell
Export-Clixml -InputObject $ht -Path ht.xml
$ht2 = Import-Clixml ht.xml
$ht2['name']  # => 'Joe Doe'
```

### Tips & Pitfalls {.cols-3}

#### Property vs Index

```powershell
$ht['first-name']='Ana'
$ht.first-name   # WRONG (interprets minus)
$ht.'first-name' # OK
$ht[1]           # use indexer for numeric key
```

#### Valid Dotted Access

```powershell
$ht = @{ user='max'; active=$true }
$ht.user     # => 'max'
$ht.active   # => True
# Only for simple, identifier-like keys
```

#### Quick Copy & Defaults

```powershell
$copy = $ht.Clone()         # shallow copy
$incr = $ht.GetEnumerator() |
        % { $_.Value ??= 0 } # set null to 0 (PS 7+)
```
