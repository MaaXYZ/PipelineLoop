file(GLOB_RECURSE pipeline_ext_src *.h *.hpp *.cpp)
file(GLOB_RECURSE pipeline_ext_header ${MAA_PLUGIN_PUBLIC_INC}/*)

add_library(PipelineExt SHARED ${pipeline_ext_src} ${pipeline_ext_header})

target_include_directories(
    PipelineExt
    PUBLIC ${MAA_PLUGIN_PUBLIC_INC}
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

target_compile_definitions(PipelineExt PRIVATE MAA_PLUGIN_EXPORTS)

target_link_libraries(PipelineExt PRIVATE MaaUtils MaaFramework::MaaFramework)

install(TARGETS PipelineExt
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION bin
    ARCHIVE DESTINATION lib
)

if(WIN32)
    install(FILES $<TARGET_PDB_FILE:PipelineExt> DESTINATION symbol OPTIONAL)
endif()

install(DIRECTORY ${MAA_PLUGIN_PUBLIC_INC}/ DESTINATION include)

add_dependencies(PipelineExt MaaUtils)
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${pipeline_ext_src})
