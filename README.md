# Vulnfetcher
> An enumeration tool that can be chained to nmap, or check a long list of installed debian packages (generated by 'dpkg -l > file) against known vulnerabilities.

> The tool uses searchengines, and can run unattended in the background. When you come back, it will have a list of potential exploits and vulnerabilities ready for you.

> ![Vulnfetcher Demo](/demo/vulnfetcher-optimized.gif)

## Install dependencies
```
pip3 install requests
pip3 install beautifulsoup4
pip3 install lxml
```
## Install
```
cd /opt
git clone https://github.com/gnothiseautonlw/vulnfetcher.git
```
## Usage
###nmap chaining
```
nmap -sC -sV -oA scan <CHANGE_THIS_TO_TARGET_HOST> && python3 /opt/vulnfetcher/vulnfetcher.py -sr scan.xml
```
###dpkg
```
dpkg -l > file
python3 vulnfetcher.py file
```
## Output
Appart from the onscreen report while scanning and afterwards, the tool writes two files in the same folder as the input file:
* file.vulnfetcher.json
   * A sorted json file, containing all information the tool found and that it used to score the findings
* file.vulnfetcher.report
   * A sorted textfile containing the report that's printed on screen after the search is done

## Help
```
python3 vulnfetcher.py -h
```
## How it works
### Parsing:
* It takes the input file, tries to make sense of the module names and module version numbers
* For the search term, it takes the 'mayor' - 'dot' - 'first number of minor'. So "libpam-modules 1.15.2", becomes "1.1"
* Then goes out on the web, looking with the search term: '"module_name"+"module_version"+exploit'. So in our example, it will look for '"libpam-modules"+"1.1"+"exploit"'
### Scoring:
* For each trusted site that returns a result, it get's one point.
* If an exact match for the complete version number is found, it get's two points. So in our example if '1.15.2' would be found, this get's two points
* If an exact cve-number is found, the details of that cve are fetched. If those details contain indications of a severe vulnerability, then 3 points are attibuted to that

## Suggested Development Roadmap
If a coder would like to pick this up, here's a suggested roadmap:
* Have some solution for the google restrictions
   * Currently the search is slowed down bigtime (the program waits 5 secdons in between each search), since google doesn't allow crawling their results. Having some solution for this would be great.
   * Maybe a non-profit google-api-key?
* Adding a progress bar when searching.
   * It would allow to display only results with a higher score and keep the output more condenced when the search is running
* Support for other input formats
* Improving the scoring algorithm 
   * could be improved by doing more testing
* Make code more beautiful.
   * I actually learned python to write this program, so I expect the code can be improved a lot
