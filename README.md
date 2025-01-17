# seqNMF (GPU implementation)

This is a [JAX](https://github.com/google/jax) implementation of seqNMF, a tool for unsupervised discovery of temporal sequences in high-dimensional data. With GPU accelation, the code runs ~200-400X faster than the original Numpy implementation. 

- Credit for MATLAB toolbox: [Emily Mackevicius, Andrew Bahle, and the Fee Lab](http://web.mit.edu/feelab/).
- Credit for Numpy implementation and README: [Jeremy Manning](http://www.context-lab.com/)

### Description
SeqNMF uses regularized convolutional non-negative matrix factorization to extract repeated sequential patterns from high-dimensional data. The algorithm can discovery of patterns directly from timeseries data without reference to external markers or labels.

For more information please see:
- [**Original MATLAB implementation**](https://github.com/FeeLab/seqNMF)
- [**preprint**](https://www.biorxiv.org/content/early/2018/03/02/273128)
- [**COSYNE talk**](https://www.youtube.com/watch?reload=9&v=XyWtCtZ_m-8)
- [**video tutorial**](https://cbmm.mit.edu/video/unsupervised-discovery-temporal-sequences-high-dimensional-datasets) and [**materials**](https://stellar.mit.edu/S/project/bcs-comp-tut/materials.html)

### Installation
1. Install [JAX](https://github.com/google/jax)
2. Clone this repository and ```pip install -e [path/to/seqnmf-gpu]```


### Using the toolbox
Given a N (number of features) by T (number of timepoints) data matrix, `X`, the commands below may be used to factorize the data using seqNFM:
```
from seqnmf import seqnmf
W, H, cost, loadings, power = seqnmf(X, K=20, L=100, Lambda=0.001)
```
Here `K` is the (maximum) number of factors, `L` is (maximum) sequence length, and `Lambda` is a regularization parameter.  The data matrix is factorized into a tensor product of `W` and `H` as follows:

according to:
```
                                    ----------    
                                L  /         /|
                                  /         / |
        ----------------         /---------/  |          ----------------
        |              |         |         |  |          |              |
      N |      X       |   =   N |    W    |  /   (*)  K |      H       |           
        |              |         |         | /           |              |
        ----------------         /----------/            ----------------
               T                      K                         T
```
where `W` contains each of the `N` by `L` sequence factors and `H` describes the combination of sequences that are present at each timepoint.

The `cost` output stores the reconstruction error after each iteration.  The `loadings` variable stores the factor loadings.  The `power` variable provides a measure of how well the original data is captured by the full reconstruction.

The `plot` function may be used to visualize the discovered structure by calling `plot(W, H)`.

### Demo

An example dataset, ported from the MATLAB toolbox, is provided as part of the seqnmf Python toolbox.  To apply seqNMF to the example data and generate a plot, run:
```
from seqnmf import seqnmf, plot, example_data

[W, H, cost, loadings, power] = seqnmf(example_data, K=20, L=100, Lambda=0.001, plot_it=False)

plot(W, H).show()
```
