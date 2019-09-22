matrix_sources = ['init.c', 'matrix.c', 'file.c', 'rowcol.c', 'swap.c', 'copy.c', 'minmax.c', 'prop.c', 'oper.c', 'getset.c', 'view.c', 'submatrix.c', 'view.h']

static_library('matrix',
    matrix_sources,
    include_directories: gsl_include_dir,
    dependencies: libm,
    build_by_default: true
)
