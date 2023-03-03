# linux command tips
# timezone
```sh
date -R # show time zone
sudo timedatectl set-timezone Asia/Shanghai 
date -R
```

# better ll && cd
```sh
sed -i "s/ls -alF/ls -alFh/g" ~/.bashrc
cat >>~/.bashrc <<EOF
function cd {
  builtin cd "$@" && ll
}
EOF
source ~/.bashrc
```
# ssh 
## jumper server forward
### 1
enable `AllowAgentForwarding yes` in `/etc/ssh/sshd_config` in target server host
reload sshd server
## reload
```sh
/etc/init.d/ssh reload
/etc/init.d/sshd reload
```
or
```sh
sudo systemctl reload ssh
sudo systemctl reload sshd
```
### 2 client forward
add `ForwardAgent    yes` to `~/.ssh/config`
```config
Host    xxx
    HostName        xxx.xxx.xxx.xxx
    User            ubuntu
    Port            22
    IdentityFile    ~/.ssh/id_ed25519
    ForwardAgent    yes
```
or add `-A` option in ssh:`ssh -A -p PORT user@host`

### 3 goto
`vi ~/bin/goto`
```sh
ssh-add ~/.ssh/id_ed25519
ssh -A -p PORT user@host
```
## ssh secure
```config
PermitRootLogin no # 禁止root用户远程登录
PasswordAuthentication no # 是否允许使用基于密码的认证
PermitEmptyPasswords no # 禁止使用空密码的用户登录
ChallengeResponseAuthentication no
UsePAM no
sudo systemctl reload sshd
```

# mirror
change
`deb http://archive.ubuntu.com/ubuntu focal main restricted`
to
`deb mirror://mirrors.ubuntu.com/mirrors.txt focal main restricted`
```sh
sudo sed -i -e 's/http:\/\/archive/mirror:\/\/mirrors/' -e 's/\/ubuntu/\/mirrors.txt/' /etc/apt/sources.list
```

# hostname
`sudo hostnamectl set-hostname username`
`sudo vi /etc/host`
127.0.1.1 username

# sudo no passwd
```sh
sudo bash -c 'echo "ubuntu ALL=(ALL) NOPASSWD: ALL" >>/etc/sudoers'
cat /etc/sudoers
```
# ansible
## simple install
sudo apt install python3-pip
## [official install](https://docs.ansible.com/ansible/6/installation_guide/installation_distros.html#installing-ansible-on-ubuntu)
```sh
sudo apt update
sudo apt install software-properties-common -y
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```
## host
vi host.NAME
```
[NAME]
192.168.1.[1:100]

[NAME:vars]
ansible_ssh_user=ubuntu
```
cat ~/.ssh/config
`
StrictHostKeyChecking=accept-new
`or
`
StrictHostKeyChecking no
`
## ansible command
`ansible -i host.NAME NAME -m ping`
`ansible -i host.NAME NAME -m shell -a "uptime"`
`-b`to become root`-K`to input root passwd

## copy
```sh
ansible group1  -m copy -a "src=test.conf dest=/root  mode=644 owner=root"
```
## file
```sh
ansible <host> -m file -a "path=/path/to/file state=absent"
ansible <host> -m file -a "path=/path/to/file state=present"

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
# end
