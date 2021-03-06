CMAKE_MINIMUM_REQUIRED (VERSION 2.8)

PROJECT (oscrccheck)

SET ( CRC_SOURCES
    "${PROJECT_SOURCE_DIR}/oscrccheck.c"
    "${PROJECT_SOURCE_DIR}/defines.h"
    "${CMAKE_SOURCE_DIR}/tools/oschecksum/include/oschecksum/crc32.h"
)

SET ( ADD_ADDITIONAL_LIBRARIES
    "oschecksum"
)

IF ( WIN32 )
    INCLUDE_DIRECTORIES ( "${openSAFETY_SOURCE_DIR}/contrib/osswin/" )
    SET ( CRC_SOURCES
        "${CRC_SOURCES}"
        "${openSAFETY_SOURCE_DIR}/contrib/osswin/osswin.h"
        "${CMAKE_SOURCE_DIR}/contrib/osswin/getopt.c"
    )
    SET ( ADD_ADDITIONAL_LIBRARIES "${ADD_ADDITIONAL_LIBRARIES}" "osswin" )
    SET ( CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /wd4996" )
ENDIF ( WIN32 )

INCLUDE_DIRECTORIES ( "${CMAKE_SOURCE_DIR}/tools/oschecksum/include/" )

ADD_EXECUTABLE ( oscrccheck ${CRC_SOURCES} )
TARGET_LINK_LIBRARIES ( oscrccheck ${ADD_ADDITIONAL_LIBRARIES} )
ADD_DEPENDENCIES(oscrccheck "${ADD_ADDITIONAL_LIBRARIES}" )

ADD_EXECUTABLE ( libcrccalc EXCLUDE_FROM_ALL "${PROJECT_SOURCE_DIR}/libcrccalc.c" "${PROJECT_SOURCE_DIR}/defines.h" )
#TARGET_LINK_LIBRARIES ( libcrccalc ${ADD_ADDITIONAL_LIBRARIES} )
#ADD_DEPENDENCIES(libcrccalc "${ADD_ADDITIONAL_LIBRARIES}" )

IF ( ( WIN32 OR CYGWIN ) AND ( ${LIB_TYPE} STREQUAL "SHARED" ) )
    EnsureLibraries(oscrccheck "${ADD_ADDITIONAL_LIBRARIES}" )
ENDIF ( ( WIN32 OR CYGWIN ) AND ( ${LIB_TYPE} STREQUAL "SHARED" ) )

INSTALL ( TARGETS oscrccheck DESTINATION bin )

INSTALL ( FILES "${PROJECT_SOURCE_DIR}/oschecksum.crc" DESTINATION include/opensafety )
INSTALL ( FILES "${PROJECT_SOURCE_DIR}/defines.h" DESTINATION include/opensafety )
