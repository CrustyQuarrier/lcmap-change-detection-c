## LCMAP Change Detection

This project contains application source code for Change Detection C library
and related scripts.


## Installation

To install, simply run the top-level ``make`` target:

```bash
$ git clone git@github.com:USGS-EROS/lcmap-change-detection-c.git
$ cd lcmap-change-detection-c
$ make

ccdc depends on the GNU Scientific Library (GSL)
(https://www.gnu.org/software/gsl/).
These libs are used to replace some of the mathematical funcions in the
proprietary Matlab (mathworks.com) version.  A local copy of multirobust.c
has been modified and used so other applications can link to a clean copy of
the gsl lib.
```

The executables and scripts will be installed into ``./bin`` by default. This
can be overridden by setting a ``BIN`` environment variable or using a ``BIN``
variable when running the target:

```bash
$ BIN=/my/path/bin make
```

For additional notes, such as installing dependencies (Ubuntu), overriding
``Makefile`` variables, etc., see:

* [Building CCDC](../..//wiki/Building-CCDC)


## Usage

[We're in active development on making this not only work, but be usable.
Ticket #5 has some early usability notes + tasks that we're trying to hit right
away, if you're interested in tracking this.]

Continuous Change Detection and Classification
Version 05.04, topic/matlab-parity

usage:
ccdc --row=<input row number> --col=<input col number> --num-rows=<number of rows> --num-cols=<number of columns> [--in-path=<input directory>] [--out-path=<output directory>] [--data-type=tifs|bip] [--scene-list-file=<file with list of sceneIDs>] [--verbose]

where the following parameters are required:
    --row=: input row number
    --col=: input col number

and the following parameters are optional:
    --num-rows=: input number of rows
    --num-cols=: input number of columns
    --in-path=: input data directory location
    --out-path=: directory location for output files
    --data-type=: type of input data files to ingest
    --scene-list-file=: file name containing list of sceneIDs (default is all files in in-path)
    -verbose: should intermediate messages be printed? (default is false)

ccdc --help will print the usage statement


Example:
ccdc --row=3845 --col=2918 --num-rows=1 --num-cols=1 --in-path=/data/user/in --out-path=/home/user/out --data-type=bip --scene-list-file=/home/user/scene_list.txt --verbose

An example of how to pipe input from stdin and output to stdout:
ccdc --row=3845 --col=2918 --in-path=stdin --out-path=stdout --verbose < pixel_value_text_file.txt > coeffs_results_text_file.txt

The stdout option eliminates the creation of the output binary file,
coeffs are just printed to stdout.  This can be re-directed to a text file, or piped to another program.

Note: Previously, the ccdc had to be run from the directory where the input data are located.
      Now, input and output directory location specifications are used.
      If in-path or out-path are not specified, current working directory is assumed.
      If scene-file-name is not specified, all scenes in in-path are processed.


## Input Data

The filesystem data located in in-path is expected to be gridded Landsat
Analysis Ready Data (ARD), co-registered and radiometrically correlated.
For landsat 8, Surface reflectance (SR) bands 2-7 are used for the spectral
image bands, and Top Of Atmosphere (TOA) band 10 is use for the thermal band,
in addition to the Cloud Mask band (cfmask). for Landsats 4, 5, and 7 SR bands
1-5 and 7 are used for spectral imagery, and TOA band 6 for thermal data.

For --data-type=bip, these bands are stacked into a single
Band Interleaved by Pixel (BIP) format file, with an ENVI style header file
with the same name.  The base of the file name is the sceneID.  There is
a pair of these files for each sceneID defined in the input specifications.
This data format is compatible with the original matalb-developed application.

For example:

LC80440272013106/LC80440272013106LGN01_MTLstack
LC80440272013106/LC80440272013106LGN01_MTLstack.hdr
.......


For --data-type=tifs, each band is in a separate .tif file, one set of band
files for each sceneID:

LC80440272013106LGN01_cfmask.img
LC80440272013106LGN01_sr_band2.hdr
LC80440272013106LGN01_sr_band2.img
LC80440272013106LGN01_sr_band3.img
LC80440272013106LGN01_sr_band4.img
LC80440272013106LGN01_sr_band5.img
LC80440272013106LGN01_sr_band6.img
LC80440272013106LGN01_sr_band7.img
LC80440272013106LGN01_toa_band10.img
.......


The values required for --in-path=stdin are a set values per line for each
input sceneID: Julian date (since 0); the 6 spectral band values; thermal band
value; and cfmask value.  For example:
2456397 3737 3578 3524 3833 1343 1377 2502 4
2456413 596 808 898 1408 220 173 2696 4
2456429 7745 7945 8147 8537 5463 4454 2649 4
2456445 94 156 164 758 807 492 2809 0
2456461 264 564 467 1680 1465 901 2866 0
2456477 150 299 245 1308 1108 650 2950 0
2456493 156 297 231 1377 1085 629 2965 0
2456509 2792 2171 2242 3050 1385 1158 2683 4
2456525 115 220 180 1014 809 465 2886 0
2456541 4732 4605 4570 4892 2174 2287 2349 4
2456557 73 85 65 410 208 100 2800 2
2456573 71 121 106 610 417 278 2774 0
2456589 54 81 64 229 170 101 2800 1
......


## Development

Development notes for C-CCDC are maintained in the project wiki. For more
details, see:

 * [CCDC Development](../../wiki/CCDC Development)
 * [Using CCDC with Docker](../../CCDC-%26-Docker)


## Implementation

### CCDC - Continuous Change Detection and Classification (Algorithm)

* <b>NOTE:</b> This algorithm is not validated and considered prototype.
* See [CCDC ADD](http://landsat.usgs.gov/documents/ccdc_add.pdf) for the
  detailed description.


## More Information

This project is hosted by the US Geological Survey (USGS) Earth Resources
Observation and Science (EROS) Land Change Monitoring, Assessment, and
Projection ([LCMAP](https://github.com/USGS-EROS?utf8=%E2%9C%93&query=lcmap))
Project.  For questions regarding this source code, please contact the
[Landsat Contact Us](https://landsat.usgs.gov/contactus.php) page and specify
``USGS LCMAP`` in the "Regarding" section.
