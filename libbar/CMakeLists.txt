SET(MY_SRC
    bar.cpp
)

ADD_LIBRARY(libbar
    ${MY_SRC}
)

INCLUDE_DIRECTORIES(
    ${CMAKE_SOURCE_DIR}/libfoo
)
