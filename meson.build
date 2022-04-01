project('gsl', 'c', version: '2.1', default_options : 'c_std=gnu99')

pkg = import('pkgconfig')

cc = meson.get_compiler('c')
libm = cc.find_library('m', required : false)

gsl_defines = ['-DHAVE_CONFIG_H']

conf = configuration_data()
conf.set('package', meson.project_name())
conf.set('version', meson.project_version())
conf.set('GSL_DISABLE_DEPRECATED', 1)
conf.set('HAVE_INLINE', 1)

if target_machine.cpu_family() in ['m68k', 'x86', 'x86_64']
    conf.set('HAVE_EXTENDED_PRECISION_REGISTERS', 1)
    message('CPU family has extended precision register')
endif

if target_machine.cpu_family() in ['x86', 'x86_64'] and target_machine.system() in ['linux', 'gnu']
    conf.set('HAVE_GNUX86_IEEE_INTERFACE', 1)
    message('Detected x86 GNU/Linux IEEE interface')
elif target_machine.cpu_family() == 'm68k' and target_machine.system() == 'linux'
    conf.set('HAVE_GNUM68K_IEEE_INTERFACE', 1)
    message('Detected M68k Linux IEEE interface')
elif target_machine.cpu_family() in ['x86', 'x86_64'] and target_machine.system() == 'darwin'
    conf.set('HAVE_DARWIN86_IEEE_INTERFACE', 1)
    message('Detected x86 Darwin IEEE interface')
elif target_machine.system() in ['freebsd', 'netbsd', 'openbsd']
    conf.set('HAVE_' + target_machine.system().to_upper() + '_IEEE_INTERFACE', 1)
    message('Detected ' + target_machine.system() + ' IEEE interface')
else
    # There are other known interfaces but they are obsolete and are not covered
    # in the Meson config.
    message('No known IEEE interface found')
endif

exit_success_code='''#include <stdlib.h>
int main() {
    return EXIT_SUCCESS;
}
'''
if cc.compiles(exit_success_code, name : 'EXIT_SUCCESS is defined')
    conf.set('HAVE_EXIT_SUCCESS_AND_FAILURE', 1)
endif

ieee_comparisons_code = '''#include <math.h>
int main () {
    int status; double inf, nan;
    inf = exp(1.0e10);
    nan = inf / inf ;
    status = (nan == nan);
    return status;
}
'''

ieee_comparisons_check = cc.run(ieee_comparisons_code, args: '-lm', name : 'correct IEEE comparisons for NaN')
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
ieee_denormals_check = cc.run(ieee_denormals_code, args: '-lm', name : 'IEEE denormalized arithmetic')
if ieee_denormals_check.returncode() == 0
    conf.set('HAVE_IEEE_DENORMALS', 1)
endif

have_c99inline_code = '''extern inline void* foo() { foo(); return &foo ; };
int main () {
  return foo() != 0;
  return 0;
}'''
if cc.compiles(have_c99inline_code, name : 'C99-style inline')
    conf.set('HAVE_C99_INLINE', 1)
endif

fpu_sse_code = '''#include <stdlib.h>
#define _FPU_SETMXCSR(cw_sse) asm volatile ("ldmxcsr %0" : : "m" (*&cw_sse))
int main () {
  unsigned int mode = 0x1f80; _FPU_SETMXCSR(mode); exit(0); return 0;
}
'''
if cc.compiles(fpu_sse_code, name : 'x86 FPU SSE extensions')
    conf.set('HAVE_FPU_X86_SSE', 1)
endif

printf_longdouble_check = '''#include <stdlib.h>
#include <stdio.h>
int main () {
    const char * s = "5678.25"; long double x = 1.234 ;
    fprintf(stderr,"%Lg\n",x) ;
    sscanf(s, "%Lg", &x);
    if (x == 5678.25) {exit (0);} else {exit(1); }
}
'''
printf_longdouble_run = cc.run(printf_longdouble_check, name : 'long double printf')
if printf_longdouble_run.returncode() == 0
    conf.set('HAVE_PRINTF_LONGDOUBLE', 1)
endif

check_math_functions = ['acosh', 'asinh', 'atanh', 'hypot', 'ldexp', 'frexp', 'expm1', 'isinf', 'isfinite', 'finite', 'isnan', 'log1p']
foreach function_name : check_math_functions
    if cc.has_function(function_name, prefix : '#include <math.h>', dependencies : libm)
        conf.set('HAVE_DECL_' + function_name.to_upper(), 1)
    endif
endforeach

check_string_functions = ['memmove', 'memcpy', 'vprintf', 'strdup', 'strtol', 'strtoul']
foreach function_name : check_string_functions
    if cc.has_function(function_name)
        conf.set('HAVE_' + function_name.to_upper(), 1)
    endif
endforeach

check_fenv_functions = ['feenableexcept', 'fesettrapenable']
foreach function_name : check_fenv_functions
    if cc.has_function(function_name, prefix : '#define _GNU_SOURCE 1\n#include <fenv.h>', dependencies : libm)
        conf.set('HAVE_DECL_' + function_name.to_upper(), 1)
    endif
endforeach


check_headers = ['ieeefp.h', 'inttypes.h', 'dlfcn.h', 'string.h', 'strings.h', 'stdint.h', 'stdlib.h', 'memory.h', 'sys/stat.h', 'sys/types.h', 'unistd.h']
foreach header_name : check_headers
    if cc.has_header(header_name)
        conf.set('HAVE_' + header_name.underscorify().to_upper(), 1)
    endif
endforeach

if cc.has_type('fprnd_t', prefix : '#include <float.h>')
    conf.set('HAVE_DECL_FPRND_T', 1)
endif

configure_file(input: 'config-meson.h.in', output: 'config.h', configuration: conf)

gsl_include_dir = include_directories('include')

gsl_modules = [
    {'name': 'splinalg', 'dir': 'splinalg', 'sources': ['itersolve.c', 'gmres.c']},
    {'name': 'spmatrix', 'dir': 'spmatrix', 'sources': ['spcompress.c', 'spcopy.c', 'spgetset.c', 'spmatrix.c', 'spoper.c', 'spprop.c', 'spswap.c']},
    {'name': 'spblas', 'dir': 'spblas', 'sources': ['spdgemm.c', 'spdgemv.c']},
    {'name': 'bspline', 'dir': 'bspline', 'sources': ['bspline.c', 'greville.c']},
    {'name': 'wavelet', 'dir': 'wavelet', 'sources': ['dwt.c', 'wavelet.c', 'bspline.c', 'daubechies.c', 'haar.c']},
    {'name': 'cdf', 'dir': 'cdf', 'sources': ['beta.c', 'betainv.c', 'cauchy.c', 'cauchyinv.c', 'chisq.c', 'chisqinv.c', 'exponential.c', 'exponentialinv.c', 'exppow.c', 'fdist.c', 'fdistinv.c', 'flat.c', 'flatinv.c', 'gamma.c', 'gammainv.c', 'gauss.c', 'gaussinv.c', 'gumbel1.c', 'gumbel1inv.c', 'gumbel2.c', 'gumbel2inv.c', 'laplace.c', 'laplaceinv.c', 'logistic.c', 'logisticinv.c', 'lognormal.c', 'lognormalinv.c', 'pareto.c', 'paretoinv.c', 'rayleigh.c', 'rayleighinv.c', 'tdist.c', 'tdistinv.c', 'weibull.c', 'weibullinv.c', 'binomial.c', 'poisson.c', 'geometric.c', 'nbinomial.c', 'pascal.c', 'hypergeometric.c']},
    {'name': 'deriv', 'dir': 'deriv', 'sources': ['deriv.c']},
    {'name': 'diff', 'dir': 'diff', 'sources': ['diff.c']},
    {'name': 'ntuple', 'dir': 'ntuple', 'sources': ['ntuple.c']},
    {'name': 'monte', 'dir': 'monte', 'sources': ['miser.c', 'plain.c', 'vegas.c']},
    {'name': 'multimin', 'dir': 'multimin', 'sources': ['fdfminimizer.c', 'steepest_descent.c', 'conjugate_fr.c', 'conjugate_pr.c', 'convergence.c', 'diff.c', 'vector_bfgs.c', 'vector_bfgs2.c', 'fminimizer.c', 'simplex.c', 'simplex2.c']},
    {'name': 'min', 'dir': 'min', 'sources': ['fsolver.c', 'golden.c', 'brent.c', 'convergence.c', 'bracketing.c', 'quad_golden.c']},
    {'name': 'multiroots', 'dir': 'multiroots', 'sources': ['fdjac.c', 'fsolver.c', 'fdfsolver.c', 'convergence.c', 'newton.c', 'gnewton.c', 'dnewton.c', 'broyden.c', 'hybrid.c', 'hybridj.c']},
    {'name': 'roots', 'dir': 'roots', 'sources': ['bisection.c', 'brent.c', 'falsepos.c', 'newton.c', 'secant.c', 'steffenson.c', 'convergence.c', 'fsolver.c', 'fdfsolver.c']},
    {'name': 'odeiv2', 'dir': 'ode-initval2', 'sources': ['control.c', 'cstd.c', 'cscal.c', 'evolve.c', 'step.c', 'rk2.c', 'rk2imp.c', 'rk4.c', 'rk4imp.c', 'rkf45.c', 'rk8pd.c', 'rkck.c', 'bsimp.c', 'rk1imp.c', 'msadams.c', 'msbdf.c', 'driver.c']},
    {'name': 'odeiv', 'dir': 'ode-initval', 'sources': ['control.c', 'cstd.c', 'cscal.c', 'evolve.c', 'step.c', 'rk2.c', 'rk2imp.c', 'rk2simp.c', 'rk4.c', 'rk4imp.c', 'rkf45.c', 'rk8pd.c', 'rkck.c', 'bsimp.c', 'gear1.c', 'gear2.c']},
    {'name': 'histogram', 'dir': 'histogram', 'sources': ['add.c', 'get.c', 'init.c', 'params.c', 'reset.c', 'file.c', 'pdf.c', 'add2d.c', 'get2d.c', 'init2d.c', 'params2d.c', 'reset2d.c', 'file2d.c', 'pdf2d.c', 'calloc_range.c', 'calloc_range2d.c', 'copy.c', 'copy2d.c', 'maxval.c', 'maxval2d.c', 'oper.c', 'oper2d.c', 'stat.c', 'stat2d.c']},
    {'name': 'interpolation', 'dir': 'interpolation', 'sources': ['accel.c', 'akima.c', 'cspline.c', 'interp.c', 'linear.c', 'spline.c', 'poly.c', 'steffen.c', 'inline.c', 'interp2d.c', 'bilinear.c', 'bicubic.c', 'spline2d.c']},
    {'name': 'integration', 'dir': 'integration', 'sources': ['qk15.c', 'qk21.c', 'qk31.c', 'qk41.c', 'qk51.c', 'qk61.c', 'qk.c', 'qng.c', 'qag.c', 'qags.c', 'qagp.c', 'workspace.c', 'qcheb.c', 'qawc.c', 'qmomo.c', 'qaws.c', 'qmomof.c', 'qawo.c', 'qawf.c', 'glfixed.c', 'cquad.c']},
    {'name': 'sum', 'dir': 'sum', 'sources': ['levin_u.c', 'levin_utrunc.c', 'work_u.c', 'work_utrunc.c']},
    {'name': 'siman', 'dir': 'siman', 'sources': ['siman.c']},
    {'name': 'statistics', 'dir': 'statistics', 'sources': ['mean.c', 'variance.c', 'absdev.c', 'skew.c', 'kurtosis.c', 'lag1.c', 'p_variance.c', 'minmax.c', 'ttest.c', 'median.c', 'covariance.c', 'quantiles.c', 'wmean.c', 'wvariance.c', 'wabsdev.c', 'wskew.c', 'wkurtosis.c']},
    {'name': 'rstat', 'dir': 'rstat', 'sources': ['rstat.c', 'rquantile.c']},
    {'name': 'multilarge', 'dir': 'multilarge', 'sources': ['multilarge.c', 'normal.c', 'tsqr.c']},
    {'name': 'multifit', 'dir': 'multifit', 'sources': ['multilinear.c', 'multiwlinear.c', 'work.c', 'lmniel.c', 'lmder.c', 'fsolver.c', 'fdfsolver.c', 'fdfridge.c', 'fdjac.c', 'convergence.c', 'gradient.c', 'covar.c', 'multirobust.c', 'robust_wfun.c', 'multireg.c']},
    {'name': 'fit', 'dir': 'fit', 'sources': ['linear.c']},
    {'name': 'poly', 'dir': 'poly', 'sources': ['dd.c', 'eval.c', 'solve_quadratic.c', 'solve_cubic.c', 'zsolve_quadratic.c', 'zsolve_cubic.c', 'zsolve.c', 'zsolve_init.c', 'deriv.c']},
    {'name': 'fft', 'dir': 'fft', 'sources': ['dft.c', 'fft.c']},
    {'name': 'randist', 'dir': 'randist', 'sources': ['bernoulli.c', 'beta.c', 'bigauss.c', 'binomial.c', 'cauchy.c', 'chisq.c', 'dirichlet.c', 'discrete.c', 'erlang.c', 'exponential.c', 'exppow.c', 'fdist.c', 'flat.c', 'gamma.c', 'gauss.c', 'gausszig.c', 'gausstail.c', 'geometric.c', 'gumbel.c', 'hyperg.c', 'laplace.c', 'levy.c', 'logarithmic.c', 'logistic.c', 'lognormal.c', 'multinomial.c', 'nbinomial.c', 'pareto.c', 'pascal.c', 'poisson.c', 'rayleigh.c', 'shuffle.c', 'sphere.c', 'tdist.c', 'weibull.c', 'landau.c', 'binomial_tpe.c']},
    {'name': 'rng', 'dir': 'rng', 'sources': ['borosh13.c', 'cmrg.c', 'coveyou.c', 'default.c', 'file.c', 'fishman18.c', 'fishman20.c', 'fishman2x.c', 'gfsr4.c', 'knuthran2.c', 'knuthran.c', 'knuthran2002.c', 'lecuyer21.c', 'minstd.c', 'mrg.c', 'mt.c', 'r250.c', 'ran0.c', 'ran1.c', 'ran2.c', 'ran3.c', 'rand48.c', 'rand.c', 'random.c', 'randu.c', 'ranf.c', 'ranlux.c', 'ranlxd.c', 'ranlxs.c', 'ranmar.c', 'rng.c', 'slatec.c', 'taus.c', 'taus113.c', 'transputer.c', 'tt.c', 'types.c', 'uni32.c', 'uni.c', 'vax.c', 'waterman14.c', 'zuf.c', 'inline.c']},
    {'name': 'qrng', 'dir': 'qrng', 'sources': ['qrng.c', 'niederreiter-2.c', 'sobol.c', 'halton.c', 'reversehalton.c', 'inline.c']},
    {'name': 'dht', 'dir': 'dht', 'sources': ['dht.c']},
    {'name': 'specfunc', 'dir': 'specfunc', 'sources': ['airy.c', 'airy_der.c', 'airy_zero.c', 'atanint.c', 'bessel.c', 'bessel_I0.c', 'bessel_I1.c', 'bessel_In.c', 'bessel_Inu.c', 'bessel_J0.c', 'bessel_J1.c', 'bessel_Jn.c', 'bessel_Jnu.c', 'bessel_K0.c', 'bessel_K1.c', 'bessel_Kn.c', 'bessel_Knu.c', 'bessel_Y0.c', 'bessel_Y1.c', 'bessel_Yn.c', 'bessel_Ynu.c', 'bessel_amp_phase.c', 'bessel_i.c', 'bessel_j.c', 'bessel_k.c', 'bessel_olver.c', 'bessel_temme.c', 'bessel_y.c', 'bessel_zero.c', 'bessel_sequence.c', 'beta.c', 'beta_inc.c', 'clausen.c', 'coulomb.c', 'coupling.c', 'coulomb_bound.c', 'dawson.c', 'debye.c', 'dilog.c', 'elementary.c', 'ellint.c', 'elljac.c', 'erfc.c', 'exp.c', 'expint.c', 'expint3.c', 'fermi_dirac.c', 'gegenbauer.c', 'gamma.c', 'gamma_inc.c', 'hyperg_0F1.c', 'hyperg_2F0.c', 'hyperg_1F1.c', 'hyperg_2F1.c', 'hyperg_U.c', 'hyperg.c', 'laguerre.c', 'lambert.c', 'legendre_H3d.c', 'legendre_P.c', 'legendre_Qn.c', 'legendre_con.c', 'legendre_poly.c', 'log.c', 'mathieu_angfunc.c', 'mathieu_charv.c', 'mathieu_coeff.c', 'mathieu_radfunc.c', 'mathieu_workspace.c', 'poch.c', 'pow_int.c', 'psi.c', 'result.c', 'shint.c', 'sinint.c', 'synchrotron.c', 'transport.c', 'trig.c', 'zeta.c']},
    {'name': 'eigen', 'dir': 'eigen', 'sources': ['jacobi.c', 'symm.c', 'symmv.c', 'nonsymm.c', 'nonsymmv.c', 'herm.c', 'hermv.c', 'gensymm.c', 'gensymmv.c', 'genherm.c', 'genhermv.c', 'gen.c', 'genv.c', 'sort.c', 'francis.c', 'schur.c']},
    {'name': 'linalg', 'dir': 'linalg', 'sources': ['multiply.c', 'exponential.c', 'tridiag.c', 'lu.c', 'luc.c', 'hh.c', 'qr.c', 'qrpt.c', 'lq.c', 'ptlq.c', 'svd.c', 'householder.c', 'householdercomplex.c', 'hessenberg.c', 'hesstri.c', 'cholesky.c', 'choleskyc.c', 'symmtd.c', 'hermtd.c', 'bidiag.c', 'balance.c', 'balancemat.c', 'inline.c']},
    {'name': 'blas', 'dir': 'blas', 'sources': ['blas.c']},
    {'name': 'ieeeutils', 'dir': 'ieee-utils', 'sources': ['print.c', 'make_rep.c', 'env.c', 'fp.c', 'read.c']},
    {'name': 'sort', 'dir': 'sort', 'sources': ['sort.c', 'sortind.c', 'sortvec.c', 'sortvecind.c', 'subset.c', 'subsetind.c']},
    {'name': 'multiset', 'dir': 'multiset', 'sources': ['init.c', 'file.c', 'multiset.c', 'inline.c']},
    {'name': 'combination', 'dir': 'combination', 'sources': ['init.c', 'file.c', 'combination.c', 'inline.c']},
    {'name': 'permutation', 'dir': 'permutation', 'sources': ['init.c', 'file.c', 'permutation.c', 'permute.c', 'canonical.c', 'inline.c']},
    {'name': 'matrix', 'dir': 'matrix', 'sources': ['init.c', 'matrix.c', 'file.c', 'rowcol.c', 'swap.c', 'copy.c', 'minmax.c', 'prop.c', 'oper.c', 'getset.c', 'view.c', 'submatrix.c']},
    {'name': 'vector', 'dir': 'vector', 'sources': ['init.c', 'file.c', 'vector.c', 'copy.c', 'swap.c', 'prop.c', 'minmax.c', 'oper.c', 'reim.c', 'subvector.c', 'view.c']},
    {'name': 'block', 'dir': 'block', 'sources': ['init.c', 'file.c', 'block.c']},
    {'name': 'cheb', 'dir': 'cheb', 'sources': ['deriv.c', 'eval.c', 'init.c', 'integ.c']},
    {'name': 'complex', 'dir': 'complex', 'sources': ['math.c', 'inline.c']},
    {'name': 'err', 'dir': 'err', 'sources': ['error.c', 'stream.c', 'message.c', 'strerror.c']},
    {'name': 'resulttest', 'dir': 'test', 'sources': ['results.c']},
    {'name': 'sys', 'dir': 'sys', 'sources': ['minmax.c', 'prec.c', 'hypot.c', 'log1p.c', 'expm1.c', 'coerce.c', 'invhyp.c', 'pow_int.c', 'infnan.c', 'fdiv.c', 'fcmp.c', 'ldfrexp.c']},
]

cblas_sources = ['sasum.c', 'saxpy.c', 'scasum.c', 'scnrm2.c', 'scopy.c', 'sdot.c', 'sdsdot.c', 'sgbmv.c', 'sgemm.c', 'sgemv.c', 'sger.c', 'snrm2.c', 'srot.c', 'srotg.c', 'srotm.c', 'srotmg.c', 'ssbmv.c', 'sscal.c', 'sspmv.c', 'sspr.c', 'sspr2.c', 'sswap.c', 'ssymm.c', 'ssymv.c', 'ssyr.c', 'ssyr2.c', 'ssyr2k.c', 'ssyrk.c', 'stbmv.c', 'stbsv.c', 'stpmv.c', 'stpsv.c', 'strmm.c', 'strmv.c', 'strsm.c', 'strsv.c', 'dasum.c', 'daxpy.c', 'dcopy.c', 'ddot.c', 'dgbmv.c', 'dgemm.c', 'dgemv.c', 'dger.c', 'dnrm2.c', 'drot.c', 'drotg.c', 'drotm.c', 'drotmg.c', 'dsbmv.c', 'dscal.c', 'dsdot.c', 'dspmv.c', 'dspr.c', 'dspr2.c', 'dswap.c', 'dsymm.c', 'dsymv.c', 'dsyr.c', 'dsyr2.c', 'dsyr2k.c', 'dsyrk.c', 'dtbmv.c', 'dtbsv.c', 'dtpmv.c', 'dtpsv.c', 'dtrmm.c', 'dtrmv.c', 'dtrsm.c', 'dtrsv.c', 'dzasum.c', 'dznrm2.c', 'caxpy.c', 'ccopy.c', 'cdotc_sub.c', 'cdotu_sub.c', 'cgbmv.c', 'cgemm.c', 'cgemv.c', 'cgerc.c', 'cgeru.c', 'chbmv.c', 'chemm.c', 'chemv.c', 'cher.c', 'cher2.c', 'cher2k.c', 'cherk.c', 'chpmv.c', 'chpr.c', 'chpr2.c', 'cscal.c', 'csscal.c', 'cswap.c', 'csymm.c', 'csyr2k.c', 'csyrk.c', 'ctbmv.c', 'ctbsv.c', 'ctpmv.c', 'ctpsv.c', 'ctrmm.c', 'ctrmv.c', 'ctrsm.c', 'ctrsv.c', 'zaxpy.c', 'zcopy.c', 'zdotc_sub.c', 'zdotu_sub.c', 'zdscal.c', 'zgbmv.c', 'zgemm.c', 'zgemv.c', 'zgerc.c', 'zgeru.c', 'zhbmv.c', 'zhemm.c', 'zhemv.c', 'zher.c', 'zher2.c', 'zher2k.c', 'zherk.c', 'zhpmv.c', 'zhpr.c', 'zhpr2.c', 'zscal.c', 'zswap.c', 'zsymm.c', 'zsyr2k.c', 'zsyrk.c', 'ztbmv.c', 'ztbsv.c', 'ztpmv.c', 'ztpsv.c', 'ztrmm.c', 'ztrmv.c', 'ztrsm.c', 'ztrsv.c', 'icamax.c', 'idamax.c', 'isamax.c', 'izamax.c', 'xerbla.c']

gsl_module_libs = []
foreach module : gsl_modules
    dir_sources = []
    foreach filename : module['sources']
        dir_sources += module['dir'] / filename
    endforeach
    module_include_dir = include_directories(module['dir'])
    gsl_module_libs += static_library('gsl' + module['name'],
        dir_sources,
        include_directories: [gsl_include_dir, module_include_dir],
        c_args : gsl_defines,
        dependencies: libm
    )
endforeach

cblas_libname = get_option('blas')
if cblas_libname == 'internal'
    dir_sources = []
    foreach filename : cblas_sources
        dir_sources += 'cblas' / filename
    endforeach
    module_include_dir = include_directories('cblas')
    cblas_lib = static_library('gslcblas',
        dir_sources,
        include_directories: [gsl_include_dir, module_include_dir],
        c_args : gsl_defines,
        dependencies: libm,
        install: true
    )
    cblas_dep = declare_dependency(link_with : cblas_lib)
else
    cblas_dep = dependency(cblas_libname)
endif

install_subdir('include/gsl', install_dir : 'include')

# we may need to add the placeholder source file here
libgsl = library('gsl', [],
    include_directories: gsl_include_dir,
    c_args: gsl_defines,
    link_whole: gsl_module_libs,
    dependencies: [cblas_dep, libm],
    install: true
)

pkg.generate(libgsl,
    filebase : 'gsl',
    name : 'gsl',
    version : meson.project_version(),
    description : 'GNU Scientific Library',
)

libgsl_dep = declare_dependency(
    dependencies : libm,
    include_directories : gsl_include_dir,
    link_with : libgsl,
)
