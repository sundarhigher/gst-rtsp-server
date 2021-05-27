cmake_minimum_required(VERSION 3.7)
set (CMAKE_CXX_STANDARD 14)

project (gst-rtsp-server)
find_package(GStreamer REQUIRED)

# do some configure variables
include(CheckIncludeFiles)

check_include_files("dlfcn.h" HAVE_DLFCN_H)
check_include_files("fcntl.h" HAVE_FCNTL_H)
check_include_files("inttypes.h" HAVE_INTTYPES_H)
check_include_files("memory.h" HAVE_MEMORY_H)
check_include_files("netinet/in.h" HAVE_NETINET_IN_H)
check_include_files("netinet/ip.h" HAVE_NETINET_IP_H)
check_include_files("netinet/tcp.h" HAVE_NETINET_TCP_H)
check_include_files("pthread.h" HAVE_PTHREAD_H)
check_include_files("stdint.h" HAVE_STDINT_H)
check_include_files("stdlib.h" HAVE_STDLIB_H)
check_include_files("strings.h" HAVE_STRINGS_H)
check_include_files("string.h" HAVE_STRING_H)
check_include_files("sys/param.h" HAVE_SYS_PARAM_H)
check_include_files("sys/socket.h" HAVE_SYS_SOCKET_H)
check_include_files("sys/stat.h" HAVE_SYS_STAT_H)
check_include_files("sys/time.h" HAVE_SYS_TIME_H)
check_include_files("sys/types.h" HAVE_SYS_TYPES_H)
check_include_files("sys/utsname.h" HAVE_SYS_UTSNAME_H)
check_include_files("unistd.h" HAVE_UNISTD_H)
check_include_files("windows.h" HAVE_WINDOWS_H)
check_include_files("winsock2.h" HAVE_WINSOCK2_H)
check_include_files("ws2tcpip.h" HAVE_WS2TCPIP_H)

include (CheckSymbolExists)

check_symbol_exists("dcgettext" "libintl.h" HAVE_DCGETTEXT)
check_symbol_exists("getpagesize" "unistd.h" HAVE_GETPAGESIZE)
check_symbol_exists("gmtime_r" "time.h" HAVE_GMTIME_R)
check_symbol_exists("mmap" "sys/mman.h" HAVE_MMAP)
check_symbol_exists("pipe2" "unistd.h" HAVE_PIPE2)
check_symbol_exists("getrusage" "sys/resource.h" HAVE_GETRUSAGE)

include(CheckTypeSize)
check_type_size("char" SIZEOF_CHAR)
check_type_size("int" SIZEOF_INT)
check_type_size("long" SIZEOF_LONG)
check_type_size("short" SIZEOF_SHORT)
check_type_size("void*" SIZEOF_VOIDP)

find_program (ORCC_FOUND NAMES orcc)
if(ORCC_FOUND)
set(HAVE_ORC TRUE)
endif(ORCC_FOUND)

if(WIN32)
set(GST_EXPORT "__declspec(dllexport) extern")
else()
set(GST_EXPORT "extern")
endif(WIN32)

configure_file(config.h.in config.h)

include_directories(
  ${CMAKE_CURRENT_BINARY_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/gst/rtsp-server
  ${GSTREAMER_INCLUDE_DIR}
)

add_definitions(
  -DHAVE_CONFIG_H
  -DBUILDING_GST_RTSP_SERVER
)


set(GST_RTSP_SERVER_HDRS
  gst/rtsp-server/rtsp-auth.h
  gst/rtsp-server/rtsp-address-pool.h
  gst/rtsp-server/rtsp-context.h
  gst/rtsp-server/rtsp-params.h
  gst/rtsp-server/rtsp-sdp.h
  gst/rtsp-server/rtsp-thread-pool.h
  gst/rtsp-server/rtsp-media.h
  gst/rtsp-server/rtsp-media-factory.h
  gst/rtsp-server/rtsp-media-factory-uri.h
  gst/rtsp-server/rtsp-mount-points.h
  gst/rtsp-server/rtsp-permissions.h
  gst/rtsp-server/rtsp-stream.h
  gst/rtsp-server/rtsp-stream-transport.h
  gst/rtsp-server/rtsp-session.h
  gst/rtsp-server/rtsp-session-media.h
  gst/rtsp-server/rtsp-session-pool.h
  gst/rtsp-server/rtsp-token.h
  gst/rtsp-server/rtsp-client.h
  gst/rtsp-server/rtsp-server.h
  gst/rtsp-server/rtsp-server-object.h
  gst/rtsp-server/rtsp-server-prelude.h
  gst/rtsp-server/rtsp-onvif-server.h
  gst/rtsp-server/rtsp-onvif-client.h
  gst/rtsp-server/rtsp-onvif-media-factory.h
  gst/rtsp-server/rtsp-onvif-media.h
)

set(GST_RTSP_SERVER_SRCS
  gst/rtsp-server/rtsp-address-pool.c
  gst/rtsp-server/rtsp-auth.c
  gst/rtsp-server/rtsp-client.c
  gst/rtsp-server/rtsp-context.c
  gst/rtsp-server/rtsp-latency-bin.c
  gst/rtsp-server/rtsp-media.c
  gst/rtsp-server/rtsp-media-factory.c
  gst/rtsp-server/rtsp-media-factory-uri.c
  gst/rtsp-server/rtsp-mount-points.c
  gst/rtsp-server/rtsp-params.c
  gst/rtsp-server/rtsp-permissions.c
  gst/rtsp-server/rtsp-sdp.c
  gst/rtsp-server/rtsp-server.c
  gst/rtsp-server/rtsp-session.c
  gst/rtsp-server/rtsp-session-media.c
  gst/rtsp-server/rtsp-session-pool.c
  gst/rtsp-server/rtsp-stream.c
  gst/rtsp-server/rtsp-stream-transport.c
  gst/rtsp-server/rtsp-thread-pool.c
  gst/rtsp-server/rtsp-token.c
  gst/rtsp-server/rtsp-onvif-server.c
  gst/rtsp-server/rtsp-onvif-client.c
  gst/rtsp-server/rtsp-onvif-media-factory.c
  gst/rtsp-server/rtsp-onvif-media.c
)

#create the library
add_library(gst-rtsp-server SHARED
  ${GST_RTSP_SERVER_SRCS}
  ${GST_RTSP_SERVER_HDRS}
)

target_link_libraries(
  gst-rtsp-server
  ${GSTREAMER_LIBRARIES}
)

# set variables for compilation from outside
#set(PSYLVER_GSTREAMER_INCLUDE_DIR ${PsylverGStreamer_SOURCE_DIR} PARENT_SCOPE)

source_group(rtsp-server FILES ${GST_RTSP_SERVER_HDRS} ${GST_RTSP_SERVER_SRCS})
