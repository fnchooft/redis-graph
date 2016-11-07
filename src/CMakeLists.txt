# The file list for building the graph database library.
# They can be used directly for unit tests.
# the ../src part is so that they can be accessed from outside the src tree
set(_graph_files  
      ../src/edge.c
      ../src/node.c
      ../src/value.c
      ../src/module.c
      ../src/triplet.c
      ../src/value_cmp.c
      ../src/query_executor.c

      ../src/parser/ast.c
      ../src/parser/lex.yy.c
      ../src/parser/grammar.c

      ../src/rmutil/sds.c
      ../src/rmutil/util.c
      ../src/rmutil/vector.c
      ../src/rmutil/strings.c
      )


set (graph_files ${_graph_files} PARENT_SCOPE)

# Libgraph target - a static library for graph database that can be linked in any module
add_library(Llibgraph STATIC  ${_graph_files})

target_compile_options(Llibgraph PUBLIC "-fPIC" "-DREDIS_MODULE_TARGET" "-I${CMAKE_CURRENT_LIST_DIR}")


# build the parser source code before building Llibgraph
#add_custom_command(
#                TARGET Llibgraph
#                PRE_BUILD
#                COMMAND lex -i lexer.l
#                COMMAND lemon -s grammar.y 
#                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/src/parser)



add_library(module MODULE
    module.c
    rmutil/util.c
    rmutil/strings.c
)

add_library(librmutil STATIC IMPORTED ../../src/rmutil/librmutil.a)

target_compile_options(module PUBLIC "-DREDIS_MODULE_TARGET")

target_link_libraries(module Llibgraph)