Technical comment on Evidence for a limit to human lifespan
================
Philipp Berens
October 9, 2016

Dong et al. claim to present statistical evidence in favor of an absolute limit to the human lifespan. Here I present a reanalysis of a central figure in their paper showing that in fact the data is uninformative with regards to the question whether there is a limit to human lifespan or not.

The model by the authors
------------------------

The authors graph the maximum age reported at death (MRAD) for each year between 1968 and 2006. I acquired the data using WebPlotDigitizer and rounded the numbers to full years (which is what likely was the case for the original data). Originally the data came from the [IDL Database](http://www.supercentenarians.org/).

Here is the raw data, as presented by the authors, fitting separate regression for years up to 1994 and after 1995.

![](analysis_files/figure-markdown_github/unnamed-chunk-1-1.png)

The plot shows the raw data points in black and separate linear fits with 95%-CIs for years before and after 1995. It is not clear from the paper why the authors chose 1995 as a point to separate models.

We can also obtain the statistics for this model by fitting a linear model with the additional group-variable as predictor including interactions, allowing for a changes slope and offset for the data after 1995.

``` r
mdl1 <- lm(Age~Year*Group,tbl)
summary.lm(mdl1)
```

    ## 
    ## Call:
    ## lm(formula = Age ~ Year * Group, data = tbl)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -4.4359 -1.0757  0.0871  0.7616  6.1166 
    ## 
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)  
    ## (Intercept)      -191.0734   116.0246  -1.647   0.1104  
    ## Year                0.1531     0.0585   2.617   0.0139 *
    ## Group>=1995       858.5757   347.1723   2.473   0.0195 *
    ## Year:Group>=1995   -0.4293     0.1737  -2.472   0.0196 *
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.956 on 29 degrees of freedom
    ## Multiple R-squared:  0.4163, Adjusted R-squared:  0.3559 
    ## F-statistic: 6.893 on 3 and 29 DF,  p-value: 0.001209

Consistent with the paper, the fitted model has a slope of 0.153 years for years before 1995 and one of -0.276 for years afterwards (compare their Figure 2a).

A linear model
--------------

A simple alternative hypothesis to the claim of the authors would be that MRAD actually keeps increasing and therefore, that there is no limit to human lifespan. To model this idea, we fit a simple linear model to the data:

![](analysis_files/figure-markdown_github/unnamed-chunk-3-1.png)

The plots shows the raw data points again, with a linear regression with 95% CIs fitted to all the data.

``` r
mdl2 <- lm(Age~Year,tbl)
summary.lm(mdl2)
```

    ## 
    ## Call:
    ## lm(formula = Age ~ Year, data = tbl)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -3.4791 -1.3544  0.0139  0.6343  7.6428 
    ## 
    ## Coefficients:
    ##               Estimate Std. Error t value Pr(>|t|)   
    ## (Intercept) -134.56615   69.84837  -1.927  0.06325 . 
    ## Year           0.12465    0.03511   3.550  0.00125 **
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 2.088 on 31 degrees of freedom
    ## Multiple R-squared:  0.2891, Adjusted R-squared:  0.2662 
    ## F-statistic: 12.61 on 1 and 31 DF,  p-value: 0.001251

In this case, MRAD increases slightly by 0.12 years per year.

Model comparison
----------------

Which model is better? In the paper, the authors fail to provide evidence for their model, they seem to argue that the data looks like there is a saturation effect or a decline in MRAD after 1995.

One can do better and objectively compare the two fitted models. If we look at the output of the models above, the model by the authors explains a little more variance in the data than the linear model (0.42 vs. 0.29). On the other hand, the model also uses four parameters to do so, compared to only two in the linear model.

We can therefore ask if the increase in explained variance is "worth" the additional parameters, e.g. by comparing the Bayesian Information Criterion (BIC) of the two models. This is a goodness of fit measure penalized for the number of parameters.

``` r
BIC(mdl1)
```

    ## [1] 151.1461

``` r
BIC(mdl2)
```

    ## [1] 150.6574

Following Kass and Raftery (1993), a BIC difference of 0.49 is not worth mentioning, providing no evidence of one versus the other model.

Bayesian modeling
-----------------

Above, we followed a classical frequentist approach towards regression modeling. Alternatively, one can take a Baysian approach and fit linear models of different complexity using Bayesian methods. In this framework, model comparison comes very naturally.

We fit the models using the package `rstanarm`, which allows relatively straightforward use of Bayesian methods. We use the build in R2 prior, following the recommendation to set the location parameter to 0.5.

    ## 
    ## SAMPLING FOR MODEL 'lm' NOW (CHAIN 1).
    ## 
    ## Chain 1, Iteration:    1 / 2000 [  0%]  (Warmup)
    ## Chain 1, Iteration:  200 / 2000 [ 10%]  (Warmup)
    ## Chain 1, Iteration:  400 / 2000 [ 20%]  (Warmup)
    ## Chain 1, Iteration:  600 / 2000 [ 30%]  (Warmup)
    ## Chain 1, Iteration:  800 / 2000 [ 40%]  (Warmup)
    ## Chain 1, Iteration: 1000 / 2000 [ 50%]  (Warmup)
    ## Chain 1, Iteration: 1001 / 2000 [ 50%]  (Sampling)
    ## Chain 1, Iteration: 1200 / 2000 [ 60%]  (Sampling)
    ## Chain 1, Iteration: 1400 / 2000 [ 70%]  (Sampling)
    ## Chain 1, Iteration: 1600 / 2000 [ 80%]  (Sampling)
    ## Chain 1, Iteration: 1800 / 2000 [ 90%]  (Sampling)
    ## Chain 1, Iteration: 2000 / 2000 [100%]  (Sampling)
    ##  Elapsed Time: 18.974 seconds (Warm-up)
    ##                26.141 seconds (Sampling)
    ##                45.115 seconds (Total)
    ## 
    ## 
    ## SAMPLING FOR MODEL 'lm' NOW (CHAIN 2).
    ## 
    ## Chain 2, Iteration:    1 / 2000 [  0%]  (Warmup)
    ## Chain 2, Iteration:  200 / 2000 [ 10%]  (Warmup)
    ## Chain 2, Iteration:  400 / 2000 [ 20%]  (Warmup)
    ## Chain 2, Iteration:  600 / 2000 [ 30%]  (Warmup)
    ## Chain 2, Iteration:  800 / 2000 [ 40%]  (Warmup)
    ## Chain 2, Iteration: 1000 / 2000 [ 50%]  (Warmup)
    ## Chain 2, Iteration: 1001 / 2000 [ 50%]  (Sampling)
    ## Chain 2, Iteration: 1200 / 2000 [ 60%]  (Sampling)
    ## Chain 2, Iteration: 1400 / 2000 [ 70%]  (Sampling)
    ## Chain 2, Iteration: 1600 / 2000 [ 80%]  (Sampling)
    ## Chain 2, Iteration: 1800 / 2000 [ 90%]  (Sampling)
    ## Chain 2, Iteration: 2000 / 2000 [100%]  (Sampling)
    ##  Elapsed Time: 15.579 seconds (Warm-up)
    ##                20.804 seconds (Sampling)
    ##                36.383 seconds (Total)
    ## 
    ## 
    ## SAMPLING FOR MODEL 'lm' NOW (CHAIN 3).
    ## 
    ## Chain 3, Iteration:    1 / 2000 [  0%]  (Warmup)
    ## Chain 3, Iteration:  200 / 2000 [ 10%]  (Warmup)
    ## Chain 3, Iteration:  400 / 2000 [ 20%]  (Warmup)
    ## Chain 3, Iteration:  600 / 2000 [ 30%]  (Warmup)
    ## Chain 3, Iteration:  800 / 2000 [ 40%]  (Warmup)
    ## Chain 3, Iteration: 1000 / 2000 [ 50%]  (Warmup)
    ## Chain 3, Iteration: 1001 / 2000 [ 50%]  (Sampling)
    ## Chain 3, Iteration: 1200 / 2000 [ 60%]  (Sampling)
    ## Chain 3, Iteration: 1400 / 2000 [ 70%]  (Sampling)
    ## Chain 3, Iteration: 1600 / 2000 [ 80%]  (Sampling)
    ## Chain 3, Iteration: 1800 / 2000 [ 90%]  (Sampling)
    ## Chain 3, Iteration: 2000 / 2000 [100%]  (Sampling)
    ##  Elapsed Time: 16.307 seconds (Warm-up)
    ##                17.821 seconds (Sampling)
    ##                34.128 seconds (Total)
    ## 
    ## 
    ## SAMPLING FOR MODEL 'lm' NOW (CHAIN 4).
    ## 
    ## Chain 4, Iteration:    1 / 2000 [  0%]  (Warmup)
    ## Chain 4, Iteration:  200 / 2000 [ 10%]  (Warmup)
    ## Chain 4, Iteration:  400 / 2000 [ 20%]  (Warmup)
    ## Chain 4, Iteration:  600 / 2000 [ 30%]  (Warmup)
    ## Chain 4, Iteration:  800 / 2000 [ 40%]  (Warmup)
    ## Chain 4, Iteration: 1000 / 2000 [ 50%]  (Warmup)
    ## Chain 4, Iteration: 1001 / 2000 [ 50%]  (Sampling)
    ## Chain 4, Iteration: 1200 / 2000 [ 60%]  (Sampling)
    ## Chain 4, Iteration: 1400 / 2000 [ 70%]  (Sampling)
    ## Chain 4, Iteration: 1600 / 2000 [ 80%]  (Sampling)
    ## Chain 4, Iteration: 1800 / 2000 [ 90%]  (Sampling)
    ## Chain 4, Iteration: 2000 / 2000 [100%]  (Sampling)
    ##  Elapsed Time: 19.962 seconds (Warm-up)
    ##                17.419 seconds (Sampling)
    ##                37.381 seconds (Total)

    ## Warning: There were 28 divergent transitions after warmup. Increasing adapt_delta above 0.99 may help. See
    ## http://mc-stan.org/misc/warnings.html#divergent-transitions-after-warmup

    ## Warning: Examine the pairs() plot to diagnose sampling problems

    ## 
    ## SAMPLING FOR MODEL 'lm' NOW (CHAIN 1).
    ## 
    ## Chain 1, Iteration:    1 / 2000 [  0%]  (Warmup)
    ## Chain 1, Iteration:  200 / 2000 [ 10%]  (Warmup)
    ## Chain 1, Iteration:  400 / 2000 [ 20%]  (Warmup)
    ## Chain 1, Iteration:  600 / 2000 [ 30%]  (Warmup)
    ## Chain 1, Iteration:  800 / 2000 [ 40%]  (Warmup)
    ## Chain 1, Iteration: 1000 / 2000 [ 50%]  (Warmup)
    ## Chain 1, Iteration: 1001 / 2000 [ 50%]  (Sampling)
    ## Chain 1, Iteration: 1200 / 2000 [ 60%]  (Sampling)
    ## Chain 1, Iteration: 1400 / 2000 [ 70%]  (Sampling)
    ## Chain 1, Iteration: 1600 / 2000 [ 80%]  (Sampling)
    ## Chain 1, Iteration: 1800 / 2000 [ 90%]  (Sampling)
    ## Chain 1, Iteration: 2000 / 2000 [100%]  (Sampling)
    ##  Elapsed Time: 2.855 seconds (Warm-up)
    ##                2.63 seconds (Sampling)
    ##                5.485 seconds (Total)
    ## 
    ## 
    ## SAMPLING FOR MODEL 'lm' NOW (CHAIN 2).
    ## 
    ## Chain 2, Iteration:    1 / 2000 [  0%]  (Warmup)
    ## Chain 2, Iteration:  200 / 2000 [ 10%]  (Warmup)
    ## Chain 2, Iteration:  400 / 2000 [ 20%]  (Warmup)
    ## Chain 2, Iteration:  600 / 2000 [ 30%]  (Warmup)
    ## Chain 2, Iteration:  800 / 2000 [ 40%]  (Warmup)
    ## Chain 2, Iteration: 1000 / 2000 [ 50%]  (Warmup)
    ## Chain 2, Iteration: 1001 / 2000 [ 50%]  (Sampling)
    ## Chain 2, Iteration: 1200 / 2000 [ 60%]  (Sampling)
    ## Chain 2, Iteration: 1400 / 2000 [ 70%]  (Sampling)
    ## Chain 2, Iteration: 1600 / 2000 [ 80%]  (Sampling)
    ## Chain 2, Iteration: 1800 / 2000 [ 90%]  (Sampling)
    ## Chain 2, Iteration: 2000 / 2000 [100%]  (Sampling)
    ##  Elapsed Time: 1.598 seconds (Warm-up)
    ##                3.756 seconds (Sampling)
    ##                5.354 seconds (Total)
    ## 
    ## 
    ## SAMPLING FOR MODEL 'lm' NOW (CHAIN 3).
    ## 
    ## Chain 3, Iteration:    1 / 2000 [  0%]  (Warmup)
    ## Chain 3, Iteration:  200 / 2000 [ 10%]  (Warmup)
    ## Chain 3, Iteration:  400 / 2000 [ 20%]  (Warmup)
    ## Chain 3, Iteration:  600 / 2000 [ 30%]  (Warmup)
    ## Chain 3, Iteration:  800 / 2000 [ 40%]  (Warmup)
    ## Chain 3, Iteration: 1000 / 2000 [ 50%]  (Warmup)
    ## Chain 3, Iteration: 1001 / 2000 [ 50%]  (Sampling)
    ## Chain 3, Iteration: 1200 / 2000 [ 60%]  (Sampling)
    ## Chain 3, Iteration: 1400 / 2000 [ 70%]  (Sampling)
    ## Chain 3, Iteration: 1600 / 2000 [ 80%]  (Sampling)
    ## Chain 3, Iteration: 1800 / 2000 [ 90%]  (Sampling)
    ## Chain 3, Iteration: 2000 / 2000 [100%]  (Sampling)
    ##  Elapsed Time: 2.873 seconds (Warm-up)
    ##                2.37 seconds (Sampling)
    ##                5.243 seconds (Total)
    ## 
    ## 
    ## SAMPLING FOR MODEL 'lm' NOW (CHAIN 4).
    ## 
    ## Chain 4, Iteration:    1 / 2000 [  0%]  (Warmup)
    ## Chain 4, Iteration:  200 / 2000 [ 10%]  (Warmup)
    ## Chain 4, Iteration:  400 / 2000 [ 20%]  (Warmup)
    ## Chain 4, Iteration:  600 / 2000 [ 30%]  (Warmup)
    ## Chain 4, Iteration:  800 / 2000 [ 40%]  (Warmup)
    ## Chain 4, Iteration: 1000 / 2000 [ 50%]  (Warmup)
    ## Chain 4, Iteration: 1001 / 2000 [ 50%]  (Sampling)
    ## Chain 4, Iteration: 1200 / 2000 [ 60%]  (Sampling)
    ## Chain 4, Iteration: 1400 / 2000 [ 70%]  (Sampling)
    ## Chain 4, Iteration: 1600 / 2000 [ 80%]  (Sampling)
    ## Chain 4, Iteration: 1800 / 2000 [ 90%]  (Sampling)
    ## Chain 4, Iteration: 2000 / 2000 [100%]  (Sampling)
    ##  Elapsed Time: 2.464 seconds (Warm-up)
    ##                3.012 seconds (Sampling)
    ##                5.476 seconds (Total)

    ## Warning: There were 1972 divergent transitions after warmup. Increasing adapt_delta above 0.99 may help. See
    ## http://mc-stan.org/misc/warnings.html#divergent-transitions-after-warmup

    ## Warning: Examine the pairs() plot to diagnose sampling problems

``` r
summary(bmdl1)
```

    ## stan_lm(formula = Age ~ Year * Group, data = tbl, prior = R2(location = 0.5, 
    ##     what = "mean"))
    ## 
    ## Family: gaussian (identity)
    ## Algorithm: sampling
    ## Posterior sample size: 4000
    ## Observations: 33
    ## 
    ## Estimates:
    ##                    mean   sd     2.5%   25%    50%    75%    97.5%
    ## (Intercept)      -164.0  115.1 -397.6 -236.6 -166.6  -85.5   65.5 
    ## Year                0.1    0.1    0.0    0.1    0.1    0.2    0.3 
    ## Group>=1995       791.3  344.7  126.7  567.3  794.5 1012.9 1467.0 
    ## Year:Group>=1995   -0.4    0.2   -0.7   -0.5   -0.4   -0.3   -0.1 
    ## sigma               2.0    0.2    1.6    1.8    2.0    2.1    2.5 
    ## log-fit_ratio       0.0    0.1   -0.2   -0.1    0.0    0.1    0.3 
    ## R2                  0.4    0.1    0.1    0.3    0.4    0.5    0.6 
    ## mean_PPD          113.4    0.5  112.5  113.1  113.4  113.7  114.4 
    ## log-posterior     -72.2    2.1  -77.1  -73.4  -71.8  -70.6  -69.1 
    ## 
    ## Diagnostics:
    ##                  mcse Rhat n_eff
    ## (Intercept)      3.2  1.0  1274 
    ## Year             0.0  1.0  1274 
    ## Group>=1995      7.1  1.0  2372 
    ## Year:Group>=1995 0.0  1.0  2369 
    ## sigma            0.0  1.0  2519 
    ## log-fit_ratio    0.0  1.0  2038 
    ## R2               0.0  1.0  1722 
    ## mean_PPD         0.0  1.0  3715 
    ## log-posterior    0.1  1.0   866 
    ## 
    ## For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).

``` r
summary(bmdl2)
```

    ## stan_lm(formula = Age ~ Year, data = tbl, prior = R2(location = 0.5, 
    ##     what = "mean"))
    ## 
    ## Family: gaussian (identity)
    ## Algorithm: sampling
    ## Posterior sample size: 4000
    ## Observations: 33
    ## 
    ## Estimates:
    ##                 mean   sd     2.5%   25%    50%    75%    97.5%
    ## (Intercept)   -128.4   67.3 -253.1 -173.9 -129.0  -85.1    9.8 
    ## Year             0.1    0.0    0.1    0.1    0.1    0.1    0.2 
    ## sigma            2.1    0.3    1.6    1.9    2.1    2.3    2.7 
    ## log-fit_ratio    0.0    0.1   -0.2   -0.1    0.0    0.1    0.3 
    ## R2               0.3    0.1    0.1    0.2    0.3    0.3    0.5 
    ## mean_PPD       113.4    0.5  112.4  113.1  113.4  113.8  114.4 
    ## log-posterior  -74.1    1.5  -78.0  -74.9  -73.8  -73.1  -72.3 
    ## 
    ## Diagnostics:
    ##               mcse Rhat n_eff
    ## (Intercept)   3.7  1.0   330 
    ## Year          0.0  1.0   329 
    ## sigma         0.0  1.0   621 
    ## log-fit_ratio 0.0  1.0   436 
    ## R2            0.0  1.0   352 
    ## mean_PPD      0.0  1.0  3471 
    ## log-posterior 0.1  1.0   516 
    ## 
    ## For each parameter, mcse is Monte Carlo standard error, n_eff is a crude measure of effective sample size, and Rhat is the potential scale reduction factor on split chains (at convergence Rhat=1).

Comparing the fitted model to the frequentist models above shows that the posterior modes of the coefficients are very similar to the estimated values above.

For model comparison, we follow Vehtari et al. (2015) and compute a leave-one-out estimate of the prediction error from the fitted model.

``` r
loo_bmdl1 <- loo(bmdl1, k_threshold = 0.7)
```

    ## 2 problematic observation(s) found.
    ## Model will be refit 2 times.

    ## 
    ## Fitting model 1 out of 2 (leaving out observation 1)

    ## 
    ##  Elapsed Time: 15.949 seconds (Warm-up)
    ##                14.727 seconds (Sampling)
    ##                30.676 seconds (Total)
    ## 
    ## 
    ##  Elapsed Time: 16.723 seconds (Warm-up)
    ##                11.081 seconds (Sampling)
    ##                27.804 seconds (Total)
    ## 
    ## 
    ##  Elapsed Time: 14.54 seconds (Warm-up)
    ##                14.308 seconds (Sampling)
    ##                28.848 seconds (Total)
    ## 
    ## 
    ##  Elapsed Time: 16.686 seconds (Warm-up)
    ##                9.658 seconds (Sampling)
    ##                26.344 seconds (Total)

    ## 
    ## Fitting model 2 out of 2 (leaving out observation 10)

    ## 
    ##  Elapsed Time: 17.006 seconds (Warm-up)
    ##                10.87 seconds (Sampling)
    ##                27.876 seconds (Total)
    ## 
    ## 
    ##  Elapsed Time: 19.91 seconds (Warm-up)
    ##                12.284 seconds (Sampling)
    ##                32.194 seconds (Total)
    ## 
    ## 
    ##  Elapsed Time: 17.367 seconds (Warm-up)
    ##                12.269 seconds (Sampling)
    ##                29.636 seconds (Total)
    ## 
    ## 
    ##  Elapsed Time: 20.926 seconds (Warm-up)
    ##                14.416 seconds (Sampling)
    ##                35.342 seconds (Total)

``` r
loo_bmdl2 <- loo(bmdl2, k_threshold = 0.7)
```

    ## 1 problematic observation(s) found.
    ## Model will be refit 1 times.

    ## 
    ## Fitting model 1 out of 1 (leaving out observation 1)

    ## 
    ##  Elapsed Time: 4.209 seconds (Warm-up)
    ##                2.675 seconds (Sampling)
    ##                6.884 seconds (Total)
    ## 
    ## 
    ##  Elapsed Time: 2.565 seconds (Warm-up)
    ##                6.246 seconds (Sampling)
    ##                8.811 seconds (Total)
    ## 
    ## 
    ##  Elapsed Time: 2.768 seconds (Warm-up)
    ##                2.884 seconds (Sampling)
    ##                5.652 seconds (Total)
    ## 
    ## 
    ##  Elapsed Time: 2.788 seconds (Warm-up)
    ##                2.919 seconds (Sampling)
    ##                5.707 seconds (Total)

``` r
compare(loo_bmdl1,loo_bmdl2)
```

    ## elpd_diff        se 
    ##      -0.3       3.9

The comparison shows that there is not evidence for the model by the authors, in agreement with the frequentist results above.

TODO: Diagnostic plots for the fits

Conclusion
----------

TODO

References
----------

-   Kass and Raftery (1993): Bayes Factor, Journal of the American Statistical Assosciation, [link](http://www.tandfonline.com/doi/abs/10.1080/01621459.1995.10476572)

-   Vehtari, Gelman and Gabry (2016): Practical Bayesian model evaluation using leave-one-out cross-validation and WAIC, arxiv [link](https://arxiv.org/pdf/1507.04544v5.pdf)
