# $Id: PKGBUILD 108477 2014-03-27 14:48:11Z fyan $
# Maintainer: Stéphane Gaudreault <stephane@archlinux.org>

# pyopencl requires numpy
BUILD_PYPY3=0

pkgbase=pypy-pyopencl-git
if ((BUILD_PYPY3)); then
  pkgname=('pypy3-pyopencl-git' 'pypy-pyopencl-git')
  makedepends=('pypy-setuptools' 'pypy3-setuptools' 'libcl' 'opencl-headers'
    'mesa' 'pypy-mako' 'pypy3-mako' 'pypy-numpy' 'pypy3-numpy')
else
  pkgname=('pypy-pyopencl-git')
  makedepends=('pypy-setuptools' 'libcl' 'opencl-headers'
    'mesa' 'pypy-mako' 'pypy-numpy')
fi
pkgver=2014.1.0.363.g3b531d1
pkgrel=1
pkgdesc="A complete, object-oriented language binding of OpenCL to Python. PyPy version on top of cffi"
arch=('i686' 'x86_64')
url="http://mathema.tician.de/software/pyopencl"
license=('custom')
epoch=1
source=('LICENSE.txt' 'cc')
sha1sums=('2e6966b3d9b15603ce2c3ff79eeadd63c5d066b7'
          '70e53f2b3b32efe852c9d2c266f49dab16df9b20')
options=('debug' 'strip')

_gitname=pyopencl

_gitroot=https://github.com/yuyichao/pyopencl
# _gitroot=/home/yuyichao/projects/dev/pyopencl-cffi/
# _gitref=test
_gitref=cffi-test
# _gitroot=https://github.com/pyopencl/pyopencl
# _gitref=cffi
exec 5>&1

_fetch_git() {
  cd "$srcdir"

  if [ -d "$srcdir/$_gitname/.git" ]; then
    cd "$_gitname"
    msg "Reset current branch"
    git reset --hard HEAD
    git clean -fdx
    msg "Fetching branch $_gitref from $_gitroot..."
    git fetch --force --update-head-ok \
      "$_gitroot" "$_gitref:$_gitref" --
    msg "Checking out branch $_gitref..."
    git checkout "$_gitref" --
    git reset --hard "$_gitref" --
    git clean -fdx
    msg "The local files are updated."
  else
    msg "Cloning branch $_gitref from $_gitroot to $_gitname..."
    git clone --single-branch --branch "$_gitref" \
      "$_gitroot" "$_gitname"
    cd "$_gitname"
  fi
  git submodule init
  git submodule sync
  git submodule update
  git fetch --tags "$_gitroot"
  msg "GIT checkout done or server timeout"
}

pkgver() {
  (_fetch_git >&5 2>&1)
  cd "$srcdir/$_gitname"

  git describe --tags | sed -e 's/^[^0-9]*//' -e 's/-/.0./' -e 's/-/./g'
}

build() {
  (_fetch_git)
  rm -rf "${srcdir}/$_gitname-python2"
  cd "$srcdir"

  cp -a pyopencl{,-python2}

  # hack to use g++ since gcc doesn't link to the required c++ runtime libraries.
  export PATH="${srcdir}:${PATH}"

  if ((BUILD_PYPY3)); then
    cd "${srcdir}/pyopencl"
    pypy3 ./configure.py --cl-enable-gl
    pypy3 setup.py build
  fi

  cd "$srcdir/pyopencl-python2"
  pypy ./configure.py --cl-enable-gl
  pypy setup.py build
}

package_pypy3-pyopencl-git() {
  depends=('libcl' 'opencl-headers' 'mesa' "pypy3>=2.3" "pypy3<2.4"
    'pypy3-mako' 'pypy3-pytools' 'pypy3-setuptools')

  cd "${srcdir}/pyopencl"
  pypy3 setup.py install --root="${pkgdir}" --optimize=1 --skip-build \
    --install-lib=/opt/pypy3/lib/python3.2/site-packages

  install -D -m644 "$srcdir"/LICENSE.txt \
    "${pkgdir}"/usr/share/licenses/${pkgname}/LICENSE
}

package_pypy-pyopencl-git() {
  depends=('libcl' 'opencl-headers' 'mesa' "pypy>=2.4" "pypy<2.5"
    'pypy-numpy' 'pypy-mako' 'pypy-pytools' 'pypy-setuptools' 'gcc-libs')

  cd "${srcdir}/pyopencl-python2"
  pypy setup.py install --root="${pkgdir}" --optimize=1 --skip-build

  install -D -m644 "$srcdir"/LICENSE.txt \
    "${pkgdir}"/usr/share/licenses/${pkgname}/LICENSE
}
