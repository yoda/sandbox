cmake_minimum_required(VERSION 2.6)
PROJECT(sandbox)

set(OGRE_SDK /Developer/SDKs/OgreSDK)

# Find actual Ogre root
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

IF(APPLE)
    #SET(GUI_TYPE MACOSX_BUNDLE)
    #INCLUDE_DIRECTORIES(/Library/Frameworks/Ogre.framework/Headers)
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

set(OGRE_LIB_TYPE SHARED)
set(OGRE_STATIC OFF)
if (APPLE)
    set(CMAKE_OSX_ARCHITECTURES "i386")
    #include_directories("${CMAKE_CURRENT_SOURCE_DIR}/include/OGRE/OSX")
endif ()

if (CMAKE_COMPILER_IS_GNUCXX)
    add_definitions(-msse)
endif ()

FIND_PACKAGE(OpenGL)
FIND_PACKAGE(OGRE)
FIND_PACKAGE(OIS)
FIND_PACKAGE(Boost)

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

target_link_libraries (SampleApp
    ${OIS_LIBRARY}
    ${Boost_LIBRARIES}
    ${EXTRA_LIBS}
)

MESSAGE("OIS_LIBRARY: ${OIS_LIBRARY}")
MESSAGE("OctreeZone: ${OGRE_Plugin_PCZSceneManager_INCLUDE_DIRS}")
MESSAGE("OGRE_INCLUDE_DIR: ${OGRE_INCLUDE_DIR}")
MESSAGE("OGRE_INCLUDE_DIRS: ${OGRE_INCLUDE_DIRS}")
MESSAGE("OGRE_LIBRARY: ${OGRE_LIBRARY}")
