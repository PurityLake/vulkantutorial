cmake_minimum_required(VERSION 3.24)
project(vulkantutorial)

set(CMAKE_CXX_STANDARD 20)

file(GLOB_RECURSE SOURCE_FILES
        ${CMAKE_SOURCE_DIR}/src/*.c
        ${CMAKE_SOURCE_DIR}/src/*.cpp)

file(GLOB_RECURSE INCLUDE_FILES
        ${CMAKE_SOURCE_DIR}/include/*.h
        ${CMAKE_SOURCE_DIR}/include/*.hpp)

link_directories(${CMAKE_SOURCE_DIR}/lib)

add_executable(${PROJECT_NAME} ${INCLUDE_FILES} ${SOURCE_FILES} src/main.cpp)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/")

find_package(Vulkan REQUIRED)

# GLM
find_package(GLM REQUIRED)
message(STATUS "GLM included at ${GLM_INCLUDE_DIR}")

# GLFW
find_package(GLFW3 REQUIRED)
message(STATUS "Found GLFW3 in ${GLFW3_INCLUDE_DIR}")

# STB_IMAGE
add_library(STB_IMAGE "${CMAKE_SOURCE_DIR}/thirdparty/stb_image.cpp")

set(LIBS glfw3 STB_IMAGE)

include_directories(${CMAKE_SOURCE_DIR}/include ${Vulkan_INCLUDE_DIR})
target_link_libraries(${PROJECT_NAME} ${Vulkan_LIBRARIES} ${LIBS})

# Copy dlls
if(WIN32)
    add_custom_command(TARGET ${PROJECT_NAME} PRE_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy_directory
            "${PROJECT_SOURCE_DIR}/dlls"
            $<TARGET_FILE_DIR:${PROJECT_NAME}>)
endif()

add_custom_command(TARGET ${PROJECT_NAME} PRE_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${PROJECT_SOURCE_DIR}/resources
        $<TARGET_FILE_DIR:${PROJECT_NAME}>/resources)