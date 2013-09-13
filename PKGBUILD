# Contributor: vacapetr@centrum.cz

pkgname=mythplugins-mythweb-git
pkgver=0.27.RC1.2~gc662401
pkgrel=1
arch=('i686' 'x86_64')
url="http://www.mythtv.org"
license=('GPL')
makedepends=("mythtv" 'git')
pkgdesc="Web interface for MythTV's backend 0.26-fixes"
depends=("mythtv")
optdepends=('php-apache'
              'lighttpd')
replaces=('mythplugins-mythweb')
conflicts=('mythplugins-mythweb')

_gitname="mythweb"
_gitbranch="fixes/0.27"

source=("git://github.com/MythTV/mythweb.git#branch=${_gitbranch}"
    'mythweb.ini' )
md5sums=('SKIP'
    '3232aa087fa2ff983cae2c3dcc8254b0')

backup=('etc/php/conf.d/mythweb.ini')
install='mythweb.install'

pkgver() {
    cd $srcdir/$_gitname
    # Use the tag of the last commit (x.y.z.<no_of_commits>~<commit_hash>)
    git describe --always | sed -e 's/^v//' -e 's/-/./g' -e 's/\(.*\)\.\(.*\)/\1~\2/'
}

package() {
    cd $srcdir/$_gitname
    mkdir -p "${pkgdir}/var/lib/mythtv/mythweb"/{image_cache,php_sessions,data}
    cp -R "${srcdir}/${_gitname}"/* "${pkgdir}/var/lib/mythtv/mythweb"
    chown -R http:http "${pkgdir}/var/lib/mythtv/mythweb"
    chmod g+rw "${pkgdir}/var/lib/mythtv/mythweb"/{image_cache,php_sessions,data}
    install -D -m 644 "${srcdir}/mythweb.ini" "${pkgdir}/etc/php/conf.d/mythweb.ini"
}

