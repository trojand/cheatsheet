# Download apt and pip3 packages offline with their dependencies

## Description

Commonly used if you want to install something on a remote machine without internet


* Download apt packages + dependencies on debian-based OS [^1]

```bash
# On Kali Machine
mkdir apt_offline_downloads
cd apt_offline_downloads
# for i in $(apt-cache depends <package_name> | grep -E 'Depends|Recommends|Suggests' | cut -d ':' -f 2,3 | sed -e s/'<'/''/ -e s/'>'/''/); do sudo apt-get download $i 2>>errors.txt; done

# i.e. for i in $(apt-cache depends python3-pip | grep -E 'Depends|Recommends|Suggests' | cut -d ':' -f 2,3 | sed -e s/'<'/''/ -e s/'>'/''/); do sudo apt-get download $i 2>>errors.txt; done

# On remote linux machine (Debian-based)
sudo apt-get install ./<package_name.deb>
# i.e. sudo apt-get install ./python3-pip_<version>.deb
# or
sudo dpkg -i <package_name.deb>
```

* Download and install pip3 packages (dependencies) offline [^2]

```bash
# On Kali Machine
mkdir pip3_downloads
cd pip3_downloads
pip3 download <package_name>
# i.e. pip3 download -r requirements.txt


# Transfer for to remote linux machine with pip3 installed (i.e. via scp)


# On remote machine with python3 and pip3 installed
pip3 install --no-index --find-links '.' '<package_name.whl' 
# or
pip3 install --no-index --find-links '.' -r requirements.txt
# or
pip3 install --no-index --find-links="./pip3_downloads" -r requirements.txt

# Then you may proceed to `python3 setup.py install`
```

    
[^1]: [ostechnix](https://ostechnix.com/download-packages-dependencies-locally-ubuntu/)
[^2]: [StackOverflow](https://stackoverflow.com/questions/11091623/how-to-install-packages-offline)