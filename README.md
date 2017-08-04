# WFIRST Simulation Tools

The WFIRST team at STScI has developed an [exposure time calculator](http://www.stsci.edu/wfirst/software/Pandeia) and [a PSF model](http://www.stsci.edu/wfirst/software/webbpsf) for the science community to plan how they will use WFIRST. These tools are available separately as the [Pandeia exposure time calculator engine](http://www.stsci.edu/wfirst/software/Pandeia) and [WebbPSF point spread function modeling package](http://www.stsci.edu/wfirst/software/webbpsf), but are provided here with comprehensive setup documentation for local installation as well as tutorials on their use.

**To stay abreast of changes and make sure you always have the latest WFIRST simulation tools, you may wish to [subscribe to our mailing list](https://maillist.stsci.edu/scripts/wa.exe?SUBED1=WFIRST-TOOLS&A=1).** This list is low-traffic and only for announcements.

## Tutorial notebooks

The tutorials are stored as Jupyter Notebooks--documents which interleave code, figures, and prose explanations--and can be run locally once you have followed the setup instructions below. They can also be viewed in a browser.

  * [WebbPSF-WFIRST Tutorial](./blob/master/notebooks/WebbPSF-WFIRST_Tutorial.ipynb) -- Simulate a PSF for the WFIRST Wide-Field Instrument by selecting a detector position. Evaluate PSF differences between two detector positions. Shows both the WebbPSF notebook GUI and a brief example of performing calculations with the API.
  * [Pandeia-WFIRST Imaging](./blob/master/notebooks/Pandeia-WFIRST%20Imaging.ipynb) -- Calculate exposure times and simulate detector "postage stamps" for scenes made up of point sources and extended sources.
  * [GalSim WFIRST Demo](./blob/master/notebooks/WFIRST%20GalSim%20Demo.ipynb) -- Simulate a sample of galaxies drawn on a single WFIRST Wide-Field Instrument detector. Derived from `demo13.py` in version 1.4.4 of the GalSim project. (**Note:** This uses the GalSim PSF model for WFIRST, which makes simplifying assumptions for speedier computation relative to WebbPSF.)

## Play with the tools in a temporary environment in the cloud

We have automated the setup of a temporary evaluation environment for community users to evaluate the WFIRST Simulation Tools from STScI. This depends on a free third-party service called Binder, currently available in beta (without guarantees of uptime).

To launch in Binder *(beta)*, follow this URL: https://beta.mybinder.org/v2/gh/spacetelescope/wfirst-tools/master

It may take a few minutes to start up. Feel free to explore and run example calculations. Launching an environment through Binder will always use the most recent supported versions of our tools.

Simulation products can be saved and retrieved through the file browser, but the environment is **temporary**. After a certain time period, the entire environment will be shut down and the resources returned to the cloud whence it came.

If you wish to save code or output products, you **must** download them from the Jupyter interface. (Or, better yet, switch to a local installation of the tools!)

## Run locally in a container with Docker

1. Start by installing the free [Docker Community Edition](https://www.docker.com/community-edition) locally. This will make the `docker` command available in your terminal.
2. Clone this repository to a folder on your computer and `cd` into it.
3. Execute `./run.sh` to build and start a Docker container. (The first time you build the container, you will have to download a lot of data files, but subsequent builds will be quick.) You should see a lot of output, ending with something like:

   ```
   [C 12:34:56.000 NotebookApp]

       Copy/paste this URL into your browser when you connect for the first time,
       to login with a token:
           http://localhost:8888/?token=aabbccddeeff00112233445566778899
   ```

  Open that URL in a browser, and you'll see a Jupyter notebook interface to an environment with the tools available. (The `run.sh` script forwards `localhost:8888` to the same port in the container, so you can copy the URL as-is.)

**To stay abreast of changes and make sure you always have the latest WFIRST simulation tools, you may wish to [subscribe to our mailing list](https://maillist.stsci.edu/scripts/wa.exe?SUBED1=WFIRST-TOOLS&A=1).** This list is low-traffic and only for announcements.

### Keeping your environment up to date

From time to time, we will release new versions of the tools or new notebooks. You can clone a fresh copy by following the instructions again, or use `git pull` from a terminal in the repository folder. (If you've run or modified your copies of the notebooks, you may want to make copies so your changes aren't clobbered. Use `git checkout .` to discard changes or `git stash` to temporarily stash them before `git pull`-ing.)

## Install the simulation tools locally

The WebbPSF point-spread function model and Pandeia exposure time calculator engine are currently available for local installation by members of the science community. The required packages are distributed as part of Astroconda, a suite of astronomy-focused software packages for use with the [conda](https://conda.io/docs/) package manager for macOS and Linux.

*STIPS, the Space Telescope Image Product Simulator, is not currently available for local installation. See the page at http://www.stsci.edu/wfirst/software/STIPS for information on obtaining access to STIPS.*

### Before we begin

Astroconda depends on [conda](https://conda.io/docs/), a system that can manage multiple environments without letting packages in one clobber those in another. To accomplish this, it uses features of bash, the default shell on new Mac and Linux systems. Verify that you are running bash by running `ps` in a new terminal window and verifying that `bash` appears in the `CMD` column.

If you are using another shell, bear in mind that you **must** start a bash login shell (`bash -l`) to follow this guide and to run the simulation tools in a `conda` environment.

### Installing Astroconda

If you have already installed Astroconda, skip ahead to "Creating a WFIRST Tools environment".

The [Getting Started](http://astroconda.readthedocs.io/en/latest/getting_started.html) instructions for Astroconda cover setting up the conda package manager and certain environment variables. Enable the Astroconda channel with the command `conda config --add channels http://ssb.stsci.edu/astroconda` (as explained in the [Selecting a Software Stack](http://astroconda.readthedocs.io/en/latest/installation.html#configure-conda-to-use-the-astroconda-channel) document).

The WFIRST Simulation Tools suite includes Pandeia, an exposure time and signal-to-noise calculator that (for now) depends on Python 2.7. To create a Python 2.7 environment for WFIRST Simulation Tools, use the following command:

```
conda create -n wfirst-tools --yes python=2.7 numpy scipy astropy \
                                   ipython-notebook ipykernel \
                                   pyfftw pysynphot photutils \
                                   webbpsf webbpsf-data
```

This will create an environment called `wfirst-tools` containing the essential packages for WFIRST simulations. To use it, you must activate it every time you open a new terminal window. Go ahead and do that now:

```
source activate wfirst-tools
```

Next, create a new directory somewhere with plenty of space to hold the reference files and navigate there in your terminal (with `cd /path/to/reference/file/space` or similar).

### Installing synthetic photometry reference information

To obtain the [reference data](http://pysynphot.readthedocs.io/en/latest/#installation-and-setup) used for synthetic photometry, you will need to retrieve them via FTP. The `curl` command line tool can be used as follows to retrieve the archives:

```
curl -OL ftp://ftp.stsci.edu/cdbs/tarfiles/synphot1.tar.gz    # 85 MB
curl -OL ftp://ftp.stsci.edu/cdbs/tarfiles/synphot2.tar.gz    # 34 MB
curl -OL ftp://ftp.stsci.edu/cdbs/tarfiles/synphot5.tar.gz    # 505 MB
```

This retrieves interstellar extinction curves, several spectral atlases, and a grid of stellar spectra derived from [PHOENIX](http://www.hs.uni-hamburg.de/index.php?option=com_content&view=article&id=14&Itemid=294&lang=en) models. Extract them into the current directory:

```
tar xvzf ./synphot1.tar.gz
tar xvzf ./synphot2.tar.gz
tar xvzf ./synphot5.tar.gz
```

This will create a tree of files rooted at `grp/hst/cdbs/` in the current directory.

(Instructions for installing the full set of PySynphot reference data, including things like HST instrument throughput reference files, can be found [in the PySynphot documentation](http://pysynphot.readthedocs.io/en/latest/index.html#installation-and-setup).)

### Installing the Pandeia engine

Pandeia is available through PyPI (the Python Package Index), rather than Astroconda. Fortunately, we can install it into our `wfirst-tools` environment with the following command:

```
pip install pandeia.engine==1.1
```

Note that the `==1.1` on the package name explicitly requests version 1.1, which is the version that is compatible with the bundled reference data.

Pandeia also depends on a collection of reference data to define the characteristics of the JWST and WFIRST instruments. Download it (1.6 GB) as follows and extract:

```
curl -OL https://github.com/spacetelescope/wfirst-tools/raw/master/pandeia_wfirst_data.tar.gz
tar xvzf ./pandeia_wfirst_data.tar.gz
```

This creates a folder called `pandeia_wfirst_data` in the current directory.

## Running the simulation tools locally

WebbPSF, Pandeia, and pysynphot all depend on certain environment variables to determine the paths to reference data.

You may wish to save these variables in your `~/.bash_profile` file, or [a new conda/activate.d/ script](https://conda.io/docs/using/envs.html#saved-environment-variables) so they are always set when you go to run the WFIRST simulation tools.

### Configuring environment variables

Where you see `$(pwd)` in the following commands, substitute in the directory where you have chosen to store the reference data (e.g. `echo "$(pwd)"` becomes `echo "/path/to/reference/file/space"`).

Configure the PySynphot CDBS path:

```
export PYSYN_CDBS="$(pwd)/grp/hst/cdbs"
```

To test that pysynphot can find its reference files, use the following command:

```
python -c "import warnings; warnings.simplefilter('ignore'); import pysynphot; print pysynphot.Icat('phoenix', 5750, 0.0, 4.5).name"
```

If you see "phoenix(Teff=5750,z=0,logG=4.5)" appear in your terminal, pysynphot and its reference data files have been installed correctly.

Next, configure the Pandeia path:

```
export pandeia_refdata="$(pwd)/pandeia_wfirst_data"
```

To test that Pandeia can find its reference files, use the following command:

```
python -c 'from pandeia.engine.wfirst import WFIRSTImager; WFIRSTImager(mode="imaging")'
```

If you do not see any errors, Pandeia was able to instantiate a WFIRST WFI model successfully.

### Viewing and running these example notebooks

In a terminal where you have run `source activate wfirst-tools` and set the above environment variables, navigate to the directory where you would like to keep the example notebooks and clone this repository from GitHub:

```
git clone git@github.com:josePhoenix/wfirst-tools.git
```

This will create a new folder called `wfirst-tools` containing this README and all of the example notebooks. From this directory, simply run `jupyter notebook`. Choose `Getting Started.ipynb` from the file list, and explore the available examples of WebbPSF and Pandeia calculations.

## Resources

The STScI helpdesk at help@stsci.edu is available for members of the WFIRST scientific community. For issues with WebbPSF, we prefer that you report your issues in the GitHub issue tracker for the speediest response: https://github.com/mperrin/webbpsf/issues (choose the green "New Issue" button after logging in).

