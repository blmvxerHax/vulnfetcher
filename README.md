# Vulnfetcher
Checks a filelist (generated by 'dpkg -l > file') against known vulnerabilities, by searching websites and scoring the results

# Install
cd /opt
git clone https://github.com/gnothiseautonlw/vulnfetcher.git

# Usage
dpkg -l > file
python3 vulnfetcher.py file

# Help
python3 vulnfetcher.py -h
