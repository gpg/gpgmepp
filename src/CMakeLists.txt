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
    PRIVATE ${LibGpgError_INCLUDE_DIRS}
    INTERFACE $<INSTALL_INTERFACE:include>
)

target_link_libraries(Gpgmepp Gpgme::Gpgme)

set_target_properties(Gpgmepp PROPERTIES
    OUTPUT_NAME gpgmepp
    VERSION "${LIBGPGMEPP_SOVERSION}.${LIBGPGMEPP_LT_AGE}.${LIBGPGMEPP_LT_REVISION}"
    SOVERSION ${LIBGPGMEPP_SOVERSION}
)

include(GenerateExportHeader)
generate_export_header(Gpgmepp)
# create a copy of the export header in a gpgme++/ folder so that the includes
# in the interfaces headers work during the build
configure_file(${CMAKE_CURRENT_BINARY_DIR}/gpgmepp_export.h gpgme++/gpgmepp_export.h COPYONLY)

include(ECMSetupVersion)
ecm_setup_version(PROJECT
                  VARIABLE_PREFIX GPGMEPP
                  SOVERSION "${LIBGPGMEPP_SOVERSION}"
                  VERSION_HEADER "${CMAKE_CURRENT_BINARY_DIR}/gpgmepp_version.h"
                  PACKAGE_VERSION_FILE "${CMAKE_CURRENT_BINARY_DIR}/GpgmeppConfigVersion.cmake"
                  COMPATIBILITY AnyNewerVersion
)

include(CMakePackageConfigHelpers)
set(CMAKECONFIG_INSTALL_DIR ${CMAKE_INSTALL_LIBDIR}/cmake/Gpgmepp)
configure_package_config_file(GpgmeppConfig.cmake.in
                              ${CMAKE_CURRENT_BINARY_DIR}/GpgmeppConfig.cmake
                              INSTALL_DESTINATION ${CMAKECONFIG_INSTALL_DIR}
)

if (MSVC)
    install(TARGETS Gpgmepp EXPORT GpgmeppTargets "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_BINDIR}")
else()
    install(TARGETS Gpgmepp EXPORT GpgmeppTargets)
endif()

install(FILES ${Gpgmepp_HEADERS}
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/gpgme++)

install(FILES ${Gpgmepp_interface_HEADERS}
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/gpgme++/interfaces)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/gpgmepp_version.h
              ${CMAKE_CURRENT_BINARY_DIR}/gpgmepp_export.h
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/gpgme++)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/GpgmeppConfigVersion.cmake
              ${CMAKE_CURRENT_BINARY_DIR}/GpgmeppConfig.cmake
        DESTINATION ${CMAKECONFIG_INSTALL_DIR})
install(EXPORT GpgmeppTargets
        DESTINATION "${CMAKECONFIG_INSTALL_DIR}"
        FILE GpgmeppTargets.cmake)
install(FILES ${CMAKE_SOURCE_DIR}/cmake/modules/FindGpgme.cmake
              ${CMAKE_SOURCE_DIR}/cmake/modules/FindLibGpgError.cmake
        DESTINATION ${CMAKECONFIG_INSTALL_DIR})
