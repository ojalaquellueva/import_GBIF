# A simple example of downloading from GBIF and importing to PostgreSQL
Prepared by: Brad Boyle (ojalaquellueva AT gmail)  
Date: 10 Aug. 2022

## I. Download from GBIF

### 1. Request GBIF download
* BIEN example: all plant occurrences

1. Go to https://www.gbif.org/
2. Log in
3. Click "occurrences" tab above search window.
4. Click "Scientific Name" on left hand menu and select "Major Groups", then "Plantae".
5. Select "Download" option at the top of the data table.
6. Wait for GBIF to process your request and send you a download link.


### 2. Download GBIF file and unpack
* Among many other files, will extract "occurrences.txt". This is the main occurences file, and the only one needed for this example

```
#######################################
# Some parameters - adjust accordingly
#######################################

# Data directory
# Download file to here
DD="~/bien/gnrs/gnrsmss/import_GBIF/data"

# GBIF download file (from GBIF download link)
DLF="0113553-200613084148143.zip"

# Name of download file on your filesystem...whatever you want
DLF_LOCAL="gbif_download.zip"

#######################################
# Download the file and unpack
#######################################

cd $DD
wget https://api.gbif.org/v1/occurrence/download/request/$DLF -O $DLF_LOCAL
unzip $DLF_LOCAL
```

## II. Prepare occurences file for import to Postgres

### 1. Check file type 
* If file is utf-8, you should see this:

```
file -bi occurrence.txt
text/plain; charset=utf-8
```

If not, convert as follows:
* You may need to install iconv first
* This example assumes that command `file` reported that file was of type ISO-8859-1.

```
mv occurrence.txt occurrence.txt.bak
iconv -f ISO-8859-1 -t utf-8 -o occurrence.txt occurrence.txt.bak
rm occurrence.txt.bak
```

### 2. Double-up backslashes so Postgres takes them literally

```
sed 's/\\/\\\\/g' occurrence.txt > occurrence_cleaned.txt
```

### 3. Fix badly formed quotes

```
sed -i 's/,""/,"/g' occurrence_cleaned.txt
sed -i 's/"",/",/g' occurrence_cleaned.txt

```

#### 4. Create sample file for testing
* Run a test import first with this sample before importing the full file

```
head -1000 occurrence_cleaned.txt > occurrence_cleaned_sample.txt
```

## III. Import to Postgres

See script "import_gbif.sql" for SQL commands needed to import to PostgreSQL.

