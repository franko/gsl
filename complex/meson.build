gsl_libraries += static_library('complex', ['math.c', 'inline.c'],
    include_directories: gsl_include_dir,
    c_args : gsl_defines,
    dependencies: libm,
    build_by_default: true
)
