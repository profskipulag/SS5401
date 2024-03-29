# SS5401 fetch_and_simulate.py
A package for managing batch runs of Fall3D, in order to generate training data for DT-GEO WP5 DTC4. The package consists of `pyf3d`, a package that contains some helper classes, including

 * `Fall3DInputFile`, a class that is an in-memory representation of a Fall3D.inp input file for
   * generating new input files from scratch
   * reading existing files and modifying them
   * writing them to disk
   * performing type and value checks of file values
   * various visualisations
 * `CARRASource`, a class for automatically fetching the appropriate meteodata and managing local storage to prevent spurious orders, and writing the appropriate meteo input data for Fall3D
 * `Fall3DBatch`, a class that takes a base Fall3D input file and a pandas dataframe of parameter values and generates a large number of runs, one for each row of the dataframe.

The useage of these classes is illustarted in the Jupyter notebook `notebook.ipynb`, and the script `fetch_and_simulate.py` is the application of the library for the purposes of DTC4. 

## To do
 * visualisations for Fall3D input files
 * ERASource, GFSSource, etc..
 
## Package structure


    SS5401/
    ├── docker-compose.yaml                     - orchestrates containers, ports, volumes
    ├── Dockerfile                              - specifies the container that runs the API
    ├── .dockerignore                           - files to be excluded from the build context
    ├── environment.yaml                        - the required python packages
    ├── .github                                 - github workflows (coverage, CI, etc.)
    │   └── workflows
    │       └── python-package.yml
    ├── .gitignore                              - files to be ignored by git (e.g. .cdsapirc)
    ├── mnt
    │   ├── archive                             - local storage for reanalysis data from CDS
    │   │   └── README.md
    │   ├── aux                                 - extra files needed
    │   │   ├── CARRA_orography_west.nc         - USER SUPPLIED any CARRA dataset with full lat lon grids for reprojection    
    │   │   └── README.md
    │   ├── examples                            - example input files
    │   │   ├── default_so2_reykjanes.inp       - example input file used for SO2 dispersion
    │   │   ├── README.md
    │   │   └── stations.pts                    - example inpui file specifying station locationsa
    │   ├── runs                                - where the training data will be stored
    │   │   └── README.md
    │   └── secrets
    │       ├── .cdsapirc                       - USER SUPPLIED cdsapi login credentials 
    │       └── README.md
    ├── notebook.ipynb                          - notebook illustrating example usage (note requires manual setup of environment with conda)
    ├── pyf3d                                   - package for manipulating Fall3D input files and generating random batch runs
    │   ├── __init__.py
    │   ├── fstrings.py                         - formatting strings for writing a Fall3D input file
    │   ├── regex_strings.py                    - regex strings for reading a Fall3D input file
    │   └── source.py				- class definitions for file sections, the input file, meteo data sources, batch runs
    ├── README.md                               - this file
    ├── fetch_and_simulate.py                   - the script that calls pyf3d to generate input files and runsa Fall3D
    └── test_pyf3d.py                           - unit tests - run with pytest


## To download the repository
Clone the repository to your machine

    git clone https://github.com/profskipulag/SS5401.git

You will be asked for your username and password. For the password github now requires a token:
- on github, click yur user icon in the top right corner
- settings -> developer settings -> personal access tokens -> Tokens (classic) -> Generate new token -> Generate new token (classic) 
- enter you authentifcation code
- under note give it a name, click "repo" to select al check boxes, then click generate token
- copy result enter it as password

## To run the jupyter notebook
Create a new conda environment from the environment.yaml file:

    conda env create -f environment.yaml

Activate the environment

    conda activate ss5401
    
Launch the notebook server

    jupyter notebook
    
Navigate to the SS5401 directory and click the file `notebook.ipynb` to launch it.

## To run the docker container
The user needs to provide two files, a netcdf file of the whole of the west CARRA domain (e.g. orography)

    mnt/aux/CARRA_orography_west.nc
    
which can be easily downloaded from Copernicus Climate Data Center (CDS) here https://cds.climate.copernicus.eu/cdsapp#!/dataset/reanalysis-carra-single-levels?tab=form. The software also needs the users CDS API key, stored as `.cdsapirc` - see https://cds.climate.copernicus.eu/api-how-to for more details .

    mnt/secrets/.cdsapirc
    
then in the SS5401 directory edit `fetch_and_simulate.py` to generate random Fall3D runs of your choice, and run:

    sudo docker compose up --build 
    
The directory `mnt` is mounted in to the container from the host filesystem so results will persist and the container finihses. Data retrieved from CDS wil be stored in 

    mnt/archive
    
Fall3D runs are stored in

    mnt/runs/batch/name/[run0, run1, run2, ..., runN]
    
## To run unit tests
Create the conda environment if you haven't already

    conda env create -f environment.yaml

Activate the environment

    conda activate ss5401
    
Run `pytest`

    pytest test_pyf3d



    
  
