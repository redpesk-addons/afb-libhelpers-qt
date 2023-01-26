###########################################################################
# Copyright 2015, 2016, 2017 IoT.bzh
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
###########################################################################

CMAKE_MINIMUM_REQUIRED(VERSION 3.13)
project(afb-libhelpers
    VERSION 10.0.0
    DESCRIPTION "Library providing some helpful functions for apps using Application Framework Binder"
    HOMEPAGE_URL "https://github.com/redpesk-common/afb-libhelpers"
    LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 14)

include(GNUInstallDirs)

ADD_COMPILE_OPTIONS(
    -Wall
    -Wextra
    -Wconversion
    -Wno-unused-parameter
    -Wno-sign-compare
    -Wno-sign-conversion
    -Werror=maybe-uninitialized
    $<$<COMPILE_LANGUAGE:C>:-Werror=implicit-function-declaration>
    -ffunction-sections
    -fdata-sections
    -fPIC)

add_link_options(
    -Wl,--no-undefined)

INCLUDE(FindPkgConfig)

pkg_check_modules(AFB REQUIRED afb-binding)

# Define target includes
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

add_link_options(${AFB_LDFLAGS})
add_compile_options(${AFB_CFLAGS})


# Add target to project dependency list
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

find_package(Qt5WebSockets REQUIRED)

# Define Qt targets
ADD_LIBRARY(afb-helpers-qt SHARED qafbwebsocketclient.cpp)

include_directories(${Qt5WebSockets_INCLUDE_DIRS})
target_link_libraries(afb-helpers-qt Qt5::WebSockets)

# Define Qt target includes
TARGET_INCLUDE_DIRECTORIES(afb-helpers-qt
    PUBLIC  ${CMAKE_CURRENT_SOURCE_DIR}
)

CONFIGURE_FILE(afb-libhelpers-qt.pc.in afb-helpers-qt.pc @ONLY)
INSTALL(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/afb-helpers-qt.pc
    DESTINATION
    ${CMAKE_INSTALL_LIBDIR}/pkgconfig
   )
INSTALL(TARGETS afb-helpers-qt
    DESTINATION ${CMAKE_INSTALL_LIBDIR}
   )
INSTALL(FILES qafbwebsocketclient.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
   )
