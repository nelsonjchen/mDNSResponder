cmake_minimum_required(VERSION 3.2)

project(dnssd LANGUAGES C CXX)

set(dnssd_VERSION 576.30.4)

add_library(dnssd
  mDNSShared/dns_sd.h
  mDNSWindows/DLLStub/DLLStub.h
  mDNSWindows/DLLStub/DLLStub.cpp
)
target_include_directories(dnssd
  PRIVATE
  $<BUILD_INTERFACE:mDNSCore/;mDNSShared/;mDNSWindows/;mDNSWindows/DLL>
  INTERFACE
  $<INSTALL_INTERFACE:include>
)

target_compile_definitions(dnssd PRIVATE
  _USRDLL
  MDNS_DEBUGMSGS=0
  WIN32_LEAN_AND_MEAN
  USE_TCP_LOOPBACK
  _CRT_SECURE_NO_DEPRECATE
  _CRT_SECURE_CPP_OVERLOAD_STANDARD_NAMES=1
  NOT_HAVE_SA_LEN
)

target_link_libraries(dnssd
  PRIVATE ws2_32.lib)


include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/dnssd-config-version.cmake
  VERSION ${dnssd_VERSION}
  COMPATIBILITY AnyNewerVersion
)

install(TARGETS dnssd EXPORT dnssd
  RUNTIME DESTINATION bin
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib)


install(FILES
  mDNSShared/dns_sd.h
  DESTINATION include)

install(EXPORT dnssd
  FILE
    dnssd.cmake
  NAMESPACE
    dnssd::
  DESTINATION
    lib/cmake/dnssd
)
install(
  FILES
    cmake/dnssd-config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/dnssd-config-version.cmake
  DESTINATION
    lib/cmake/dnssd
)

if(MSVC AND BUILD_SHARED_LIBS)
   install(FILES
     $<TARGET_PDB_FILE:dnssd>
     DESTINATION lib)
endif()
