# We need thread support
find_package(Threads REQUIRED)

# Enable ExternalProject CMake module
include(ExternalProject)

# Download and install GoogleTest
ExternalProject_Add(
    gtest
    URL https://github.com/google/googletest/archive/master.zip
    PREFIX ${CMAKE_CURRENT_BINARY_DIR}/gtest
    # Disable install step
    INSTALL_COMMAND ""
)

# Get GTest source and binary directories from CMake project
ExternalProject_Get_Property(gtest source_dir binary_dir)

# Create a libgtest target to be used as a dependency by test programs
add_library(libgtest IMPORTED STATIC GLOBAL)
add_dependencies(libgtest gtest)

# Create a libgmock target to be used as a dependency by test programs
add_library(libgmock IMPORTED STATIC GLOBAL)
add_dependencies(libgmock gtest)


IF ( WIN32 )
    foreach( OUTPUTCONFIG ${CMAKE_CONFIGURATION_TYPES} )
        string( TOUPPER ${OUTPUTCONFIG} OUTPUTCONFIG )
        set_target_properties( libgtest PROPERTIES 
            "IMPORTED_LOCATION_${OUTPUTCONFIG}" "${binary_dir}/googlemock/gtest/${OUTPUTCONFIG}/gtest.lib" 
            "IMPORTED_LINK_INTERFACE_LIBRARIES" "${CMAKE_THREAD_LIBS_INIT}"
        )
        set_target_properties( libgmock PROPERTIES 
            "IMPORTED_LOCATION_${OUTPUTCONFIG}" "${binary_dir}/googlemock/${OUTPUTCONFIG}/gmock.lib" 
            "IMPORTED_LINK_INTERFACE_LIBRARIES" "${CMAKE_THREAD_LIBS_INIT}"
        )
    endforeach( OUTPUTCONFIG CMAKE_CONFIGURATION_TYPES )
ELSE ( WIN32 )
    set_target_properties( libgtest PROPERTIES 
        "IMPORTED_LOCATION" "${binary_dir}/googlemock/gtest/libgtest.a" 
        "IMPORTED_LINK_INTERFACE_LIBRARIES" "${CMAKE_THREAD_LIBS_INIT}"
)
    set_target_properties( libgmock PROPERTIES 
        "IMPORTED_LOCATION" "${binary_dir}/googlemock/libgmock.a" 
        "IMPORTED_LINK_INTERFACE_LIBRARIES" "${CMAKE_THREAD_LIBS_INIT}"
)

ENDIF ( WIN32 )

# I couldn't make it work with INTERFACE_INCLUDE_DIRECTORIES
include_directories("${source_dir}/googletest/include"
                    "${source_dir}/googlemock/include")


SET(MY_SRC
    main.cpp
    testfoo.cpp
)

ADD_EXECUTABLE(testfoo
    ${MY_SRC}
)

TARGET_LINK_LIBRARIES(testfoo
    libfoo
    libbar
    libgtest
    libgmock
)

INCLUDE_DIRECTORIES(
   ${CMAKE_SOURCE_DIR}/libfoo
   ${CMAKE_SOURCE_DIR}/libbar
)

INSTALL(TARGETS testfoo DESTINATION test)
