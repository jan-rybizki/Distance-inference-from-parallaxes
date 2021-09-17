# Distance-inference-from-parallaxes

Here we want to give a short overview on the inference of the distances for the [Gaia Catalogue of Nearby Stars (GCNS) paper](https://ui.adsabs.harvard.edu/abs/2021A%26A...649A...6G).

We start from a prior based on [GeDR3mock](https://ui.adsabs.harvard.edu/abs/2020PASP..132g4501R/abstract).
The query is as follows:

``` sql
SELECT * FROM(
SELECT parallax, GAVO_NORMAL_RANDOM(parallax,
POWER(10, ((LOG10(parallax_error)+1)*1.3)-1)) AS
parallax_obs
-- This adds observational noise to the true
parallaxes
FROM gedr3mock.main) AS sample
WHERE parallax_obs > 8
```
The result of the query is stored in GeDR3mock_query_distance_prior.fits

We then infer the distances of GCNS stars. The data is again queried and we downsample by a factor of 1000:

``` sql
SELECT phot_g_mean_mag, phot_rp_mean_mag, source_id, parallax, parallax_error, 
astrometric_params_solved, nu_eff_used_in_astrometry, pseudocolour, ecl_lat
FROM gaiaedr3.gaia_source
WHERE parallax >= 8
AND MOD(random_index,1000) = 0
```

The inference script can be found in the jupyter notebook: 'Distance inference.ipynb'

First we need to calculate the parallax zero-point correction according to [Lindegren+20](https://ui.adsabs.harvard.edu/abs/2021A%26A...649A...4L/abstract). For that we installed this [package](https://gitlab.com/icc-ub/public/gaiadr3_zeropoint).
Then we use an MCMC to sample the posterior over the distance parameter. The result are the percentiles and the MCMC convergence indicators as provided by the GCNS. We store the resulting data in the file 'dist_cat_edr3.fits'.
