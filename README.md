# sams2
sams2 squid 5.5 centos 9


make -f Makefile.cvs
./configure
вивід:
Use MySQL API: yes
Use PostgreSQL API: no
Use unixODBC API: no
Use LDAP API: yes
Using pcre: pcre
Use dynamic plugin: yes

патч для файлу libtool
команда
patch -l  вставити текст. декілька разів Ctrl-D. для збереження

--- libtool.old 2012-02-14 17:34:10.363994833 +0400
+++ libtool     2012-02-14 15:55:27.142358890 +0400
@@ -5986,7 +5986,8 @@
        case $dir in
        [\\/]* | [A-Za-z]:[\\/]*) ;;
        *)
-         absdir=`cd "$dir" && pwd`
+#        absdir=`cd "$dir" && pwd`
+         absdir="/usr/lib"
          test -z "$absdir" && \
            func_fatal_error "cannot determine absolute directory name of \`$dir'"
          dir="$absdir"


збираємо SAMS2:
make
make install


 http://localhost/setup.php по інструкції


/usr/local/etc/sams2.conf 
DB_USER=username
DB_PASSWORD=userpassword
SQUIDROOTDIR=/etc/squid
SQUIDLOGDIR=/var/log/squid


/etc/systemd/system/sams2.service

cp sams2.service /etc/systemd/system/

/etc/systemd/system/sams2.service
текст
[Unit]
Description=SAMS Access Management System
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/bin/sams2daemon -f /usr/local/etc/sams2.conf
ExecStop=/usr/local/bin/sams2daemon --stop
PIDFile=/var/run/sams2daemon.pid
TimeoutSec=300
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target



systemctl daemon-reload
systemctl enable sams2.service
systemctl start sams2.service




(далі не пам'ятаю, пізніше перевстановлю систему перевірю чи треба робити, шмат з другої інструкції)
Делаем symlink (иначе sams не будет работать): sudo ln -s /usr/sbin/squid3 /usr/sbin/squid
Копируем из папки с исходниками скрипт для init.d: sudo cp debian/init.d /etc/init.d/sams2daemon
Даём права на исполнение: sudo chmod a+x /etc/init.d/sams2daemon


