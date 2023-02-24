# linux command tips
# ssh 
## forward
### 1
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
### 2
enable `AllowAgentForwarding yes` in `/etc/ssh/sshd_config`

## ssh secure
```config
PermitRootLogin no # 禁止root用户远程登录
PasswordAuthentication no # 是否允许使用基于密码的认证
PermitEmptyPasswords no # 禁止使用空密码的用户登录
ChallengeResponseAuthentication no
UsePAM no

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
