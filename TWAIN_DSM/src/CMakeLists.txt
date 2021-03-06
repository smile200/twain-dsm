# this is just a basic CMakeLists.txt, for more information see the cmake manpage...
cmake_minimum_required(VERSION 2.4)

# Setup the install prefix, if it's not already defined
IF(NOT CMAKE_INSTALL_PREFIX)
	SET(CMAKE_INSTALL_PREFIX "/usr/local")
ENDIF(NOT CMAKE_INSTALL_PREFIX)

# Setup the build prefix, if it's not already defined
IF(NOT CMAKE_BUILD_TYPE)
	SET(CMAKE_BUILD_TYPE "Release")
ENDIF(NOT CMAKE_BUILD_TYPE)

#add definitions, compiler switches, etc.
IF(APPLE)
	SET(CMAKE_OSX_ARCHITECTURES "i386;x86_64")
	ADD_DEFINITIONS(-Wall -Wextra -Werror -isysroot /Developer/SDKs/MacOSX10.6.sdk -mmacosx-version-min=10.6 -fexceptions -fPIC)
ELSE()
	ADD_DEFINITIONS(-Wall -Wextra -Werror)
ENDIF()

#let's not be sharing our symbols...
IF(APPLE)
	SET(CMAKE_SHARED_LINKER_FLAGS "-Wl,-ldl -Wl,-framework,CoreServices -Wl,-framework,Foundation")
ELSE()
	SET(CMAKE_SHARED_LINKER_FLAGS "-Wl,-Bsymbolic -Wl,--no-undefined -Wl,-ldl")
ENDIF()

#project name
PROJECT(twaindsm)

#project version
SET(${PROJECT_NAME}_MAJOR_VERSION 2)
SET(${PROJECT_NAME}_MINOR_VERSION 4)
SET(${PROJECT_NAME}_PATCH_LEVEL 2)

#build a shared library
ADD_LIBRARY(twaindsm SHARED dsm.cpp apps.cpp log.cpp)
target_link_libraries(twaindsm dl)

#
SET_TARGET_PROPERTIES(twaindsm PROPERTIES
					  VERSION ${${PROJECT_NAME}_MAJOR_VERSION}.${${PROJECT_NAME}_MINOR_VERSION}.${${PROJECT_NAME}_PATCH_LEVEL}
					  SOVERSION ${${PROJECT_NAME}_MAJOR_VERSION})

#add an install target here
INSTALL(FILES twain.h DESTINATION include)
INSTALL(TARGETS twaindsm 
		LIBRARY DESTINATION lib
		PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

