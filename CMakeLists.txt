#
# Copyright (C) 2016 The Android Open Source Project
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

# Build a static lib and copy it into distribution place for independent app to use
# we could not delete the bin directory is because android studio would error out
# when it checks target's existance. Only distribution place is used as import to app

cmake_minimum_required(VERSION 3.6.0)

set(CMAKE_VERBOSE_MAKEFILE on)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/android)
add_definitions(-DHAVE_CONFIG_H -DFPM_DEFAULT)

add_library(mad STATIC
            version.c
            fixed.c
            bit.c
            timer.c
            stream.c
            frame.c
            synth.c
            decoder.c
            layer12.c
            layer3.c
            huffman.c)

# target_link_libraries(mad android log)

# copy out the lib binary... need to leave the static lib around to pass gradle check
set(distribution_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../../../../../${TARGET_DIR})
set_target_properties(mad
                      PROPERTIES
                      ARCHIVE_OUTPUT_DIRECTORY
                      "${distribution_DIR}/libmad/lib/${ANDROID_ABI}")

# copy out lib header file...
add_custom_command(TARGET mad POST_BUILD
                   COMMAND "${CMAKE_COMMAND}" -E
                   copy "${CMAKE_CURRENT_SOURCE_DIR}/mad.h"
                   "${distribution_DIR}/libmad/include/mad.h"
                   COMMENT "Copying mad to output directory")
