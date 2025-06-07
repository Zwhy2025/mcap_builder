add_library(mcap lib.cpp)

target_include_directories(mcap PUBLIC
  $<BUILD_INTERFACE:${MCAP_INCLUDE_DIR}>
  $<INSTALL_INTERFACE:include/>
)

set(DEPENDENCIES "")

find_path(LZ4_INCLUDE_DIR lz4.h HINTS /usr/include)
find_library(LZ4_LIBRARY NAMES lz4 HINTS /usr/lib/x86_64-linux-gnu)
if (LZ4_INCLUDE_DIR AND LZ4_LIBRARY)
  message(STATUS "Found lz4 manually: include=${LZ4_INCLUDE_DIR} lib=${LZ4_LIBRARY}")
  target_include_directories(mcap PUBLIC ${LZ4_INCLUDE_DIR})
  list(APPEND DEPENDENCIES ${LZ4_LIBRARY})
else()
  message(WARNING "LZ4 not found, disabling")
  target_compile_definitions(mcap PUBLIC "MCAP_COMPRESSION_NO_LZ4")
endif()

find_path(ZSTD_INCLUDE_DIR zstd.h HINTS /usr/include)
find_library(ZSTD_LIBRARY NAMES zstd HINTS /usr/lib/x86_64-linux-gnu)
if (ZSTD_INCLUDE_DIR AND ZSTD_LIBRARY)
  message(STATUS "Found zstd manually: include=${ZSTD_INCLUDE_DIR} lib=${ZSTD_LIBRARY}")
  target_include_directories(mcap PUBLIC ${ZSTD_INCLUDE_DIR})
  list(APPEND DEPENDENCIES ${ZSTD_LIBRARY})
else()
  message(WARNING "ZSTD not found, disabling")
  target_compile_definitions(mcap PUBLIC "MCAP_COMPRESSION_NO_ZSTD")
endif()


target_link_libraries(mcap PUBLIC ${DEPENDENCIES})
