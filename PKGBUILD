##############################################################################

_mesa_commit="#commit=790759df"

##############################################################################

PGO="off" # possible values are ("generate", "use", "off")
_march="" # processor architeture, leave empty if unkown

# adapt the naming depeding on the compiled type
if [ "$PGO" != "off" ]; then
    pkgdesc="an open-source implementation of the OpenGL specification, compiled with LTO and PGO. Git version"
else
    pkgdesc="an open-source implementation of the OpenGL specification, compiled with LTO. Git version"
fi

##############################################################################

# sourcing scripts and envars
source "$startdir/scripts/utils/envar"
source "$startdir/scripts/aux-build"
source "$startdir/scripts/aux-package"
source "$startdir/scripts/aux-cleaning"
source "$startdir/scripts/utils/directories"

###############################################################################

pkgname=mesa-lto-pgo-git

pkgver=24.1.0_devel.f3fe1f2f18d_off

pkgrel=1

arch=('x86_64')

makedepends=('git' 'python-mako' 'xorgproto'
              'libxml2' 'libvdpau' 'libva' 'elfutils' 'libxrandr'
              'ocl-icd' 'wayland-protocols' 'meson' 'ninja' 'glslang' 'cbindgen' 'cmake' 'python-packaging'
              'python-pyaml' 'spirv-tools' 'spirv-llvm-translator'
            )

depends=('libdrm' 'libxxf86vm' 'libxdamage' 'libxshmfence' 'libelf'
         'libomxil-bellagio' 'libunwind' 'libglvnd' 'wayland' 'lm_sensors' 'libclc'
         'libx11' 'vulkan-icd-loader' 'zstd' 'expat'
        )

optdepends=('opengl-man-pages: for the OpenGL API man pages')

provides=('opencl-mesa' 'vulkan-intel' 'vulkan-radeon' 'vulkan-mesa-layers' 'libva-mesa-driver' 'mesa-vdpau'
          'vulkan-swrast' 'vulkan-driver' 'opengl-driver' 'opencl-driver'
         )

conflicts=('mesa' 'opencl-mesa' 'vulkan-intel' 'vulkan-radeon' 'vulkan-mesa-layers' 'libva-mesa-driver' 'mesa-vdpau' 'vulkan-swrast')

url="https://www.mesa3d.org"

license=('custom')

source=("mesa::git+https://gitlab.freedesktop.org/mesa/mesa.git$_mesa_commit"
        "LICENSE"
       )

md5sums=('SKIP'
         '5c65a0fe315dd347e09b1f2826a1df5a'
        )

sha512sums=('SKIP'
            '25da77914dded10c1f432ebcbf29941124138824ceecaf1367b3deedafaecabc082d463abcfa3d15abff59f177491472b505bcb5ba0c4a51bb6b93b4721a23c2'
           )

options=(!debug)

##############################################################################

pkgver()
{
    cd mesa
    read -r _ver <VERSION
    echo ${_ver/-/_}.$(git rev-parse --short HEAD)_$PGO
}

prepare()
{
    # remove old _build directory if it exists
    _remove_build_dir

    # remove generated files from last build with PGO="generate" option
    _remove_pgo_build_dir
}

build()
{
    if [ "$PGO" = "generate" ]; then
        _generate_pgo_build
    elif [ "$PGO" = "use" ]; then
        _use_pgo_build
    else
        _no_pgo_build
    fi
}

package()
{
    # ninja install command, changed depending on type build
    _ninja_install

    if [ "$PGO" = "generate" ]; then
        # remove all unneeded files and directories from last pgo generate compile
        _remove_unneeded_pgo_build
    fi

    _remove_unneeded_files

    # indirect rendering
    ln -s "/usr/lib/libGLX_mesa.so.0" "$pkgdir/usr/lib/libGLX_indirect.so.0"

    install -m644 -Dt "$pkgdir/usr/share/licenses/$pkgname" "$srcdir/LICENSE"
}
##############################################################################
