<!-- github markdown built using 
render("README.Rmd", output_format = "md_document")
-->
SCORPIUS
========

[![Build Status](https://travis-ci.org/rcannood/SCORPIUS.png?branch=master)](https://travis-ci.org/rcannood/SCORPIUS)

**SCORPIUS an unsupervised approach for inferring developmental chronologies from single-cell RNA sequencing data.** In comparison to similar approaches, it has three main advantages:

-   **It accurately reconstructs trajectories for a wide variety of dynamic cellular processes.** The performance was evaluated using a new, quantitative evaluation pipeline, comparing the performance of current state-of-the-art techniques on 10 publicly available single-cell RNA sequencing datasets.

-   **It automatically identifies marker genes, speeding up knowledge discovery.**

-   **It is fully unsupervised.** Prior knowledge of the relevant marker genes or cellular states of individual cells is not required.

Installing SCORPIUS
-------------------

You can install the latest version from github with

``` r
devtools::install_github("rcannood/SCORPIUS")
```

<!--
You can install:

* the latest released version from CRAN with

    ```R
    install.packages("SCORPIUS")
    ```

* the latest development version from github with

    ```R
    devtools::install_github("rcannood/SCORPIUS")
    ```
-->
If you encounter a clear bug, please file a minimal reproducible example on [github](https://github.com/rcannood/SCORPIUS/issues).

Learning SCORPIUS
-----------------

This section describes the main workflow of SCORPIUS without going in depth in the R code. For a more detailed explanation, see the vignettes listed below.

To start using SCORPIUS, simply write:

``` r
library(SCORPIUS)
```

The `ginhoux` dataset (See Schlitzer et al. 2015) contains 248 dendritic cell progenitors in one of three cellular cellular states: MDP, CDP or PreDC.

``` r
data(ginhoux)
expression <- ginhoux$expression
group.name <- ginhoux$sample.info$group.name
```

With the following code, SCORPIUS reduces the dimensionality of the dataset and provides a visual overview of the dataset. In this plot, cells that are similar in terms of expression values will be placed closer together than cells with dissimilar expression values.

``` r
dist <- correlation.distance(expression)

# filter outliers
filt <- outlier.filter(dist)
expression <- expression[filt, ]
group.name <- group.name[filt]
dist <- dist[filt, filt]

# reduce dimensionality
space <- reduce.dimensionality(dist, ndim=2)
draw.trajectory.plot(space, group.name)
```

![](README_files/figure-markdown_github/reduce%20dimensionality-1.png)

To infer and visualise a trajectory through these cells, run:

``` r
traj <- infer.trajectory(space)
draw.trajectory.plot(space, group.name, traj$final.path)
```

![](README_files/figure-markdown_github/infer%20trajectory-1.png)

Finally, to identify and visualise candidate marker genes, execute the following code:

``` r
tafs <- find.trajectory.aligned.features(expression, traj$time, verbose=F)
expr.tafs <- tafs$smooth.x[,tafs$tafs]
modules <- extract.modules(expr.tafs)
draw.trajectory.heatmap(expr.tafs, traj$time, group.name, modules)
```

![](README_files/figure-markdown_github/find%20tafs-1.png)

Learning SCORPIUS, part 2
-------------------------

Vignettes with more elaborate examples are available:

-   [Investigating differentiating dendritic cell progenitors](vignettes/ginhoux.md): `vignette("ginhoux", package="SCORPIUS")`
-   [Inferring trajectories from simulated data](vignettes/simulated-data.md): `vignette("simulated-data", package="SCORPIUS")`

Related approaches
------------------

-   [Monocle](https://bioconductor.org/packages/release/bioc/html/monocle.html)
-   [Waterfall](http://dx.doi.org/10.1016/j.stem.2015.07.013)
-   [Embeddr](https://github.com/kieranrcampbell/embeddr)

References
----------

Schlitzer, Andreas, V Sivakamasundari, Jinmiao Chen, Hermi Rizal Bin Sumatoh, Jaring Schreuder, Josephine Lum, Benoit Malleret, et al. 2015. “Identification of cDC1- and cDC2-committed DC progenitors reveals early lineage priming at the common DC progenitor stage in the bone marrow.” *Nature Immunology* 16 (7): 718–26. doi:[10.1038/ni.3200](http://dx.doi.org/10.1038/ni.3200).
