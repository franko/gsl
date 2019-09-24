gsl_libraries += static_library('blas', 'blas.c',
    include_directories: gsl_include_dir,
    dependencies: libm,
    c_args : gsl_defines,
    build_by_default: true
)
