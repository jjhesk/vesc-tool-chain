# vesc-tool-chain
The mac version of vesc program

# https://doc.qt.io/qt-5/macos-deployment.html

# QT5 SDK installation
brew install qt@5

# vesc_tool Repository clone
git clone git@github.com:vedderb/vesc_tool.git vesc_tool

# Compile
cd vesc_tool
rm -rf build/macOS/*

export PATH="/usr/local/opt/qt@5/bin:$PATH"
export LDFLAGS="-L/usr/local/opt/qt@5/lib"
export CPPFLAGS="-I/usr/local/opt/qt@5/include"

qmake -config release "LIBS+= -dead_strip" "CONFIG += release_macos build_original"
make clean
_CPUs=$(($(sysctl -n hw.ncpu)+1))
make -j${_CPUs}

# Build app name with current version number
APP_VERSION=$(grep ^VT_VERSION vesc_tool.pro | tr -d "VT_VERSION = ")
APP_NAME="vesc_tool_${APP_VERSION}.app"

# Add the required Qt libs to the app and sign with your own Apple developer certificate
macdeployqt build/macos/$APP_NAME -codesign="<yourID>"

# Or add only needed Qt libs without signature
macdeployqt build/macos/$APP_NAME

# Clean up
rm -rf build/macos/obj
