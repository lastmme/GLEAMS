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

- Wout Bittremieux, Damon H. May, Jeffrey Bilmes, William Stafford Noble. **A learned embedding for efficient joint analysis of millions of mass spectra.** _Nature Methods_ 19, 675–678 (2022). [doi:10.1038/s41592-022-01496-1](https://doi.org/10.1038/s41592-022-01496-1)

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

For detailed usage information, see the command-line help messages:

```
gleams --help
gleams embed --help
gleams cluster --help
```

### Spectrum embedding

GLEAMS provides the `gleams embed` command to convert MS/MS spectra in peak files to 32-dimensional embeddings. Example:

```
gleams embed *.mzML --embed_name GLEAMS_embed
```

This will read the MS/MS spectra from all matched mzML files and export the results to a two-dimensional NumPy array of dimension _n_ x 32 in file `GLEAMS_embed.npy`, with _n_ the number of MS/MS spectra read from the mzML files.
Additionally, a tabular file `GLEAMS_embed.parquet` will be created containing corresponding metadata for the embedded spectra.

### Embedding clustering

After converting the MS/MS spectra to 32-dimensional embeddings, they can be clustered to group spectra with similar embeddings using the `gleams cluster` command. Example:

```
gleams cluster --embed_name GLEAMS_embed --cluster_name GLEAMS_cluster --distance_threshold 0.3
```

This will perform hierarchical clustering on the embeddings with the given distance threshold.
The output will be written to the `GLEAMS_cluster.npy` NumPy file with cluster labels per embedding (`-1` indicates noise, minimum cluster size 2).
Additionally, a file `GLEAMS_cluster_medoids.npy` will be created containing indexes of the cluster representative spectra (medoids).

### Advanced usage

Full configuration of GLEAMS, including various configurations to train the neural network, can be modified in the `gleams/config.py` file.

Frequently Asked Questions
--------------------------

**I get a "This repository is over its data quota" error when trying to install GLEAMS. Help!**

Sometimes you can get the following error when trying to install GLEAMS following the instructions above:

> **Warning**
> This repository is over its data quota. Account responsible for LFS bandwith should purchase more data packs to restore access.

This is caused by many people downloading GLEAMS recently, running out of Git LFS bandwith used to download the model weights.

You can circumvent this error by cloning the repository and downloading the weights file manually:

1. Create and activate a new Conda environment with the necessary dependencies, as described in step 1 above.
2. Clone the GLEAMS repository _while skipping the model weights_:
```
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/bittremieux/GLEAMS.git
```
3. Manually download the [GLEAMS model weights from the latest release](https://github.com/bittremieux/GLEAMS/releases/download/v0.3/gleams_82c0124b.hdf5).
4. Move the model weights file to the `data/` directory in the cloned GLEAMS repository.
5. From the root of the GLEAMS repository (e.g. `cd GLEAMS`), use pip to install GLEAMS:
```
pip install .
```

**Where can I find the GLEAMS training data?**

GLEAMS was trained on 30 million PSMs from the [MassIVE-KB (v1) dataset](https://massive.ucsd.edu/ProteoSAFe/static/massive-kb-libraries.jsp).
As this is a very large dataset, the spectra are not readily available as a single download.

To compile the full training dataset, on the [MassIVE website](https://massive.ucsd.edu/ProteoSAFe/static/massive.jsp), go to [MassIVE Knowledge Base](https://massive.ucsd.edu/ProteoSAFe/static/massive-kb-libraries.jsp) > [Human HCD Spectral Library](https://massive.ucsd.edu/ProteoSAFe/status.jsp?task=82c0124b6053407fa41ba98f53fd8d89) > [All Candidate library spectra](https://massive.ucsd.edu/ProteoSAFe/result.jsp?task=82c0124b6053407fa41ba98f53fd8d89&view=candidate_library_spectra) > Download.
This will give you a zipped TSV file with the metadata and peptide identifications for all 30 million PSMs.
Using the filename (column "filename") you can then [retrieve the corresponding spectra from the MassIVE FTP server](https://github.com/bittremieux/GLEAMS/blob/master/gleams/metadata/metadata.py#L176) and extract the required spectra using their scan number (column "scan").

These 30 million PSMs are a subset of all spectrum identifications that were obtained during compilation of the MassIVE-KB resource (maximum top 100 PSMs for 2.1 million unique precursors).
All 185 million PSMs at 1% FDR obtained using MSGF+, as described by [Wang _et al._](https://doi.org/10.1016/j.cels.2018.08.004), can be retrieved in multiple mzTab files from MassIVE.
To do so, extract all unique search task identifiers in the "proteosafe_task" column from the previously downloaded metadata TSV file.
Next, use these identifiers to compile the following URLs: `https://proteomics2.ucsd.edu/ProteoSAFe/result.jsp?task=[ID]&view=view_result_list` by replacing `[ID]` by each identifier.
Finally, on each search task web page, click "Download" to download a zip file that contains all PSMs for that search task in the mzTab format.

The full 669 million spectra that were processed using GLEAMS are all spectra in peak files listed in the MassIVE-KB metadata TSV file.

Contact
-------

For more information you can visit the [official code website](https://github.com/bittremieux/GLEAMS) or send an email to <wbittremieux@health.ucsd.edu>.
