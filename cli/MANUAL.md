# Quick & Dirty PA Web Sin Shamer

Setup python3, selenium and a selenium driver (change python2.py
if you do not want to use cromium driver)

## Fast track

If you are on a Debian stable you can simply

```
apt-get install python3 python3-selenium chromium-driver 
```

## Manual installation of requirements

- [Python3](https://www.python.org)

E.g. on an Arch machine
```
pacman -S python3
```

If your Python3 package doesn't bundle Pip, you will have to [install it](https://pip.pypa.io/en/stable/installation/).

### Setup the repo and packages

Clone the repo:
```bash
git clone https://github.com/hermescenter/monitorapa.git
```
Enter the repo directory:
```bash
cd monitorapa
```
Create a Python Virtual Environment:
```bash
python -m venv .venv
```
Activate your environment:

- Windows
  ```bash
  .\.venv\Scripts\activate.bat
  ```
- Linux
  ```bash
  ./.venv/bin/activate
  ```
```

Install the required packages:
```bash
pip install -r requirements.txt
```


# Script execution

The scripts assume to be run from the repository root.

Since the list of PA is updated daily, all script must be run on the
same copy of the list, to preserve results' meaning.

So the output directory will be created after each the download of the 
list with a date in ISO 8601 format (YYYY-MM-DD), and each script will
output in the LAST of such folders in out/ (alphabetically sorted).

So if you want to run a script in a previous date, just rename the
folder with a grain of salt.

## Point 1

```
python3 cli/point1.py
```
Downloads `out/YYYY-MM-DD/enti.tsv`

## Point 2

```
python3 cli/point2.py check/google_analytics.js
```
Check for Google Analytics presence over all the website listed in 
`out/YYYY-MM-DD/enti.tsv` (Tab Separated Value).
Other scripts in `check/` can be used instead of `google_analytics.js`.

The software works this way:

1. point2.py open the target websites in the Selenium Driver
   and run the provided JavaScript
2. google_analytics.js contains the JavaScript code used to detect
   Google Analytics. If it find GA, it put the tracking ID (or any
   other metadata) in document's title or empty it if the site is
   free from Google Analytics surveillance.
3. python.py, after 5 seconds, get the title and write it into a file
   in the out/YYYY-MM-DD/point2/google_analytics/ directory named
   after its line number in the `out/YYYY-MM-DD/enti.tsv`
   file, for example out/YYYY-MM-DD/google_analytics/point2/100.OK.txt
   (or out/YYYY-MM-DD/google_analytics/point2/100.ERR.txt in case of error)

Obviously, if GA is not found, the file will be empty.
If GA is found, it will contains the tracking ID (or any other metadata)

The file creation time will be used as the time of the test.

## Point 3
```
python3 cli/point3.py check/google_analytics.js [format]
```

Will produce a new file `out/YYYY-MM-DD/google_analytics/point3/enti.[format]`
enriched as for specification.

Supported format are `tsv` (the default, always produced) or `csv` (TODO) or `json` (TODO).

## Point 4
```
python3 cli/point4.py check/google_analytics.js
```

Will send an email to the PAs that are still hosting the
Google surveillance tool.
The list of PAs to mail will be read in the output file of `cli/point3.py`

The sender mail and SMTP server must be configured in a configuration
file named `cli/point4.cfg` that must be kept secret.
A sample (mock) configuration is provided in `cli/point4_sample.cfg`
