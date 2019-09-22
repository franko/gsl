project('gsl', 'c', version : '2.1', default_options : 'c_std=c99')

conf = configuration_data()
conf.set('GSL_DISABLE_DEPRECATED', 1)
conf.set('HAVE_CONFIG_H', 1)
conf.set('HAVE_IEEE_COMPARISONS', 1)

configure_file(input: 'config-meson.h.in', output: 'config.h', configuration: conf)

libm = dependency('m', required: false)

gsl_include_dir = include_directories('.')

subdir('gsl')
subdir('sys')
subdir('matrix')
