# $Id: PKGBUILD 84738 2013-02-24 00:14:35Z foutrelis $
# Maintainer:  Sergej Pupykin <pupykin.s+arch@gmail.com>
# Maintainer:  Bartłomiej Piotrowski <nospam@bpiotrowski.pl>
# Contributor: Miroslaw Szot <mss@czlug.icis.pcz.pl>

_cfgdir=/etc/nginx
_tmpdir=/var/lib/nginx

pkgname=nginx-extra
_pkgname=nginx
pkgver=1.2.7
pkgrel=2
pkgdesc='Lightweight HTTP server and IMAP/POP3 proxy server, with several extra modules enabled'
arch=('i686' 'x86_64')
depends=('pcre' 'zlib' 'openssl' 'geoip' 'libxslt' 'gd' 'modsecurity27-apache' 'passenger')
makedepends=('git')
provides=("$_pkgname=$pkgver")
conflicts=("$_pkgname")
url="http://nginx.org"
license=('custom')
install=nginx.install
backup=(${_cfgdir:1}/fastcgi.conf
		${_cfgdir:1}/fastcgi_params
		${_cfgdir:1}/koi-win
		${_cfgdir:1}/koi-utf
		${_cfgdir:1}/mime.types
		${_cfgdir:1}/nginx.conf
		${_cfgdir:1}/scgi_params
		${_cfgdir:1}/uwsgi_params
		${_cfgdir:1}/win-utf
		${_cfgdir:1}/rails.conf
		${_cfgdir:1}/modsecurity.conf
		etc/logrotate.d/nginx)
source=(http://nginx.org/download/${_pkgname}-${pkgver}.tar.gz
        service
        logrotate
        rails.conf
        modsecurity.conf)
sha256sums=('2457a878943fb409ec4fcb46b43af222d06a584f93228e17a4f02b0e7bfc9de3'
            '77da8ce4d8378048606a25e09270ee187d6b226ee750b6cb4313af5549f5156a'
            'a21b564eaf83b4b4ce3a436e895bd37e000677fb314b89818f89f30caca6e6d9'
            '42f448e66a21f4afdbd97019d6f4557f0485488b8eae76be5dc89484edf88562'
            'fa6a053479fc070b9ebc70203589b528d331d0156704fa88de2466fac875cb64')

build() {
	cd "$srcdir"/${_pkgname}-${pkgver}

	./configure \
        --prefix=$_cfgdir \
        --conf-path=$_cfgdir/nginx.conf \
        --sbin-path=/usr/sbin/nginx \
        --pid-path=/var/run/nginx.pid \
        --lock-path=/var/lock/nginx.lock \
        --user=http --group=http \
        --http-log-path=/var/log/nginx/access.log \
        --error-log-path=/var/log/nginx/error.log \
        --http-client-body-temp-path=$_tmpdir/client-body \
        --http-proxy-temp-path=$_tmpdir/proxy \
        --http-fastcgi-temp-path=$_tmpdir/fastcgi \
        --http-scgi-temp-path=$_tmpdir/scgi \
        --http-uwsgi-temp-path=$_tmpdir/uwsgi \
        --with-imap --with-imap_ssl_module \
        --with-ipv6 --with-pcre-jit \
        --with-file-aio \
        --with-http_dav_module \
        --with-http_geoip_module \
        --with-http_gzip_static_module \
        --with-http_realip_module \
        --with-http_ssl_module \
        --with-http_stub_status_module \
        --with-http_addition_module \
        --with-http_xslt_module \
        --with-http_image_filter_module \
        --with-http_sub_module \
        --with-http_secure_link_module \
        --add-module=/usr/lib/passenger/ext/nginx \
	--add-module=/usr/lib/modsecurity/ext/nginx
        #--with-http_flv_module \
        #--with-http_random_index_module \
        #--with-http_mp4_module \
        #--with-http_degradation_module \
        #--with-http_perl_module \

	make
}

package() {
	cd "$srcdir"/${_pkgname}-${pkgver}
	make DESTDIR="$pkgdir" install

	sed -e 's|\<user\s\+\w\+;|user html;|g' \
		-e '44s|html|/usr/share/nginx/html|' \
		-e '54s|html|/usr/share/nginx/html|' \
		-i "$pkgdir"/etc/nginx/nginx.conf
	rm "$pkgdir"/etc/nginx/*.default

	install -d "$pkgdir"/$_tmpdir
	install -dm700 "$pkgdir"/$_tmpdir/proxy

	install -d "$pkgdir"/usr/share/nginx
	mv "$pkgdir"/etc/nginx/html/ "$pkgdir"/usr/share/nginx

	install -Dm644 "$srcdir"/rails.conf "$pkgdir"/etc/nginx/rails.conf
	install -Dm644 "$srcdir"/modsecurity.conf "$pkgdir"/etc/nginx/modsecurity.conf	
	install -Dm644 "$srcdir"/logrotate "$pkgdir"/etc/logrotate.d/nginx
	install -Dm644 "$srcdir"/service "$pkgdir"/usr/lib/systemd/system/nginx.service
	install -Dm644 LICENSE "$pkgdir"/usr/share/licenses/nginx/LICENSE
	rm -rf "$pkgdir"/var/run
}
