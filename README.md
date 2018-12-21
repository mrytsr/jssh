# Jssh

## Usage

    
1. 把下面的脚本复制到 ~/jssh， 然后chmod +x ~/jssh
1. 在需要ssh登陆的服务器上，安装screen
1. 用jssh登陆

       jssh user@{yourhostname}

1. 如果在.ssh/config中事先配置好了host，那么登陆过程可以简化为，参见 
    [SSH Config 那些你所知道和不知道的事 | Deepzz's Blog](https://deepzz.com/post/how-to-setup-ssh-config.html)

       jssh {host}

1. 推荐使用ssh-agent，[ssh-agent 使用指南 - 肆意的青春 - SegmentFault 思否](https://segmentfault.com/a/1190000002449006)

![截图](https://snag.gy/hws0d8.jpg)

```
#!/bin/bash
# 本脚本用于安全，快捷的登录
source <(ssh-agent) && ssh-add ~/.ssh/id_rsa
ssh -o connecttimeout=1 -At ${@} "$(cat <<'EOF'
echo '
termcapinfo xterm* ti@:te@ 
escape ^Ss 
altscreen on 
vbell off 
maptimeout 0 
defc1 off 
caption always "(Jssh) %c:%s %D %Y%m%d%{= dd} %-Lw%45L>%{+bu}%n %t%{-}%+Lw%-09< %="
shell bash 
shelltitle "$ |bash"
' > ~/.screenrc
screen -d A;
rm ~/.ssh/ssh_auth_sock;
echo $SSH_AUTH_SOCK;
ln -sf $SSH_AUTH_SOCK ~/.ssh/ssh_auth_sock; 
grep jssh ~/.bashrc || echo "
#jssh
export SSH_AUTH_SOCK=~/.ssh/ssh_auth_sock
test x\$TERM = xscreen &&
    stty stop '' &&
    PROMPT_COMMAND='echo -ne \"\\033k\$(basename \$(pwd))\\033\\\\\"'
" >> ~/.bashrc

screen -dRR;
EOF
)"

```
