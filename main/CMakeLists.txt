SET(MY_SRC
    main.cpp
)

ADD_EXECUTABLE(myapp
    ${MY_SRC}
)

TARGET_LINK_LIBRARIES(myapp
    libfoo
    libbar
)

INCLUDE_DIRECTORIES(
   ${CMAKE_SOURCE_DIR}/libfoo
   ${CMAKE_SOURCE_DIR}/libbar
)
