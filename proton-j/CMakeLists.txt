#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.
#

include(UseJava)
include(ProtonUseJava)
set(CMAKE_JAVA_TARGET_VERSION ${PN_VERSION})
file(GLOB_RECURSE SOURCES_ABS "src/main/java/*.java" "../tests/*/ProtonJInterop.java")
add_jar(proton-j ${SOURCES_ABS})
rebuild_jar(proton-j proton-j-${PN_VERSION}.jar)
set (JAVA_INSTALL_DIR ${SHARE_INSTALL_DIR}/java CACHE PATH "Installation directory for all JARs except those using JNI")
mark_as_advanced (JAVA_INSTALL_DIR)
install_jar(proton-j ${JAVA_INSTALL_DIR})

# add relevant CTest support
find_program (MAVEN_EXE NAMES mvn.cmd mvn DOC "Location of the maven program")
mark_as_advanced (MAVEN_EXE)
if (CMAKE_BUILD_TYPE MATCHES "Coverage")
  message (STATUS "Building for coverage analysis: testing disabled for Proton-J")
elseif (MAVEN_EXE)
  add_test (proton-java ${MAVEN_EXE} clean test --file ${Proton_SOURCE_DIR}/pom.xml)
else ()
  message (STATUS "Cannot find Maven: testing disabled for Proton-J")
endif ()
