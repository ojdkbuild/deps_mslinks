# Copyright 2019, akashche at redhat.com
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

cmake_minimum_required ( VERSION 2.8.12 )

# project
project ( itw_mslinks NONE )

set ( ${PROJECT_NAME}_SRCDIR ${OJDKBUILD_DIR}/upstream/mslinks )

file ( GLOB_RECURSE ${PROJECT_NAME}_JAVASRC ${${PROJECT_NAME}_SRCDIR}/src/*.java )

set ( ${PROJECT_NAME}_JAVASRC_STR "" )
foreach ( _file ${${PROJECT_NAME}_JAVASRC} )
    set ( ${PROJECT_NAME}_JAVASRC_STR "${${PROJECT_NAME}_JAVASRC_STR} ${_file}\n" )
endforeach ( )

configure_file (
        ${CMAKE_CURRENT_LIST_DIR}/resources/mslinks_sources_list.txt
        ${CMAKE_CURRENT_BINARY_DIR}/mslinks_sources_list.txt )

add_custom_target ( ${PROJECT_NAME}_build_jdk )
if ( NOT icedtea_web_ENABLE_STANDALONE )
    add_dependencies ( ${PROJECT_NAME}_build_jdk image )
endif ( )

add_custom_command ( OUTPUT mslinks.jar
        COMMAND ${icedtea_web_ITW_JAVA_HOME}/bin/javac -g -encoding utf-8 -source 8 -target 8
                -d ${CMAKE_CURRENT_BINARY_DIR}
                -sourcepath ${${PROJECT_NAME}_SRCDIR}/netx
                @mslinks_sources_list.txt
        COMMAND ${icedtea_web_ITW_JAVA_HOME}/bin/jar -cf mslinks.jar io/* mslinks/*
        DEPENDS ${PROJECT_NAME}_build_jdk
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
        COMMENT "Building mslinks jar ..." )

add_custom_target ( ${PROJECT_NAME} DEPENDS mslinks.jar )
