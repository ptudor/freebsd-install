### freebsd-install
January 2016

#### Give root more history
```bash
sed -ie "s/1000/9900/g" /root/.cshrc
```
#### Some filesystems before we get started
(todo: the zfs defaults before this point.)
```bash
zfs create zroot/var/db
zfs create zroot/var/db/freebsd-update
zfs create zroot/usr/local
zfs create zroot/usr/local/bin
zfs create zroot/usr/local/etc
zfs create zroot/usr/local/lib
zfs create zroot/usr/local/libexec
zfs create zroot/usr/local/man
zfs create zroot/usr/local/sbin
zfs create zroot/usr/local/www
zfs create zroot/usr/ports/distfiles
zfs create zroot/var/db/pkg
zfs create zroot/var/db/ports
zfs create zroot/var/db/portsnap
zfs create zroot/var/db/mysql
zfs set recordsize=16k zroot/var/db/mysql
zfs create zroot/var/portbuild
zfs set compression=off zroot/var/portbuild
```

#### Update the OS and fetch ports
```bash
cp /dev/null /etc/motd && /usr/libexec/locate.updatedb &
freebsd-update fetch && freebsd-update install && \
portsnap fetch && portsnap extract && \
cd /usr/ports/ports-mgmt/portmaster/ && make install clean 
```

#### Add some software

Let's get started with openssl and openssh, enough to stop and reboot.
```bash
portmaster security/openssl security/openssh-portable 
portmaster security/gnutls security/polarssl security/wolfssl \
  security/sudo shells/bash sysutils/tmux net/mosh sysutils/coreutils
```
Since we're running openssh from ports on 22, run base on port 222 and firewall.
```bash
sed -i -e "s/^#Port 22/Port 222/g" /etc/ssh/sshd_config && \
sed -i -e "s/^#ChallengeResponseAuthentication yes/ChallengeResponseAuthentication no/g" /etc/ssh/sshd_config && \
sed -i -e "s/^#ChallengeResponseAuthentication yes/ChallengeResponseAuthentication no/g" /usr/local/etc/ssh/sshd_config 
cat <<EOF >> /usr/local/etc/ssh/ssh_config
Host *
  ControlMaster auto
  ControlPath /tmp/ssh-%u@%L-%r@%h-%p
  ForwardX11 yes
  VerifyHostKeyDNS yes
EOF

```
ChallengeResponseAuthentication

We'll probably do something with DNS or HTTP.
```bash
portmaster dns/unbound dns/nsd net/rsync net/ntp-devel www/nginx-devel dns/ldns
/usr/local/sbin/unbound-anchor && /usr/local/sbin/unbound-control-setup
```

Drop in some more packages.
```bash
portmaster security/nmap ftp/wget ftp/curl editors/vim-lite editors/pico-alpine \ 
 net-mgmt/irrtoolset games/cowsay games/lolcat games/dopewars
```

Pick and choose some common ones...
```bash
portmaster audio/darkice devel/git devel/subversion net-mgmt/zabbix24-agent
 databases/redis databases/memcached audio/mpg123 multimedia/ffmpeg
 x11-fonts/linuxlibertine x11-fonts/ubuntu-font x11-fonts/webfonts x11-fonts/liberation-fonts-ttf
 x11-fonts/dejavu x11-fonts/font-bitstream-100dpi x11-fonts/font-bitstream-75dpi
 security/keybase security/outguess lang/gawk mail/mutt mail/alpine
 graphics/optipng security/scanssh dns/sshfp dns/nss_mdns
 databases/mysql56-client databases/mysql56-server 
 security/john graphics/barcode graphics/libqrencode multimedia/rtmpdump
 security/scrypt security/aescrypt graphics/dmtx-utils graphics/libdmtx
```
