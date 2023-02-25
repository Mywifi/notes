# linux command tips
# better ll
```sh
sed -i "s/ls -alF/ls -alFh/g" ~/.bashrc
source ~/.bashrc
```
# ssh 
## forward
### 1
enable `AllowAgentForwarding yes` in `/etc/ssh/sshd_config` in target server host
reload sshd server
### 2
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

### 3
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
```
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
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```
## host
vi host.NAME
```
[NAME]
192.168.1.[1:100]
```
## command
`ansible -i host.NAME NAME -m ping`
`ansible -i host.NAME NAME -m shell -a "uptime"`

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
# end
