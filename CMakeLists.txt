cmake_minimum_required(VERSION 3.19 FATAL_ERROR)
project(simple LANGUAGES CXX)

# Rust packages
string(TOUPPER "${CMAKE_BUILD_TYPE}" CMAKE_BUILD_TYPE_UPPER)
if (${CMAKE_BUILD_TYPE_UPPER} MATCHES DEBUG OR
${CMAKE_BUILD_TYPE_UPPER} MATCHES RELWITHDEBINFO)
  message(WARNING "Building debug rust packages")
  set(TARGET_BUILD_TYPE debug)
  set(CORROSION_PROF dev)
else()
  message(WARNING "Building release rust packages")
  set(TARGET_BUILD_TYPE release)
  set(CORROSION_PROF release)
endif()

include(FetchContent)
FetchContent_Declare(
    Corrosion
    GIT_REPOSITORY https://github.com/corrosion-rs/corrosion.git
    GIT_TAG 1c6974c2473765449e7c4649f9f96f1b751420c3 # Optionally specify a commit hash, version tag or branch here
)
# Set any global configuration variables such as `Rust_TOOLCHAIN` before this line!
FetchContent_MakeAvailable(Corrosion)

corrosion_import_crate(
  MANIFEST_PATH ${CMAKE_CURRENT_SOURCE_DIR}/Cargo.toml
  IMPORTED_CRATES CORROSION_IMPORTED
  NO_LINKER_OVERRIDE
  FLAGS "--timings" "-v" "-j1"
  PROFILE ${CORROSION_PROF}
)
