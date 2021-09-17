# Distance-inference-from-parallaxes

Here we want to give a short overview on the inference of the distances for the [Gaia Catalogue of Nearby Stars (GCNS) paper](https://ui.adsabs.harvard.edu/abs/2021A%26A...649A...6G).

We start from a prior based on [GeDR3mock](https://ui.adsabs.harvard.edu/abs/2020PASP..132g4501R/abstract).
The query is as follows:

``` sql
SELECT * FROM(
SELECT parallax, GAVO_RANDOM_NORMAL(parallax,
POWER(10, ((LOG10(parallax_error)+1)*1.3)-1)) AS
parallax_obs
-- This adds observational noise to the true
parallaxes
FROM gedr3mock.main) AS sample
WHERE parallax_obs > 8
```
