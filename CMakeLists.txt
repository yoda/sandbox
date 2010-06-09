cmake_minimum_required(VERSION 2.6)
PROJECT(sandbox)

set(OGRE_SDK /Developer/SDKs/OgreSDK)

# Find actual Ogre root
# Stole this stuff from Ogre's CmakeLists.txt in theory it lets me use their cmake find scripts...in theory
if (WIN32 OR APPLE)
  get_filename_component(OGRE_ROOT_DIR "${OGRE_SDK}" ABSOLUTE)
  set(OGRE_TEMPLATES_DIR "${OGRE_ROOT_DIR}/cmake")
  set(OGRE_WORK_DIR "${OGRE_ROOT_DIR}")
  set(CMAKE_MODULE_PATH "${OGRE_ROOT_DIR}/cmake")
elseif (UNIX)
  get_filename_component(OGRE_ROOT_DIR "${OGRE_SOURCE_DIR}/../.." ABSOLUTE)
  set(OGRE_TEMPLATES_DIR "${OGRE_ROOT_DIR}/lib/OGRE/cmake")
  set(OGRE_WORK_DIR "${OGRE_ROOT_DIR}")
  set(CMAKE_MODULE_PATH "${OGRE_ROOT_DIR}/lib/OGRE/cmake")
endif ()


# Cmake framework stuff to make frameworks actually work.
IF(APPLE)
    INCLUDE_DIRECTORIES(/Library/Frameworks/Cg.framework/Headers)
    INCLUDE_DIRECTORIES(/Developer/SDKs/MacOSX10.6.sdk/System/Library/Frameworks/IOKit.framework/Versions/A/Headers)
    INCLUDE_DIRECTORIES ( /Developer/Headers/FlatCarbon )
    FIND_LIBRARY(CARBON_LIBRARY Carbon)
    FIND_LIBRARY(OGRE_LIBRARY Ogre)
    FIND_LIBRARY(CG_LIBRARY CG)
    FIND_LIBRARY(IOKit_LIBRARY IOKit)
    MARK_AS_ADVANCED (OGRE_LIBRARY)
    MARK_AS_ADVANCED (IOKit_LIBRARY)
    MARK_AS_ADVANCED (CARBON_LIBRARY)
    MARK_AS_ADVANCED (CG_LIBRARY)
    SET(EXTRA_LIBS ${OGRE_LIBRARY} ${IOKit_LIBRARY} ${CARBON_LIBRARY} ${CG_LIBRARY}) 
ENDIF (APPLE)

# Stuff I found in CMakeLists.txt in Ogre the i386 is nessecary for me.
set(OGRE_LIB_TYPE SHARED)
set(OGRE_STATIC OFF)
if (APPLE)
    set(CMAKE_OSX_ARCHITECTURES "i386")
endif ()

# Wont work if compiling for iphone
if (CMAKE_COMPILER_IS_GNUCXX)
    add_definitions(-msse)
endif ()

# Find packages finding ogre here is probably redundant
FIND_PACKAGE(OpenGL)
FIND_PACKAGE(OGRE)
FIND_PACKAGE(OIS)
FIND_PACKAGE(Boost)

# include within the sdk dir is required for ExampleFramework, not sure for non exampleframework stuff.
INCLUDE_DIRECTORIES(
    ${OpenGL_INCLUDE_DIR}
    "${OGRE_SDK}/include/OGRE/OSX"
    "${OGRE_SDK}/include/OGRE"
    ${OGRE_INCLUDE_DIR}
    ${OIS_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
    ${OGRE_Plugin_PCZSceneManager_INCLUDE_DIRS}
    ${OGRE_Plugin_OctreeZone_INCLUDE_DIRS}
)

ADD_EXECUTABLE(SampleApp
        src/SampleApp.cpp
)

# Extra libs is from the framework stuff
target_link_libraries (SampleApp
    ${OIS_LIBRARY}
    ${Boost_LIBRARIES}
    ${EXTRA_LIBS}
)

# Debug stuff...
MESSAGE("OIS_LIBRARY: ${OIS_LIBRARY}")
MESSAGE("OctreeZone: ${OGRE_Plugin_PCZSceneManager_INCLUDE_DIRS}")
MESSAGE("OGRE_INCLUDE_DIR: ${OGRE_INCLUDE_DIR}")
MESSAGE("OGRE_INCLUDE_DIRS: ${OGRE_INCLUDE_DIRS}")
MESSAGE("OGRE_LIBRARY: ${OGRE_LIBRARY}")
