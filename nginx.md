# stat() "/root/deploy/website/" failed (13: Permission denied)
chmod a+x /root/deploy/website
需要对index.html的父目录有x权限才能进入该目录，然后读取该目录下的文件
