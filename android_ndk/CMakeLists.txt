cmake_minimum_required(VERSION 3.5)

project(android_ndk)

find_package(ament_cmake REQUIRED)

include(FetchContent)

FetchContent_Declare(
    android_ndk
    URL https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip
    DOWNLOAD_EXTRACT_TIMESTAMP false
)

FetchContent_GetProperties(android_ndk)

FetchContent_MakeAvailable(android_ndk)

# Construct an "android-sdk" directory, which must look like:
# ./android-sdk/cmdline-tools/latest/
#                                    lib/
#                                    bin/

set(_ANDROID_SDK_DIR ${CMAKE_CURRENT_BINARY_DIR}/android-sdk)
set(_ANDROID_SDK_CMDLINE_TOOLS_DIR ${_ANDROID_SDK_DIR}/cmdline-tools/latest)
set(_ANDROID_SDKMANAGER ${_ANDROID_SDK_CMDLINE_TOOLS_DIR}/bin/sdkmanager)

file(MAKE_DIRECTORY ${_ANDROID_SDK_CMDLINE_TOOLS_DIR})
file(COPY ${android_ndk_SOURCE_DIR}/ DESTINATION ${_ANDROID_SDK_CMDLINE_TOOLS_DIR})

# Install the required Android SDK / NDK packages
execute_process(
    COMMAND bash -c "yes | ${_ANDROID_SDKMANAGER} \"build-tools;34.0.0\" \"ndk;26.3.11579264\" \"platform-tools\" \"platforms;android-22\""
)

install(DIRECTORY ${_ANDROID_SDK_DIR}/ DESTINATION android-sdk USE_SOURCE_PERMISSIONS)

ament_package()