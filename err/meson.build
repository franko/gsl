static_library('err', ['error.c', 'stream.c', 'message.c', 'strerror.c'],
    include_directories: gsl_include_dir,
    c_args : gsl_defines,
    dependencies: libm,
    build_by_default: true
)
