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
