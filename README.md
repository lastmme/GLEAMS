GLEAMS
======

![GLEAMS](gleams_logo.png)

For more information:

* [Official code website](https://github.com/bittremieux/GLEAMS)

GLEAMS is a Learned Embedding for Annotating Mass Spectra.
GLEAMS encodes mass spectra as vectors of features and feeds them to a neural network to embed them into a 32-dimensional space in which spectra generated by the same peptide are close together.
It then detects spectrum clusters of spectra generated by the same peptide.

The software is available as open-source under the BSD license.

If you use GLEAMS in your work, please cite the following publication:

- Wout Bittremieux, Damon H. May, Jeffrey Bilmes, William Stafford Noble. **A learned embedding for efficient joint analysis of millions of mass spectra.** _bioRxiv_ (2021). [doi:10.1101/483263](https://doi.org/10.1101/483263)

Installation
------------

GLEAMS requires Python 3.8, a Linux operating system, and a CUDA-enabled GPU.

1. Create a Conda environment and install the necessary compiler tools and GPU runtime:
```
conda env create -f https://raw.githubusercontent.com/bittremieux/GLEAMS/master/environment.yml && conda activate gleams
```
2. Install GLEAMS:
```
pip install git+https://github.com/bittremieux/GLEAMS.git
```

Using GLEAMS
------------

GLEAMS provides command-line functionality to convert MS/MS spectra in peak files to 32-dimensional embeddings. Example:

```
gleams *.mzML
```

This will read the MS/MS spectra from all matched mzML files and export the results to a two-dimensional NumPy array of dimension _n_ x 32, with _n_ the number of MS/MS spectra read from the mzML files.
Additionally, a tabular file in the Parquet format will be created containing corresponding metadata for the embedded spectra.

For more information, see the command-line help message:

```
gleams --help
```

Contact
-------

For more information you can visit the
[official code website](https://github.com/bittremieux/GLEAMS) or send an email to <wbittremieux@health.ucsd.edu>.
 
