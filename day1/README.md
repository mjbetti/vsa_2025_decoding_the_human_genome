# Day 1 (6/23/2025)
## Logging in to the Linux server
You can use the following command to log in to the Linux server:
```
ssh username@servername.org
```

## Navigating around the terminal
The following basic commands will help you navigate around the terminal environment:

* ```pwd``` - Prints out the path of your current working directory, telling you which directory you are currently in.
* ```ls``` - Lists all of the files and folders in your current working directory.
* ```cd``` - Changes your current working directory. \
    * ```cd ~``` - Changes to your home directory.
    * ```cd /path/to/directory``` - Changes to a specific path of your choosing.
    * ```cd ..``` - Moves you "up" one directory in the current path hierarchy.
* ```less``` - Allows you to "see" inside of a file to view its contents.
    * i.e. ```less example.txt```
* ```head``` - Print out the first few lines of a file
    * i.e. ```head example.txt``` prints out the first few lines of ```example.txt```
* ```tail``` - Print out the last few lines of a file
    * i.e. ```tail example.txt``` prints out the last few lines of ```example.txt```
* ```mv``` - Move a file from one path to another.
    * i.e. ```mv example.txt sample.txt``` changes the file name of ```example.txt``` to ```sample.txt```.
    * ```mv /home/example.txt /mnt/16tb``` moves the file ```example.txt``` from the directory ```/home``` to ```/mnt/16tb```.
* ```cp``` - Copy a file from one path to another
    * i.e. ```cp example.txt sample.txt``` creates a copy of ```example.txt``` called ```sample.txt```.
    * ```cp /home/example.txt /mnt/16tb``` copies the file ```example.txt``` to the directory ```/mnt/16tb```.
* ```nano``` - Opens a text editor to create a new file or modify an existing one.
    * i.e. ```nano test.py``` opens a Python script that can be created (if it doesn not yet exist) or modified (if it already exists).
* ```rm``` - Deletes a file or directory. BE CAREFUL, as any deletion is permanent and cannot be undone.
    * i.e. ```rm example.txt``` - Deletes the file ```example.txt```.
    * ```rmdir /home/test``` - Removes the directory ```/home/test``` (if it is empty).
    * ```rm -r /home/test``` - Recursively removes the directory ```/home/test``` and all files/directories it contains.
* ```wget``` - Downloads files from a web URL.
    * ```wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh``` downloads a shell script from the Anaconda website into the current working directory.
* ```chmod``` - Modifies the permissions of an existing file or directory
    * For the sake of simplicity, use ```chmod 777 filename``` to give total read/write permissions to a file or directory. 
    * ```chmod -r 777 dirname``` can be used to recursively change the permissions of both the directory and all files/directories nested inside.

## Transferring files to and from a Linux server using the command line
You will often need to upload new datasets to the server for analysis and download analysis results to your local machine. This can be done using the ```scp```, or secure copy, command.

Say you have a figure on your server located at ```/mnt/16tb/test_analysis/example.pdf```. From a local terminal window (not logged into the remote server):
1. Run the command ```scp username@servername.org:/mnt/16tb/test_analysis/example.pdf ./```
2. You will be prompted to your server account password.
3. The file will be downloaded into your current working directory.

## Installing miniconda 
Miniconda is a package manager that allows you to easily install many commonly used software relevant to bioinformatics analysis.

A full set of installation instructions can be found in the official documentation here: https://www.anaconda.com/docs/getting-started/miniconda/install#linux.

In short, miniconda3 can be installed in the following steps:
1. Make a new folder in your home directory where miniconda will be installed.
```
mkdir -p ~/miniconda3
```
2. Download the installation shell script from the Anaconda website:
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
```
3. Execute the installation script and delete it once it finishes running. Follow the on-screen instructions to continue the installation:
```
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```

## Installing packages using miniconda 
One of the benefits of using a package manager like miniconda is that it will automatically install both your desired tool and all of its dependencies (the other software it needs to work), streamlining the process of getting your analysis environment up and running quickly.

If you want to know if a package you wish to install is available on miniconda, it helps to Google "conda install softwarename". This should lead you to the Anaconda website, where you can find the specific installation command.

For example, one of the software tools we will use later on is called bedtools. If we want to install bedtools using miniconda, for example, we can find the installation command here in the Anaconda documentation: https://anaconda.org/bioconda/bedtools.

Here, we learn that the installation command is the following:
```
conda install bioconda::bedtools
```

## Installing compute environments using miniconda
Sometimes, rather than installing only a single software package, you will need to install a collection of different tools to run some analysis workflow. One of the ways that this can be done is using a ```.yml``` file, which basically instructs miniconda to download compatible versions of all software packages together and package them together in a dedicated computing environment. Once created, these specialized compute environments can be activated and deactivated at will whenever they are needed.

One of the software tools we will be using later on is called S-PrediXcan. The ```.yml``` file you will need to install all of S-PrediXcan's required dependencies is called ```spredixcan_env.yml``` and is provided for you in this repository.

Move into the directory containing this ```.yml``` file and run the following command to install this environment:
```
conda env create -f spredixcan_env.yml
```

Your console output should look something like the following:
```
Retrieving notices: done
Channels:
 - r
 - conda-forge
 - anaconda
 - bioconda
 - defaults
Platform: linux-64
Collecting package metadata (repodata.json): done
Solving environment: done

==> WARNING: A newer version of conda exists. <==
    current version: 25.5.0
    latest version: 25.5.1

Please update conda by running

    $ conda update -n base -c conda-forge conda

Downloading and Extracting Packages:
                                                                        
Preparing transaction: done                                                                                                       
Verifying transaction: done                                                                                                         
Executing transaction: done                                                                                                         
Installing pip dependencies: | Ran pip subprocess with arguments:                                                                   
['/home/mbetti/miniconda3/envs/spredixcan_env/bin/python', '-m', 'pip', 'install', '-U', '-r', '/home/mbetti/vsa_2025_decoding_the_human_genome/day1/condaenv.urys8jo1.requirements.txt', '--exists-action=b']                                          
Pip subprocess output:                                                    
Collecting numpy==1.21.6                                                                                                            
  Downloading numpy-1.21.6-cp37-cp37m-manylinux_2_12_x86_64.manylinux2010_x86_64.whl (15.7 MB)                                                                                                      
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 15.7/15.7 MB 10.4 MB/s eta 0:00:00                                                                        
Collecting pandas==1.3.5                                                                                                            
  Downloading pandas-1.3.5-cp37-cp37m-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (11.3 MB)                                                                                                      
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 11.3/11.3 MB 10.6 MB/s eta 0:00:00                                                                        
Collecting python-dateutil==2.9.0.post0                                                                                             
  Using cached python_dateutil-2.9.0.post0-py2.py3-none-any.whl (229 kB)                                                                    
Collecting pytz==2025.2                                                                                                             
  Downloading pytz-2025.2-py2.py3-none-any.whl (509 kB)                                                                                     
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 509.2/509.2 kB 9.7 MB/s eta 0:00:00
Collecting scipy==1.7.3
  Downloading scipy-1.7.3-cp37-cp37m-manylinux_2_12_x86_64.manylinux2010_x86_64.whl (38.1 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 38.1/38.1 MB 8.0 MB/s eta 0:00:00
Collecting six==1.17.0
  Downloading six-1.17.0-py2.py3-none-any.whl (11 kB)
Installing collected packages: pytz, six, numpy, scipy, python-dateutil, pandas
Successfully installed numpy-1.21.6 pandas-1.3.5 python-dateutil-2.9.0.post0 pytz-2025.2 scipy-1.7.3 six-1.17.0

done
#
# To activate this environment, use
#
#     $ conda activate spredixcan_env
#
# To deactivate an active environment, use
#
#     $ conda deactivate
```

To activate the installed environment, run the following command:
```
conda activate spredixcan_env
```

To deactivate the environment, run the following command:
```
conda deactivate
```

## Additional resources
For additional practice navigating around a command line environment and writing basic scripts in the R programming language, see https://www.rforbiologists.org.