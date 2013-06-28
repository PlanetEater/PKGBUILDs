# $Id: PKGBUILD 73064 2012-06-30 06:07:26Z jconder $
# Maintainer: Jonathan Conder <jonno.conder@gmail.com>
# Contributor: Giovanni Scafora <giovanni@archlinux.org>
# Contributor: Juergen Hoetzel <juergen@archlinux.org>
# Contributor: <kleptophobiac@gmail.com>
# Contributor: dorphell <dorphell@archlinux.org>

# ALARM: Kevin Mihelich <kevin@archlinuxarm.org>
#  - correctly define ARM cpus for configure
#  - disabled vdpau, mmx

pkgname=mythtv
pkgver=20130728
pkgrel=1
epoch=1
pkgdesc="A Homebrew PVR project - 0.26/fixes branch"
arch=('i686' 'x86_64')
url="http://www.mythtv.org/"
license=('GPL')
depends=('avahi' 'fftw' 'lame' 'libass' 'libavc1394' 'libcdio' 'libiec61883'
         'libpulse' 'libva' 'libvdpau' 'libxinerama' 'lirc-utils'
	 'mariadb-clients' 'mysql-python' 'perl-dbd-mysql' 'perl-io-socket-inet6'
	 'perl-libwww' 'perl-net-upnp' 'python2-lxml' 'qtwebkit' 'urlgrabber'
	 'x264')
makedepends=('glew' 'libcec' 'libxml2' 'openssl' 'yasm')
optdepends=('glew: for GPU commercial flagging'
            'libcec: for consumer electronics control capabilities'
	    'libxml2: to read blu-ray metadata'
	    'openssl: for AirTunes (RAOP) support'
            'xmltv: to download tv listings')
conflicts=('myththemes' 'mythplugins-mythvideo')
replaces=('myththemes' 'mythplugins-mythvideo')
backup=('etc/conf.d/mythbackend')
install='mythtv.install'
source=('alsa.patch'
	'mythbackend.service')
md5sums=('f64b8219e3d27a2edf96733b851e576b'
	'e4d572dcc307d6d8ae26bee5aebf9f3a')

_gitname="mythtv"
_gitroot="git://github.com/MythTV/mythtv.git"

repo_root='/nas/pkg/repos'

build() {
  msg "Connecting to GIT server...."

  if [ -f ${repo_root}/${_gitname}/HEAD ]; then
     msg "Updating the existing bare repo..."
     cd ${repo_root}/${_gitname}
     git fetch
  else
     msg "Cloning to a new bare repo..."
     git clone --bare --single-branch --branch fixes/0.26 "${_gitroot}" "${repo_root}/${_gitname}"
     #git clone "${_gitroot}" "${_gitname}"
  fi
  cd ${srcdir}
  msg "GIT checkout done or server timeout"
  msg "Cloning to a temporary build repo..."
  rm -rf "${srcdir}/${_gitname}-build"
  git clone "${repo_root}/${_gitname}" "${srcdir}/${_gitname}-build"

  msg "Starting make..."
  cd "${srcdir}/${_gitname}-build/${_gitname}"

  patch -Np1 -i "$srcdir/alsa.patch"
  msg "Replacing references to python..."
  find 'bindings/python' 'contrib' -type f | xargs sed -i 's@^#!.*python$@#!/usr/bin/python2@'

  msg "PHP wizardry..."
  sed -re 's@Key, &@Key, @' -i 'bindings/php/MythBase.php'

  [[ $CARCH == "arm" ]] && ARCH="armv5te"
  [[ $CARCH == "armv6h" ]] && ARCH="armv6l"
  [[ $CARCH == "armv7" ]] && ARCH="armv7-a"

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
	      --enable-vaapi \
	      --python=python2
  make
}

package() {
  cd "$srcdir/${_gitname}-build/${_gitname}"
  make INSTALL_ROOT="$pkgdir" install

  install -D -m644 "$srcdir/mythbackend.service" "$pkgdir/usr/lib/systemd/system/mythbackend.service"
  install -D -m644 'database/mc.sql' "$pkgdir/usr/share/mythtv/mc.sql"

  mkdir -p "$pkgdir/usr/share/mythtv"
  cp -R 'contrib' "$pkgdir/usr/share/mythtv"
  mkdir -p "$pkgdir/var/log/mythtv"
}
