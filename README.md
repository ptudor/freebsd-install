# freebsd-install

sed -ie "s/1000/9900/g" /root/.cshrc

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


portsnap fetch && portsnap extract 
/usr/libexec/locate.updatedb &
freebsd-update fetch && freebsd-update install

cd /usr/ports/ports-mgmt/portmaster/ ; make install clean 

portmaster security/openssl security/polarssl security/wolfssl security/openssh-portable 
portmaster dns/unbound dns/nsd net/rsync net/ntp www/nginx-devel dns/ldns
/usr/local/sbin/unbound-anchor
portmaster security/sudo shells/bash sysutils/tmux net/mosh sysutils/coreutils
portmaster security/nmap ftp/wget ftp/curl editors/vim editors/pico-alpine 
portmaster net-mgmt/irrtoolset games/cowsay games/lolcat games/dopewars
portmaster audio/darkice devel/git devel/subversion net-mgmt/zabbix24-agent
portmaster databases/redis databases/memcached audio/mpg123 multimedia/ffmpeg
portmaster x11-fonts/linuxlibertine x11-fonts/ubuntu-font x11-fonts/webfonts x11-fonts/liberation-fonts-ttf
portmaster x11-fonts/dejavu x11-fonts/font-bitstream-100dpi x11-fonts/font-bitstream-75dpi
portmaster security/keybase security/outguess lang/gawk mail/mutt mail/alpine
portmaster graphics/optipng security/scanssh dns/sshfp dns/nss_mdns
portmaster databases/mysql56-client databases/mysql56-server 
portmaster security/john graphics/barcode graphics/libqrencode multimedia/rtmpdump
portmaster security/scrypt security/aescrypt graphics/dmtx-utils graphics/libdmtx
