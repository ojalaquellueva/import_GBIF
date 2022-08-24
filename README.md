# A simple example download of GBIF and import to PostgreSQL
Prepared by: Brad Boyle (ojalaquellueva AT gmail)
Date: 10 Aug. 2022

# I. Download

### 1. Prepare GBIF download
* BIEN example: all plant occurrences

1. Go to https://www.gbif.org/
2. Log in
3. Click "occurrences" tab above search window.
4. Click "Scientific Name" on left hand menu and select "Major Groups", then "Plantae".
5. Select "Download" option at the top of the data table.
6. Wait for GBIF to process your request and send you a download link.


### 1. Set parameters
* Adjust accordingly

```
# Data directory
# Download file to here
DD="~/bien/gnrs/gnrsmss/import_GBIF/data"

# GBIF download file (from GBIF download link)
DLF="0113553-200613084148143.zip"

# Name of download file on your filesystem...whatever you want
DLF_LOCAL="gbif_download.zip"
```

### 1. Get GBIF download file from GBIF & unpack
```
cd $DD
wget https://api.gbif.org/v1/occurrence/download/request/$DLF -O $DLF_LOCAL
unzip $DLF_LOCAL
```

From this point on, all you want is file `occurrence.txt`

### 2. Check file type of ``occurrence.txt`
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
```

### 3. Double-up backslashes so Postgres takes them literally

```
sed 's/\\/\\\\/g' occurrence.txt > occurrence_cleaned.txt
```

### 4. Fix badly formed quotes

```
sed -i 's/,""/,"/g' occurrence_cleaned.txt
sed -i 's/"",/",/g' occurrence_cleaned.txt

```

#### 5. Create sample file for testing

```
head -1000 occurrence_cleaned.txt > occurrence_cleaned_sample.txt
```

#### 6. Import to Postgres

See script "import_gbif.sql" for SQL commands needed to import to PostgreSQL.

