project('gsl', 'c', version : '2.1', default_options : 'c_std=c99')

conf = configuration_data()
conf.set('GSL_DISABLE_DEPRECATED', 1)
conf.set('HAVE_CONFIG_H', 1)

configure_file(output: 'config.h', configuration: conf)

libm = dependency('m', required: false)

subdir('gsl')
subdir('sys')

# gsl_sources = [
#    'blas/blas.c', 'block/block.c'
# ]
