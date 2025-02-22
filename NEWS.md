
## NetCoMi 1.0.3 <img src="man/figures/NetCoMi_logo_800x400_300dpi.png" align="right" width="200" />

This is a minor release with some bug fixes and changes in the
documentation.

### Bug fixes

-   `netConstruct()` threw an error if the data had no row and/or column
    names, which is fixed.

-   An edge list is added to the output of `netConstruct()` (issue
    [\#41](https://github.com/stefpeschel/NetCoMi/issues/41)). See the
    help page for details.

-   `SPRING`’s fast version of latent correlation computation
    (implemented in [mixedCCA](https://github.com/irinagain/mixedCCA))
    is currently not available due to deprecation of the R package
    `chebpol`. The issue is fixed by setting the `netConstruct()`
    parameter `measurePar$Rmethod` internally to “original” if SPRING is
    used for association estimation.

-   In `plot.microNetProps()`: The `xpd` parameter is changed to `NA` so
    that plotting outside the plot region is possible (useful for
    legends or additional text).

-   Labels in the network plot can now be suppressed by setting
    `labels = FALSE` (issue
    [\#43](https://github.com/stefpeschel/NetCoMi/issues/43))

-   The `netCompare()` function threw an error if one of the permutation
    networks was empty, i.e. had no edges with weight different from
    zero (issue
    [\#38](https://github.com/stefpeschel/NetCoMi/issues/38)), which is
    now fixed.

-   Fix issues [\#29](https://github.com/stefpeschel/NetCoMi/issues/29)
    and [\#40](https://github.com/stefpeschel/NetCoMi/issues/40), where
    permutation tests did not terminate for small sample sizes. Now, if
    the possible number of permutations (resulting from the sample size)
    is smaller than that defined by the user, the function stops and
    returns an error.

-   Fix a bug in `diffnet()` (issue
    [\#51](https://github.com/stefpeschel/NetCoMi/issues/51)), where
    colors in differential networks could not be changed.

-   `diffnet()` threw an error if the `netConstruct()` argument
    `jointPrepro` was set to `TRUE`.

## NetCoMi 1.0.2

This release includes a range of new features and fixes known bugs and
issues.

### New features

#### Improved installation process

Packages that are optionally required in certain settings are not
installed together with `NetCoMi` anymore. Instead, there is a new
function `installNetCoMiPacks()` for installing the remaining packages.
If not installed via `installNetCoMiPacks()`, the required package is
installed by the respective NetCoMi function when needed.

#### installNetCoMiPacks()

New function for installing the R packages used in NetCoMi not listed as
`dependencies` or `imports` in NetCoMi’s description file.

#### netConstruct()

-   New argument `matchDesign`: Implements matched-group
    (i.e. matched-pair) designs, which are used for permutation tests in
    `netCompare()` and `diffnet()`. `c(1,2)`, for instance, means that
    one sample in the first group is matched to two samples in the
    second group. If the argument is not `NULL`, the matched-group
    design is kept when generating permuted data.

-   New argument `jointPrepro`: Specifies whether two data sets (of
    group one and two) should be preprocessed together. Preprocessing
    includes sample and taxa filtering, zero treatment, and
    normalization. Defaults to `TRUE` if `data` and `group` are given,
    and to `FALSE` if `data` and `data2` are given, which is similar to
    the behavior of `NetCoMi 1.0.1`. For dissimilarity networks, no
    joint preprocessing is possible.

-   `mclr(){SPRING}` is now available as normalization method.

-   `clr{SpiecEasi}` is used for centered log-ratio transformation
    instead of `cenLR(){robCompositions}`.

-   `"symBetaMode"` is accepted as list element of `measurePar`, which
    is passed to `symBeta(){SpiecEasi}`. Only needed for SpiecEasi or
    SPRING associations.

-   The pseudocount (if `zeroMethod = "pseudo"`) may be freely
    specified. In v1.0.1, only unit pseudocounts were possible.

#### netAnalyze()

-   Global network properties are now computed for the whole network as
    well as for the largest connected component (LCC). The summary of
    network properties now contains for the whole network only
    statistics that are not based on shortest paths (or, more generally,
    also meaningful for disconnected networks). For the LCC, all global
    properties available in NetCoMi are shown.

-   New global network properties (see the docu of `netAnalyze()` for
    definitions):

    -   Number of components (only whole network)
    -   Relative LCC size (only LCC)
    -   Positive edge percentage
    -   Natural connectivity
    -   Average dissimilarity (only meaningful for the LCC)
    -   Average path length (only meaningful for the LCC)

-   New argument `centrLCC`: Specifies whether to compute centralities
    only for the LCC. If `TRUE`, centrality values of disconnected
    components are zero.

-   New argument `avDissIgnoreInf`: Indicates whether infinite values
    should be ignored in the average dissimilarity. If `FALSE`,
    infinities are set to 1.

-   New argument `sPathAlgo`: Algorithm used for computing shortest
    paths

-   New argument `sPathNorm`: Indicates whether shortest paths should be
    normalized by average dissimilarity to improve interpretability.

-   New argument `normNatConnect`: Indicates whether to normalize
    natural connectivity values.

-   New argument `weightClustCoef`: Specifies the algorithm used for
    computing the global clustering coefficient. If `FALSE`,
    `transitivity(){igraph}` with `type = "global"` is used (similar to
    `NetCoMi 1.0.1`). If `TRUE`, the local clustering coefficient is
    computed using `transitivity(){igraph}` with `type = "barrat"`. The
    global clustering coefficient is then the arithmetic mean of local
    values.

-   Argument `connect` has been changed to `connectivity`.

-   Documentation extended by definitions of network properties.

#### summary.microNetProps()

-   New argument `clusterLCC`: Indicates whether clusters should be
    shown for the whole network or only for the LCC.

-   The `print` method for `summary.microNetProps` was completely
    revised.

#### plot.microNetProps()

-   All normalization methods available for network construction can now
    be used for scaling node sizes (argument `nodeSize`).

-   New argument `normPar`: Optional parameters used for normalization.

-   Usage of `colorVec` changed: Node colors can now be set separately
    in both groups (`colorVec` can be a single vector or a list with two
    vectors). Usage depends on `nodeColor` (see docu of `colorVec`).

-   New argument `sameFeatCol`: If `nodeColor = "feature"` and
    `colorVec` is not given, `sameFeatCol` indicates whether same
    features should have same colors in both groups.

-   Argument `colorNegAsso` has been renamed to `negDiffCol`. Using the
    old name leads to a warning.

-   New functionality for using the same layout in both groups (if two
    networks are plotted). In addition to computing the layout for one
    group and adopting it for the other group, a union of both layout
    can be computed and used in both groups so that nodes are placed as
    optimal as possible equally for both networks. This option is
    applied via `sameLayout = TRUE` and `layoutGroup = "union"`. Many
    thanks to [Christian L.
    Müller](https://github.com/muellsen?tab=followers) and [Alice
    Sommer](https://www.iq.harvard.edu/people/alice-sommer) for
    providing the idea and R code for this new feature!

#### netCompare()

-   New arguments for storing association and count matrices of the
    permuted data into an external file:

    -   `fileLoadAssoPerm`
    -   `fileLoadCountsPerm`
    -   `storeAssoPerm`
    -   `fileStoreAssoPerm`
    -   `storeCountsPerm`
    -   `fileStoreCountsPerm`

-   New argument `returnPermProps`: If `TRUE`, global network properties
    and the respective absolute group differences of the permuted data
    are returned.

-   New argument `returnPermCentr`: If `TRUE`, the computed centrality
    values and the respective absolute group differences of the permuted
    data are returned as list with a matrix for each centrality measure.

-   The arguments `assoPerm` and `dissPerm` are still existent for
    compatibility with `NetCoMi 1.0.1` but the former elements
    `assoPerm` and `dissPerm` are not returned anymore (matrices are
    stored in an external file instead).

#### createAssoPerm()

New function for creating association/dissimilarity matrices for
permuted count data. The stored count or association/dissimilarity
matrices can then be passed to `netCompare()` or `diffnet()` to decrease
runtime. The function also allows to generate a matrix permuted group
labels without computing associations. Using this matrix,
`createAssoPerm()` furthermore allows to estimate the permutation
associations/dissimilarities in blocks (by passing only a subset of the
permuted group matrix to `createAssoPerm()`).

#### summary.microNetComp()

Summary method has been adapted to the new network properties (analogous
to the summary of `microNetProps` objects, which are returned from
`netAnalyze()`)

#### diffnet()

-   New arguments for storing association and count matrices of the
    permuted data into an external file:

    -   `fileLoadAssoPerm`
    -   `fileLoadCountsPerm`
    -   `storeAssoPerm`
    -   `fileStoreAssoPerm`
    -   `storeCountsPerm`
    -   `fileStoreCountsPerm`

-   The argument `assoPerm` is still existent for compatibility with
    `NetCoMi 1.0.1` but the former element `assoPerm` is not returned
    anymore (matrices are stored in an external file instead).

-   Changed output: For permutation tests and Fisher’s z-test, a vector
    and matrix with p-values and the corresponding matrix with group
    differences are returned for both with and without multiple testing
    adjustment.

-   Documentation has been revised.

#### plot.diffnet()

-   New argument `adjusted`: Indicates whether the adjacency matrix
    (matrix with group differences) based on adjusted or unadjusted
    p-values should be plotted.

-   New argument `legendPos` for positioning the legend.

-   New argument `legendArgs` for specifying further arguments passed to
    `legend`.

#### colToTransp()

-   The function is now exported and its name has changed from
    `col_to_transp()` to `colToTransp()`. The function expects a color
    vector as input and adds transparency to each color.

### Bug fixes

The major issues fixed in this release are:

-   The following error is solved:
    `Error in update.list(...): argument "new" is  missing`. The error
    was caused by a conflict between `SpiecEasi` and `metagenomeSeq`, in
    particular by `gplot` as a dependency of `metagenomeSeq`. A former
    version of `gplot` was dependend on `gdata`, which caused the
    conflict. So, please update `gplot` and remove the package `gdata`
    to fix the error.

-   `sparcc()` from SpiecEasi package is now used for estimating SparCC
    associations. For some users, NetCoMi’s `Rccp` implementation of
    SparCC caused errors when installing NetCoMi. If these are fixed,
    the Rcpp implementation will be included again, so that users can
    decide between the two SparCC versions.

-   VST transformations are now computed correctly.

-   Error when plotting two networks, where one network is empty, has
    been fixed.
