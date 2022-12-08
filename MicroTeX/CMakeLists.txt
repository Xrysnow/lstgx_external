cmake_minimum_required(VERSION 3.6)

set(lib_name MicroTeX)
set(target_name ext_${lib_name})

project(${lib_name})
set(CMAKE_CXX_STANDARD 17)

set(src_dir ${CMAKE_CURRENT_SOURCE_DIR}/src/src)

add_library(${target_name} STATIC
  # atom folder
  ${src_dir}/atom/atom_basic.cpp
  ${src_dir}/atom/atom_char.cpp
  ${src_dir}/atom/atom_impl.cpp
  ${src_dir}/atom/atom_matrix.cpp
  ${src_dir}/atom/atom_row.cpp
  ${src_dir}/atom/atom_space.cpp
  ${src_dir}/atom/colors_def.cpp
  ${src_dir}/atom/unit_conversion.cpp
  # box folder
  ${src_dir}/box/box.cpp
  ${src_dir}/box/box_factory.cpp
  ${src_dir}/box/box_group.cpp
  ${src_dir}/box/box_single.cpp
  # core folder
  ${src_dir}/core/core.cpp
  ${src_dir}/core/formula.cpp
  ${src_dir}/core/formula_def.cpp
  ${src_dir}/core/glue.cpp
  ${src_dir}/core/localized_num.cpp
  ${src_dir}/core/macro.cpp
  ${src_dir}/core/macro_def.cpp
  ${src_dir}/core/macro_impl.cpp
  ${src_dir}/core/parser.cpp
  # fonts folder
  ${src_dir}/fonts/alphabet.cpp
  ${src_dir}/fonts/font_basic.cpp
  ${src_dir}/fonts/font_info.cpp
  ${src_dir}/fonts/fonts.cpp
  # utils folder
  ${src_dir}/utils/string_utils.cpp
  ${src_dir}/utils/utf.cpp
  ${src_dir}/utils/utils.cpp
  # res folder
  ${src_dir}/res/builtin/formula_mappings.res.cpp
  ${src_dir}/res/builtin/symbol_mapping.res.cpp
  ${src_dir}/res/builtin/tex_param.res.cpp
  ${src_dir}/res/builtin/tex_symbols.res.cpp
  ${src_dir}/res/font/bi10.def.cpp
  ${src_dir}/res/font/bx10.def.cpp
  ${src_dir}/res/font/cmbsy10.def.cpp
  ${src_dir}/res/font/cmbx10.def.cpp
  ${src_dir}/res/font/cmbxti10.def.cpp
  ${src_dir}/res/font/cmex10.def.cpp
  ${src_dir}/res/font/cmmi10.def.cpp
  ${src_dir}/res/font/cmmi10_unchanged.def.cpp
  ${src_dir}/res/font/cmmib10.def.cpp
  ${src_dir}/res/font/cmmib10_unchanged.def.cpp
  ${src_dir}/res/font/cmr10.def.cpp
  ${src_dir}/res/font/cmss10.def.cpp
  ${src_dir}/res/font/cmssbx10.def.cpp
  ${src_dir}/res/font/cmssi10.def.cpp
  ${src_dir}/res/font/cmsy10.def.cpp
  ${src_dir}/res/font/cmti10.def.cpp
  ${src_dir}/res/font/cmti10_unchanged.def.cpp
  ${src_dir}/res/font/cmtt10.def.cpp
  ${src_dir}/res/font/dsrom10.def.cpp
  ${src_dir}/res/font/eufb10.def.cpp
  ${src_dir}/res/font/eufm10.def.cpp
  ${src_dir}/res/font/i10.def.cpp
  ${src_dir}/res/font/moustache.def.cpp
  ${src_dir}/res/font/msam10.def.cpp
  ${src_dir}/res/font/msbm10.def.cpp
  ${src_dir}/res/font/r10.def.cpp
  ${src_dir}/res/font/r10_unchanged.def.cpp
  ${src_dir}/res/font/rsfs10.def.cpp
  ${src_dir}/res/font/sb10.def.cpp
  ${src_dir}/res/font/sbi10.def.cpp
  ${src_dir}/res/font/si10.def.cpp
  ${src_dir}/res/font/special.def.cpp
  ${src_dir}/res/font/ss10.def.cpp
  ${src_dir}/res/font/stmary10.def.cpp
  ${src_dir}/res/font/tt10.def.cpp
  ${src_dir}/res/parser/font_parser.cpp
  ${src_dir}/res/parser/formula_parser.cpp
  ${src_dir}/res/reg/builtin_font_reg.cpp
  ${src_dir}/res/reg/builtin_syms_reg.cpp
  ${src_dir}/res/sym/amsfonts.def.cpp
  ${src_dir}/res/sym/amssymb.def.cpp
  ${src_dir}/res/sym/base.def.cpp
  ${src_dir}/res/sym/stmaryrd.def.cpp
  ${src_dir}/res/sym/symspecial.def.cpp

  ${src_dir}/latex.cpp
  ${src_dir}/render.cpp
)

use_cocos2dx_compile_options(${target_name})
add_compile_options("/utf-8")

target_include_directories(${target_name} PRIVATE ${src_dir})
target_include_directories(${target_name} INTERFACE ${src_dir})

target_link_libraries(${target_name} ext_tinyxml2)
target_include_directories(${target_name} PRIVATE ${COCOS2DX_ROOT_PATH}/external/tinyxml2)

set_target_properties(${target_name}
  PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
  FOLDER "External"
)

if(XCODE OR VS)
  cocos_mark_code_files(${target_name})
endif()
