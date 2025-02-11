include(FetchContent)

set(BUILD_STATIC_LIBS ON)
set(BUILD_SHARED_LIBS OFF)
set(BUILD_SQUISH_WITH_OPENMP OFF CACHE BOOL "" FORCE)

function(px_add_dependency NAME URL URL_HASH)
    if (EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/${NAME}/CMakeLists.txt)
        add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/${NAME})
        set(${NAME}_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/${NAME} PARENT_SCOPE)
    else ()
        FetchContent_Declare(
                ${NAME}
                URL ${URL}
                URL_HASH SHA1=${URL_HASH}
        )

        if (NOT ${NAME}_POPULATED)
            message(STATUS "Downloading ${NAME}")
            FetchContent_Populate(${NAME})
            add_subdirectory(${${NAME}_SOURCE_DIR} ${${NAME}_BINARY_DIR})
        endif ()

        set(${NAME}_SOURCE_DIR ${${NAME}_SOURCE_DIR} PARENT_SCOPE)
    endif ()
endfunction()

px_add_dependency(doctest https://github.com/doctest/doctest/archive/refs/tags/v2.4.9.zip d1563419fa370c34c90e028c2e903a70c8dc07b2)
px_add_dependency(mio https://github.com/mandreyel/mio/archive/3f86a95c0784d73ce6815237ec33ed25f233b643.zip 62a0e43b07a6b66e415ad01f4b225c72a3c9935b)
px_add_dependency(lexy https://github.com/foonathan/lexy/releases/download/v2022.05.1/lexy-src.zip 252185ebbf004047725f9ed98b4e8b6e4aa06d98)
px_add_dependency(fmt https://github.com/fmtlib/fmt/releases/download/9.1.0/fmt-9.1.0.zip 8de922ba88fccaec1e7778bc069f342084573486)
px_add_dependency(glm https://github.com/g-truc/glm/releases/download/0.9.9.8/glm-0.9.9.8.zip 45408897f419944fb28d8fd835791f237be2ec19)
px_add_dependency(libsquish https://downloads.sourceforge.net/project/libsquish/libsquish-1.15.tgz 51844b9a8bc815a27e2cc0ffbede5fee3ef75110)

# msvc: disable -wno-* flags
if (NOT MSVC)
    target_compile_options(mio INTERFACE -Wno-conversion)
    target_compile_options(squish PRIVATE -Wno-unused-but-set-variable)
endif ()

target_include_directories(squish PUBLIC ${libsquish_SOURCE_DIR})
