find_package(SWIG REQUIRED)
include(${SWIG_USE_FILE})
find_package(Perl REQUIRED)
find_package(PerlLibs REQUIRED)
set(CUSTOM_PERL_SITE_ARCH_DIR ${PERL_SITEARCH} CACHE STRING "Custom installation directory for perl binary extension")
include_directories(${PERL_INCLUDE_PATH})
include_directories(${PROJECT_SOURCE_DIR}/src/mapscript/swiginc)
include_directories(${PROJECT_SOURCE_DIR}/src/mapscript/)
add_definitions(${PERL_EXTRA_C_FLAGS})
set(CMAKE_SWIG_FLAGS -shadow -w314)
include_directories(${PROJECT_SOURCE_DIR}/src/mapscript/perl)
include_directories(${PROJECT_BINARY_DIR}/src/mapscript/)

if (${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION} VERSION_GREATER 3.7)
    swig_add_library(perlmapscript TYPE MODULE LANGUAGE perl5 SOURCES ../mapscript.i)
else ()
    swig_add_module(perlmapscript perl5 ../mapscript.i)
endif ()

SWIG_LINK_LIBRARIES(perlmapscript ${PERL_LIBRARIES} ${MAPSERVER_LIBMAPSERVER})
set_target_properties(perlmapscript PROPERTIES OUTPUT_NAME mapscript)
set_target_properties(perlmapscript PROPERTIES PREFIX "")
if(APPLE)
  set_target_properties(perlmapscript PROPERTIES SUFFIX ".bundle")
endif(APPLE)

install(FILES $<TARGET_FILE:${SWIG_MODULE_perlmapscript_REAL_NAME}> DESTINATION ${CUSTOM_PERL_SITE_ARCH_DIR}/auto/mapscript)
install(FILES $<TARGET_FILE_DIR:${SWIG_MODULE_perlmapscript_REAL_NAME}>/mapscript.pm DESTINATION ${CUSTOM_PERL_SITE_ARCH_DIR})

file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/tests DESTINATION  ${CMAKE_CURRENT_BINARY_DIR})
file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/examples DESTINATION  ${CMAKE_CURRENT_BINARY_DIR})
