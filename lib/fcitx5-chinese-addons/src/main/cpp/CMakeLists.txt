cmake_minimum_required(VERSION 3.18)

project(fcitx5-android-lib-fcitx5-chinese-addons VERSION ${VERSION_NAME})

# For reproducible build
add_link_options("LINKER:--hash-style=gnu,--build-id=none")

# prefab dependency
find_package(fcitx5 REQUIRED CONFIG)
get_target_property(FCITX5_CMAKE_MODULES fcitx5::cmake INTERFACE_INCLUDE_DIRECTORIES)
set(CMAKE_MODULE_PATH ${FCITX5_CMAKE_MODULES} ${CMAKE_MODULE_PATH})

find_package(ECM MODULE)
find_package(Fcitx5Core MODULE)
find_package(Fcitx5Module MODULE)

find_package(libime REQUIRED CONFIG)
get_target_property(LIBIME_CMAKE_MODULES libime::cmake INTERFACE_INCLUDE_DIRECTORIES)
set(CMAKE_MODULE_PATH ${LIBIME_CMAKE_MODULES} ${CMAKE_MODULE_PATH})

find_package(LibIMECore MODULE)
find_package(LibIMEPinyin MODULE)
find_package(LibIMETable MODULE)

find_package(fcitx5-lua REQUIRED CONFIG)
get_target_property(FCITX5_LUA_CMAKE_MODULES fcitx5-lua::cmake INTERFACE_INCLUDE_DIRECTORIES)
set(CMAKE_MODULE_PATH ${FCITX5_LUA_CMAKE_MODULES} ${CMAKE_MODULE_PATH})

find_package(Fcitx5ModuleLuaAddonLoader MODULE)

# dummy target to export src/main/cpp/cmake
add_custom_target(cmake)

# prebuilt boost
list(APPEND CMAKE_FIND_ROOT_PATH "${PREBUILT_DIR}/boost/${ANDROID_ABI}/lib/cmake")
find_package(Boost 1.87.0 REQUIRED COMPONENTS iostreams CONFIG)
add_compile_definitions("BOOST_ALL_NO_EMBEDDED_GDB_SCRIPTS")

# Skip OpenCC/Marisa as they have missing dependencies in prebuilt
# The prebuilt OpenCC was compiled with Marisa support but Marisa lib is missing
# This causes linker errors. We disable OpenCC to work around this.
set(ENABLE_CLOUDPINYIN OFF CACHE BOOL "" FORCE)

option(ENABLE_TEST "" OFF)
option(ENABLE_GUI "" OFF)
option(ENABLE_BROWSER "" OFF)
option(USE_WEBKIT "" OFF)
option(ENABLE_CLOUDPINYIN "" OFF)
set(CMAKE_FIND_PACKAGE_PREFER_CONFIG ON)

add_subdirectory(fcitx5-chinese-addons)
# rename to include executable in apk
set_target_properties(scel2org5 PROPERTIES OUTPUT_NAME libscel2org5.so)
