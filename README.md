Using the DADA2 pipeline for sequence variant identification
============================================================

Here we provide a R Notebook template to generate a sequences variants table from FASTQ files generated by Paired-End Illumina sequencing.
For maximal reproducibility, we also provide a R project file and its associated packrat packages managment directory.

Note: Commands described in this documentation assume that you are using a Unix CLI.

## Resources

* Git clone URL: https://github.com/chuvpne/dada2.git
* Documentation: https://github.com/chuvpne/dada2
* DADA2: https://benjjneb.github.io/dada2
* illumina-utils: https://github.com/merenlab/illumina-utils
* docker image with pre-installed environment: https://hub.docker.com/r/chuvpne/pne-docker

## Rights

* Copyright (c) 2018 Service de Pneumologie, Centre Hospitalier Universitaire Vaudois (CHUV), Switzerland
* License: The R Notebook template (.Rmd) is provided under the MIT license (See LICENSE.txt for details)
* Authors: A. Rapin, C. Pattaroni, B.J. Marsland

## System requirements

* R 3.5.1: https://www.r-project.org
* R packrat package: https://cran.r-project.org/web/packages/packrat
* RStudio: https://www.rstudio.com
* GNU parallel: https://www.gnu.org/software/parallel
* illumina-utils: https://github.com/merenlab/illumina-utils
* git: https://git-scm.com/

R packages are managed using packrat. This ensures maximal reproducibility and portability of the analysis by using an encapsulated, version controlled, installation of R packages instead of any system-level R packages installation.

Note that additional system requirements as well as dependencies may be necessary for the installation of some R packages.

For maximal reproducibility and easy deployement across machines and platforms, you can open the R Notebook template using RStudio server running on a docker container. A suitable docker image satisfying all system requirements for using the provided R Notebook template is available at https://hub.docker.com/r/chuvpne/pne-docker. Instructions are further provided below in the <b>Working on docker</b> section.

## Files

* `R1.fastq.gz`: FASTQ file for the forward read
* `R2.fastq.gz`: FASTQ file for the reverse read
* `Index.fastq.gz`: FASTQ file for the index read
* `barcode_to_sample.txt`: A text file mapping index barcodes to samples
* A DADA2-formatted reference database (https://benjjneb.github.io/dada2/training.html). Per example, Silva version 132: `silva_nr_v132_train_set.fa.gz`

It is assumed that the FASTQ files were archived using gzip.

Please pay particular attention to the format of the files. The following points are critical:

* FASTQ files must use the Phred+33 quality score format and sequences headers (`@` lines) must fit the standard format of the CASAVA 1.8 output:

```
@EAS139:136:FC706VJ:2:2104:15343:197393 1:N:0:0
```

* The `barcode_to_sample.txt` file must contain two tab-delimited columns: the first for samples names and the second for samples barcodes as shown <a href="https://github.com/merenlab/illumina-utils/blob/master/examples/demultiplexing/barcode_to_sample.txt" target="_blank">here</a>. Avoid special characters.

If multiple sequencing runs have to be analyzed together, create a directory for each run and place the respective FASTQ and `barcode_to_sample.txt` files inside.

## Preparation

Get a copy of this repository and set it as your working directory:
```
$ git clone https://github.com/chuvpne/dada2.git my_project_dir
$ cd my_project_dir
```


If not done yet, get a copy of the DADA2-formatted reference database of your choice at https://benjjneb.github.io/dada2/training.html. We recommend to store it in a directory dedicated to databases instead of keeping it inside the main project directory.
Per example, create a `db` directory in your home directory and download the Silva version 132 reference database into it (Assuming wget is installed):
```
$ mkdir $HOME/db
$ wget https://zenodo.org/record/1172783/files/silva_nr_v132_train_set.fa.gz -P $HOME/db/
```


Place your FASTQ files and the `barcode_to_sample.txt` file in a directory, then place this directory within the directory named `run_data`.
The final directory structure should look like:
<pre>
my_project_dir
├── run_data
|   └── <b>run01</b>
|       ├── <b>R1.fastq.gz</b>
|       ├── <b>R2.fastq.gz</b>
|       ├── <b>Index.fastq.gz</b>
|       └── <b>barcode_to_sample.txt</b>
├── packrat
|   └── ...
├── dada2.Rmd
├── dada2.Rproj
├── LICENSE.txt
└── README.md
</pre>

If multiple sequencing runs have to be analyzed together, place each run directory inside the directory named `run_data`.
In this case, the final directory structure should look like:
<pre>
my_project_dir
├── run_data
|   ├── <b>run01</b>
|   |   ├── <b>R1.fastq.gz</b>
|   |   ├── <b>R2.fastq.gz</b>
|   |   ├── <b>Index.fastq.gz</b>
|   |   └── <b>barcode_to_sample.txt</b>
|   ├── <b>run02</b>
|   |   ├── <b>R1.fastq.gz</b>
|   |   ├── <b>R2.fastq.gz</b>
|   |   ├── <b>Index.fastq.gz</b>
|   |   └── <b>barcode_to_sample.txt</b>
.   .
.   .
.   .
|   └── <b>runNN</b>
|       ├── <b>R1.fastq.gz</b>
|       ├── <b>R2.fastq.gz</b>
|       ├── <b>Index.fastq.gz</b>
|       └── <b>barcode_to_sample.txt</b>
├── packrat
|   └── ...
├── dada2.Rmd
├── dada2.Rproj
├── LICENSE.txt
└── README.md
</pre>

## Usage

After loading the `dada2.Rproj` R project file in RStudio, open the `dada2.Rmd` R Notebook template in RStudio and follow the instructions in the text and comments.

## Working on docker

A docker image satisfying all system requirements for using the provided R Notebook template is available at https://hub.docker.com/r/chuvpne/pne-docker.
See https://github.com/chuvpne/pne-docker for the documentation.
This docker image comes with pre-installed R, RStudio server and illumina-utils. The R packrat package is also pre-installed and all system requirements for the installation and usage of the R dada2 package are met.

The DADA2-formatted reference database must be mounted on the docker container.
To do that, add the following option to the `docker run` command documented at https://github.com/chuvpne/pne-docker:

`-v /path/to/train_set.fa.gz/directory:/home/$USER/db:ro`

Replace `/path/to/train_set.fa.gz/directory` by the right path to the directory containing the reference database. Per example: `$HOME/db/silva`.

