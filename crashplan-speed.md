http://networkrockstar.ca/2013/09/speeding-up-crashplan-backups/

I navigated to the CrashPlan configuration directory, /usr/local/crashplan/conf/ (this is on Linux; Windows users, you’ll have to figure out there this is yourself, sorry!), and started digging.  I stumbled across this gem in the file my.service.xml :

syno : `/volume1/@appstore/CrashPlan/conf/my.service.xml` (à ajouter dans config file editor pour plus de simplicité)

`<dataDeDupAutoMaxFileSize>1073741824</dataDeDupAutoMaxFileSize>`
`<dataDeDupAutoMaxFileSizeForWan>0</dataDeDupAutoMaxFileSizeForWan>`

Hmm.  Interesting.  “0” is often used as a metavalue that means “unlimited”… so maybe CrashPlan will ALWAYS dedupe EVERYTHING when going over a WAN link, but only dedupes files smaller than 1GB when going over a LAN link, presumably because they recognize that there’s a balance between CPU capacity and network capacity.  It seems that they assume everyone has ridiculously slow upload speeds that are typical of most residential Internet connections.

Being the smarty that I am, I figured I’d set it to not dedupe any files larger than 1 byte when going over the WAN:

`<dataDeDupAutoMaxFileSizeForWan>1</dataDeDupAutoMaxFileSizeForWan>`

(Note:  If you use backup sets, you will have more than one of these lines, one per backup set.  I suggest changing them all;  I have not done any testing to see what happens if you disable it only for some of the backup sets.)

I then restarted the CrashPlan engine (/etc/init.d/crashplan restart).

And… VOILA.    My CPU usage dropped from 100% of 1 core down to ~10% of 1 core, and my upload jumped from 2.5Mbps (and dropping) to 7.5Mbps (and holding/fluctuating between 6.9Mbps and 7.5Mbps).   I’m now seeing patterns that more accurately cover the “variable network bandwidth due to shared service” theory, without seeing logarithmic decay in performance.
