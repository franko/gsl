matrix_sources = ['init.c', 'matrix.c', 'file.c', 'rowcol.c', 'swap.c', 'copy.c', 'minmax.c', 'prop.c', 'oper.c', 'getset.c', 'view.c', 'submatrix.c', 'view.h']

gsl_libraries += static_library('matrix',
    matrix_sources,
    include_directories: gsl_include_dir,
    c_args : gsl_defines,
    dependencies: libm,
    build_by_default: true
)
