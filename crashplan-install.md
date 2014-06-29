http://pcloadletter.co.uk/2012/01/30/crashplan-syno-package/comment-page-17/

CrashPlan packages for Synology NAS
2,363 Replies
UPDATE – The instructions and notes on this page apply to all three versions of the package hosted on my repo: CrashPlan, CrashPlan PRO, and CrashPlan PROe.

CrashPlan is a popular online backup solution which supports continuous syncing. With this your NAS can become even more resilient – it could even get stolen or destroyed and you would still have your data. Whilst you can pay a small monthly charge for a storage allocation in the Cloud, one neat feature CrashPlan offers is for individuals to collaboratively backup their important data to each other – for free! You could install CrashPlan on your laptop and have it continuously protecting your documents to your NAS, even whilst away from home.

CrashPlan-Windows

CrashPlan is a Java application, and one that’s typically difficult to install on a NAS – therefore an obvious candidate for me to simplify into a package, given that I’ve made a few others. I tried and failed a few months ago, getting stuck at compiling the Jtux library for ARM CPUs (the Oracle Java for Embedded doesn’t come with any headers).

I noticed a few CrashPlan setup guides linking to my Java package, and decided to try again based on these: Kenneth Larsen’s blog post, the Vincesoft blog article for installing on ARM processor Iomega NAS units, and this handy PDF document which is a digest of all of them, complete with download links for the additional compiled ARM libraries. I used the PowerPC binaries Christophe had compiled on his chreggy.fr blog, so thanks go to him. I wanted make sure the package didn’t require the NAS to be bootstrapped, so I picked out the few generic binaries that were needed (bash, nice and cpio) directly from the Optware repo.

Aside from the challenges of getting the library dependencies fixed for ARM and QorIQ PowerPC systems, there was also the matter of compliance – Code 42 Software’s EULA prohibits redistribution of their work. I had to make the syno package download CrashPlan for Linux (after the end user agrees their EULA), then I had to write my own script to extract this archive and mimic their installer, since their installer is interactive. It took a lot of slow testing, but I managed it!

CPPROe package info

My most recent package version introduces handling of the automatic updates which Code 42 sometimes publish to the clients. This has proved to be quite a challenge to get working as testing was very laborious. I can confirm that it worked with the update from CrashPlan PRO 3.2 to 3.2.1 , and from CrashPlan 3.2.1 to 3.4.1:

CrashPlan-update-repair

 
Installation

This package is for Marvell Kirkwood, Marvell Armada 370/XP, Intel and Freescale QorIQ/PowerQUICC PowerPC CPUs only, so please check which CPU your NAS has. It will work on an unmodified NAS, no hacking or bootstrapping required. It will only work on older PowerQUICC PowerPC models that are running DSM 5.0. It is technically possible to run CrashPlan on older DSM versions, but it requires chroot-ing to a Debian install. Christophe from chreggy.fr has recently released packages to automate this.
In the User Control Panel in DSM, enable the User Homes service.
Install the package directly from Package Center in DSM. In Settings -> Package Sources add my package repository URL which is http://packages.pcloadletter.co.uk.
You will need to install either one of my Java SE Embedded packages first (Java 6 or 7). Read the instructions on that page carefully too.
If you previously installed CrashPlan manually using the Synology Wiki, you can find uninstall instructions here.
 
Notes

The package downloads the CrashPlan installer directly from Code 42 Software, following acceptance of their EULA. I am complying with their wish that no one redistributes it.

CrashPlan is installed in headless mode – backup engine only. This is configured by a desktop client, but operates independently of it.
The engine daemon script checks the amount of system RAM and scales the Java heap size appropriately (up to the default maximum of 512MB). This can be overridden in a persistent way if you are backing up very large backup sets by editing /volume1/@appstore/CrashPlan/syno_package.vars. If you’re considering buying a NAS purely to use CrashPlan and intend to back up more than a few hundred GB then I strongly advise buying one of the Intel models which come with 1GB RAM and can be upgraded to 3GB very cheaply. RAM is very limited on the ARM ones. 128MB RAM on the J series means CrashPlan is running with only one fifth of the recommended heap size, so I doubt it’s viable for backing up very much at all. My DS111 has 256MB of RAM and currently backs up around 60GB with no issues. I have found that a 512MB heap was insufficient to back up more than 2TB of files on a Windows server. It kept restarting the backup engine every few minutes until I increased the heap to 1024MB.

As with my other syno packages, the daemon user account password is randomized when it is created using the openssl binary. DSM Package Center runs as the root user so my script starts the package using an su command. This means that you can change the password yourself and CrashPlan will still work.
The default location for saving friends’ backups is set to /volume1/crashplan/backupArchives (where /volume1 is you primary storage volume) to eliminate the chance of them being destroyed accidentally by uninstalling the package.

The first time you run the server you will need to stop it and restart it before you can connect the client. This is because a config file that’s only created on first run needs to be edited by one of my scripts. The engine is then configured to listen on all interfaces on the default port 4243.
Once the engine is running, you can manage it by installing CrashPlan on another computer, and editing the file conf/ui.properties on that computer so that this line:

#serviceHost=127.0.0.1
is uncommented (by removing the hash symbol) and set to the IP address of your NAS, e.g.:
serviceHost=192.168.1.210

On Windows you can also disable the CrashPlan service if you will only use the client.
If you need to manage CrashPlan from a remote location, I suggest you do so using SSH tunnelling as per this support document.
The package supports upgrading to future versions while preserving the machine identity, logs, login details, and cache. Upgrades can now take place without requiring a login from the client afterwards.

If you remove the package completely and re-install it later, you can re-attach to previous backups. When you log in to the Desktop Client with your existing account after a re-install, you can select “adopt computer” to merge the records, and preserve your existing backups. I haven’t tested whether this also re-attaches links to friends’ CrashPlan computers and backup sets, though the latter does seem possible in the Friends section of the GUI. It’s probably a good idea to test that this survives a package reinstall before you start relying on it. Sometimes, particularly with CrashPlan PRO I think, the adopt option is not offered. In this case you can log into CrashPlan Central and retrieve your computer’s GUID. On the CrashPlan client, double-click on the logo in the top right and you’ll enter a command line mode. You can use the GUID command to change the system’s GUID to the one you just retrieved from your account.
The log which is displayed in the package’s Log tab is actually the activity history. If you’re trying to troubleshoot an issue you will need to use an SSH session to inspect the two engine log files which are:

/volume1/@appstore/CrashPlan/log/engine_output.log

/volume1/@appstore/CrashPlan/log/engine_error.log

When CrashPlan downloads and attempts to run an automatic update, the script will most likely fail and stop the package. This is typically caused by syntax differences with the Synology versions of certain Linux shell commands (like rm, mv, or ps). You will need to wait several minutes in the event of this happening before you take action, because the update script tries to restart CrashPlan 10 times at 10 second intervals. After this, you simply start the package again in Package Center and my scripts will fix the update, then run it. One final package restart is required before you can connect with the CrashPlan Desktop client (remember to update that too).

After their backup is seeded some users may wish to schedule the CrashPlan engine using cron so that it only runs at certain times. This is particularly useful on ARM systems because CrashPlan currently prevents hibernation while it is running (unresolved issue, reported to Code 42). To schedule, edit /etc/crontab and add the following entries for starting and stopping CrashPlan:

55 2 * * * root /var/packages/CrashPlan/scripts/start-stop-status start

0  4 * * * root /var/packages/CrashPlan/scripts/start-stop-status stop

This example would configure CrashPlan to run daily between 02:55 and 04:00am. CrashPlan by default will scan the whole backup selection for changes at 3:00am so this is ideal. The simplest way to edit crontab if you’re not really confident with Linux is to install Merty’s Config File Editor package, which requires the official Synology Perl package to be installed too (since DSM 4.2). After editing crontab you will need to restart the cron daemon for the changes to take effect:

/usr/syno/etc.defaults/rc.d/S04crond.sh stop

/usr/syno/etc.defaults/rc.d/S04crond.sh start

It is vitally important that you do not improvise your own startup commands or use a different account because this will most likely break the permissions on the config files, causing additional problems. The package scripts are designed to be run as root, and they will in turn invoke the CrashPlan engine using its own dedicated user account.

If you update DSM later, you will need to re-install the Java package or else UTF-8 and locale support will be broken by the update.
If you decide to sign up for one of CrashPlan’s paid backup services as a result of my work on this, I would really appreciate it if you could use this affiliate link, or consider donating using the PayPal button on the right.

Changelog:

* 0027 Fixed open file handle limit for very large backup sets (ulimit fix)
* 0026 Updated all CrashPlan clients to version 3.6.3, improved handling of Java temp files
* 0025 glibc version shim no longer used on Intel Synology models running DSM 5.0
* 0024 Updated to CrashPlan PROe 3.6.1.4 and added support for PowerPC 2010 Synology models running DSM 5.0
* 0023 Added support for Intel Atom Evansport and Armada XP CPUs in new DSx14 products
* 0022 Updated all CrashPlan client versions to 3.5.3, compiled native binary dependencies to add support for Armada 370 CPU (DS213j), start-stop-status.sh now * updates the new javaMemoryHeapMax value in my.service.xml to the value defined in syno_package.vars
* 0021 Updated CrashPlan to version 3.5.2
* 0020 Fixes for DSM 4.2
* 018 Updated CrashPlan PRO to version 3.4.1
* 017 Updated CrashPlan and CrashPlan PROe to version 3.4.1, and improved in-app update handling
* 016 Added support for Freescale QorIQ CPUs in some x13 series Synology models, and installer script now downloads native binaries separately to reduce repo * hosting bandwidth, PowerQUICC PowerPC processors in previous Synology generations with older glibc versions are not supported
* 015 Added support for easy scheduling via cron – see updated Notes section
* 014 DSM 4.1 user profile permissions fix
* 013 implemented update handling for future automatic updates from Code 42, and incremented CrashPlanPRO client to release version 3.2.1
* 012 incremented CrashPlanPROe client to release version 3.3
* 011 minor fix to allow a wildcard on the cpio archive name inside the main installer package (to fix CP PROe client since Code 42 Software had amended the cpio * file version to 3.2.1.2)
* 010 minor bug fix relating to daemon home directory path
* 009 rewrote the scripts to be even easier to maintain and unified as much as possible with my imminent CrashPlan PROe server package, fixed a timezone bug (* tightened regex matching), moved the script-amending logic from installer.sh to start-stop-status.sh with it now applying to all .sh scripts each startup so * perhaps updates from Code42 might work in future, if wget fails to fetch the installer from Code42 the installer will look for the file in the public shared * folder
* 008 merged the 14 package scripts each (7 for ARM, 7 for Intel) for CP, CP PRO, & CP PROe – 42 scripts in total – down to just two! ARM & Intel are now * supported by the same package, Intel synos now have working inotify support (Real-Time Backup) thanks to rwojo’s shim to pass the glibc version check, upgrade * process now retains login, cache and log data (no more re-scanning), users can specify a persistent larger max heap size for very large backup sets
* 007 fixed a bug that broke CrashPlan if the Java folder moved (if you changed version)
* 006 installation now fails without User Home service enabled, fixed Daylight Saving Time support, automated replacing the ARM libffi.so symlink which is * destroyed by DSM upgrades, stopped assuming the primary storage volume is /volume1, reset ownership on /var/lib/crashplan and the Friends backup location after * installs and upgrades
* 005 added warning to restart daemon after 1st run, and improved upgrade process again
* 004 updated to CrashPlan 3.2.1 and improved package upgrade process, forced binding to 0.0.0.0 each startup
* 003 fixed ownership of /volume1/crashplan folder
* 002 updated to CrashPlan 3.2
* 001 intial public release
