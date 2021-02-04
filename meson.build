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
    {'name': 'splinalg', 'sources': ['splinalg/itersolve.c', 'splinalg/gmres.c']},
    {'name': 'spmatrix', 'sources': ['spmatrix/spcompress.c', 'spmatrix/spcopy.c', 'spmatrix/spgetset.c', 'spmatrix/spmatrix.c', 'spmatrix/spoper.c', 'spmatrix/spprop.c', 'spmatrix/spswap.c']},
    {'name': 'spblas', 'sources': ['spblas/spdgemm.c', 'spblas/spdgemv.c']},
    {'name': 'bspline', 'sources': ['bspline/bspline.c', 'bspline/greville.c']},
    {'name': 'wavelet', 'sources': ['wavelet/dwt.c', 'wavelet/wavelet.c', 'wavelet/bspline.c', 'wavelet/daubechies.c', 'wavelet/haar.c']},
    {'name': 'cdf', 'sources': ['cdf/beta.c', 'cdf/betainv.c', 'cdf/cauchy.c', 'cdf/cauchyinv.c', 'cdf/chisq.c', 'cdf/chisqinv.c', 'cdf/exponential.c', 'cdf/exponentialinv.c', 'cdf/exppow.c', 'cdf/fdist.c', 'cdf/fdistinv.c', 'cdf/flat.c', 'cdf/flatinv.c', 'cdf/gamma.c', 'cdf/gammainv.c', 'cdf/gauss.c', 'cdf/gaussinv.c', 'cdf/gumbel1.c', 'cdf/gumbel1inv.c', 'cdf/gumbel2.c', 'cdf/gumbel2inv.c', 'cdf/laplace.c', 'cdf/laplaceinv.c', 'cdf/logistic.c', 'cdf/logisticinv.c', 'cdf/lognormal.c', 'cdf/lognormalinv.c', 'cdf/pareto.c', 'cdf/paretoinv.c', 'cdf/rayleigh.c', 'cdf/rayleighinv.c', 'cdf/tdist.c', 'cdf/tdistinv.c', 'cdf/weibull.c', 'cdf/weibullinv.c', 'cdf/binomial.c', 'cdf/poisson.c', 'cdf/geometric.c', 'cdf/nbinomial.c', 'cdf/pascal.c', 'cdf/hypergeometric.c']},
    {'name': 'deriv', 'sources': ['deriv/deriv.c']},
    {'name': 'diff', 'sources': ['diff/diff.c']},
    {'name': 'ntuple', 'sources': ['ntuple/ntuple.c']},
    {'name': 'monte', 'sources': ['monte/miser.c', 'monte/plain.c', 'monte/vegas.c']},
    {'name': 'multimin', 'sources': ['multimin/fdfminimizer.c', 'multimin/steepest_descent.c', 'multimin/conjugate_fr.c', 'multimin/conjugate_pr.c', 'multimin/convergence.c', 'multimin/diff.c', 'multimin/vector_bfgs.c', 'multimin/vector_bfgs2.c', 'multimin/fminimizer.c', 'multimin/simplex.c', 'multimin/simplex2.c']},
    {'name': 'min', 'sources': ['min/fsolver.c', 'min/golden.c', 'min/brent.c', 'min/convergence.c', 'min/bracketing.c', 'min/quad_golden.c']},
    {'name': 'multiroots', 'sources': ['multiroots/fdjac.c', 'multiroots/fsolver.c', 'multiroots/fdfsolver.c', 'multiroots/convergence.c', 'multiroots/newton.c', 'multiroots/gnewton.c', 'multiroots/dnewton.c', 'multiroots/broyden.c', 'multiroots/hybrid.c', 'multiroots/hybridj.c']},
    {'name': 'roots', 'sources': ['roots/bisection.c', 'roots/brent.c', 'roots/falsepos.c', 'roots/newton.c', 'roots/secant.c', 'roots/steffenson.c', 'roots/convergence.c', 'roots/fsolver.c', 'roots/fdfsolver.c']},
    {'name': 'odeiv2', 'sources': ['ode-initval2/control.c', 'ode-initval2/cstd.c', 'ode-initval2/cscal.c', 'ode-initval2/evolve.c', 'ode-initval2/step.c', 'ode-initval2/rk2.c', 'ode-initval2/rk2imp.c', 'ode-initval2/rk4.c', 'ode-initval2/rk4imp.c', 'ode-initval2/rkf45.c', 'ode-initval2/rk8pd.c', 'ode-initval2/rkck.c', 'ode-initval2/bsimp.c', 'ode-initval2/rk1imp.c', 'ode-initval2/msadams.c', 'ode-initval2/msbdf.c', 'ode-initval2/driver.c']},
    {'name': 'odeiv', 'sources': ['ode-initval/control.c', 'ode-initval/cstd.c', 'ode-initval/cscal.c', 'ode-initval/evolve.c', 'ode-initval/step.c', 'ode-initval/rk2.c', 'ode-initval/rk2imp.c', 'ode-initval/rk2simp.c', 'ode-initval/rk4.c', 'ode-initval/rk4imp.c', 'ode-initval/rkf45.c', 'ode-initval/rk8pd.c', 'ode-initval/rkck.c', 'ode-initval/bsimp.c', 'ode-initval/gear1.c', 'ode-initval/gear2.c']},
    {'name': 'histogram', 'sources': ['histogram/add.c', 'histogram/get.c', 'histogram/init.c', 'histogram/params.c', 'histogram/reset.c', 'histogram/file.c', 'histogram/pdf.c', 'histogram/add2d.c', 'histogram/get2d.c', 'histogram/init2d.c', 'histogram/params2d.c', 'histogram/reset2d.c', 'histogram/file2d.c', 'histogram/pdf2d.c', 'histogram/calloc_range.c', 'histogram/calloc_range2d.c', 'histogram/copy.c', 'histogram/copy2d.c', 'histogram/maxval.c', 'histogram/maxval2d.c', 'histogram/oper.c', 'histogram/oper2d.c', 'histogram/stat.c', 'histogram/stat2d.c']},
    {'name': 'interpolation', 'sources': ['interpolation/accel.c', 'interpolation/akima.c', 'interpolation/cspline.c', 'interpolation/interp.c', 'interpolation/linear.c', 'interpolation/spline.c', 'interpolation/poly.c', 'interpolation/steffen.c', 'interpolation/inline.c', 'interpolation/interp2d.c', 'interpolation/bilinear.c', 'interpolation/bicubic.c', 'interpolation/spline2d.c']},
    {'name': 'integration', 'sources': ['integration/qk15.c', 'integration/qk21.c', 'integration/qk31.c', 'integration/qk41.c', 'integration/qk51.c', 'integration/qk61.c', 'integration/qk.c', 'integration/qng.c', 'integration/qag.c', 'integration/qags.c', 'integration/qagp.c', 'integration/workspace.c', 'integration/qcheb.c', 'integration/qawc.c', 'integration/qmomo.c', 'integration/qaws.c', 'integration/qmomof.c', 'integration/qawo.c', 'integration/qawf.c', 'integration/glfixed.c', 'integration/cquad.c']},
    {'name': 'sum', 'sources': ['sum/levin_u.c', 'sum/levin_utrunc.c', 'sum/work_u.c', 'sum/work_utrunc.c']},
    {'name': 'siman', 'sources': ['siman/siman.c']},
    {'name': 'statistics', 'sources': ['statistics/mean.c', 'statistics/variance.c', 'statistics/absdev.c', 'statistics/skew.c', 'statistics/kurtosis.c', 'statistics/lag1.c', 'statistics/p_variance.c', 'statistics/minmax.c', 'statistics/ttest.c', 'statistics/median.c', 'statistics/covariance.c', 'statistics/quantiles.c', 'statistics/wmean.c', 'statistics/wvariance.c', 'statistics/wabsdev.c', 'statistics/wskew.c', 'statistics/wkurtosis.c']},
    {'name': 'rstat', 'sources': ['rstat/rstat.c', 'rstat/rquantile.c']},
    {'name': 'multilarge', 'sources': ['multilarge/multilarge.c', 'multilarge/normal.c', 'multilarge/tsqr.c']},
    {'name': 'multifit', 'sources': ['multifit/multilinear.c', 'multifit/multiwlinear.c', 'multifit/work.c', 'multifit/lmniel.c', 'multifit/lmder.c', 'multifit/fsolver.c', 'multifit/fdfsolver.c', 'multifit/fdfridge.c', 'multifit/fdjac.c', 'multifit/convergence.c', 'multifit/gradient.c', 'multifit/covar.c', 'multifit/multirobust.c', 'multifit/robust_wfun.c', 'multifit/multireg.c']},
    {'name': 'fit', 'sources': ['fit/linear.c']},
    {'name': 'poly', 'sources': ['poly/dd.c', 'poly/eval.c', 'poly/solve_quadratic.c', 'poly/solve_cubic.c', 'poly/zsolve_quadratic.c', 'poly/zsolve_cubic.c', 'poly/zsolve.c', 'poly/zsolve_init.c', 'poly/deriv.c']},
    {'name': 'fft', 'sources': ['fft/dft.c', 'fft/fft.c']},
    {'name': 'randist', 'sources': ['randist/bernoulli.c', 'randist/beta.c', 'randist/bigauss.c', 'randist/binomial.c', 'randist/cauchy.c', 'randist/chisq.c', 'randist/dirichlet.c', 'randist/discrete.c', 'randist/erlang.c', 'randist/exponential.c', 'randist/exppow.c', 'randist/fdist.c', 'randist/flat.c', 'randist/gamma.c', 'randist/gauss.c', 'randist/gausszig.c', 'randist/gausstail.c', 'randist/geometric.c', 'randist/gumbel.c', 'randist/hyperg.c', 'randist/laplace.c', 'randist/levy.c', 'randist/logarithmic.c', 'randist/logistic.c', 'randist/lognormal.c', 'randist/multinomial.c', 'randist/nbinomial.c', 'randist/pareto.c', 'randist/pascal.c', 'randist/poisson.c', 'randist/rayleigh.c', 'randist/shuffle.c', 'randist/sphere.c', 'randist/tdist.c', 'randist/weibull.c', 'randist/landau.c', 'randist/binomial_tpe.c']},
    {'name': 'rng', 'sources': ['rng/borosh13.c', 'rng/cmrg.c', 'rng/coveyou.c', 'rng/default.c', 'rng/file.c', 'rng/fishman18.c', 'rng/fishman20.c', 'rng/fishman2x.c', 'rng/gfsr4.c', 'rng/knuthran2.c', 'rng/knuthran.c', 'rng/knuthran2002.c', 'rng/lecuyer21.c', 'rng/minstd.c', 'rng/mrg.c', 'rng/mt.c', 'rng/r250.c', 'rng/ran0.c', 'rng/ran1.c', 'rng/ran2.c', 'rng/ran3.c', 'rng/rand48.c', 'rng/rand.c', 'rng/random.c', 'rng/randu.c', 'rng/ranf.c', 'rng/ranlux.c', 'rng/ranlxd.c', 'rng/ranlxs.c', 'rng/ranmar.c', 'rng/rng.c', 'rng/slatec.c', 'rng/taus.c', 'rng/taus113.c', 'rng/transputer.c', 'rng/tt.c', 'rng/types.c', 'rng/uni32.c', 'rng/uni.c', 'rng/vax.c', 'rng/waterman14.c', 'rng/zuf.c', 'rng/inline.c']},
    {'name': 'qrng', 'sources': ['qrng/qrng.c', 'qrng/niederreiter-2.c', 'qrng/sobol.c', 'qrng/halton.c', 'qrng/reversehalton.c', 'qrng/inline.c']},
    {'name': 'dht', 'sources': ['dht/dht.c']},
    {'name': 'specfunc', 'sources': ['specfunc/airy.c', 'specfunc/airy_der.c', 'specfunc/airy_zero.c', 'specfunc/atanint.c', 'specfunc/bessel.c', 'specfunc/bessel_I0.c', 'specfunc/bessel_I1.c', 'specfunc/bessel_In.c', 'specfunc/bessel_Inu.c', 'specfunc/bessel_J0.c', 'specfunc/bessel_J1.c', 'specfunc/bessel_Jn.c', 'specfunc/bessel_Jnu.c', 'specfunc/bessel_K0.c', 'specfunc/bessel_K1.c', 'specfunc/bessel_Kn.c', 'specfunc/bessel_Knu.c', 'specfunc/bessel_Y0.c', 'specfunc/bessel_Y1.c', 'specfunc/bessel_Yn.c', 'specfunc/bessel_Ynu.c', 'specfunc/bessel_amp_phase.c', 'specfunc/bessel_i.c', 'specfunc/bessel_j.c', 'specfunc/bessel_k.c', 'specfunc/bessel_olver.c', 'specfunc/bessel_temme.c', 'specfunc/bessel_y.c', 'specfunc/bessel_zero.c', 'specfunc/bessel_sequence.c', 'specfunc/beta.c', 'specfunc/beta_inc.c', 'specfunc/clausen.c', 'specfunc/coulomb.c', 'specfunc/coupling.c', 'specfunc/coulomb_bound.c', 'specfunc/dawson.c', 'specfunc/debye.c', 'specfunc/dilog.c', 'specfunc/elementary.c', 'specfunc/ellint.c', 'specfunc/elljac.c', 'specfunc/erfc.c', 'specfunc/exp.c', 'specfunc/expint.c', 'specfunc/expint3.c', 'specfunc/fermi_dirac.c', 'specfunc/gegenbauer.c', 'specfunc/gamma.c', 'specfunc/gamma_inc.c', 'specfunc/hyperg_0F1.c', 'specfunc/hyperg_2F0.c', 'specfunc/hyperg_1F1.c', 'specfunc/hyperg_2F1.c', 'specfunc/hyperg_U.c', 'specfunc/hyperg.c', 'specfunc/laguerre.c', 'specfunc/lambert.c', 'specfunc/legendre_H3d.c', 'specfunc/legendre_P.c', 'specfunc/legendre_Qn.c', 'specfunc/legendre_con.c', 'specfunc/legendre_poly.c', 'specfunc/log.c', 'specfunc/mathieu_angfunc.c', 'specfunc/mathieu_charv.c', 'specfunc/mathieu_coeff.c', 'specfunc/mathieu_radfunc.c', 'specfunc/mathieu_workspace.c', 'specfunc/poch.c', 'specfunc/pow_int.c', 'specfunc/psi.c', 'specfunc/result.c', 'specfunc/shint.c', 'specfunc/sinint.c', 'specfunc/synchrotron.c', 'specfunc/transport.c', 'specfunc/trig.c', 'specfunc/zeta.c']},
    {'name': 'eigen', 'sources': ['eigen/jacobi.c', 'eigen/symm.c', 'eigen/symmv.c', 'eigen/nonsymm.c', 'eigen/nonsymmv.c', 'eigen/herm.c', 'eigen/hermv.c', 'eigen/gensymm.c', 'eigen/gensymmv.c', 'eigen/genherm.c', 'eigen/genhermv.c', 'eigen/gen.c', 'eigen/genv.c', 'eigen/sort.c', 'eigen/francis.c', 'eigen/schur.c']},
    {'name': 'linalg', 'sources': ['linalg/multiply.c', 'linalg/exponential.c', 'linalg/tridiag.c', 'linalg/lu.c', 'linalg/luc.c', 'linalg/hh.c', 'linalg/qr.c', 'linalg/qrpt.c', 'linalg/lq.c', 'linalg/ptlq.c', 'linalg/svd.c', 'linalg/householder.c', 'linalg/householdercomplex.c', 'linalg/hessenberg.c', 'linalg/hesstri.c', 'linalg/cholesky.c', 'linalg/choleskyc.c', 'linalg/symmtd.c', 'linalg/hermtd.c', 'linalg/bidiag.c', 'linalg/balance.c', 'linalg/balancemat.c', 'linalg/inline.c']},
    {'name': 'blas', 'sources': ['blas/blas.c']},
    {'name': 'cblas', 'sources': ['cblas/sasum.c', 'cblas/saxpy.c', 'cblas/scasum.c', 'cblas/scnrm2.c', 'cblas/scopy.c', 'cblas/sdot.c', 'cblas/sdsdot.c', 'cblas/sgbmv.c', 'cblas/sgemm.c', 'cblas/sgemv.c', 'cblas/sger.c', 'cblas/snrm2.c', 'cblas/srot.c', 'cblas/srotg.c', 'cblas/srotm.c', 'cblas/srotmg.c', 'cblas/ssbmv.c', 'cblas/sscal.c', 'cblas/sspmv.c', 'cblas/sspr.c', 'cblas/sspr2.c', 'cblas/sswap.c', 'cblas/ssymm.c', 'cblas/ssymv.c', 'cblas/ssyr.c', 'cblas/ssyr2.c', 'cblas/ssyr2k.c', 'cblas/ssyrk.c', 'cblas/stbmv.c', 'cblas/stbsv.c', 'cblas/stpmv.c', 'cblas/stpsv.c', 'cblas/strmm.c', 'cblas/strmv.c', 'cblas/strsm.c', 'cblas/strsv.c', 'cblas/dasum.c', 'cblas/daxpy.c', 'cblas/dcopy.c', 'cblas/ddot.c', 'cblas/dgbmv.c', 'cblas/dgemm.c', 'cblas/dgemv.c', 'cblas/dger.c', 'cblas/dnrm2.c', 'cblas/drot.c', 'cblas/drotg.c', 'cblas/drotm.c', 'cblas/drotmg.c', 'cblas/dsbmv.c', 'cblas/dscal.c', 'cblas/dsdot.c', 'cblas/dspmv.c', 'cblas/dspr.c', 'cblas/dspr2.c', 'cblas/dswap.c', 'cblas/dsymm.c', 'cblas/dsymv.c', 'cblas/dsyr.c', 'cblas/dsyr2.c', 'cblas/dsyr2k.c', 'cblas/dsyrk.c', 'cblas/dtbmv.c', 'cblas/dtbsv.c', 'cblas/dtpmv.c', 'cblas/dtpsv.c', 'cblas/dtrmm.c', 'cblas/dtrmv.c', 'cblas/dtrsm.c', 'cblas/dtrsv.c', 'cblas/dzasum.c', 'cblas/dznrm2.c', 'cblas/caxpy.c', 'cblas/ccopy.c', 'cblas/cdotc_sub.c', 'cblas/cdotu_sub.c', 'cblas/cgbmv.c', 'cblas/cgemm.c', 'cblas/cgemv.c', 'cblas/cgerc.c', 'cblas/cgeru.c', 'cblas/chbmv.c', 'cblas/chemm.c', 'cblas/chemv.c', 'cblas/cher.c', 'cblas/cher2.c', 'cblas/cher2k.c', 'cblas/cherk.c', 'cblas/chpmv.c', 'cblas/chpr.c', 'cblas/chpr2.c', 'cblas/cscal.c', 'cblas/csscal.c', 'cblas/cswap.c', 'cblas/csymm.c', 'cblas/csyr2k.c', 'cblas/csyrk.c', 'cblas/ctbmv.c', 'cblas/ctbsv.c', 'cblas/ctpmv.c', 'cblas/ctpsv.c', 'cblas/ctrmm.c', 'cblas/ctrmv.c', 'cblas/ctrsm.c', 'cblas/ctrsv.c', 'cblas/zaxpy.c', 'cblas/zcopy.c', 'cblas/zdotc_sub.c', 'cblas/zdotu_sub.c', 'cblas/zdscal.c', 'cblas/zgbmv.c', 'cblas/zgemm.c', 'cblas/zgemv.c', 'cblas/zgerc.c', 'cblas/zgeru.c', 'cblas/zhbmv.c', 'cblas/zhemm.c', 'cblas/zhemv.c', 'cblas/zher.c', 'cblas/zher2.c', 'cblas/zher2k.c', 'cblas/zherk.c', 'cblas/zhpmv.c', 'cblas/zhpr.c', 'cblas/zhpr2.c', 'cblas/zscal.c', 'cblas/zswap.c', 'cblas/zsymm.c', 'cblas/zsyr2k.c', 'cblas/zsyrk.c', 'cblas/ztbmv.c', 'cblas/ztbsv.c', 'cblas/ztpmv.c', 'cblas/ztpsv.c', 'cblas/ztrmm.c', 'cblas/ztrmv.c', 'cblas/ztrsm.c', 'cblas/ztrsv.c', 'cblas/icamax.c', 'cblas/idamax.c', 'cblas/isamax.c', 'cblas/izamax.c', 'cblas/xerbla.c']},
    {'name': 'ieeeutils', 'sources': ['ieee-utils/print.c', 'ieee-utils/make_rep.c', 'ieee-utils/env.c', 'ieee-utils/fp.c', 'ieee-utils/read.c']},
    {'name': 'sort', 'sources': ['sort/sort.c', 'sort/sortind.c', 'sort/sortvec.c', 'sort/sortvecind.c', 'sort/subset.c', 'sort/subsetind.c']},
    {'name': 'multiset', 'sources': ['multiset/init.c', 'multiset/file.c', 'multiset/multiset.c', 'multiset/inline.c']},
    {'name': 'combination', 'sources': ['combination/init.c', 'combination/file.c', 'combination/combination.c', 'combination/inline.c']},
    {'name': 'permutation', 'sources': ['permutation/init.c', 'permutation/file.c', 'permutation/permutation.c', 'permutation/permute.c', 'permutation/canonical.c', 'permutation/inline.c']},
    {'name': 'matrix', 'sources': ['matrix/init.c', 'matrix/matrix.c', 'matrix/file.c', 'matrix/rowcol.c', 'matrix/swap.c', 'matrix/copy.c', 'matrix/minmax.c', 'matrix/prop.c', 'matrix/oper.c', 'matrix/getset.c', 'matrix/view.c', 'matrix/submatrix.c']},
    {'name': 'vector', 'sources': ['vector/init.c', 'vector/file.c', 'vector/vector.c', 'vector/copy.c', 'vector/swap.c', 'vector/prop.c', 'vector/minmax.c', 'vector/oper.c', 'vector/reim.c', 'vector/subvector.c', 'vector/view.c']},
    {'name': 'block', 'sources': ['block/init.c', 'block/file.c', 'block/block.c']},
    {'name': 'cheb', 'sources': ['cheb/deriv.c', 'cheb/eval.c', 'cheb/init.c', 'cheb/integ.c']},
    {'name': 'complex', 'sources': ['complex/math.c', 'complex/inline.c']},
    {'name': 'err', 'sources': ['err/error.c', 'err/stream.c', 'err/message.c', 'err/strerror.c']},
    {'name': 'test', 'sources': ['test/results.c']},
    {'name': 'sys', 'sources': ['sys/minmax.c', 'sys/prec.c', 'sys/hypot.c', 'sys/log1p.c', 'sys/expm1.c', 'sys/coerce.c', 'sys/invhyp.c', 'sys/pow_int.c', 'sys/infnan.c', 'sys/fdiv.c', 'sys/fcmp.c', 'sys/ldfrexp.c']},
]

gsl_sources = []
foreach module : gsl_modules
    gsl_sources += module['sources']
endforeach

gsl_lib = library('gsl',
    gsl_sources,
    include_directories: gsl_include_dir,
    c_args : gsl_defines,
    dependencies: libm
)

