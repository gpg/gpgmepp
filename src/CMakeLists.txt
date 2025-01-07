# CMakeLists.txt for GpgME++
# Copyright 2021 Daniel Vrátil <dvratil@kde.org>
# Copyright 2025 g10 Code GmbH
# Software engineering by Carl Schwan <carl.schwan@gnupg.com>
# Software engineering by Ingo Klöcker <dev@ingo-kloecker.de>
#
# This file is part of GPGME++.
#
# GPGME++ is free software; you can redistribute it and/or modify it
# under the terms of the GNU Lesser General Public License as
# published by the Free Software Foundation; either version 2.1 of the
# License, or (at your option) any later version.
#
# GPGME++ is distributed in the hope that it will be useful, but WITHOUT
# ANY WARRANTY; without even the implied warranty of MERCHANTABILITY
# or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU Lesser General
# Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public
# License along with this program; if not, see <https://gnu.org/licenses/>.
# SPDX-License-Identifier: LGPL-2.1-or-later

add_compile_definitions(BUILDING_GPGMEPP)
add_compile_definitions(GPG_ERR_SOURCE_DEFAULT=GPG_ERR_SOURCE_GPGME)
if(WIN32)
  add_compile_definitions(GPG_ERR_ENABLE_GETTEXT_MACROS=1)
endif()

add_library(Gpgmepp SHARED)

target_sources(Gpgmepp PRIVATE
    callbacks.cpp
    configuration.cpp
    context.cpp
    context_vanilla.cpp
    data.cpp
    decryptionresult.cpp
    defaultassuantransaction.cpp
    editinteractor.cpp
    encryptionresult.cpp
    engineinfo.cpp
    eventloopinteractor.cpp
    exception.cpp
    gpgaddexistingsubkeyeditinteractor.cpp
    gpgadduserideditinteractor.cpp
    gpgagentgetinfoassuantransaction.cpp
    gpggencardkeyinteractor.cpp
    gpgrevokekeyeditinteractor.cpp
    gpgsetexpirytimeeditinteractor.cpp
    gpgsetownertrusteditinteractor.cpp
    gpgsignkeyeditinteractor.cpp
    importresult.cpp
    key.cpp
    keygenerationresult.cpp
    keylistresult.cpp
    scdgetinfoassuantransaction.cpp
    signingresult.cpp
    statusconsumerassuantransaction.cpp
    swdbresult.cpp
    tofuinfo.cpp
    trustitem.cpp
    util.cpp
    verificationresult.cpp
    vfsmountresult.cpp
)

set(Gpgmepp_HEADERS
    configuration.h
    context.h
    data.h
    decryptionresult.h
    defaultassuantransaction.h
    editinteractor.h
    encryptionresult.h
    engineinfo.h
    error.h
    eventloopinteractor.h
    exception.h
    global.h
    gpgaddexistingsubkeyeditinteractor.h
    gpgadduserideditinteractor.h
    gpgagentgetinfoassuantransaction.h
    gpggencardkeyinteractor.h
    gpgmefw.h
    gpgmepp_export.h
    gpgrevokekeyeditinteractor.h
    gpgsetexpirytimeeditinteractor.h
    gpgsetownertrusteditinteractor.h
    gpgsignkeyeditinteractor.h
    importresult.h
    key.h
    keygenerationresult.h
    keylistresult.h
    notation.h
    result.h
    scdgetinfoassuantransaction.h
    signingresult.h
    statusconsumerassuantransaction.h
    swdbresult.h
    tofuinfo.h
    trustitem.h
    verificationresult.h
    vfsmountresult.h
)

set(Gpgmepp_PRIVATE_HEADERS
    callbacks.h
    context_p.h
    data_p.h
    result_p.h
    util.h
)

set(Gpgmepp_interface_HEADERS
    interfaces/assuantransaction.h
    interfaces/dataprovider.h
    interfaces/passphraseprovider.h
    interfaces/progressprovider.h
    interfaces/statusconsumer.h
)

target_include_directories(Gpgmepp
    PRIVATE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    ${LibGpgError_INCLUDE_DIRS}
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/src>
)
target_link_libraries(Gpgmepp Gpgme::Gpgme)

set_target_properties(Gpgmepp PROPERTIES
    OUTPUT_NAME gpgmepp
    VERSION "${LIBGPGMEPP_SOVERSION}.${LIBGPGMEPP_LT_AGE}.${LIBGPGMEPP_LT_REVISION}"
    SOVERSION ${LIBGPGMEPP_SOVERSION}
)

set(resolved_includedir "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_INCLUDEDIR}")
set(resolved_libdir "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}")
set(resolved_bindir "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_BINDIR}")
set(libsuffix "${CMAKE_SHARED_LIBRARY_SUFFIX}")

if (MSVC)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/GpgmeppConfig-msvc.cmake.in.in
                   ${CMAKE_CURRENT_BINARY_DIR}/GpgmeppConfig.cmake @ONLY)
elseif (WIN32)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/GpgmeppConfig-w32.cmake.in.in
                   ${CMAKE_CURRENT_BINARY_DIR}/GpgmeppConfig.cmake @ONLY)
else()
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/GpgmeppConfig.cmake.in.in
                   ${CMAKE_CURRENT_BINARY_DIR}/GpgmeppConfig.cmake @ONLY)
endif()
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/GpgmeppConfigVersion.cmake.in
               ${CMAKE_CURRENT_BINARY_DIR}/GpgmeppConfigVersion.cmake @ONLY)
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/gpgmepp_version.h.in
               ${CMAKE_CURRENT_BINARY_DIR}/gpgmepp_version.h)

if (MSVC)
    install(TARGETS Gpgmepp DESTINATION ${resolved_bindir})
else()
    install(TARGETS Gpgmepp)
endif()

install(FILES ${Gpgmepp_HEADERS}
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/gpgme++)

install(FILES ${Gpgmepp_interface_HEADERS}
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/gpgme++/interfaces)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/gpgmepp_version.h
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/gpgme++)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/GpgmeppConfigVersion.cmake
              ${CMAKE_CURRENT_BINARY_DIR}/GpgmeppConfig.cmake
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/Gpgmepp)
