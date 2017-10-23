# VA and CVA heritability simulations 
Code for running simulations to determine scenarios in which raw and contextual value added measures may suffer from genetic bias. 

Last updated: 23/10/2017

## Stata code
	clear
	cd "<directory>"
	
	********************************************************************************
	* Code for running a range of VA/CVA simulations with varying levels of 
	* 		measurement error on educational attainment scores, and covariates 
	* 		with varying levels of genetic correlation. 
	********************************************************************************
	
	* Creating simulation program for raw VA measures
	qui program define simva
	args obs m_error1 m_error2 gen_corr covar1 covar2 gcovar1 gcovar2
	drop _all
	set obs `obs'
	gen e=rnormal()
	gen g=rnormal()
	gen covar=rnormal()
	gen gcovar=g*`gen_corr' + rnormal()*(1-`gen_corr')
	gen score = g /// 
		+ `covar1'*covar ///
		+ `gcovar1'*gcovar ///
		+ `m_error1'*e
	gen e2=rnormal()
	gen teacher=rnormal()
	gen score2 = g ///  
		+ `covar2'*covar ///
		+ `gcovar2'*gcovar ///
		+ `m_error2'*e2
	qui reg score2 score
	qui predict vapred
	qui predict vapredresid, residuals
	qui reg vapredresid g
	end
	
	* Creating simulation program for CVA measures
	qui program define simcva
	args obs m_error1 m_error2 gen_corr covar1 covar2 gcovar1 gcovar2
	drop _all
	set obs `obs'
	gen e=rnormal()
	gen g=rnormal()
	gen covar=rnormal()
	gen gcovar=g*`gen_corr' + rnormal()*(1-`gen_corr')
	gen score = g /// 
		+ `covar1'*covar ///
		+ `gcovar1'*gcovar ///
		+ `m_error1'*e
	gen e2=rnormal()
	gen teacher=rnormal()
	gen score2 = g ///  
		+ `covar2'*covar ///
		+ `gcovar2'*gcovar ///
		+ `m_error2'*e2
	qui reg score2 score covar gcovar
	qui predict cvapred
	qui predict cvapredresid, residuals
	qui reg cvapredresid g
	end
	
	log using "va_paper_simulations.log", replace
	* VA
	* Equal measurement error on scores
	qui simulate _b _se, reps(10): simva 1000000 0 0 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va1)
	
	qui simulate _b _se, reps(10): simva 1000000 0.25 0.25 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va2)
	
	qui simulate _b _se, reps(10): simva 1000000 0.50 0.50 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va3)
	
	qui simulate _b _se, reps(10): simva 1000000 0.75 0.75 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va4)
	
	* Varying measurement error on scores
	qui simulate _b _se, reps(10): simva 1000000 0 0.25 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va5)
	
	qui simulate _b _se, reps(10): simva 1000000 0 0.50 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va6)
	
	qui simulate _b _se, reps(10): simva 1000000 0 0.75 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va7)
	
	qui simulate _b _se, reps(10): simva 1000000 0.25 0 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va8)
	
	qui simulate _b _se, reps(10): simva 1000000 0.50 0 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va9)
	
	qui simulate _b _se, reps(10): simva 1000000 0.75 0 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va10)
	
	* Varying covar values
	qui simulate _b _se, reps(10): simva 1000000 0 0 0.5 0.5 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va11)
	
	qui simulate _b _se, reps(10): simva 1000000 0 0 0.5 2 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va12)
	
	qui simulate _b _se, reps(10): simva 1000000 0 0 0.5 1 0.5 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va13)
	
	qui simulate _b _se, reps(10): simva 1000000 0 0 0.5 1 2 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va14)
	
	* Varying gcovar values
	qui simulate _b _se, reps(10): simva 1000000 0 0 0.5 1 1 0.5 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va15)
	
	qui simulate _b _se, reps(10): simva 1000000 0 0 0.5 1 1 2 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va16)
	
	qui simulate _b _se, reps(10): simva 1000000 0 0 0.5 1 1 1 0.5
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va17)
	
	qui simulate _b _se, reps(10): simva 1000000 0 0 0.5 1 1 1 2
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(va18)
	
	* CVA
	* Equal measurement error on scores
	qui simulate _b _se, reps(10): simcva 1000000 0 0 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva1)
	qui simulate _b _se, reps(10): simcva 1000000 0.25 0.25 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva2)
	
	qui simulate _b _se, reps(10): simcva 1000000 0.50 0.50 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva3)
	
	qui simulate _b _se, reps(10): simcva 1000000 0.75 0.75 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva4)
	
	* Varying measurement error on scores
	qui simulate _b _se, reps(10): simcva 1000000 0 0.25 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva5)
	
	qui simulate _b _se, reps(10): simcva 1000000 0 0.50 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva6)
	
	qui simulate _b _se, reps(10): simcva 1000000 0 0.75 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva7)
	
	qui simulate _b _se, reps(10): simcva 1000000 0.25 0 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva8)
	
	qui simulate _b _se, reps(10): simcva 1000000 0.50 0 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva9)
	
	qui simulate _b _se, reps(10): simcva 1000000 0.75 0 0.5 1 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva10)
	
	* Varying covar values
	qui simulate _b _se, reps(10): simcva 1000000 0 0 0.5 0.5 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva11)
	
	qui simulate _b _se, reps(10): simcva 1000000 0 0 0.5 2 1 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva12)
	
	qui simulate _b _se, reps(10): simcva 1000000 0 0 0.5 1 0.5 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva13)
	
	qui simulate _b _se, reps(10): simcva 1000000 0 0 0.5 1 2 1 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva14)
	
	* Varying gcovar values
	qui simulate _b _se, reps(10): simcva 1000000 0 0 0.5 1 1 0.5 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva15)
	
	qui simulate _b _se, reps(10): simcva 1000000 0 0 0.5 1 1 2 1
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva16)
	
	qui simulate _b _se, reps(10): simcva 1000000 0 0 0.5 1 1 1 0.5
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva17)
	
	qui simulate _b _se, reps(10): simcva 1000000 0 0 0.5 1 1 1 2
	summ *
	collapse (mean) _b_g _se_g
	mkmat _b_g _se_g, matrix(cva18)
	
	program drop simva
	program drop simcva
	
	log close
	
	* Renaming matrices: 
	matrix rowname va1 = "va1"
	matrix rowname va2 = "va2"
	matrix rowname va3 = "va3"
	matrix rowname va4 = "va4"
	matrix rowname va5 = "va5"
	matrix rowname va6 = "va6"
	matrix rowname va7 = "va7"
	matrix rowname va8 = "va8"
	matrix rowname va9 = "va9"
	matrix rowname va10 = "va10"
	matrix rowname va11 = "va11"
	matrix rowname va12 = "va12"
	matrix rowname va13 = "va13"
	matrix rowname va14 = "va14"
	matrix rowname va15 = "va15"
	matrix rowname va16 = "va16"
	matrix rowname va17 = "va17"
	matrix rowname va18 = "va18"
	matrix rowname cva1 = "va1"
	matrix rowname cva2 = "va2"
	matrix rowname cva3 = "va3"
	matrix rowname cva4 = "va4"
	matrix rowname cva5 = "va5"
	matrix rowname cva6 = "va6"
	matrix rowname cva7 = "va7"
	matrix rowname cva8 = "va8"
	matrix rowname cva9 = "va9"
	matrix rowname cva10 = "va10"
	matrix rowname cva11 = "va11"
	matrix rowname cva12 = "va12"
	matrix rowname cva13 = "va13"
	matrix rowname cva14 = "va14"
	matrix rowname cva15 = "va15"
	matrix rowname cva16 = "va16"
	matrix rowname cva17 = "va17"
	matrix rowname cva18 = "va18"
	
	
	* Creating graph: 
	coefplot ///
	(matrix(va1[.,1]) , se(va1[.,2]) ms(C) msize(small) mc(black) ciopts(lc(black) lwidth(vthin)) offset(0)) ///
	(matrix(cva1[.,1]) , se(cva1[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(black) lwidth(vthin)) offset(0.2)) ///
	(matrix(va2[.,1]) , se(va2[.,2]) ms(C) msize(small) mc(black) ciopts(lc(eltblue) lwidth(vthin)) offset(0))  ///
	(matrix(cva2[.,1]) , se(cva2[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(eltblue) lwidth(vthin)) offset(0.2))  ///
	(matrix(va3[.,1]) , se(va3[.,2]) ms(C) msize(small) mc(black) ciopts(lc(edkblue) lwidth(vthin)) offset(0))  ///
	(matrix(cva3[.,1]) , se(cva3[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(edkblue) lwidth(vthin)) offset(0.2))  ///
	(matrix(va4[.,1]) , se(va4[.,2]) ms(C) msize(small) mc(black) ciopts(lc(blue) lwidth(vthin)) offset(0)) ///
	(matrix(cva4[.,1]) , se(cva4[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(blue) lwidth(vthin)) offset(0.2)) ///
	(matrix(va5[.,1]) , se(va5[.,2]) ms(C) msize(small) mc(black) ciopts(lc(black) lwidth(vthin)) offset(0)) ///
	(matrix(va6[.,1]) , se(va6[.,2]) ms(C) msize(small) mc(black) ciopts(lc(eltblue) lwidth(vthin)) offset(0))  ///
	(matrix(va7[.,1]) , se(va7[.,2]) ms(C) msize(small) mc(black) ciopts(lc(edkblue) lwidth(vthin)) offset(0))  ///
	(matrix(va8[.,1]) , se(va8[.,2]) ms(C) msize(small) mc(black) ciopts(lc(blue) lwidth(vthin)) offset(0)) ///
	(matrix(va9[.,1]) , se(va9[.,2]) ms(C) msize(small) mc(black) ciopts(lc(black) lwidth(vthin)) offset(0)) ///
	(matrix(va10[.,1]) , se(va10[.,2]) ms(C) msize(small) mc(black) ciopts(lc(eltblue) lwidth(vthin)) offset(0))  ///
	(matrix(va11[.,1]) , se(va11[.,2]) ms(C) msize(small) mc(black) ciopts(lc(edkblue) lwidth(vthin)) offset(0))  ///
	(matrix(va12[.,1]) , se(va12[.,2]) ms(C) msize(small) mc(black) ciopts(lc(blue) lwidth(vthin)) offset(0)) ///
	(matrix(va13[.,1]) , se(va13[.,2]) ms(C) msize(small) mc(black) ciopts(lc(black) lwidth(vthin)) offset(0)) ///
	(matrix(va14[.,1]) , se(va14[.,2]) ms(C) msize(small) mc(black) ciopts(lc(eltblue) lwidth(vthin)) offset(0))  ///
	(matrix(va15[.,1]) , se(va15[.,2]) ms(C) msize(small) mc(black) ciopts(lc(edkblue) lwidth(vthin)) offset(0))  ///
	(matrix(va16[.,1]) , se(va16[.,2]) ms(C) msize(small) mc(black) ciopts(lc(blue) lwidth(vthin)) offset(0)) ///
	(matrix(va17[.,1]) , se(va17[.,2]) ms(C) msize(small) mc(black) ciopts(lc(blue) lwidth(vthin)) offset(0)) ///
	(matrix(va18[.,1]) , se(va18[.,2]) ms(C) msize(small) mc(black) ciopts(lc(blue) lwidth(vthin)) offset(0)) ///
	(matrix(cva5[.,1]) , se(cva5[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(black) lwidth(vthin)) offset(0.2)) ///
	(matrix(cva6[.,1]) , se(cva6[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(eltblue) lwidth(vthin)) offset(0.2))  ///
	(matrix(cva7[.,1]) , se(cva7[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(edkblue) lwidth(vthin)) offset(0.2))  ///
	(matrix(cva8[.,1]) , se(cva8[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(blue) lwidth(vthin)) offset(0.2)) ///
	(matrix(cva9[.,1]) , se(cva9[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(black) lwidth(vthin)) offset(0.2)) ///
	(matrix(cva10[.,1]) , se(cva10[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(eltblue) lwidth(vthin)) offset(0.2))  ///
	(matrix(cva11[.,1]) , se(cva11[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(edkblue) lwidth(vthin)) offset(0.2))  ///
	(matrix(cva12[.,1]) , se(cva12[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(blue) lwidth(vthin)) offset(0.2)) ///
	(matrix(cva13[.,1]) , se(cva13[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(black) lwidth(vthin)) offset(0.2)) ///
	(matrix(cva14[.,1]) , se(cva14[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(eltblue) lwidth(vthin)) offset(0.2))  ///
	(matrix(cva15[.,1]) , se(cva15[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(edkblue) lwidth(vthin)) offset(0.2))  ///
	(matrix(cva16[.,1]) , se(cva16[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(blue) lwidth(vthin)) offset(0.2)) ///
	(matrix(cva17[.,1]) , se(cva17[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(blue) lwidth(vthin)) offset(0.2)) ///
	(matrix(cva18[.,1]) , se(cva18[.,2]) ms(C) msize(small) mc(gs10) ciopts(lc(blue) lwidth(vthin)) offset(0.2)) ///
	, graphregion(color(white)) plotregion(lc(white)) xline(0) ylabel(,labsize(small)) xlabel(,labsize(small)) xtitle("Estimated heritability") ///
	legend(order(2 "VA" 4 "CVA")) ///
	rescale(100) ///
	headings(va1 = "{bf:Equal measurement error}" ///
	va5 = "{bf:Unequal measurement error}" ///
	va11 = "{bf:Non-genetic covariate effect}" ///
	va15 = "{bf:Genetic covariate effect}" ///
	, labsize(small)) ///
	coeflabels( ///
	va1 = "0, 0" ///
	va2 = "0.25, 0.25" ///
	va3 = "0.5, 0.5" ///
	va4 = "0.75 ,0.75" ///
	va5 = "0, 0.25" ///
	va6 = "0, 0.50" ///
	va7 = "0, 0.75" ///
	va8 = "0.25, 0" ///
	va9 = "0.50, 0" ///
	va10 = "0.75, 0" ///
	va11 = "0.5, 1" ///
	va12 = "2, 1" ///
	va13 = "1, 0.5" ///
	va14 = "1, 2" ///
	va15 = "0.5, 1" ///
	va16 = "2, 1" ///
	va17 = "1, 0.5" ///
	va18 = "1, 2")
	
	graph save "simulations", replace
	
	* END
