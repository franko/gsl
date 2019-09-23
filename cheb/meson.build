static_library('cheb', ['deriv.c', 'eval.c', 'init.c', 'integ.c'],
    include_directories: gsl_include_dir,
    c_args : gsl_defines,
    dependencies: libm,
    build_by_default: true
)
