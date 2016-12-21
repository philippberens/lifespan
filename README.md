# lifespan
Re-analysis of data from Dong et al.: [Evidence for a limit to human lifespan] (http://www.nature.com/nature/journal/vaop/ncurrent/full/nature19793.html)

[Philipp Berens](http://www.berenslab.org) and [Tom Wallis](http://tomwallis.info)

In their paper, Dong et al. claim to provide statistical evidence for an absolute limit to human lifespan. Here we re-analyze the data from their central figure using model comparison. The data from the central figure of the paper provides no support for or against their model of saturation / decline relative to a simple linear increase, but supplementary data recovered from [the Gerontological Research Group](http://www.grg.org/Adams/A.HTM) *does* provide evidence for their model (see our [R report](analysis.md)). 

We also used the more appropriate framework of Extreme Value Theory to model the data [here](extreme_value_analysis.md). While the EVA models clearly fit the data better then the linear models, they did not change the core conclusion above: the GRG data provide evidence for the authors' trend break model over a linear model. The data from Dong et al Fig 2a conversely provide support for the linear model.

In our opinion, such statistical comparisons should have been part of the Dong et al. paper. We contacted Dong et al. about our analysis. Regarding the EVA, they replied "you analyzed the maximum of the MRAD (please correct us if we are wrong) which is different from the “average” of the MRAD we analyzed in the paper". We are not sure what that means. Also, regarding the missing data from the GRG database, Dong et al. explain that they "used the data for the oldest record holders for the GRG database. So the “missing” is due to the fact that the MRAD persons were younger than Jeanne Calment who was holding the record for the world’s oldest person at the time". If MRAD is maximum reported age at death in a given year, we don't see how the explanation by the authors matters.

Finally, Adam Lenart pointed out that the maximum over samples from some distribution depends on the number of samples [here](https://github.com/adamlenart/rstuff/blob/master/supercentenarian/sample_size.md). As the number of entries in the GRG database grows until the mid-ninties and is roughly constant since then, this likely explains the effect Dong et al. observe in our data. 

A good collection of links can be found on [pubpeer](https://pubpeer.com/publications/48DAAE1F561C62D32E477F193462E4).

[![DOI](https://zenodo.org/badge/70421471.svg)](https://zenodo.org/badge/latestdoi/70421471)


