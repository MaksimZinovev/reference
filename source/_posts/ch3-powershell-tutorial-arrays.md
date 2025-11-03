---
title: PowerShell Arrays
date: 2025-11-03 12:00:00
background: bg-[#397fe4]
tags:
  - script
  - windows
categories:
  - Programming
  - Operating System
intro: |
  Quick reference for creating, manipulating, searching, and optimizing array structures (fixed, ArrayList, and generic List[T]) in PowerShell.
plugins:
  - copyCode
---

## PowerShell Arrays — Quick Reference

### Table of Contents

- [Array Basics](#array-basics)
- [Add/Remove Items](#addremove-items)
- [Indexing & Slicing](#indexing-slicing)
- [Filtering & Search](#filtering-search)
- [Join/Split/Sort/Reverse](#joinsplitsortreverse)
- [ArrayList (Resizable)](#arraylist-resizable)
- [Generic List[T] (Typed)](#generic-listt-typed)
- [Tips & Pitfalls](#tips-pitfalls)
- [From Draft (preserved style)](#from-draft-preserved-style)

## Array Basics {.cols-3}

### Create & Inspect

```powershell
$myArray = @('test1','test2','test3')
$myArray.GetType().Name      # => Object[]
$myArray.Count               # => 3
$myArray                     # => test1, test2, test3
```

### Collect & Single‑Item

```powershell
@(Get-Process | Select-Object -First 2).Count  # => 2
,42 | ForEach-Object GetType | `
  Select-Object -Expand Name                   # => Int32
@()                                            # => empty array
```

### Strongly Typed Arrays

```powershell
[int[]]$nums = 1,2,3
$nums += 4                     # => 1,2,3,4 (new array)
[string[]]$s = @()             # => typed empty array
```

## Add/Remove Items {.cols-3}

### Append (arrays are fixed-size)

```powershell
$myArray = $myArray + 'test4'  # => test1..test4
$myArray += 'test5'            # => test1..test5
$myArray                       # => test1, test2, test3, test4, test5
```

### Remove by Value (filter to new array)

```powershell
'remove test2:' | Write-Output
$myArray = $myArray -ne 'test2'   # keep items != 'test2'
$myArray                           # => test1, test3, test4, test5
```

### Keep / Drop Multiple

```powershell
$myArray = $myArray -notin @('test1','test5') # drop both
$myArray                                      # => test3, test4
$only3 = $myArray -in @('test3')              # => test3
```

## Indexing & Slicing {.cols-3}

### Index & Range

```powershell
$myArray = 'a','b','c','d'
$myArray[0]                  # => a
$myArray[-1]                 # => d
$myArray[1..2]               # => b, c
$myArray[2,0]                # => c, a
```

### Insert by Rebuild

```powershell
$idx = 1
$myArray = $myArray[0..($idx-1)] + 'X' + `
           $myArray[$idx..($myArray.Count-1)]
$myArray                      # => a, X, b, c, d
```

### Jagged vs Multidimensional

```powershell
$jag = @(@(1,2), @(3,4)); $jag[1][0]  # => 3
$grid = New-Object 'object[,]' 2,3
$grid[0,1] = 'A'; $grid[0,1]         # => A
```

## Filtering & Search {.cols-3}

### Pipeline & Script Methods

```powershell
$arr = 'test1','test2','prod1'
$arr | Where-Object { $_ -like 'test*' }      # => test1, test2
$arr.Where({ $_ -ne 'test2' })                # => test1, prod1
$arr.ForEach({ $_.ToUpper() })                # => TEST1, TEST2, PROD1
```

### Membership & Index

```powershell
$arr -contains 'prod1'        # => True
'prod1' -in $arr              # => True
$arr.IndexOf('test2')         # => 1 (or -1 if not found)
```

### Unique & Grouping

```powershell
('a','b','a') | Select-Object -Unique        # => a, b
('a','b','a') | Group-Object | `
  Sort-Object Count -Desc | `
  ForEach-Object { "$($_.Name):$($_.Count)" } # => a:2, b:1
```

## Join/Split/Sort/Reverse {.cols-3}

### Join & Split

```powershell
$csv = ('x','y','z') -join ','    # => x,y,z
'one,two,three' -split ','        # => one, two, three
```

### Sort & Reverse

```powershell
$nums = 5,3,9,1
$nums | Sort-Object               # => 1, 3, 5, 9
[Array]::Reverse($nums); $nums    # => 9, 5, 3, 1
```

### Map & Reduce

```powershell
$nums | ForEach-Object { $_ * 2 }          # => 10,6,18,2
($nums | Measure-Object -Sum).Sum          # => 18
```

## ArrayList (Resizable) {.cols-3}

### ArrayList Create & Inspect

```powershell
$myArrayList = [System.Collections.ArrayList]@()
$myList = New-Object System.Collections.ArrayList  # recommended
$myList.GetType().Name            # => ArrayList
$myList.IsFixedSize               # => False
```

### Add / AddRange / Show

```powershell
$myList.Add('itemA')              # => 0 (index)
[void]$myList.Add('itemB')        # => (no output)
$myList.AddRange(@('itemC','itemC')) # adds multiple items
$myList                           # => itemA, itemB, itemC, itemC
```

### Remove / RemoveRange / Insert

```powershell
''; 'remove itemC:' | Write-Output
$myList.Remove('itemC')           # removes first 'itemC'
$myList                           # => itemA, itemB, itemC

''; 'remove range:' | Write-Output
$myList.RemoveRange(0,1)          # remove 1 from index 0
$myList                           # => itemB, itemC

$myList.Insert(0,'HEAD')          # insert at index
$myList                           # => HEAD, itemB, itemC
```

### Other Ops

```powershell
$myList.Contains('itemB')         # => True
$myList.IndexOf('itemB')          # => 1
$myList.Clear(); $myList.Count    # => 0
```

## Generic List[T] (Typed) {.cols-3}

### Why & How

```powershell
# Faster than ArrayList; type-safe
$list = [System.Collections.Generic.List[string]]::new()
$list.Add('one'); $list.AddRange(@('two','three'))
$list[1]                              # => two
```

### Convert Array → List

```powershell
$arr = 'a','b','c'
$list = [System.Collections.Generic.List[string]]$arr
$list.Add('d'); $list                 # => a, b, c, d
```

### Remove & Capacity

```powershell
$list.Remove('two')                   # => True
$list.RemoveAt(0); $list              # => c, d
"$($list.Count)/$($list.Capacity)"    # => e.g. 2/4
```

## Tips & Pitfalls {.cols-2 .marker-round}

### Tips

- `+` / `+=` on arrays creates a **new array** (copy cost).
- Use **ArrayList** / **List[T]** for frequent inserts/removals.
- Use filtering (`Where-Object` / `.Where()`) to “remove” items.
- `,x` forces a single‑item array; `@()` collects pipeline output.
- Typed arrays (`[int[]]`) enforce element types on assignment.
- `-contains` / `-in` are membership (scalar) operators.
- Prefer `List[T]` over `ArrayList` when you want type safety.
