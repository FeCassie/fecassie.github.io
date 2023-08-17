---
title: A Closer Look At Built-In macOS Application Security
author: ferdogan
date: 2023-07-31 00:00:00 +0800
categories: [macOS Security]
tags: [<span style="color:red">●</span> macOS, <span style="color:blue">●</span> security, <span style="color:yellow">●</span> internals, <span style="color:gray">●</span> Apple]
pin: false
mermaid: true
---

## Introduction
In an increasingly connected world, the security of our digital lives has become more critical than ever. As users rely heavily on their computers for work, personal communication, and handling sensitive information, the need for a secure operating system is paramount.

While macOS is widely regarded for its user-friendly nature, it's important to acknowledge that no operating system is immune to security threats. From malware and phishing attacks to data breaches and unauthorized access, macOS users must be vigilant and proactive in safeguarding their systems and personal information.

In this research, we'll take a closer look at the world of macOS security. We will shed light on the various layers of protection offered by Apple.

## Built-In macOS Application Security
When it comes to protecting macOS-powered devices, Apple has implemented a range of robust security measures that often go unnoticed.

macOS takes a multi-faceted approach to safeguarding its system against potential threats from Internet-downloaded applications. It integrates a variety of protective measures to ensure that such apps are free from known malware. By leveraging advanced technologies, macOS not only detects and eliminates malware but also establishes additional barriers to prevent untrusted apps from compromising user data.

## Transparency, Consent, and Control - TCC
Transparency, Consent, and Control (TCC) is a fundamental aspect of privacy and security in modern digital ecosystems. It refers to a set of mechanisms and practices designed to provide users with clear visibility and control over the permissions and access rights granted to applications and services on their devices.

In macOS, TCC serves as a crucial component of privacy protection by offering users the ability to manage which applications have access to sensitive data and system resources. This includes permissions for accessing location data, contacts, calendars, microphones, cameras, and more. By allowing users to review and modify these permissions, macOS ensures that individuals have granular control over their personal information.

The transparency aspect of TCC ensures that users are fully informed about the data an application requests and the specific purpose for which it is required. When an application attempts to access sensitive data for the first time, macOS displays a consent prompt, clearly stating the information being requested and the purpose it will serve. This transparency empowers users to make informed decisions about granting or denying access.

![TCC Warning](/assets/img/macos_application_security/tcc_warning.png){: width="400" height="400" }

Under *System Preferences > Privacy & Security*, users can see which applications have permissions to which services.

![Full Disk Access](/assets/img/macos_application_security/full_disk_access.png){: width="500" height="500" }

### TCC Database
TCC is an SQLite database, and this database is located in `/Library/Application\ Support/com.apple.TCC/TCC.db`. The following command allows users to view the System TCC database.
```bash
sqlite3 /Library/Application\ Support/com.apple.TCC/TCC.db
```
The TCC database includes the following tables.
- access 
- access_overrides
- active_policy admin
- expired
- policies

![TCC Database](/assets/img/macos_application_security/tcc_database.png)

The database also allows users to view System applications and TCC permissions.
![TCC Database](/assets/img/macos_application_security/TCC.png)

## File Quarantine
File Quarantine is the predecessor of Gatekeeper and was introduced with OS X Leopard (10.5). With the File Quarantine feature, the extended attribute-(XA) `com.apple.quarantine` attribute is added to the files downloaded from the Internet.

File Quarantine is designed to notify users when an application downloaded from the Internet is run for the first time.

![File Quarantine](/assets/img/macos_application_security/file_quarantine.png){: width="400" height="400" }

The warning above is triggered when trying to open files with the `com.apple.quarantine` extended attribute. Files that have `@` at the end of their permissions flags have extended attributes.
![Extended Attributes](/assets/img/macos_application_security/extended_attributes.png)

### xattr
The `xattr` utility allows users to view a file's extended attributes.
![xattr](/assets/img/macos_application_security/xattr.png){: width="500" height="500" }

The `xattr -p` command is used to display extended attribute values.
![xattr -p](/assets/img/macos_application_security/xattr_p.png)

### XA Values - com.apple.quarantine
The structure of the values in `com.apple.quarantine` as below.
![quarantine](/assets/img/macos_application_security/quarantine.png)

### QuarantineEventsV2 Database - com.apple.quarantine
All flag information of the `com.apple.quarantine` attribute is available in the `com.apple.LaunchServices.QuarantineEventsV2` SQLite database. The database is located at `~/Library/Preferences/com.apple.LaunchServices.QuarantineEventsV2`.
![QuarantineEventsV2](/assets/img/macos_application_security/quarantine_events_v2.png)

The following SQL command can be used to display the values in the `LSQuarantineEvent`.
![LSQuarantineEventV2](/assets/img/macos_application_security/lsquarantineeventv2.png)

### LSQuarantineEvent Table
The LSQuarantineEvent table includes the following columns.
- LSQuarantineEventIdentifier
- LSQuarantineTimeStamp
- LSQuarantineAgentBundleIdentifier
- LSQuarantineAgentName
- LSQuarantineDataURLString
- LSQuarantineSenderName
- LSQuarantineSenderAddress
- LSQuarantineTypeNumber
- LSQuarantineOriginTitle
- LSQuarantineOriginURLString
- LSQuarantineOriginAlias

An example value and table column information in the `LSQuarantineEvent` table are as follows.
![LSQuarantineEvent](/assets/img/macos_application_security/lsquarantineevent.png)

## Gatekeeper
Gatekeeper is a built-in security technology designed to run trusted software on macOS. When downloading an application from the Internet outside the AppStore and opening that application, Gatekeeper confirms that the application comes from the identified developer and is known to Apple (notarization).
![Gatekeeper](/assets/img/macos_application_security/gatekeeper.png){: width="400" height="400" }

The operations of the Gatekeeper service are performed at the system level by `spctl - SecAssessment system policy security macOS binary`.

The spctl command is used to check whether an application is signed and notarized. The command returns either "accepted" or "rejected", depending on the Gatekeeper's evaluation.
![spctl](/assets/img/macos_application_security/spctl.png)

> The main difference between Gatekeeper and File Quarantine is the signature check. File Quarantine does not perform signature checks.
{: .prompt-warning }

![gatekeeper_warning](/assets/img/macos_application_security/gatekeeper_warning.png){: width="400" height="400" }

### Gatekeeper Database
Gatekeeper performs the controls from its own database, and it keeps a `blocklist` for applications. The Gatekeeper's database is in SQLite format and is located at;
```shell
/Library/Apple/System/Library/CoreServices/XProtect.bundle/Contents/Resources/gk.db
```

![gatekeeper_db](/assets/img/macos_application_security/gatekeeper_db.png){: width="400" height="400" }

The following SQL command can be used to display the IDs in blocked_teams table.
![blocked_teams](/assets/img/macos_application_security/blocked_teams.png){: width="350" height="350" }

## Malware Detection - XProtect.bundle
macOS has a built-in security tool called `XProtect` that provides signature-based detection. XProtect performs malware detection using `YARA` signatures. YARA rules are regularly updated by Apple.

XProtect performs a continuous security scan of the macOS system, including in the following cases.
- Launch a downloaded app for the first time 
- Change an app in the file system
- XProtect signatures are updated

> XProtect is included in File Quarantine technology.
{: .prompt-info }

When an application downloaded from the Internet using an application sensitive to File Quarantine technology is opened, A warning appears in front of the user that the application is downloaded from the Internet (**Gatekeeper**).

When this application downloaded from the Internet is run, File Quarantine technology checks whether the relevant application matches any of the signatures in XProtect. If there is a match, another warning is displayed saying that running the application may harm the system (**File Quarantine**).

XProtect checks application files and file hashes against these signatures when the application is first launched or whenever it changes. When XProtect detects a matching signature, it prevents the relevant code from running, and the user is informed of the option to delete the relevant application (**Gatekeeper**).
![xprotect_warning](/assets/img/macos_application_security/xprotect_warning.png){: width="400" height="400" }

### XProtect Files
XProtect is included in macOS in .bundle format. XProtect files are located at; 
```bash
/Library/Apple/System/Library/CoreServices/XProtect.bundle
```
![xprotect_bundle](/assets/img/macos_application_security/xprotect_bundle.png){: width="500" height="500" }

#### XProtect.yara
This file contains Apple's built-in `YARA` rules.
![xprotect_yara](/assets/img/macos_application_security/xprotect_yara.png)

#### XProtect.meta.plist
This `.plist` file contains information about malicious application plugins (Java, Flash, etc.) and harmful Safari extensions.

Extension block definitions are made according to the bundle identifier and related Developer ID information.
![xprotect_meta](/assets/img/macos_application_security/xprotect_meta.png)

#### XProtect.plist
This `.plist` file contains information about the application bundle and the harmful content in it.
![xprotect_plist](/assets/img/macos_application_security/xprotect_plist.png)

As an example, the content of the `OSX.28a9883` signature name in XProtect.plist is given below.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<array>
	<dict>
		<key>Description</key>
		<string>OSX.28a9883</string>
		<key>LaunchServices</key>
		<dict>
			<key>LSItemContentType</key>
			<string>com.apple.application-bundle</string>
		</dict>
		<key>Matches</key>
		<array>
			<dict>
				<key>MatchFile</key>
				<dict>
					<key>NSURLTypeIdentifierKey</key>
					<string>public.unix-executable</string>
				</dict>
				<key>MatchType</key>
				<string>Match</string>
				<key>Pattern</key>
				<string>3A6C6162656C3A706C697374506174683A</string>
			</dict>
			<dict>
				<key>MatchFile</key>
				<dict>
					<key>NSURLTypeIdentifierKey</key>
					<string>public.unix-executable</string>
				</dict>
				<key>MatchType</key>
				<string>Match</string>
				<key>Pattern</key>
				<string>3A62696E3A706C6973743A</string>
			</dict>
			<dict>
				<key>MatchFile</key>
				<dict>
					<key>NSURLTypeIdentifierKey</key>
					<string>public.unix-executable</string>
				</dict>
				<key>MatchType</key>
				<string>Match</string>
				<key>Pattern</key>
				<string>214023247E5E262A28295B5D7B7D3A3B3C3E2C2E31713277336534723574367937753869396F3070415A5358444346564742484E4A4D4B4C5157455254595549</string>
			</dict>
		</array>
	</dict>
	<dict>
```

The "string" values in the "pattern" keys can be analyzed by converting them to ASCII format as follows. These patterns are searched by XProtect for each signature in the plist file.
![unhex](/assets/img/macos_application_security/unhex.png)

#### LegacyEntitlementAllowlist.plist
This plist file is undocumented by Apple.
It is seen that only cdhash information is included in the plist. Detailed information about cdhash can be found in Apple documentation.

> **cdhash:** The code directory hash identifies a specific version of a program. This allows the system to verify that the contents of a binary have not changed since being code-signed.
{: .prompt-tip }

![legacyentitlement](/assets/img/macos_application_security/legacyentitlement.png)

#### XProtect Unified Logs
An example of unified logs of XProtect events are given below.
```bash
log show -info -backtrace -debug -loss -signpost -predicate 
'subsystem == "com.apple.xprotect"'
```
![xprotect_unified_logs](/assets/img/macos_application_security/xprotect_unified_logs.png)

## Malware Removal Tool - MRT
MRT is another built-in anti-malware tool for macOS which is available on macOS 10.8.3 and higher versions. 
When it is infected with malware, the macOS system is cleaned with MRT. 

MRT checks the malware database regularly updated by Apple and removes infections. Similar to XProtect, MRT is activated automatically at system startup. 
MRT is located at;
```bash
/Library/Apple/System/Library/CoreServices/MRT.app
```
![mrt](/assets/img/macos_application_security/mrt.png){: width="500" height="500" }

When the MRT binary is analyzed, data related to different malware types are seen.
![mrt_reversing](/assets/img/macos_application_security/mrt_reversing.png)

For example, when the data of the `Mughtesec` malware is examined, it is understood that the MRT application keeps behavioral patterns of the malicious software and detects the malware by comparing the activities in the system with these patterns.
![mrt_reversing2](/assets/img/macos_application_security/mrt_reversing2.png)

> MRT is retired from macOS and is being replaced by XProtect.
{: .prompt-warning }

## XProtect Remediator - XProtect.app
With the release of macOS 12.3, Apple also released a new macOS tool called XProtect Remediator (XPR). 
XProtect Remediator is reminiscent of MRT in the sense that it is an application package, but it contains different binaries for different malware.

![xprotect_remediator](/assets/img/macos_application_security/xprotect_remediator.png){: width="400" height="400" }

> Different binaries for different malware are in `XProtectRemediator<FAMILY_NAME>` format.
{: .prompt-tip }
 
The main differences between MRT and XProtect Remediator are as follows.
- MRT only works during the reboot and login phases.
- XProtect Remediator scans at regular intervals.

Unified logs of XProtect Remediator events can be examined as follows.
```bash
log show -info -backtrace -debug -loss -signpost -predicate 'subsystem == "com.apple.XProtectFramework.PluginAPI" && category == "XPEvent.structured"'
```
When the logs are examined, it is seen that the binaries in XProtect Remediator are scanned regularly.
![xprotect_remediator2](/assets/img/macos_application_security/xprotect_remediator2.png)

## Malware Response in Apple
When new malware is discovered, the following steps are taken.
- Any associated Developer ID certificates are revoked.
- Notarization revocation tickets are issued for all files (apps and associated files).
- XProtect signatures are developed and released.

## Behavioral Detection - BastionRules
Apple has released a new XProtect module with macOS Ventura. With this module, it is seen that XProtect has a behavioral detection feature. 
The SQLite database file of this module is located at;
```bash
/var/protected/xprotect/XPdb
```
![bastionrules](/assets/img/macos_application_security/bastionrules.png)

An example rule structure and information in the `events` table is as follows.
![bastionrules2](/assets/img/macos_application_security/bastionrules2.png)

XPdb and XProtect activities, in general, are handled by the `System Configuration Policy Daemon - syspolicyd`.
> When the `syspolicyd` binary is analyzed, the `registerBastionProfile` function is seen in the `SandboxManager` class. The **retain** array draws attention to this function.
{: .prompt-tip }
![bastionrules3](/assets/img/macos_application_security/bastionrules3.png)

When the offset is analyzed, the following data can be seen.
![bastionrules4](/assets/img/macos_application_security/bastionrules4.png)

The edited version of the data in `aNversion3nnall` is as follows.
```bash
(version 3)

(allow default job-creation file-write-setugid)
(allow file-test-existence)

(define (system-binary signing-id)

 (require-all
  (signing-identifier signing-id)

  (process-attribute is-platform-binary)))

(define bastion-usual-offenders
 (require-any
  (system-binary \"com.apple.imagent\")
  (system-binary \"com.apple.imtransferservices.IMTransferAgent\")

  (system-binary \"com.apple.fseventsd\")
  (system-binary \"com.apple.mds\")
  (system-binary \"com.apple.mdsync\")
  (system-binary \"com.apple.XProtectFramework.plugins.MRTv3\")

  (system-binary \"com.apple.MRT\")
  (system-binary \"com.apple.XProtectFramework.plugins.Pirrit\")

  (system-binary \"com.apple.mdworker_shared\")

  (system-binary \"com.apple.jetsam_priority\")

  (system-binary \"com.apple.diskarbitrationd\")

  (system-binary \"com.apple.StorageManagement.Service\")

  (system-binary \"com.apple.dt.IDECacheDeleteAppExtension\")

  (system-binary \"com.apple.STMExtension.Applications\")

  (system-binary \"com.apple.StorageManagement.Service\")

  (system-binary \"com.apple.coreservices.uiagent\")

  (system-binary \"com.apple.imdpersistence.IMDPersistenceAgent\")))


(define rule-one-offenders

(require-any

  (system-binary \"com.apple.Safari.PasswordBreachAgent\")

  (system-binary \"com.apple.Safari.History\")

  (system-binary \"com.apple.Safari.CacheDeleteExtension\")

  (system-binary \"com.apple.Finder\")

  (system-binary \"com.apple.SafariBookmarksSyncAgent\")

  (system-binary \"com.apple.UserEventAgent\")

  (system-binary \"com.apple.appkit.xpc.openAndSavePanelService\")

  (system-binary \"com.apple.SafariNotificationAgent\")

  (system-binary \"com.apple.dt.SKAgent\")

  (system-binary \"com.apple.backupd\")

  (system-binary \"com.apple.STMExtension.Developer\")

  (system-binary \"com.apple.STMExtension.Mail\")

  (system-binary \"com.apple.Safari\")))


(define rule-two-offenders

 (require-any

  (system-binary \"com.apple.suggestd\")

  (system-binary \"com.apple.IMAutomaticHistoryDeletionAgent\")

  (system-binary \"com.apple.MobileSMS\")

  (system-binary \"com.apple.MobileSMS.spotlight\")

  (system-binary \"com.apple.photolibraryd\")

  (system-binary \"com.apple.Spotlight\")

  (system-binary \"com.apple.coreduetd\")

  (system-binary \"com.apple.filecoordinationd\")

  (system-binary \"com.apple.Safari.SandboxBroker\")

  (system-binary \"com.apple.quicklook.ThumbnailsAgent\")

  (system-binary \"com.apple.messages.StorageManagementExtension\")

  (system-binary \"com.apple.corespotlightd\")

  (system-binary \"com.apple.mdwrite\")))


(define rule-three-offenders

 (require-any

  (system-binary \"com.apple.Safari\")

  (system-binary \"com.apple.cfprefsd\")

  (system-binary \"com.apple.mail\")

  (system-binary \"com.apple.lsd\")

  (system-binary \"com.apple.sharingd\")

  (system-binary \"com.apple.dataaccess.dataaccessd\")

  (system-binary \"com.apple.defaults\")))


(with-filter

 (require-not (require-any bastion-usual-offenders rule-one-offenders))

  (allow (with user-approval \"BastionRule-1\") file*

   (subpath \"${ANY_USER_HOME}/Library/Application Support/Google/Chrome/Default/\"))

  (allow (with user-approval \"BastionRule-1\") file*

   (subpath \"${ANY_USER_HOME}/Library/Application Support/Firefox/Profiles/\"))

  (allow (with user-approval \"BastionRule-1\") file* 

   (subpath \"${ANY_USER_HOME}/Library/Safari/\")))

(with-filter

 (require-not (require-any bastion-usual-offenders rule-two-offenders))

  (allow (with user-approval \"BastionRule-2\") file* 

   (subpath \"${ANY_USER_HOME}/Library/Messages/\"))

  (allow (with user-approval \"BastionRule-2\") file*

   (subpath \"${ANY_USER_HOME}/Library/Application Support/Microsoft/Teams/\"))

  (allow (with user-approval \"BastionRule-2\") file*

   (subpath \"${ANY_USER_HOME}/Library/Application Support/Slack/\"))

  (allow (with user-approval \"BastionRule-2\") file*

   (subpath \"${ANY_USER_HOME}/Library/Application Support/WhatsApp/\")))


(with-filter

 (require-not (require-any bastion-usual-offenders rule-three-offenders))

  (allow (with user-approval \"BastionRule-3\") file*


(literal \"${ANY_USER_HOME}/Library/Preferences/com.apple.LaunchServices.QuarantineEventsV2\")))


(with-filter

 (require-not (process-attribute is-platform-binary))

  (allow (with user-approval \"BastionRule-4\") socket-ioctl

   (ioctl-command SIOCIFCREATE SIOCGIFDESC)))
```

When these data are analyzed, it is understood that Bastion rules are the working mechanism.
It sets up four filters using the `with-filter` construct to control access permissions based on specific conditions:

1. The first filter, **BastionRule-1**, restricts file access to specific subpaths within Chrome, Firefox, and Safari application support directories, but only if the accessed binary is not part of either `bastion-usual-offenders` or `rule-one-offenders`.
2. The second filter, **BastionRule-2**, allows access to subpaths within Messages, Microsoft Teams, Slack, and WhatsApp application support directories, but only if the accessed binary is not part of either `bastion-usual-offenders` or `rule-two-offenders`.
3. The third filter, **BastionRule-3**, grants access to a specific file (`com.apple.LaunchServices.QuarantineEventsV2`) in the user's preferences directory (`Library/Preferences/`) but only if the accessed binary is not part of either `bastion-usual-offenders` or `rule-three-offenders`.
4. The fourth filter, **BastionRule-4**, allows access to a specific socket ioctl command (`SIOCIFCREATE` and `SIOCGIFDESC`), but only if the process attribute `is-platform-binary` is not satisfied.

There are four types of Bastion rules. For example, with the **BastionRule-1** type rule, it is understood that the application movements in the `~/Library/Application Support/` directory are followed.

> In macOS Ventura, these behaviors are not blocked; they are saved in the XPdb database.
{: .prompt-warning }

## Conclusion
macOS has many built-in security features that provide good security for the average user. With regular updates and responsible user practices, macOS is a great option for everyday use. 

For those who are looking for a more secure, safe, and reliable computing experience, learning the details of Apple's security utilities is a great way to safeguard user data and privacy. Understanding long-standing applications such as File Quarantine and Gatekeeper and new features such as XProtect Remediator would help users and security professionals attain a higher level of security.

> This post is shared on [Picus Blog](https://www.picussecurity.com/resource/blog/securing-macos-a-closer-look-at-built-in-macos-application-security) as well.
{: .prompt-tip }

