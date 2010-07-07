cmake_minimum_required(VERSION 2.8)
PROJECT(sandbox)
INCLUDE(ExternalProject)

#ExternalProject_Add(bullet
#PREFIX "bullet"
#URL "http://bullet.googlecode.com/files/bullet-2.76.tgz"
#URL "${PROJECT_SOURCE_DIR}/lib/bullet-2.76.tgz"
#CMAKE_ARGS -DBUILD_DEMOS=OFF -DBUILD_EXTRAS=OFF -DUSE_GLUT=OFF
#INSTALL_COMMAND ""
#)

if(WIN32)
	set(OGRE_HOME $ENV{OGRE_HOME})
	set(CMAKE_MODULE_PATH "${OGRE_HOME}/CMake/;${CMAKE_MODULE_PATH}")
	set(BOOST_ROOT "${OGRE_HOME}/boost_1_42")
endif(WIN32)

if(UNIX)
	set(CMAKE_MODULE_PATH "/usr/local/lib/OGRE/cmake/;${CMAKE_MODULEPATH}")
	
	#work around windows needing to diffentiate between debug and release
	#and FindOGRE not populating these anyway.
	set(OGRE_PLUGIN_DIR_REL "/usr/local/lib/OGRE/")
	set(OGRE_PLUGIN_DIR_DBG "/usr/local/lib/OGRE/")
endif(UNIX)

# Find Boost
if (NOT OGRE_BUILD_PLATFORM_IPHONE)
	if (WIN32 OR APPLE)
		set(Boost_USE_STATIC_LIBS TRUE)
	else ()
		# Statically linking boost to a dynamic Ogre build doesn't work on Linux 64bit
		set(Boost_USE_STATIC_LIBS ${OGRE_STATIC})
	endif ()
	if (MINGW)
		# this is probably a bug in CMake: the boost find module tries to look for
		# boost libraries with name libboost_*, but CMake already prefixes library
		# search names with "lib". This is the workaround.
		set(CMAKE_FIND_LIBRARY_PREFIXES ${CMAKE_FIND_LIBRARY_PREFIXES} "")
	endif ()
	set(Boost_ADDITIONAL_VERSIONS "1.42" "1.42.0" "1.41.0" "1.41" "1.40.0" "1.40" "1.39.0" "1.39" "1.38.0" "1.38" "1.37.0" "1.37" )
	# Components that need linking (NB does not include header-only components like bind)
	set(OGRE_BOOST_COMPONENTS thread date_time)
	find_package(Boost COMPONENTS ${OGRE_BOOST_COMPONENTS} QUIET)
	if (NOT Boost_FOUND)
		# Try again with the other type of libs
		set(Boost_USE_STATIC_LIBS NOT ${Boost_USE_STATIC_LIBS})
		find_package(Boost COMPONENTS ${OGRE_BOOST_COMPONENTS} QUIET)
	endif()
	find_package(Boost QUIET)
 
	# Set up referencing of Boost
	include_directories(${Boost_INCLUDE_DIR})
	add_definitions(-DBOOST_ALL_NO_LIB)
	set(OGRE_LIBRARIES ${OGRE_LIBRARIES} ${Boost_LIBRARIES})
endif()


FIND_PACKAGE(OGRE)
FIND_PACKAGE(OIS)


INCLUDE_DIRECTORIES(
${OGRE_INCLUDE_DIR}
${OIS_INCLUDE_DIRS}
${Boost_INCLUDE_DIR}
)

#MESSAGE("OGRE_INCLUDE_DIRS: ${OGRE_INCLUDE_DIRS}")
#MESSAGE("OGRE_INCLUDE_DIR: ${OGRE_INCLUDE_DIR}")
#MESSAGE("OGRE_LIBRARIES: ${OGRE_LIBRARIES}")
#MESSAGE("OGRE_ROOT: ${OGRE_ROOT}")
#MESSAGE("OGRE_HOME: ${OGRE_HOME}")

ADD_SUBDIRECTORY(tutorial)

if(UNIX AND NOT APPLE)
	install(FILES /usr/local/share/OGRE/plugins.cfg
		DESTINATION bin
	)
endif(UNIX AND NOT APPLE)
