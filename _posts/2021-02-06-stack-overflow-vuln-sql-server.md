---
title: Discovering an Undisclosed Stack Overflow Vulnerability in Microsoft SQL Server (CVE-2019–1068)
author: ferdogan
date: 2021-02-06 00:00:00 +0800
categories: [Vulnerability Research]
tags: [<span style="color:AliceBlue">●</span> root cause analysis]
pin: false
mermaid: true
---

> Root cause analysis and PoC for a Microsoft SQL Server Stack Overflow Vulnerability by reversing svl.dll.

## Introduction
We would like to share one of our vulnerability analysis works in this blog post which covers a silently patched stack based memory corruption vulnerability (CVE-2019–1068) in svl.dll, which can be used for a Denial of Service attack and possible Remote Code Execution.

This blog post is also shared on blogs of both [Ataberk](https://0xsaiyajin.github.io/) and [Cem](https://cemk.me/).

This issue affects the following versions of Microsoft SQL Server:
- Microsoft SQL Server 2014
- Microsoft SQL Server 2016
- Microsoft SQL Server 2017

It was patched on 9 July 2019. More information about this issue can be found [here](https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2019-1068).

> This blog post describes the root cause analysis and includes the Proof of Concept script.
{: .prompt-tip }

## Details of CVE-2019-1068
In the beginning, we searched for the undisclosed memory corruption vulnerabilities of Microsoft SQL Server from MSRC’s web portal. As seen on the screenshot below, CVSS v3.0 score of the vulnerability was calculated as 8.8 (High) on NVD of NIST.

![CVE-2019-1068](/assets/img/cve_2019_1068_analysis/cve_2019_1068.png)

After a short discussion, we jumped into patch analysis of the vulnerability, we started with the description page of the vulnerability.

The summary of the affected versions is listed below.

![Affected Versions](/assets/img/cve_2019_1068_analysis/affected_versions.png)

> **Executive Summary**
A remote code execution vulnerability exists in Microsoft SQL Server when it incorrectly handles processing of internal functions. An attacker who successfully exploited this vulnerability could execute code in the context of the SQL Server Database Engine service account. To exploit the vulnerability, an authenticated attacker would need to submit a specially crafted query to an affected SQL server. The security update addresses the vulnerability by modifying how the Microsoft SQL Server Database Engine handles the processing of functions.
{: .prompt-info }

The executive summary tells that an authenticated attacker would need to run a crafted query. By considering this summary, our work focused on SQL Server’s query handler engine.

## Patch Analysis
By comparing the difference across all of the files in Cumulative Update 14 and Cumulative Update 15 we discovered the patched DLL.

It’s possible to see that only a few functions of `svl.dll` were patched:
- `SvlPathUtilIsCrossPlatform(ushort const * const)`
- `SvlPathUtilHasDriveLetter(ushort const * const)`
- `SvlPathHandlerT<UriPathTraits>::ValidatePath(ushort const * const)`
- `SvlPathHandlerT<XPlatPathTraits>::ValidatePath(ushort const * const)`

This can be seen on the screenshot below:

![Patched Functions](/assets/img/cve_2019_1068_analysis/patched_functions.png)

The patched version contains the following function:
- `SvlPathUtilHasDriveLetter(ushort const * const)`

When we look into the difference between two updated versions, it’s possible to see one of these functions processes a user-generated string even if it is not in a valid path format.

![Patch-Diffing](/assets/img/cve_2019_1068_analysis/patch-diffing.png)

As seen on the screenshot above, the vulnerable version of the function has fewer lines of code.

Both versions of the `SvlPathUtilHasDriveLetter` use the [iswalpha](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/isalpha-iswalpha-isalpha-l-iswalpha-l?view=msvc-160#return-value) function to check if the first character of the user-controlled input string is between A and Z. Additionally they both check if the second character of the string equals to “:” character or not.

The improvement in the updated code block adds an extra check on the third character of the input string. It checks if the third character is one of “\” or “/” characters. Function returns “1” and the program continues properly if all conditions meet the requirements.

## Triggering The Bug
To trigger the bug, all we needed was to create a crafted SQL query which includes a path value that triggers the vulnerable function in CU14. We looked for a way of doing it on Microsoft’s online documentations as seen on the screenshot below.

![microsoft-docs](/assets/img/cve_2019_1068_analysis/microsoft-docs.png)

After several attempts, we were able to trigger the vulnerability by using the following command on the screenshot. Whenever this SQL Query is processed by SQL Server, it crashes.

![sql-server-crash](/assets/img/cve_2019_1068_analysis/sql-server-crash.png)

## Crash Analysis
The dynamic analysis process was started by attaching the SQL server to WinDBG. During the analysis, it was observed that none of the user-controlled inputs were written in any part of the memory.

As seen on the screenshot below which shows the call stack analysis step, `svl!PositionT<Win32PathTraits>::SetBuffer` is called by vulnerable DLL then program flow continues with the `svl!SvlPathHandlerT<Win32PathTraits>::NormalizePath` function.

We discovered that `svl!SvlPathHandlerT<Win32PathTraits>::NormalizePath` function interacts with `svl!SvlPathUtilHasDriveLetter` function which doesn’t have a proper input validation against malformed **path** strings.

Eventually, this corruption leads to an infinite recursive call of the `sqlmin!CheckFileStreamReserved` function therefore stack exhaustion occurs.

![sqlmin](/assets/img/cve_2019_1068_analysis/sqlmin.png)

### Access Violation - code c0000005

![access_violation](/assets/img/cve_2019_1068_analysis/access_violation.png)

## Conclusion
According to MSRC’s web portal, this vulnerability seems to lead to an RCE impact. We tried to achieve this impact by using some publicly known methods. Unfortunately, it seems to be leading to a stack exhaustion DoS vulnerability.

[**POC - Exploit (Denial of Service)**](https://github.com/Vulnerability-Playground/CVE-2019-1068/blob/main/cve-2019-1068_DoS_PoC.py)

EOF.