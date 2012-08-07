#!/bin/sh

echo -e "\033[32;49;1m Welcome to the open source canmp, it is your best choice! \033[39;49;0m"
mkdir -p /t/temp;
cd /t/temp;
#arch=`uname -m`
#if [ "$arch" = "x86_64" ]; then
#	 echo "exclude=*.i?86">>/etc/yum.conf
#else
#	echo 'i386'
#fi

yum -y remove httpd php mysql
yum -y install yum-fastestmirror
yum -y install make gcc+ pcre-devel gcc perl gcc-c++ patch gcc-g77 flex bison tar unzip  Autoconf 

# Install virtualmin/webmin
/usr/sbin/setenforce 0
wget http://software.virtualmin.com/gpl/scripts/install.sh;sh install.sh --force;


if [ -x "/usr/libexec/webmin" ]; then
	echo "***** virtualmin installed ***** ";
else
	echo "***** virtualmin install Failed ***** ";
	exit 0;
fi


# disable some php functions for security 

/bin/cp /etc/php.ini /etc/php.ini.bak

sed -i 's/disable_functions =/;disable_functions=passthru,exec,system,chroot,chgrp,chown,shell_exec,proc_open,proc_get_status,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server/g' /etc/php.ini



# you can use /etc/php.ini.bak in virtualmin configuration if you want more security

sed -i 's/disable_functions =/disable_functions=phpinfo,system,exec,shell_exec,passthru,proc_open,proc_close, proc_get_status,checkdnsrr,getmxrr,getservbyname,getservbyport,scandir,chgrp,chown,ini_alter,fsocket,pfsockopen,openlog,syslog,readlink,symlink,popepassthru,popen,show_source,highlight_file,ini_restore,dl,socket_listen,socket_create,socket_bind,socket_accept, socket_connect, stream_socket_server,stream_socket_accept,stream_socket_client,ftp_connect,ftp_login,ftp_pasv,ftp_get,sys_getloadavg,disk_total_space, disk_free_space,posix_ctermid,posix_get_last_error,posix_getcwd, posix_getegid,posix_geteuid,posix_getgid,posix_getgrgid,posix_getgrnam,posix_getgroups,posix_getlogin,posix_getpgid,posix_getpgrp,posix_getpid, posix_getppid,posix_getpwnam,posix_getpwuid, posix_getrlimit, posix_getsid,posix_getuid,posix_isatty,posix_kill,posix_mkfifo,posix_setegid,posix_seteuid,posix_setgid, posix_setpgid,posix_setsid,posix_setuid,posix_strerror,posix_times,posix_ttyname,posix_uname/g' /etc/php.ini.bak

sed -i 's/allow_url_fopen = On/allow_url_fopen=Off/g' /etc/php.ini.bak
sed -i 's/enable_dl = On/enable_dl=Off/g' /etc/php.ini.bak

sed -i 's/\;open_basedir =/open_basedir=\$\{HOME\}\/public_html\:\$\{HOME\}\/tmp/g' /etc/php.ini.bak


# Change localtime to Shanghai time
rm -rf /etc/localtime
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# Avoid apache stupid error: server reached MaxClients setting
mv /etc/httpd/conf.d/swtune.conf /etc/httpd/conf.d/swtune.bak

# replace the new Chinese language file edited
rm -f /usr/libexec/webmin/virtual-server/lang/zh_CN_bak;
mv /usr/libexec/webmin/virtual-server/lang/zh_CN /usr/libexec/webmin/virtual-server/lang/zh_CN_bak;
wget  http://canmp.googlecode.com/files/zh_CN.virtual-server  --output-document=/usr/libexec/webmin/virtual-server/lang/zh_CN;


rm -f /usr/libexec/webmin/virtual-server-theme/lang/zh_CN_bak;
mv /usr/libexec/webmin/virtual-server-theme/lang/zh_CN /usr/libexec/webmin/virtual-server-theme/lang/zh_CN_bak;
wget  http://canmp.googlecode.com/files/ZH_CN.virtual-server-theme  --output-document=/usr/libexec/webmin/virtual-server-theme/lang/zh_CN;

#Personalized error message.
wget  http://canmp.googlecode.com/files/403.html --output-document=/var/www/error/403.html;
sed -i 's/#    ErrorDocument 403 \/error\/HTTP_FORBIDDEN.html.var/    ErrorDocument 403 \/error\/403.html/g' /etc/httpd/conf/httpd.conf

wget  http://canmp.googlecode.com/files/404.html --output-document=/var/www/error/404.html;
sed -i 's/#    ErrorDocument 404 \/error\/HTTP_NOT_FOUND.html.var/    ErrorDocument 404 \/error\/404.html/g' /etc/httpd/conf/httpd.conf

wget  http://canmp.googlecode.com/files/500.html --output-document=/var/www/error/500.html;
sed -i 's/#    ErrorDocument 500 \/error\/HTTP_INTERNAL_SERVER_ERROR.html.var/    ErrorDocument 500 \/error\/500.html/g' /etc/httpd/conf/httpd.conf

wget  http://canmp.googlecode.com/files/503.html --output-document=/var/www/error/503.html;
sed -i 's/#    ErrorDocument 503 \/error\/HTTP_SERVICE_UNAVAILABLE.html.var/    ErrorDocument 503 \/error\/503.html/g' /etc/httpd/conf/httpd.conf




# Change the default charset from UTF-8 to off
sed -i 's/UTF-8/Off/g' /etc/httpd/conf/httpd.conf
sed -i 's/StartServers       8/StartServers       3/g' /etc/httpd/conf/httpd.conf
sed -i 's/MinSpareServers    5/MinSpareServers    2/g' /etc/httpd/conf/httpd.conf
sed -i 's/ServerTokens OS/ServerTokens ProductOnly/g' /etc/httpd/conf/httpd.conf


echo -e "<IfModule mod_deflate.c>\nSetOutputFilter DEFLATE\nAddOutputFilterByType DEFLATE text/html image/jpe image/png image/gif text/css application/x-javascript\n</IfModule>" > /etc/httpd/conf.d/bak_deflate.conf

# skip-innodb and skip-bdb for mysql, if you don't want skip them, delete this line
sed -i "s/# Disabling symbolic-links/skip-innodb\nskip-bdb\n#Disabling symbolic-links/g" /etc/my.cnf 

pass1=`openssl rand 6 -base64`
echo "canmp.${pass1}"
mysqladmin -u root password "canmp.${pass1}"
sed --in-place -e 's/pass=/pass_old=/g' /etc/webmin/mysql/config
echo "pass=canmp.${pass1}" >> /etc/webmin/mysql/config

service mysqld restart

# Stop some services(you can turn them on later if you want) and free memory
service portmap stop; chkconfig portmap off
service nfslock stop; chkconfig nfslock off
/etc/init.d/cups stop; chkconfig cups off
chkconfig yum-updatesd off
mv /etc/cron.daily/mlocate.cron /etc/cron.monthly/
service named stop ; chkconfig named off;
service spamd stop ; chkconfig spamd off;
service spamassassin stop; chkconfig spamassassin off;
service dovecot stop; chkconfig dovecot off;
#service postfix stop; chkconfig postfix off;
service mailman stop; chkconfig mailman off;

# update webmin configure
echo 'lang_root=zh_CN' >> /etc/webmin/config
sed --in-place -e 's/1048576//g' /etc/webmin/virtual-server/plans/0

rm -f /etc/webmin/virtual-server/config_bak;
mv /etc/webmin/virtual-server/config /etc/webmin/virtual-server/config_bak;
wget  http://canmp.googlecode.com/files/virtualmin.config  --output-document=/etc/webmin/virtual-server/config;

sed --in-place -e 's/# Form header/print "$text{'\''new_website_desc'\''}<p>"\; #from header/g' /usr/libexec/webmin/virtual-server/domain_form.cgi;



# upgrade RPMforge
wget http://canmp.googlecode.com/files/rpmforge.sh;sh rpmforge.sh;

# update packages
yum -y install mcrypt mbstring php-mbstring php-mcrypt php-mhash php-xml php-gd php-dom vim* php-soap lftp;


# upgrade to php5.2
wget http://canmp.googlecode.com/files/php5.2.sh;sh php5.2.sh;
yum -y upgrade;

#change the noindex.html
rm -f /var/www/error/noindex.html;
wget http://canmp.googlecode.com/files/noindex.html --output-document=/var/www/error/noindex.html;

#insta;; rpaf_module
#wget http://canmp.googlecode.com/files/rpaf.sh;sh rpaf.sh;

#Install suhosin v0.9.31
#wget http://canmp.googlecode.com/files/suhosin.sh;sh suhosin.sh;

# Install ionCube v4.0.12
wget http://canmp.googlecode.com/files/ionCube.sh;sh ionCube.sh;

#Install eacclerator v0.9.5.3 
#wget http://canmp.googlecode.com/files/eaccelerator.sh;sh eaccelerator.sh;

# Install zend optimizer v3.3.9
wget http://canmp.googlecode.com/files/zend.sh;sh zend.sh;


#Install nginx
#wget http://canmp.googlecode.com/files/canmp_nginx.sh;sh canmp_nginx.sh;

#finish
wget http://canmp.googlecode.com/files/show.sh;sh show.sh;