cmake_minimum_required(VERSION 3.10)

project(runcat LANGUAGES C)

# AddressSanitize
set(CMAKE_C_FLAGS_ASAN
    "-fsanitize=address -fno-optimize-sibling-calls -fsanitize-address-use-after-scope -fno-omit-frame-pointer -g -O1"
    CACHE STRING "Flags used by the C compiler during AddressSanitizer builds."
    FORCE)



find_package(PkgConfig REQUIRED)

# Modification by RONANBOURG 09/08/2025
# Check for appindicator3-0.1 first. If not, check for ayatana-appindicator3-0.1.
# If ayatana-appindicator3-0.1 is found, we create a flag that will trigger a little macro in the main.c file and change the header path for the one
# used by ayatana-appindicator3-0.1.
# This allows compatibility with OS using the upgraded ayatana-appindicator3-0.1 without annoying users using the legacy appindicator3-0.1.
# pkg_check_modules(APPINDICATOR REQUIRED appindicator3-0.1)
pkg_check_modules(APPINDICATOR QUIET appindicator3-0.1)

if(NOT APPINDICATOR_FOUND)
    pkg_check_modules(APPINDICATOR REQUIRED ayatana-appindicator3-0.1)
    add_definitions(-DUSE_AYATANA)
else()
    pkg_check_modules(APPINDICATOR REQUIRED appindicator3-0.1)
endif()
# ENDING Modification by RONANBOURG 09/08/2025
include_directories(${APPINDICATOR_INCLUDE_DIRS})

add_executable(runcat main.c)

target_link_libraries(runcat ${APPINDICATOR_LIBRARIES})

set(ICONS ./icons/)
install(DIRECTORY ${ICONS} DESTINATION $ENV{HOME}/.config/runcat/icons)

