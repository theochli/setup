# Anaconda Set Up

**Purpose**: Documenting the steps I took to set up Anaconda, environments and 
packages that I need, on my Ubuntu machine.

```
cd /tmp
curl -O https://repo.continuum.io/archive/Anaconda3-5.0.1-Linux-x86_64.sh
```
This uses curl to fetch the download from the Anaconda website.

Now, do a checksum to make sure there were no errors:

```
sha256sum Anaconda3-5.0.1-Linux-x86_64.sh
```

The output wil be a hex string, followed by the distribution name. Check that
the output matches the hashes [here](https://docs.continuum.io/anaconda/hashes/lin-3-64)

Now, run the set up script:

```
bash Anaconda3-5.0.1-Linux-x86_64.sh
```

and go through the sets. Once the installation has finished, this question will come up:

```
Do you wish the installer to prepend the Anaconda3 install location
to PATH in your /home/sammy/.bashrc ? [yes|no]
[no] >>> 
```

type `yes` to use the `conda` command.

To activate the installation:

```
source ~/.bashrc
```

## Setting up Environments
Setting up environments allows for more control of which packages are installed for 
which versions of Python, and when they are being used (to limit conflicts that may 
occur between dependencies).

```
conda list
conda search "^python$"
conda create --name env3 python=3
```

The last line above creates an environment for python 3. To activate this environment:

```
source activate env3
```

Now, you should see:

```
(env3) theo@thinkpad:~$
```

## Installing Basemap
Everytime I try installing Basemap, I get a lot of errors, and they do not seem to be the same everytime.
Below are some things to try:

- It seems that the version of matplotlib that ships with Anaconda may not come with mpl_toolkits. Try uninstalling and reinstalling

```
pip uninstall matplotlib
sudo apt-get install python3-matplotlib
sudo apt-get install python-tornado
sudo pip install https://github.com/matplotlib/basemap/archive/master.zip
sudo pip install --upgrade pip
sudo apt-get remove python3-matplotlib
conda install matplotlib
```

Sometimes the errors will indicate something next to try. For example, getting SSL errors and something about
tornado made me try the command `sudo apt-get install python-tornado`. Installing python3-matplotlib on its own
installed some other dependencies that I probably needed. Uninstalling removed matplotlib, then using conda to install
brought it back -- with a bunch of other dependences.

### Correcting an Error in Basemap

In the following example code:

```
from mpl_toolkits.basemap import Basemap
import matplotlib.pyplot as plt


map = Basemap(projection='ortho', 
              lat_0=0, lon_0=0)

map.drawmapboundary(fill_color='aqua')
map.fillcontinents(color='coral',lake_color='aqua')
map.drawcoastlines()

plt.show()
```

there is an error for `map.fillcontinents(color='coral',lake_color='aqua')`. This is because on 1623 in `__init__.py`
and line 1767, the program calls a deprecated matplotlib command `ax.get_axis_bgcolor()`. This needs to be changed to:
`ax.get_facecolor()`. From the GitHub project, it looks like this has been fixed in the code. Not sure why the code I pulled 
using pip install did not include this update. I updated it manually.
## Other packages that I have installed:

```
pip install nass
conda install fiona
pip install simpledbf
conda install seaborn
```


# change iPyknernel to Python2
I had another problem: I suspect that some of the Basemap functions are not working because I am working in Python3, and
they require Python2. ipython is only allowing me to choose a Python3 kernel, so I did the following (from this [link](https://ipython.readthedocs.io/en/stable/install/kernel_install.html)):

```
conda create -n ipykernel_py2 python=2 ipykernel
source activate ipykernel_py2
python -m ipykernel install --user
jupyter notebook
```

But after doing that I found that the ipykernel_py2 did not have the packages I previously installed, so re-do some installations:

```
pip install nass
conda install pandas
conda install matplotlib

# Install basemap from tarball and test installation
#  may need to install pyproj if simpletest.py fails
cd ~/Downloads/basemap-1.1.0
python setup.py install
conda install pyproj
cd examples
python simpletest.py

# other packages I need
pip install simpledbf
conda install conda
conda install fiona
pip install pyshp
```


