# Contributor: Michael Witten <mfwitten>
# Contributor: Axpere Jan <483ken _@AT@_ gmail>
# Inspiration: Gustavo Dutra <mechamo@gustavodutra.com>

# This revision number seems to work:
#
#   REVISION=a430d04ed80c9470f9ee55e79774d9dcb2796de1
#
# To use a specific revision:
#
#   cd src/hg
#   hg up "$REVISION"
#   cd -
#   makepkg --holdver -sfi
#
# To build a specific revision, it is probably
# necessary to run `makepkg' with the `--holdver'
# option.
#
# The various phases of the build can be controlled
# with the following variables (use an empty value
# to mean false):

_source=yes
_clean=yes
_compile=yes
_install=yes
#_install=                 # do not install

# RECOMMENDED
# -----------
# Use a kind of symlink to point firefox to the
# source repository, rather than installing the
# files system-wide. This allows you to hack on
# the source without having to reinstall just
# to try out the results.

_install_pointer=yes      

######################################################################

pkgname=vimperator-hg
license=(MIT)
pkgver=4797.a430d04ed80c
pkgver()
{
  cd "$srcdir/$_repo"
  printf %s.%s "$(hg identify -n)" "$(hg identify -i)"
}
pkgrel=1
pkgdesc="Make firefox look and behave like Vim; built from HEAD of branch default"
arch=(i686 x86_64)
url=http://www.vimperator.org/vimperator
depends=('firefox>=25.0')
makedepends=(mercurial zip unzip)
provides=(vimperator)

_repo=repo

source=(
  ${_source:+"$_repo"::hg+https://vimperator-labs.googlecode.com}
)
md5sums=(
  ${_source:+SKIP}
)

build()
{
  #### Clean ####

    if [[ $_clean ]]; then

      cd "$srcdir/$_repo"

      msg "Cleaning ..."
      hg status --unknown | xargs rm -rf
      hg update --clean .

    fi

  #### Compile ####

    if [[ $_compile && ! $_install_pointer ]]; then

      cd "$srcdir/$_repo/vimperator"
    
      msg "Compiling ..."
      make xpi

    fi
}

package()
{
  #### Install  ####

    if [[ ! $_install ]]; then
      return
    fi

    msg "Installing ..."

    cd "$srcdir/$_repo/vimperator"

    # This isn't very safe over time; XML parsing should really be used
    local extension_id="$(awk -F'<em:id>|</em:id>' '/<em:id>/ {print $2; exit}' install.rdf)"

    local path_install="$pkgdir/usr/lib/firefox/browser/extensions/$extension_id"

    if [[ $_install_pointer ]]; then

      install -d "$(dirname "$path_install")"
      echo "$(pwd)" > "$path_install"

    else

      local version_vimperator="$(perl -ne '/VERSION\s*=\s*(\S+)/ && print($1) && last' Makefile)"
      local path_xpi="../downloads/vimperator-$version_vimperator.xpi"

      install -d "$path_install"
      unzip -od "$path_install" "$path_xpi"

    fi
}
