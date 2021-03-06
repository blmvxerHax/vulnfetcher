# Vulnfetcher
> An enumeration tool similar to searchsploit: it searches the web for known vulnerabilities. It fetches related information and public available exploits, scores the results and orders them based on frequency and severity.

> It differs from searchsploit in that it uses searchengines. It is more forgiving when it comes to search-terms, always gives up-to-date results and plays well with nmap. It's able to process large lists unattended in the background

> ![Vulnfetcher Nmap Demo](/demo/vulnfetcher_simple_search_and_nmap_chain.gif)

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
### Verify everything works
```
python3 /opt/vulnfetcher/vulnfetcher.py ^benchmark^me
```
> The software will test modules with known vulnerabilities and return their results for each searchengine.
```
Testing Google...

Status: 200 - Score: 8 - JAMES pop3d 2.3.2 - https://www.google.com/search?q=%22JAMES%22+pop3d+%222.3%22+exploit
Status: 200 - Score: 18 - libpam-modules 1.1.0 - https://www.google.com/search?q=%22libpam-modules%22+%221.1%22+exploit
Status: 200 - Score: 10 - Microsoft IIS httpd 6.0 - https://www.google.com/search?q=%22Microsoft%22+IIS+httpd+%226.0%22+exploit

Testing DuckDuckGo...

...
```
> High scores (> 6) for each module, means reliable results for this searchengine. If you receive scores that are below 3 then avoid using this searchengine. Instructions will be provided. If both searchengines produce low scores, please report.

## Usage
### simple search
```
./vulnfetcher.py libpam-modules  	#one-word search
./vulnfetcher.py libpam-modules^2.3.2  	#Use '^' as a separator between the version and the name
./vulnfetcher.py "James smtpd"		#multiple words
./vulnfetcher.py "James smtpd^2.3.2"	#Use '^' as a separator between the version and the name
```
### nmap chaining
```
nmap -sC -sV -oA scan <CHANGE_THIS_TO_TARGET_HOST> && python3 /opt/vulnfetcher/vulnfetcher.py -sr scan.xml
```
### dpkg
> Allows you to reduce a list of say 200 installed packages to a handful of potentially vulnerable targets, sorted on probability of vulnerability:
```
dpkg -l > file
python3 /opt/vulnfetcher/vulnfetcher.py file
```
> ![Vulnfetcher Dpkg Demo](/demo/vulnfetcher_dpkg_optimized.gif)
### tab-separated file
> Create a tab-separated file with the structure:
```
<package_or_service_name_1> <tab> <version>
<package_or_service_name_2> <tab> <version>
<package_or_service_name_N> <tab> <version>
```
> Then run
```
python3 /opt/vulnfetcher/vulnfetcher.py file
```
## Output
Appart from the onscreen report while scanning and afterwards, the tool writes two files in the same folder as the input file:
* <input_file>.vulnfetcher.json
   * A sorted json file, containing all information the tool found and that it used to score the findings
* <input_file>.vulnfetcher.report
   * A sorted textfile containing the report that's printed on screen after the search is done

## Help
```
python3 /opt/vulnfetcher/vulnfetcher.py -h
```
## How it works
### Parsing:
* It takes the input file, tries to make sense of the module names and module version numbers
* For the search term, it takes the 'mayor' - 'dot' - 'first number of minor'. So "libpam-modules 1.15.2", becomes "1.1"
* Then goes out on the web, looking with the search term: '"module_name"+"module_version"+exploit'. So in our example, it will look for '"libpam-modules"+"1.1"+"exploit"'
* If it finds exact CVE numbers, it will fetch the details from the cvedetail-website and bring them to you
* If it finds a public exploit on the cvedetails page, it will fetch that exploit as well. 
* In other words, it doesn't only use searchengine pages, it also fetches information of cvedetails pages and exploit-db pages

### Scoring:
* For each trusted site that returns a result, a score is attributed. If the searchterm doesn't return any sites that are vulnerability-authorities, it penelizes the score.
* If an exact match for the complete version number is found, it adds to the score. So in our example if '1.15.2' would be found, this would result in a higher score.
* If names or version numbers aren't found, it penelizes the score.
* If an exact cve-number is found, the details of that cve are fetched. If those details contain indications of a severe vulnerability, this results in a higher score.

## Error reporting
* This software is still beta-stage: I have one error that sometimes throws off searches. It seems to have something to do with the useragent, but I have a hard time tracking the bug down. 
* If the tool doesn't find a vulnerability that you were able to find manually, please report an issue. Provide a sample of the file you used as input and a page where you eventually found the vulnerability.

## Suggested Development Roadmap
If a real coder would like to pick this up, here's a suggested roadmap:
* Currently I use duckduckgo as searchengine and crawl those results
   * Maybe there are better solutions, like maybe a non-profit google-api-key?
* Adding a progress bar when searching.
   * It would allow to display only results with a higher score and keep the output more condenced when the search is running
* Extensive testing
* Support for more input formats
* Improving the scoring algorithm 
   * could be improved by doing more testing
* Make code more beautiful.
   * I actually learned python to write this program, so I expect the code can be improved a lot
