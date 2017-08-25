
本文参考 https://gist.github.com/larsadler/1af5204efd0a153c599b0d38afce3dbc 
并修正部分执行步骤
# Cobbler-2.8.1-install-on-ubuntu-16.04
cobbler 2.8.1 在ubuntu 16.04.3 系统中安装和配置过程


apt-get update

apt-get install python python2.7 python-django python-netaddr python-yaml python-simplejson python-cheetah python-libvirt python-urlgrabber apache2 libapache2-mod-wsgi make debmirror fence-agents syslinux syslinux-utils
#增加安装syslinux syslinux-utils

a2enmod proxy proxy_http 

wget https://github.com/cobbler/cobbler/archive/v2.8.1.tar.gz

tar zxvf v2.8.1.tar.gz

cd cobbler-2.8.1

make

make install

make webtest

ln -s /etc/apache2/conf-available/cobbler.conf /etc/apache2/conf-enabled/cobbler.conf
 
ln -s /etc/apache2/conf-available/cobbler_web.conf /etc/apache2/conf-enabled/cobbler_web.conf 

ln -s /usr/local/lib/python2.7/dist-packages/cobbler /usr/lib/python2.7/dist-packages/ 

编辑 /usr/local/share/cobbler/web/settings.py 增加如下配置

SECRET_Key = 'cobbler '        
server = '172.171.9.1'       
next_server = '172.171.9.1'   
default_password_crypted = '$1$cobbler$M6SE55xZodWc9.vAKLJs6.'

注：可以使用 openssl passwd -1 -salt 'cobbler' 'cobbler' 生成秘钥


htdigest /etc/cobbler/users.digest "Cobbler" cobbler   #创建web登录的密码 设置的密码cobbler


mkdir /tftpboot

systemctl restart cobblerd

systemctl restart apache2

cobbler get-loaders

cobbler sync

同步完成后使用浏览器访h问http://172.171.9.1/cobbler_web 用户名 cobbler 密码 cobbler
