_target="aarch64-linux-gnu"

_sysroot="/usr/${_target}"
_pkgbase="boost"
pkgbase="${_target}-${_pkgbase}"
pkgname=('aarch64-linux-gnu-boost-libs' 'aarch64-linux-gnu-boost')
pkgver=1.72.0
_boostver=${pkgver//./_}
pkgrel=2
pkgdesc='Free peer-reviewed portable C++ source libraries (arm64)'
url='https://www.boost.org/'
arch=('arm64' 'x86_64')
license=('custom')
makedepends=('aarch64-linux-gnu-gcc')
source=(https://dl.bintray.com/boostorg/release/${pkgver}/source/boost_${_boostver}.tar.bz2)
sha256sums=('59c9b274bc451cf91a9ba1dd2c7fdcaf5d60b1b3aa83f2c9fa143417cc660722')
b2sums=('ab270a0e3cb24da687d86785e2d2e6d7731b4dbc07bd839eadb642dfa5a428ad584acb1f3529661a8de9a986008ff3427491041059ea2b742348d02e00761cd8')

build() {
	export JOBS="-j$(nproc)"
	export _stagedir="${srcdir}/stagedir"

	cd ${_pkgbase}_${_boostver}

	./bootstrap.sh --with-toolset=gcc 

	echo "using gcc : arm64 : aarch64-linux-gnu-gcc ;" >> project-config.jam

	install -Dm755 tools/build/src/engine/b2 "${_stagedir}"/bin/b2

	install -dm755 "${_stagedir}"/share/boostbook/
	cp -a tools/boostbook/{xsl,dtd} "${_stagedir}"/share/boostbook/


	export CPPFLAGS=$(echo "${CPPFLAGS}" | sed "s/-march=x86-64//g")
	export CFLAGS=$(echo "${CFLAGS}" | sed "s/-march=x86-64//g")
	export CXXFLAGS=$(echo "${CXXFLAGS}" | sed "s/-march=x86-64//g")
	alias strip="aarch64-linux-gnu-strip"
	alias objcopy="aarch64-linux-gnu-objcopy"
	alias readelf="aarch64-linux-gnu-readelf"

	"${_stagedir}"/bin/b2 \
		variant=release \
		debug-symbols=off \
		threading=multi \
		runtime-link=shared \
		link=shared,static \
		toolset=gcc-arm64 \
		cflags="${CPPFLAGS} ${CFLAGS} -fPIC -O3" \
		cxxflags="${CPPFLAGS} ${CXXFLAGS} --std=c++14 -fPIC -O3" \
		linkflags="${LDFLAGS}" \
		--layout=system \
		${JOBS} \
		\
		--prefix="${_stagedir}" \
		install
}

package_aarch64-linux-gnu-boost() {
	pkgdesc+="- development headers"
	depends=("${_target}-${_pkgbase}-libs=${pkgver}")
	options=('staticlibs')
	
	install -dm755 "${pkgdir}"${_sysroot}
	cp -a "${_stagedir}"/{bin,include,share} "${pkgdir}"${_sysroot}


	install -d "${pkgdir}"${_sysroot}/lib
	cp -a "${_stagedir}"/lib/*.a "${pkgdir}"${_sysroot}/lib/
	cp -a "${_stagedir}"/lib/cmake "${pkgdir}"${_sysroot}/lib/

	install -Dm644 "${srcdir}/"${_pkgbase}_${_boostver}/LICENSE_1_0.txt \
		"${pkgdir}"${_sysroot}/share/licenses/boost/LICENSE_1_0.txt
}

package_aarch64-linux-gnu-boost-libs() {
	pkgdesc+="- runtime libraries"
	depends=("aarch64-linux-gnu-gcc")
	provides=(libboost_atomic.so libboost_chrono.so libboost_container.so
		libboost_context.so libboost_contract.so libboost_coroutine.so
		libboost_date_time.so libboost_fiber.so libboost_filesystem.so
		libboost_graph.so libboost_graph_parallel.so libboost_iostreams.so
		libboost_locale.so libboost_log.so libboost_log_setup.so
		libboost_math_c99.so libboost_math_c99f.so libboost_math_c99l.so
		libboost_math_tr1.so libboost_math_tr1f.so libboost_math_tr1l.so	
		libboost_prg_exec_monitor.so libboost_program_options.so
		libboost_random.so libboost_regex.so libboost_serialization.so
		libboost_stacktrace_basic.so libboost_stacktrace_noop.so libboost_system.so
		libboost_thread.so libboost_timer.so libboost_type_erasure.so
		libboost_unit_test_framework.so libboost_wave.so libboost_wserialization.so)


   install -dm755 "${pkgdir}"${_sysroot}
   cp -a "${_stagedir}"/lib "${pkgdir}"${_sysroot}
   rm "${pkgdir}"${_sysroot}/lib/*.a
   rm -r "${pkgdir}"${_sysroot}/lib/cmake

   install -Dm644 "${srcdir}/"${_pkgbase}_${_boostver}/LICENSE_1_0.txt \
      "${pkgdir}"${_sysroot}/share/licenses/boost-libs/LICENSE_1_0.txt
}
