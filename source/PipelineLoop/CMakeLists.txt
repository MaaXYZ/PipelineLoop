file(GLOB_RECURSE pipeline_loop_src *.h *.hpp *.cpp)
file(GLOB_RECURSE pipeline_loop_header ${MAA_PLUGIN_PUBLIC_INC}/*)

add_library(PipelineLoop SHARED ${pipeline_loop_src} ${pipeline_loop_header})

target_include_directories(
    PipelineLoop
    PUBLIC ${MAA_PLUGIN_PUBLIC_INC}
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

target_compile_definitions(PipelineLoop PRIVATE MAA_PLUGIN_EXPORTS)

target_link_libraries(PipelineLoop PRIVATE MaaUtils MaaFramework::MaaFramework)

install(TARGETS PipelineLoop
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION bin
    ARCHIVE DESTINATION lib
)

if(WIN32)
    install(FILES $<TARGET_PDB_FILE:PipelineLoop> DESTINATION symbol OPTIONAL)
endif()

install(DIRECTORY ${MAA_PLUGIN_PUBLIC_INC}/ DESTINATION include)

add_dependencies(PipelineLoop MaaUtils)
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${pipeline_loop_src})
