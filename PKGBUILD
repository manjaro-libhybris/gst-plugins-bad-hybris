# Maintainer: Bardia Moshiri <fakeshell@bardia.tech>
# Contributor: Erik Inkinen <erik.inkinen@gmail.com>

pkgbase=gst-plugins-bad-hybris
pkgname=(gst-plugins-bad-libs-hybris gst-plugins-bad-hybris gst-plugin-wpe-hybris)
pkgver=1.22.0
pkgrel=3
pkgdesc="Multimedia graph framework - bad plugins"
url="https://gstreamer.freedesktop.org/"
arch=(x86_64 aarch64)
license=(LGPL)
depends=(gst-plugins-base-libs orc libdrm libx11 libgudev libusb libvdpau
         libxkbcommon-x11)
makedepends=(mjpegtools curl chromaprint libmms faad2 libdca libdvdnav
             libmodplug libgme nettle libkate wayland libofa openjpeg2 libwebp
             libsrtp sbc rtmpdump libexif libdvdread wildmidi ladspa openal
             vulkan-icd-loader libfdk-aac faac soundtouch spandsp neon
             webrtc-audio-processing libdc1394 libmpcdec zvbi openexr libbs2b
             libnice lcms2 bluez-libs glu srt aom x265 liblrdf libde265 zbar
             librsvg fluidsynth lilv lv2 gst-plugins-good python git
             gobject-introspection vulkan-headers vulkan-validation-layers
             wayland-protocols gtk3 meson shaderc libavtp libmicrodns
             libva wpewebkit)
checkdepends=(xorg-server-xvfb)
options=(!emptydirs)
_commit=d3af58d5b31941caa26c3ded85d7a7b84a91f0cc  # tags/1.18.5^0
source=("git+https://gitlab.freedesktop.org/gstreamer/gst-plugins-bad.git#commit=$_commit"
        1267.patch wpe-1.1.diff)
sha256sums=('SKIP'
            'SKIP'
            'SKIP')

prepare() {
  mv gst-plugins-bad $pkgbase
  cd $pkgbase

  # Neon 0.33.x
  # https://gitlab.freedesktop.org/gstreamer/gstreamer/-/merge_requests/1267
  patch -Np3 -i ../1267.patch

  # wpe-webkit-1.1 (libsoup3)
  patch -Np1 -i ../wpe-1.1.diff
}

build() {
  arch-meson $pkgbase build \
    -D directfb=disabled \
    -D doc=disabled \
    -D flite=disabled \
    -D gsm=disabled \
    -D iqa=disabled \
    -D magicleap=disabled \
    -D msdk=disabled \
    -D openh264=disabled \
    -D openmpt=disabled \
    -D openni2=disabled \
    -D opensles=disabled \
    -D svthevcenc=disabled \
    -D tinyalsa=disabled \
    -D uvch264=disabled \
    -D voaacenc=disabled \
    -D voamrwbenc=disabled \
    -D wasapi2=disabled \
    -D wasapi=disabled \
    -D opencv=disabled \
    -D teletext=disabled \
    -D zxing=disabled \
    -D gobject-cast-checks=disabled \
    -D package-name="GStreamer Bad Plugins (Manjaro ARM on Halium)" \
    -D package-origin="https://www.manjaro.org/"
  meson compile -C build
}

check() (
  mkdir -p -m 700 "${XDG_RUNTIME_DIR:=$PWD/runtime-dir}"
  export XDG_RUNTIME_DIR

  # elements_dtls test hangs sometimes
  xvfb-run -s '-nolisten local' \
    meson test -C build --print-errorlogs || :
)

package_gst-plugins-bad-libs-hybris() {
  pkgdesc="${pkgdesc% plugins}"
  provides=("gst-transcoder=$pkgver" "gst-plugins-bad-libs=$pkgver")
  conflicts=("gst-transcoder<1.18.0" "gst-plugins-bad-libs")
  replaces=("gst-transcoder<1.18.0")

  meson install -C build --destdir "$pkgdir"

  mkdir -p ext/lib/gstreamer-1.0
  for _x in aom assrender bs2b bz2 chromaprint closedcaption colormanagement \
            curl dc1394 de265 dtls dtsdec faac faad fdkaac fluidsynthmidi gme \
            hls kate ladspa lv2 mms modplug mpeg2enc mplex musepack \
            neonhttpsrc ofa openal openexr openjpeg opusparse resindvd rsvg \
            rtmp sbc sctp smoothstreaming sndfile soundtouch spandsp srt srtp \
            ttmlsubs vulkan waylandsink webp webrtc webrtcdsp \
            wildmidi x265 zbar va avtp microdns; do
    _x="lib/gstreamer-1.0/libgst${_x}.so"
    mv "$pkgdir/usr/$_x" "ext/$_x"
  done

  for _x in wpe; do
    mkdir -p "$_x/lib/gstreamer-1.0"
    _f="lib/gstreamer-1.0/libgst${_x}.so"
    mv "$pkgdir/usr/$_f" "$_x/$_f"
  done
}

package_gst-plugins-bad-hybris() {
  depends=("gst-plugins-bad-libs=$pkgver" gst-plugins-good
           aom libass libbs2b bzip2 chromaprint pango lcms2 curl libxml2
           libdc1394 libde265 openssl libdca faac faad2 libfdk-aac fluidsynth
           libgme nettle libkate liblrdf lilv libmms libmodplug mjpegtools
           libmpcdec neon libofa openal openexr openjpeg2 opus libdvdnav
           libdvdread librsvg rtmpdump sbc libsndfile soundtouch spandsp srt
           libsrtp zvbi vulkan-icd-loader libxcb wayland libwebp libnice
           webrtc-audio-processing wildmidi x265 zbar libavtp
           libmicrodns libva)
  optdepends=('nvidia-utils: nvcodec plugin')
  provides=("gst-plugins-bad=$pkgver")
  conflicts=("gst-plugins-bad")

  mv ext "$pkgdir/usr"
}

package_gst-plugin-wpe-hybris() {
  pkgdesc="${pkgdesc%-*}- wpe plugin"
  depends=(gst-plugins-base-libs wpewebkit)
  provides=("gst-plugin-wpe=$pkgver")
  conflicts=("gst-plugin-wpe")

  mv wpe "$pkgdir/usr"
}
