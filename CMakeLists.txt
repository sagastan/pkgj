cmake_minimum_required(VERSION 3.6)

if(NOT HOST_BUILD)
  # must be done before project()
  if(NOT DEFINED CMAKE_TOOLCHAIN_FILE)
    if(DEFINED ENV{VITASDK})
      set(CMAKE_TOOLCHAIN_FILE "$ENV{VITASDK}/share/vita.toolchain.cmake" CACHE PATH "toolchain file")
    else()
      message(FATAL_ERROR "Please define VITASDK to point to your SDK path!")
    endif()
  endif()

  include("$ENV{VITASDK}/share/vita.cmake" REQUIRED)
endif()

project(pkgj)

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup(TARGETS)

# useful for memory alignment constraints
set(CMAKE_CXX_STANDARD 17)

set(VITA_APP_NAME "PKGj")
set(VITA_TITLEID  "PKGJ00000")
set(VITA_VERSION  "0.23")

option(PKGI_ENABLE_LOGGING "enables debug logging over udp multicast" OFF)

if(PKGI_ENABLE_LOGGING)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DPKGI_ENABLE_LOGGING")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DPKGI_ENABLE_LOGGING")
endif()

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DPKGI_VERSION=\\\"${VITA_VERSION}\\\" -D_GNU_SOURCE -g -Wall -Wextra -fvisibility=hidden")
set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DPKGI_VERSION=\\\"${VITA_VERSION}\\\" -D_GNU_SOURCE -g -Wall -Wextra -fvisibility=hidden")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -flto")

if(HOST_BUILD)
  include(host.cmake)
else()
  include(cross.cmake)
endif()
