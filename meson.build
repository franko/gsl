project('gsl', 'c',  version: '2.1', default_options : 'c_std=c99')

cc = meson.get_compiler('c')
libm = cc.find_library('m', required : false)

gsl_defines = ['-DHAVE_CONFIG_H']

conf = configuration_data()
conf.set('GSL_DISABLE_DEPRECATED', 1)
conf.set('HAVE_INLINE', 1)

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

exit_success_code='''#include <stdlib.h>
int main() {
    return EXIT_SUCCESS;
}
'''
if cc.compiles(exit_success_code, name : 'EXIT_SUCCESS is defined')
    conf.set('HAVE_EXIT_SUCCESS_AND_FAILURE', 1)
endif

configure_file(input: 'config-meson.h.in', output: 'config.h', configuration: conf)

gsl_include_dir = include_directories('.')

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
    {'name': 'cblas', 'dir': 'cblas', 'sources': ['sasum.c', 'saxpy.c', 'scasum.c', 'scnrm2.c', 'scopy.c', 'sdot.c', 'sdsdot.c', 'sgbmv.c', 'sgemm.c', 'sgemv.c', 'sger.c', 'snrm2.c', 'srot.c', 'srotg.c', 'srotm.c', 'srotmg.c', 'ssbmv.c', 'sscal.c', 'sspmv.c', 'sspr.c', 'sspr2.c', 'sswap.c', 'ssymm.c', 'ssymv.c', 'ssyr.c', 'ssyr2.c', 'ssyr2k.c', 'ssyrk.c', 'stbmv.c', 'stbsv.c', 'stpmv.c', 'stpsv.c', 'strmm.c', 'strmv.c', 'strsm.c', 'strsv.c', 'dasum.c', 'daxpy.c', 'dcopy.c', 'ddot.c', 'dgbmv.c', 'dgemm.c', 'dgemv.c', 'dger.c', 'dnrm2.c', 'drot.c', 'drotg.c', 'drotm.c', 'drotmg.c', 'dsbmv.c', 'dscal.c', 'dsdot.c', 'dspmv.c', 'dspr.c', 'dspr2.c', 'dswap.c', 'dsymm.c', 'dsymv.c', 'dsyr.c', 'dsyr2.c', 'dsyr2k.c', 'dsyrk.c', 'dtbmv.c', 'dtbsv.c', 'dtpmv.c', 'dtpsv.c', 'dtrmm.c', 'dtrmv.c', 'dtrsm.c', 'dtrsv.c', 'dzasum.c', 'dznrm2.c', 'caxpy.c', 'ccopy.c', 'cdotc_sub.c', 'cdotu_sub.c', 'cgbmv.c', 'cgemm.c', 'cgemv.c', 'cgerc.c', 'cgeru.c', 'chbmv.c', 'chemm.c', 'chemv.c', 'cher.c', 'cher2.c', 'cher2k.c', 'cherk.c', 'chpmv.c', 'chpr.c', 'chpr2.c', 'cscal.c', 'csscal.c', 'cswap.c', 'csymm.c', 'csyr2k.c', 'csyrk.c', 'ctbmv.c', 'ctbsv.c', 'ctpmv.c', 'ctpsv.c', 'ctrmm.c', 'ctrmv.c', 'ctrsm.c', 'ctrsv.c', 'zaxpy.c', 'zcopy.c', 'zdotc_sub.c', 'zdotu_sub.c', 'zdscal.c', 'zgbmv.c', 'zgemm.c', 'zgemv.c', 'zgerc.c', 'zgeru.c', 'zhbmv.c', 'zhemm.c', 'zhemv.c', 'zher.c', 'zher2.c', 'zher2k.c', 'zherk.c', 'zhpmv.c', 'zhpr.c', 'zhpr2.c', 'zscal.c', 'zswap.c', 'zsymm.c', 'zsyr2k.c', 'zsyrk.c', 'ztbmv.c', 'ztbsv.c', 'ztpmv.c', 'ztpsv.c', 'ztrmm.c', 'ztrmv.c', 'ztrsm.c', 'ztrsv.c', 'icamax.c', 'idamax.c', 'isamax.c', 'izamax.c', 'xerbla.c']},
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

gsl_headers = [
    'blas/gsl_blas.h',
    'blas/gsl_blas_types.h',
    'block/gsl_block.h',
    'block/gsl_block_char.h',
    'block/gsl_block_complex_double.h',
    'block/gsl_block_complex_float.h',
    'block/gsl_block_complex_long_double.h',
    'block/gsl_block_double.h',
    'block/gsl_block_float.h',
    'block/gsl_block_int.h',
    'block/gsl_block_long.h',
    'block/gsl_block_long_double.h',
    'block/gsl_block_short.h',
    'block/gsl_block_uchar.h',
    'block/gsl_block_uint.h',
    'block/gsl_block_ulong.h',
    'block/gsl_block_ushort.h',
    'block/gsl_check_range.h',
    'bspline/gsl_bspline.h',
    'cblas/gsl_cblas.h',
    'cdf/gsl_cdf.h',
    'cheb/gsl_chebyshev.h',
    'combination/gsl_combination.h',
    'complex/gsl_complex.h',
    'complex/gsl_complex_math.h',
    'const/gsl_const.h',
    'const/gsl_const_cgs.h',
    'const/gsl_const_cgsm.h',
    'const/gsl_const_mks.h',
    'const/gsl_const_mksa.h',
    'const/gsl_const_num.h',
    'deriv/gsl_deriv.h',
    'dht/gsl_dht.h',
    'diff/gsl_diff.h',
    'eigen/gsl_eigen.h',
    'err/gsl_errno.h',
    'err/gsl_message.h',
    'fft/gsl_dft_complex.h',
    'fft/gsl_dft_complex_float.h',
    'fft/gsl_fft.h',
    'fft/gsl_fft_complex.h',
    'fft/gsl_fft_complex_float.h',
    'fft/gsl_fft_halfcomplex.h',
    'fft/gsl_fft_halfcomplex_float.h',
    'fft/gsl_fft_real.h',
    'fft/gsl_fft_real_float.h',
    'fit/gsl_fit.h',
    'histogram/gsl_histogram.h',
    'histogram/gsl_histogram2d.h',
    'ieee-utils/gsl_ieee_utils.h',
    'integration/gsl_integration.h',
    'interpolation/gsl_interp.h',
    'interpolation/gsl_interp2d.h',
    'interpolation/gsl_spline.h',
    'interpolation/gsl_spline2d.h',
    'linalg/gsl_linalg.h',
    'matrix/gsl_matrix.h',
    'matrix/gsl_matrix_char.h',
    'matrix/gsl_matrix_complex_double.h',
    'matrix/gsl_matrix_complex_float.h',
    'matrix/gsl_matrix_complex_long_double.h',
    'matrix/gsl_matrix_double.h',
    'matrix/gsl_matrix_float.h',
    'matrix/gsl_matrix_int.h',
    'matrix/gsl_matrix_long.h',
    'matrix/gsl_matrix_long_double.h',
    'matrix/gsl_matrix_short.h',
    'matrix/gsl_matrix_uchar.h',
    'matrix/gsl_matrix_uint.h',
    'matrix/gsl_matrix_ulong.h',
    'matrix/gsl_matrix_ushort.h',
    'min/gsl_min.h',
    'monte/gsl_monte.h',
    'monte/gsl_monte_miser.h',
    'monte/gsl_monte_plain.h',
    'monte/gsl_monte_vegas.h',
    'multifit/gsl_multifit.h',
    'multifit/gsl_multifit_nlin.h',
    'multilarge/gsl_multilarge.h',
    'multimin/gsl_multimin.h',
    'multiroots/gsl_multiroots.h',
    'multiset/gsl_multiset.h',
    'ntuple/gsl_ntuple.h',
    'ode-initval/gsl_odeiv.h',
    'ode-initval2/gsl_odeiv2.h',
    'permutation/gsl_permutation.h',
    'permutation/gsl_permute.h',
    'permutation/gsl_permute_char.h',
    'permutation/gsl_permute_complex_double.h',
    'permutation/gsl_permute_complex_float.h',
    'permutation/gsl_permute_complex_long_double.h',
    'permutation/gsl_permute_double.h',
    'permutation/gsl_permute_float.h',
    'permutation/gsl_permute_int.h',
    'permutation/gsl_permute_long.h',
    'permutation/gsl_permute_long_double.h',
    'permutation/gsl_permute_short.h',
    'permutation/gsl_permute_uchar.h',
    'permutation/gsl_permute_uint.h',
    'permutation/gsl_permute_ulong.h',
    'permutation/gsl_permute_ushort.h',
    'permutation/gsl_permute_vector.h',
    'permutation/gsl_permute_vector_char.h',
    'permutation/gsl_permute_vector_complex_double.h',
    'permutation/gsl_permute_vector_complex_float.h',
    'permutation/gsl_permute_vector_complex_long_double.h',
    'permutation/gsl_permute_vector_double.h',
    'permutation/gsl_permute_vector_float.h',
    'permutation/gsl_permute_vector_int.h',
    'permutation/gsl_permute_vector_long.h',
    'permutation/gsl_permute_vector_long_double.h',
    'permutation/gsl_permute_vector_short.h',
    'permutation/gsl_permute_vector_uchar.h',
    'permutation/gsl_permute_vector_uint.h',
    'permutation/gsl_permute_vector_ulong.h',
    'permutation/gsl_permute_vector_ushort.h',
    'poly/gsl_poly.h',
    'qrng/gsl_qrng.h',
    'randist/gsl_randist.h',
    'rng/gsl_rng.h',
    'roots/gsl_roots.h',
    'rstat/gsl_rstat.h',
    'siman/gsl_siman.h',
    'sort/gsl_heapsort.h',
    'sort/gsl_sort.h',
    'sort/gsl_sort_char.h',
    'sort/gsl_sort_double.h',
    'sort/gsl_sort_float.h',
    'sort/gsl_sort_int.h',
    'sort/gsl_sort_long.h',
    'sort/gsl_sort_long_double.h',
    'sort/gsl_sort_short.h',
    'sort/gsl_sort_uchar.h',
    'sort/gsl_sort_uint.h',
    'sort/gsl_sort_ulong.h',
    'sort/gsl_sort_ushort.h',
    'sort/gsl_sort_vector.h',
    'sort/gsl_sort_vector_char.h',
    'sort/gsl_sort_vector_double.h',
    'sort/gsl_sort_vector_float.h',
    'sort/gsl_sort_vector_int.h',
    'sort/gsl_sort_vector_long.h',
    'sort/gsl_sort_vector_long_double.h',
    'sort/gsl_sort_vector_short.h',
    'sort/gsl_sort_vector_uchar.h',
    'sort/gsl_sort_vector_uint.h',
    'sort/gsl_sort_vector_ulong.h',
    'sort/gsl_sort_vector_ushort.h',
    'spblas/gsl_spblas.h',
    'specfunc/gsl_sf.h',
    'specfunc/gsl_sf_airy.h',
    'specfunc/gsl_sf_bessel.h',
    'specfunc/gsl_sf_clausen.h',
    'specfunc/gsl_sf_coulomb.h',
    'specfunc/gsl_sf_coupling.h',
    'specfunc/gsl_sf_dawson.h',
    'specfunc/gsl_sf_debye.h',
    'specfunc/gsl_sf_dilog.h',
    'specfunc/gsl_sf_elementary.h',
    'specfunc/gsl_sf_ellint.h',
    'specfunc/gsl_sf_elljac.h',
    'specfunc/gsl_sf_erf.h',
    'specfunc/gsl_sf_exp.h',
    'specfunc/gsl_sf_expint.h',
    'specfunc/gsl_sf_fermi_dirac.h',
    'specfunc/gsl_sf_gamma.h',
    'specfunc/gsl_sf_gegenbauer.h',
    'specfunc/gsl_sf_hyperg.h',
    'specfunc/gsl_sf_laguerre.h',
    'specfunc/gsl_sf_lambert.h',
    'specfunc/gsl_sf_legendre.h',
    'specfunc/gsl_sf_log.h',
    'specfunc/gsl_sf_mathieu.h',
    'specfunc/gsl_sf_pow_int.h',
    'specfunc/gsl_sf_psi.h',
    'specfunc/gsl_sf_result.h',
    'specfunc/gsl_sf_synchrotron.h',
    'specfunc/gsl_sf_transport.h',
    'specfunc/gsl_sf_trig.h',
    'specfunc/gsl_sf_zeta.h',
    'specfunc/gsl_specfunc.h',
    'splinalg/gsl_splinalg.h',
    'spmatrix/gsl_spmatrix.h',
    'statistics/gsl_statistics.h',
    'statistics/gsl_statistics_char.h',
    'statistics/gsl_statistics_double.h',
    'statistics/gsl_statistics_float.h',
    'statistics/gsl_statistics_int.h',
    'statistics/gsl_statistics_long.h',
    'statistics/gsl_statistics_long_double.h',
    'statistics/gsl_statistics_short.h',
    'statistics/gsl_statistics_uchar.h',
    'statistics/gsl_statistics_uint.h',
    'statistics/gsl_statistics_ulong.h',
    'statistics/gsl_statistics_ushort.h',
    'sum/gsl_sum.h',
    'sys/gsl_sys.h',
    'test/gsl_test.h',
    'vector/gsl_vector.h',
    'vector/gsl_vector_char.h',
    'vector/gsl_vector_complex.h',
    'vector/gsl_vector_complex_double.h',
    'vector/gsl_vector_complex_float.h',
    'vector/gsl_vector_complex_long_double.h',
    'vector/gsl_vector_double.h',
    'vector/gsl_vector_float.h',
    'vector/gsl_vector_int.h',
    'vector/gsl_vector_long.h',
    'vector/gsl_vector_long_double.h',
    'vector/gsl_vector_short.h',
    'vector/gsl_vector_uchar.h',
    'vector/gsl_vector_uint.h',
    'vector/gsl_vector_ulong.h',
    'vector/gsl_vector_ushort.h',
    'wavelet/gsl_wavelet.h',
    'wavelet/gsl_wavelet2d.h',
]

copy = find_program('copy.py')

gsl_headers_dep = []
foreach filename : gsl_headers
    # configure_file(input : filename, output : '@PLAINNAME@', install_dir : 'include/gsl', copy : true)
    filename_bits = filename.split('/')
    plainname = filename_bits[filename_bits.length() - 1]
    run_command(copy, filename, 'gsl' / plainname)
    # filename_bits = filename.split('/')
    # gsl_headers_dep += custom_target(filename_bits[filename_bits.length() - 1],
    #    input : filename,
    #    output : '@PLAINNAME@',
    #    install_dir : 'include',
    #    command : ['cp', '@INPUT@', '@PLAINNAME@']) # 'gsl' / '@PLAINNAME@'])
endforeach

gsl_module_libs = []
foreach module : gsl_modules
    dir_sources = []
    foreach filename : module['sources']
        dir_sources += module['dir'] / filename
    endforeach
    module_include_dir = include_directories(module['dir'])
    gsl_module_libs += static_library(module['name'],
        dir_sources + gsl_headers_dep,
        include_directories: gsl_include_dir,
        c_args : gsl_defines,
        dependencies: libm
    )
endforeach

# we may need to add the placeholder source file here
gsl_lib = library('gsl', [],
    include_directories: gsl_include_dir,
    c_args: gsl_defines,
    link_whole: gsl_module_libs,
    dependencies: libm
)

