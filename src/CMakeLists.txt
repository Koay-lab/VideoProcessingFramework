cmake_minimum_required(VERSION 3.21)

project(Video_Processing_Framework)

option(TRACK_TOKEN_ALLOCATIONS "Debug memory allocations within VPF" FALSE )

if(TRACK_TOKEN_ALLOCATIONS)
	add_definitions(-DTRACK_TOKEN_ALLOCATIONS)
endif(TRACK_TOKEN_ALLOCATIONS)

#Undef MIN & MAX & C runtime warnings for Windows
if(WIN32)
	add_definitions(-DNOMINMAX)
	add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif(WIN32)

find_package(CUDAToolkit 11.2 REQUIRED)

find_path(
	TC_FFMPEG_ROOT_HINT
	NAMES "include/libavcodec/avcodec.h" 
)
if(NOT DEFINED TC_FFMPEG_ROOT OR TC_FFMPEG_ROOT STREQUAL "")
	set(TC_FFMPEG_ROOT "${TC_FFMPEG_ROOT_HINT}" CACHE PATH "Where to find ffmpeg")
endif()

if(WIN32 AND DEFINED TC_FFMPEG_ROOT AND TC_FFMPEG_ROOT MATCHES "^[A-Za-z]:[^/\\\\].*")
	string(SUBSTRING "${TC_FFMPEG_ROOT}" 0 2 _vpf_drive)
	string(SUBSTRING "${TC_FFMPEG_ROOT}" 2 -1 _vpf_rest)
	set(TC_FFMPEG_ROOT "${_vpf_drive}/${_vpf_rest}" CACHE PATH "Where to find ffmpeg" FORCE)
endif()

add_subdirectory(PyNvCodec)
# TODO: rename TC to NvCodec or something more descriptive
add_subdirectory(TC)
#add_subdirectory(PytorchNvCodec)
