Copying in Windows 10 Comparison
===

The target files

&emsp;&emsp;typical **node_modules** folder of a nodejs application, **491,273,625** bytes (**468** MiB), **63,288** files, **5,587** directories

Copying methods

1. Windows Explorer
2. robocopy
3. FastCopy

Disks comparison
<ol type="A">
  <li>Typical external HDD</li>
  <li>External SATA SSD</li>
  <li>M.2 NVMe SSD</li>
</ol>

Date modified and Date created
---

Normal copy (explorer.exe)

|           | Date modified | Date created |
| --------- | ------------: | -----------: |
| file      |     preserved |    _updated_ |
| directory |     _updated_ |    _updated_ |

robocopy (/E only)

|           | Date modified | Date created |
| --------- | ------------: | -----------: |
| file      |     preserved |    preserved |
| directory |     preserved |    _updated_ |

robocopy (/E /copy:DAT /dcopy:DAT)

|           | Date modified | Date created |
| --------- | ------------: | -----------: |
| file      |     preserved |    preserved |
| directory |     preserved |    preserved |

FastCopy (default settings already preserve timestamps)

|           | Date modified | Date created |
| --------- | ------------: | -----------: |
| file      |     preserved |    preserved |
| directory |     preserved |    preserved |

Directory Hierarchy / Trailing slash
---

Directory structure behavior is as follows.

**Before copying**
* Source: `D:\src\node_modules\...`
* Dest: `D:\dst\` (empty content)

robocopy (trailing or not has no differences)

|          Command          |       After copying       |
| ------------------------- | ------------------------- |
| `robocopy D:\src D:\dst`  | `D:\dst\node_modules\...` |
| `robocopy D:\src D:\dst\` | `D:\dst\node_modules\...` |

FastCopy (**Source**: `D:\src\`)

|  DestDir  |         After copying         |
| --------- | ----------------------------- |
| `D:\dst`  | `D:\dst\node_modules\...`     |
| `D:\dst\` | `D:\dst\src\node_modules\...` |

Disks' Properties
---



Comparison: HDD to HDD
---

1. Windows Explorer
    - Time measuring: using arbitrary stopwatch
    - Lising: time spent in **Discovered \<n> items** message
    - Copying: time include the listing time

2. robocopy
    - Using PowerShell
    - Listing
        ```
        Measure-Command { robocopy D:\src D:\dst\ /E /copy:DAT /dcopy:DAT /TIMFIX /R:2 /W:3 /ns /nc /nfl /ndl /np /L | Out-Default }
        ```
    - Copying
        ```
        Measure-Command { robocopy D:\src D:\dst\ /E /copy:DAT /dcopy:DAT /TIMFIX /R:2 /W:3 /ns /nc /nfl /ndl /np /mt | Out-Default }
        ```

3. FastCopy, v4.2.0, archive version, use default settings (i.e. Diff (Size/Date), Buffer 256 MB), checked **Estimate**

Target files: **468** MiB, **63,288** files

|           |    explorer    |    robocopy    |    FastCopy    |
| --------- | -------------: | -------------: | -------------: |
| Listing   |        7.3 sec |        0.4 sec |        1.4 sec |
| Copying   | 7:05 (425 sec) | 2:06 (126 sec) | 1:52 (112 sec) |
| TransRate |      1.1 MiB/s |      3.7 MiB/s |      4.2 MiB/s |
| FileRate  |  148.9 files/s |  502.3 files/s |  565.1 files/s |

