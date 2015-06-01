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
CAUTION:  This will delete files without confirmation, so be sure you have it right!! 
cd /volume1/music (or wherever your doc root is)
find . -name @eaDir -print | while read n ; echo $n ; rm -rf "$n" ; done
