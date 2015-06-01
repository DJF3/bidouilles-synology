Possibilité de faire un cron pour automatiser ces tâches.

#Solution 1

In order to completely stop the generation of @eaDir folders, it’s necessary to disable the services that are generating them.
```
cd /usr/syno/etc.defaults/rc.d/
chmod 000 S66fileindexd.sh S66synoindexd.sh S77synomkthumbd.sh S88synomkflvd.sh S99iTunes.sh
```
First, SSH into your Synology NAS box and log in as root, then type this to locate the @eaDir folders:
```find . -name "@eaDir" -type d | more```

If you’re happy you’re not going to accidentally delete something important, then make it happen:
```find . -name "@eaDir" -type d -print0 | xargs -0 rm -rf```

#Solution 2
To stop the folders from being created:
```
cd /usr/syno/etc.defaults/rc.d
S66synoindexd.sh stop
S77synomkthumbd.sh stop
S88synomkflvd.sh stop
S99iTunes.sh stop
chmod 000 S66synoindexd.sh S77synomkthumbd.sh S88synomkflvd.sh S99iTunes.sh
```

To re-enable the folders being created:
```
cd /usr/syno/etc.defaults/rc.d
chmod 655 S66synoindexd.sh synomkthumbd.sh S88synomkflvd.sh S99iTunes.sh
S66synoindexd.sh start
S77synomkthumbd.sh start
S88synomkflvd.sh start
S99iTunes.sh start
```

To delete *all* @eaDir folders on your system:
*CAUTION:  This will delete files without confirmation, so be sure you have it right!!*
```
cd /volume1/music (or wherever your doc root is)
find . -name @eaDir -print | while read n ; echo $n ; rm -rf "$n" ; done
```

Éviter la génération de vignettes dans windows :
-> Windows7 Computer properties ->Advanced Systems settings->Advanced->Performance settings->Customs
unchecked "Show thumbnail instead of Icon"
