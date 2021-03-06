project(CHAKRA_PLATFORM_AGNOSTIC)

set(PL_SOURCE_FILES
  Common/UnicodeText.Common.cpp
  Common/HiResTimer.cpp
  Common/DateTime.cpp
  Linux/NumbersUtility.cpp
  POSIX/ArraysUtility.cpp
  Common/Thread.cpp
  Common/Trace.cpp
  Common/SystemInfo.Common.cpp
  )

if(NO_ICU)
  set(PL_SOURCE_FILES
    ${PL_SOURCE_FILES}
    POSIX/UnicodeText.cpp
  )
else()
  set(PL_SOURCE_FILES
    ${PL_SOURCE_FILES}
    Common/UnicodeText.ICU.cpp
  )
endif()

if(CC_TARGET_OS_ANDROID OR CC_TARGET_OS_LINUX)
  set(PL_SOURCE_FILES ${PL_SOURCE_FILES}
    Linux/SystemInfo.cpp
    Linux/PerfTrace.cpp
    )
elseif(CC_TARGET_OS_OSX)
  set(PL_SOURCE_FILES ${PL_SOURCE_FILES}
    Unix/AssemblyCommon.cpp
    Unix/SystemInfo.cpp
    # Linux/PerfTrace.cpp # TODO : implement for OSX?
    )
endif()

add_library (Chakra.Runtime.PlatformAgnostic OBJECT
  ${PL_SOURCE_FILES}
  )

target_include_directories (
  Chakra.Runtime.PlatformAgnostic PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}
  )

if(NOT STATIC_LIBRARY)
  # CH has a direct dependency to this project
  add_library (Chakra.Runtime.PlatformAgnostic.Singular STATIC
    $<TARGET_OBJECTS:Chakra.Runtime.PlatformAgnostic>
    )
endif()
