project('gsl', 'c', version : '2.1', default_options : 'c_std=c99')

libm = dependency('m', required: false)

conf = configuration_data()
conf.set('GSL_DISABLE_DEPRECATED', 1)
conf.set('HAVE_CONFIG_H', 1)
conf.set('HAVE_INLINE', 1)

compiler = meson.get_compiler('c')

ieee_comparisons_code = '''#include <math.h>
int main () { 
    int status; double inf, nan;
    inf = exp(1.0e10);
    nan = inf / inf ;
    status = (nan == nan);
    return status;
}
'''

ieee_comparisons_check = compiler.run(ieee_comparisons_code, args: '-lm', name : 'correct IEEE comparisons for NaN')
if ieee_comparisons_check.returncode() == 0
    conf.set('HAVE_IEEE_COMPARISONS', 1)
endif

ieee_denormals_code = '''#include <math.h> 
int main () { 
    int i, status; 
    volatile double z = 1e-308;
    for (i = 0; i < 5; i++) { z = z / 10.0 ; };        
    for (i = 0; i < 5; i++) { z = z * 10.0 ; };
    status = (z == 0.0);
    return status;
}
'''
ieee_denormals_check = compiler.run(ieee_denormals_code, args: '-lm', name : 'IEEE denormalized arithmetic')
if ieee_denormals_check.returncode() == 0
    conf.set('HAVE_IEEE_DENORMALS', 1)
endif

exit_success_code='''#include <stdlib.h>
int main() {
    return EXIT_SUCCESS;
}
'''
if compiler.compiles(exit_success_code, name : 'EXIT_SUCCESS is defined')
    conf.set('HAVE_EXIT_SUCCESS_AND_FAILURE', 1)
endif

configure_file(input: 'config-meson.h.in', output: 'config.h', configuration: conf)

gsl_include_dir = include_directories('.')

subdir('gsl')
subdir('sys')
subdir('matrix')
