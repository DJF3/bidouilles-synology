http://pcloadletter.co.uk/2012/01/30/crashplan-syno-package/comment-page-17
After their backup is seeded some users may wish to schedule the CrashPlan engine using cron so that it only runs at certain times. This is particularly useful on ARM systems because CrashPlan currently prevents hibernation while it is running (unresolved issue, reported to Code 42). To schedule, edit /etc/crontab and add the following entries for starting and stopping CrashPlan:
55 2 * * * root /var/packages/CrashPlan/scripts/start-stop-status start
0  4 * * * root /var/packages/CrashPlan/scripts/start-stop-status stop
This example would configure CrashPlan to run daily between 02:55 and 04:00am. CrashPlan by default will scan the whole backup selection for changes at 3:00am so this is ideal. The simplest way to edit crontab if you’re not really confident with Linux is to install Merty’s Config File Editor package, which requires the official Synology Perl package to be installed too (since DSM 4.2). After editing crontab you will need to restart the cron daemon for the changes to take effect:
/usr/syno/etc.defaults/rc.d/S04crond.sh stop
/usr/syno/etc.defaults/rc.d/S04crond.sh start
It is vitally important that you do not improvise your own startup commands or use a different account because this will most likely break the permissions on the config files, causing additional problems. The package scripts are designed to be run as root, and they will in turn invoke the CrashPlan engine using its own dedicated user account.
