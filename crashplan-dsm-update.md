Procédure d'update du DSM pour ne mas que crashplan chie.
http://pcloadletter.co.uk/2012/01/30/crashplan-syno-package/comment-page-17/#comment-51752
----

If you update DSM later, you will need to re-install the Java package or else UTF-8 and locale support will be broken by the update.

---

Just successfully upgraded to latest DSM and Java, step by step as follows. Some steps or reboots may not have been necessary but I wanted to stay with a proven workflow!

—
Synology DS214
DSM 5.0-4482 upgrade > 5.0-4493 > 5.0-4493 Update 1
CrashPlan 3.6.3-0027
Java SE Embedded 7 – 1.7.0_55-0024 upgrade > 1.7.0_60-0025
—

NOTE: *All DSM updates require Java reinstall*

–STEP 1–
Stop CrashPlan and uninstall Java

REBOOT

–STEP 2–
Update DSM (in my case this was a 2-step upgrade to 5.0-4493 Update 1 with forced reboots between upgrades)

–STEP 3–
Reinstall Java SE Embedded 7 (if upgrading you will need to download the new version from Oracle, see link below, registration is required) and copy to ‘public’ folder. If you are not upgrading, simply reinstall the version you already have.

LINK: http://www.oracle.com/technetwork/java/embedded/downloads/javase/index.html
FILE NAME: ejre-7u60-fcs-b19-linux-arm-vfp-sflt-client_headless-07_may_2014.gz

REBOOT

–STEP 4–
Restart CrashPlan engine

–STEP 5–
Wait for CPU activity to settle then stop CrashPlan

REBOOT

–STEP 6–
Check CrashPlan user account has at least ‘read’ permissions for your backup folders

–STEP 7–
Run CrashPlan

–STEP 8–
Run CrashPlan desktop client

*The last time I performed this upgrade (April) I also uninstalled the headless CrashPlan engine, as well as Java, but that’s not necessary (at least in my situation as described above) and saves you having to re-sign in to your CrashPlan account, adopting previous backups and going through the data de-duplication process. Following the steps above I was able to start my desktop CrashPlan client and it behaved as if nothing had changed.

Hope this helps.
