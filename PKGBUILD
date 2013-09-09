# $Id: PKGBUILD 73064 2012-06-30 06:07:26Z jconder $
# Maintainer: Jonathan Conder <jonno.conder@gmail.com>
# Contributor: Giovanni Scafora <giovanni@archlinux.org>
# Contributor: Juergen Hoetzel <juergen@archlinux.org>
# Contributor: <kleptophobiac@gmail.com>
# Contributor: dorphell <dorphell@archlinux.org>

# ALARM: Kevin Mihelich <kevin@archlinuxarm.org>
#  - correctly define ARM cpus for configure
#  - disabled vdpau, mmx

pkgname=mythtv-git
pkgver=0.26.1.5~g3bce16c
pkgrel=1
epoch=2
pkgdesc="A Homebrew PVR project - 0.26/fixes branch"
arch=('i686' 'x86_64')
url="http://www.mythtv.org/"
license=('GPL')
depends=('avahi' 'fftw' 'lame' 'libass' 'libavc1394' 'libcdio' 'libiec61883'
         'libpulse' 'libxinerama' 'lirc-utils'
	 'mariadb-clients' 'mysql-python' 'perl-dbd-mysql' 'perl-io-socket-inet6'
	 'perl-libwww' 'perl-net-upnp' 'python2-lxml' 'qtwebkit' 'urlgrabber'
	 'x264')
makedepends=('git glew' 'libxml2' 'openssl' 'yasm')
optdepends=('glew: for GPU commercial flagging'
            'libcec: for consumer electronics control capabilities'
	    'libxml2: to read blu-ray metadata'
	    'openssl: for AirTunes (RAOP) support'
            'xmltv: to download tv listings')
conflicts=('mythtv' 'myththemes' 'mythplugins-mythvideo')
provides=('mythtv')
# replaces=('myththemes' 'mythplugins-mythvideo')
backup=('etc/conf.d/mythbackend')
install='mythtv.install'
_gitbranch='fixes/0.26'
source=("git://github.com/MythTV/mythtv.git#branch=$_gitbranch"
	'mythbackend.service')
md5sums=('SKIP'
	'e4d572dcc307d6d8ae26bee5aebf9f3a')
_gitname="mythtv"

pkgver() {
    cd $srcdir/$_gitname
    # Use the tag of the last commit (x.y.z.<no_of_commits>~<commit_hash>)
    git describe --always | sed -e 's|^v||' -e 's|-|.|' -e 's|-|~|'
}

prepare() {
  cd $srcdir/$_gitname/mythtv
  msg "Replacing references to python..."
  find 'bindings/python' 'contrib' -type f | xargs sed -i 's@^#!.*python$@#!/usr/bin/python2@'

  msg "PHP wizardry..."
  sed -re 's@Key, &@Key, @' -i 'bindings/php/MythBase.php'
}

build() {
  cd $srcdir/$_gitname/mythtv

  [[ $CARCH == "arm" ]] && ARCH="armv5te"
  [[ $CARCH == "armv6h" ]] && ARCH="armv6l"
  [[ $CARCH == "armv7" ]] && ARCH="armv7-a"

    msg " Starting configure..."
  ./configure --prefix=/usr \
              --cpu="$ARCH" \
              --disable-mmx \
              --disable-vdpau \
	      --disable-altivec \
	      --disable-audio-jack \
	      --disable-ccache \
	      --disable-distcc \
	      --enable-libfftw3 \
	      --enable-libmp3lame \
	      --enable-libx264 \
	      --disable-vaapi \
	      --python=python2
    msg " Starting make..."
  make
}

package() {
  cd $srcdir/$_gitname/mythtv
  make INSTALL_ROOT="$pkgdir" install

  install -D -m644 "$srcdir/mythbackend.service" "$pkgdir/usr/lib/systemd/system/mythbackend.service"
  install -D -m644 'database/mc.sql' "$pkgdir/usr/share/mythtv/mc.sql"

  mkdir -p "$pkgdir/usr/share/mythtv"
  cp -R 'contrib' "$pkgdir/usr/share/mythtv"
  mkdir -p "$pkgdir/var/log/mythtv"
}
