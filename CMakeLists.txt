cmake_minimum_required(VERSION 3.0)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")
project(HorizonScreen)

set (CMAKE_CXX_STANDARD 11)
#this builds the ctrufont header
add_custom_target(ctrufont COMMAND ${CMAKE_COMMAND} -DINFILE=${CMAKE_CURRENT_SOURCE_DIR}/data/ctrufont.bin -DOUTFILE=ctrufont_bin.h -DVARNAME=ctrufont_bin -P ${CMAKE_CURRENT_SOURCE_DIR}/cmake/bin2c.cmake)

    find_package(SDL2 REQUIRED)
    find_package(LibJpegTurbo REQUIRED)
    include_directories(${LibJpegTurbo_INCLUDE_DIRS})
    include_directories(${SDL2_INCLUDE_DIR})
    set(LIBS ${LIBS} ${LibJpegTurbo_LIBRARIES})
    set(LIBS ${LIBS} ${SDL2_LIBRARY})

include_directories(platform/PC)
include_directories(${CMAKE_CURRENT_BINARY_DIR})
set(SOURCES soos/main.cpp
        soos/inet_pton.c
        soos/tga/targa.c
        soos/utils.c)


string(TIMESTAMP DT "%Y,%m,%d")
add_definitions(-DBUILDTIME="${DT}")

if(APPLE)
    add_executable(HorizonScreen MACOSX_BUNDLE ${SOURCES})
    set_target_properties(HorizonScreen PROPERTIES MACOSX_BUNDLE_INFO_PLIST ${CMAKE_CURRENT_SOURCE_DIR}/data/Info.plist)

else()
    add_executable(HorizonScreen ${SOURCES})
endif()

add_dependencies(HorizonScreen ctrufont)
target_link_libraries(HorizonScreen ${LIBS})

install(TARGETS ${EXECUTABLE_NAME} DESTINATION bin)

# CPack packaging
include(InstallRequiredSystemLibraries)
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_SOURCE_DIR}/LICENSE")
include(CPack)