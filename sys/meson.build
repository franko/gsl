gslsys_sources = ['minmax.c', 'prec.c', 'hypot.c', 'log1p.c', 'expm1.c', 'coerce.c', 'invhyp.c', 'pow_int.c', 'infnan.c', 'fdiv.c', 'fcmp.c', 'ldfrexp.c']

static_library('gslsys',
    gslsys_sources,
    include_directories: gsl_include_dir,
    dependencies: libm,
    build_by_default: true
)
