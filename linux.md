# linux command tips
## timezone
```sh
date -R # show time zone
sudo timedatectl set-timezone Asia/Shanghai 
date -R
```
## hostname
`sudo hostnamectl set-hostname new-hostname`
`sudo vi /etc/host`
127.0.1.1 new-hostname

## new user
`adduser ubuntu`
enter your password
```bash
mkdir -p /home/ubuntu/.ssh
chown -R ubuntu /home/ubuntu/.ssh
touch /home/ubuntu/.ssh/authorized_keys
```
`echo '**pub key**' >>/home/ubuntu/.ssh/authorized_keys`


### sudo no pwd
```bash
bash -c 'echo "ubuntu ALL=(ALL) NOPASSWD: ALL" >>/etc/sudoers'
cat /etc/sudoers
```

### switch user and load path
```bash
su - ubuntu
```
### better ll && cd
```sh
sed -i "s/ls -alF/ls -alFh/g" ~/.bashrc
```
```sh
cat >>~/.bashrc <<EOF
function cd {
  builtin cd "\$@" && ll
}
EOF
source ~/.bashrc
```


### 3 goto
`vi ~/bin/goto`
```sh
ssh-add ~/.ssh/id_ed25519
ssh -A -p PORT user@host
```
## ssh secure
```config
sudo vi /etc/ssh/sshd_config

PermitRootLogin no # 禁止root用户远程登录
PasswordAuthentication no # 禁止使用基于密码的认证
PubkeyAuthentication yes   # 启用公钥认证
PermitEmptyPasswords no # 禁止使用空密码的用户登录
ChallengeResponseAuthentication no # 禁用挑战-应答认证，仅使用密码或密钥认证
UsePAM no # 禁用 PAM，仅依赖 SSH 内置的密码/密钥认证

sudo systemctl status ssh 
sudo systemctl reload ssh # 平滑重载，避免断联
sudo systemctl restart ssh # 完全重启，中断连接
```

# mirror
change
`deb http://archive.ubuntu.com/ubuntu focal main restricted`
to
`deb mirror://mirrors.ubuntu.com/mirrors.txt focal main restricted`
```sh
sudo sed -i -e 's/http:\/\/archive/mirror:\/\/mirrors/' -e 's/\/ubuntu/\/mirrors.txt/' /etc/apt/sources.list
```


# ansible
## Control node requirements
Red Hat, Debian, Ubuntu, macOS, BSDs, WSL(Windows under a Windows Subsystem for Linux)

## simple install
sudo apt install python3-pip
## [official install_on_ubuntu](https://docs.ansible.com/ansible/6/installation_guide/installation_distros.html#installing-ansible-on-ubuntu)
```sh
sudo apt update
sudo apt install software-properties-common -y
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```
## pip install
```sh
# install
python3 -m pip install --user ansible
# upgrade
python3 -m pip install --upgrade --user ansible
```
## ask pass
```
ansible all -i ./hosts -m ping -c ssh --ask-pass
```
## host
create a host file, like: `vi ./hosts`
```
192.168.2.1
[groupA]
192.168.1.[1:100]

[groupA:vars]
ansible_ssh_user=ubuntu

[all:vars]
ansible_ssh_user=root
```
cat ~/.ssh/config
`
StrictHostKeyChecking=accept-new
`or
`
StrictHostKeyChecking no
`
## ansible command
- `ansible -i ./hosts groupA -m ping`
- `ansible -i ./hosts groupA -m shell -a "uptime"`
- `-b`to become root
- `-K`to input root passwd
- `ansible all -i ./hosts -m shell -a "apt update" -b -K`
## apt
```sh
ansible ar -i ~/ar/host.ar -m apt -a "update_cache=yes"
ansible ar -i ~/ar/host.ar -m apt -a "name=atop,nethogs,nload,sysstat,iotop,htop,lm-sensors state=present" -b
```
## pip
`ansible all -i./hosts -m pip -a "requirements=/path/to/your/requirements.txt extra_args='--index-url https://pypi.tuna.tsinghua.edu.cn/simple/'"`
## mkdir
```sh
ansible all -i ./hosts -m file -a "dest=/path/to/c state=directory"
```
## copy
```sh
ansible group1  -m copy -a "src=test.conf dest=/root  mode=644 owner=root"
```
## file
```sh
# delete file
ansible <host> -m file -a "path=/path/to/file state=absent"
# create file
ansible <host> -m file -a "path=/path/to/file state=present"

```
## mount
```sh
ansible <host> -m mount -a "path=/path/to/mount state=unmounted"

```

# tmux
vi ~/.tmux.conf
`set -g mouse on` or `ctrl+b :` `setw -g mouse on`
# docker mirror
```sh
sudo bash -c 'cat >>/etc/docker/daemon.json <<EOF
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn/"]
}
EOF
'
cat /etc/docker/daemon.json
sudo systemctl restart docker
```
# docker container exit when ssh logout
```
loginctl enable-linger $UID
```
# systemd
cat frps.service 
```
[Unit]
Description=Frp Server Service
After=network.target

[Service]
Type=simple
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/bin/frps -c /etc/frp/frps.ini
LimitNOFILE=1048576

[Install]
WantedBy=multi-user.target
```
## rsync
`rsync -aS --info=progress2 src dst`


## Ubuntu 中 buff/cache 内存占用过高解决办法

方法是：
```sh
echo 1 > /proc/sys/vm/drop_caches
当然，这个文件可以设置的值分别为 1、2、3。它们所表示的含义为：

echo 1 > /proc/sys/vm/drop_caches:表示清除 pagecache。
echo 2 > /proc/sys/vm/drop_caches:表示清除回收 slab 分配器中的对象（包括目录项缓存和 inode 缓存）。slab 分配器是内核中管理内存的一种机制，其中很多缓存数据实现都是用的 pagecache。
echo 3 > /proc/sys/vm/drop_caches:表示清除 pagecache 和 slab 分配器中的缓存对象。

```

## Ubuntu挂载Windows共享文件夹
```sh
sudo apt install cifs-utils -y
sudo mount -t cifs -o username=administrator //192.168.31.26/share-folder-in-windows /share
```
## vi 保存没有权限
```sh
:w !sudo tee %
```
## nginx

[install nginx](https://nginx.org/en/linux_packages.html#Ubuntu)
```sh
sudo apt install curl gnupg2 ca-certificates lsb-release ubuntu-keyring

curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list

echo -e "Package: *\nPin: origin nginx.org\nPin: release o=nginx\nPin-Priority: 900\n" \
    | sudo tee /etc/apt/preferences.d/99nginx

sudo apt update
sudo apt install nginx
```
start nginx
```sh
sudo usermod -aG ubuntu nginx
sudo chmod -R 755 /home/ubuntu/deploy/lnpay-web/dist
chmod g+x /home/ubuntu/deploy/lnpay-web/dist/index.html

sudo systemctl start nginx
sudo nginx -s reload


```

## certbot-https 
[instructions](https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal)
```sh
sudo apt install snapd
sudo snap install core
sudo snap refresh core

sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```
## nginx conf
```conf
server {
  server_name bridgexdex.com www.bridgexdex.com;
  
  error_log /var/log/nginx/bridgexdex-error.log;
  access_log /var/log/nginx/bridgexdex-access.log;

  listen 80;
  root /home/ubuntu/deploy/bridgexdex/dist/;
  
  location / {
	try_files $uri $uri/ /index.html;
	index index.html;

	gzip on;
        gzip_min_length 1k;
        # gzip_buffers 4 16k;
        gzip_http_version 1.1;
        gzip_comp_level 4;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/gif;
        gzip_vary off;
        gzip_disable "MSIE [1-6]\.";

        if ($request_filename ~* .*\.(?:htm|html)$)  ## 配置页面不缓存
        {
                add_header Cache-Control "private, no-store, no-cache, must-revalidate, proxy-revalidate";
        }
  }
   
}
```
## github 多个密钥
`vi ~/.ssh/config`
```sh
Host github-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_work
  IdentitiesOnly yes
```
`git remote set-url origin git@github-work:username/repo.git`
# end
