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

TL;DR: Conclusion
---

1. On SATA SSD/HDD (and USB 3.0), **FastCopy** is the fastest
2. On NVMe SSD, **robocopy** is the fastest
3. Windows Explorer is **4x** slower (or **12x** slower on NVMe SSD) and do not preserve timestamps

Disks' Properties
---

|                        |   R: HDD    |   W: HDD    | R: SATA SDD | W: SATA SDD | R: NVMe SSD  | W: NVMe SSD  |
| ---------------------- | ----------: | ----------: | ----------: | ----------: | -----------: | -----------: |
| SEQ1M<sub>Q1T1</sub>   | 111.36 MB/s | 111.57 MB/s | 424.88 MB/s | 335.32 MB/s | 1981.10 MB/s | 1913.41 MB/s |
| RND4K<sub>Q1T1</sub>   |   0.51 MB/s |   1.25 MB/s |  20.80 MB/s |  39.44 MB/s |   47.42 MB/s |  157.02 MB/s |
| RND4K<sub>(IOPS)</sub> |      125.00 |      305.91 |     5077.64 |     9628.17 |     11578.13 |     38335.94 |
| RND4K<sub>(µs)</sub>   |     7983.04 |     3264.67 |      196.61 |      103.61 |        86.08 |        25.80 |

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
        Measure-Command { robocopy D:\src D:\dst /E /copy:DAT /dcopy:DAT /TIMFIX /R:2 /W:3 /ns /nc /nfl /ndl /np /L | Out-Default }
        ```
    - Copying
        ```
        Measure-Command { robocopy D:\src D:\dst /E /copy:DAT /dcopy:DAT /TIMFIX /R:2 /W:3 /ns /nc /nfl /ndl /np /mt | Out-Default }
        ```

3. FastCopy, v4.2.0, archive version, use default settings (i.e. Diff (Size/Date), Buffer 256 MB), checked **Estimate**

Target files: **468** MiB, **63,288** files

|           |    explorer    |    robocopy    |      FastCopy      |
| --------- | -------------: | -------------: | -----------------: |
| Listing   |        7.3 sec |    **0.4 sec** |            1.4 sec |
| Copying   | 7:05 (425 sec) | 2:06 (126 sec) | **1:52 (112 sec)** |
| TransRate |      1.1 MiB/s |      3.7 MiB/s |      **4.2 MiB/s** |
| FileRate  |  148.9 files/s |  502.3 files/s |  **565.1 files/s** |

Comparison: SATA SSD to SATA SSD
---

Target files: **468** MiB, **63,288** files

|           |    explorer    |    robocopy    |      FastCopy      |
| --------- | -------------: | -------------: | -----------------: |
| Listing   |        7.1 sec |    **0.4 sec** |            1.4 sec |
| Copying   | 5:50 (350 sec) | 2:01 (121 sec) | **1:49 (109 sec)** |
| TransRate |      1.3 MiB/s |      3.9 MiB/s |      **4.3 MiB/s** |
| FileRate  |  180.8 files/s |  523.0 files/s |  **580.6 files/s** |

Comparison: NVMe SSD to NVMe SSD
---

Target files: **468** MiB, **63,288** files

|           |    explorer    |      robocopy      |    FastCopy    |
| --------- | -------------: | -----------------: | -------------: |
| Listing   |        7.1 sec |        **0.4 sec** |        1.4 sec |
| Copying   | 4:07 (247 sec) |        **9.5 sec** |         20 sec |
| TransRate |      1.9 MiB/s |     **49.3 MiB/s** |     23.4 MiB/s |
| FileRate  |  256.2 files/s | **6661.9 files/s** | 3164.4 files/s |
