## UNCTAD Trade Data (SG Mission Report Dashboard - Trade Data)

### 1. Overview
Use WITS REST API to programmatically access databases in WITS. At present, the UNCTAD
TRAINS dataset and Trade Stats are available through our API module. We are planning to add more
datasets in the near future. If you are interested in a specific dataset that is currently available through
WITS, please let us know by email at wits@worldbank.org. The WITS API module supports two request
formats: SDMX and URL-based structure. The data response structure for both requests is identical
and available in XML format. - Extract from WITS Guide Version 1.4

### 2. Developer Page
The API documentation covers all aspects of querying the data; the default or first output format is XML, and the latest update on version 1.4 included conversion of the output to JSON by adding the `?format=json` option to the request/call. 

**Dev Page URL:** https://wits.worldbank.org/witsapiintro.aspx?lang=en

### 3. Using the API
The first step is to assess the data timeliness metric of the dataset; the API provides this support by offering a check into the `lastupdateddate` feature of the data using.

#### 3.1 Export partner's data
**Call:** http://wits.worldbank.org/API/V1/SDMX/V21/datasource/tradestats-trade/reporter/[ISO_Code]/year/all/partner/all/product/Total/indicator/XPRT-TRD-VL
#### 3.2 Import partner's data
**Call:** http://wits.worldbank.org/API/V1/SDMX/V21/datasource/tradestats-trade/reporter/[ISO_Code]/year/all/partner/all/product/Total/indicator/MPRT-TRD-VL

#### Data Quality Check
```python
import requests
import xml.etree.ElementTree as ET
import pandas as pd

# Send a GET request to the URL
response = requests.get("http://wits.worldbank.org/API/V1/wits/datasource/trn/dataavailability/")
if response.status_code != 200:
    print("Failed to fetch data from the URL.")
    exit()

# Parse the XML response
root = ET.fromstring(response.content)

# Define the XML namespace
namespace = {"wits": "http://wits.worldbank.org"}

# Initialize lists to store data
data = []
columns = ["Country", "ISO3 Code", "Year", "Nomenclature", "Agreements", "Partners", "Duty Estimates", "Last Updated Date"]

# Extract data from the XML
for reporter in root.findall(".//wits:reporter", namespaces=namespace):
    country = reporter.find("wits:name", namespaces=namespace).text
    iso3_code = reporter.attrib["iso3Code"]
    year = int(reporter.find("wits:year", namespaces=namespace).text)
    nomenclature = reporter.find("wits:reporternernomenclature", namespaces=namespace).text
    agreements = int(reporter.find("wits:numberofpreferentialagreement", namespaces=namespace).text)
    partners = reporter.find("wits:partnerlist", namespaces=namespace).text.strip(";")
    duty_estimates = reporter.find("wits:isspecificdutyexpressionestimatedavailable", namespaces=namespace).text
    last_updated_date = reporter.find("wits:lastupdateddate", namespaces=namespace).text

    data.append([country, iso3_code, year, nomenclature, agreements, partners, duty_estimates, last_updated_date])

# Create a DataFrame from the extracted data
df = pd.DataFrame(data, columns=columns)

# Display the DataFrame
print(df)

```


#### Trade Partners Information (X/MPRT-TRD-VL)
```python
import requests
import xml.etree.ElementTree as ET
import pandas as pd

# Send a GET request to the URL
response = requests.get("http://wits.worldbank.org/API/V1/wits/datasource/trn/dataavailability/")
if response.status_code != 200:
    print("Failed to fetch data from the URL.")
    exit()

# Parse the XML response
root = ET.fromstring(response.content)

# Define the XML namespace
namespace = {"wits": "http://wits.worldbank.org"}

# Initialize lists to store data
data = []
columns = ["Country", "ISO3 Code", "Year", "Nomenclature", "Agreements", "Partners", "Duty Estimates", "Last Updated Date"]

# Extract data from the XML
for reporter in root.findall(".//wits:reporter", namespaces=namespace):
    country = reporter.find("wits:name", namespaces=namespace).text
    iso3_code = reporter.attrib["iso3Code"]
    year = int(reporter.find("wits:year", namespaces=namespace).text)
    nomenclature = reporter.find("wits:reporternernomenclature", namespaces=namespace).text
    agreements = int(reporter.find("wits:numberofpreferentialagreement", namespaces=namespace).text)
    partners = reporter.find("wits:partnerlist", namespaces=namespace).text.strip(";")
    duty_estimates = reporter.find("wits:isspecificdutyexpressionestimatedavailable", namespaces=namespace).text
    last_updated_date = reporter.find("wits:lastupdateddate", namespaces=namespace).text

    data.append([country, iso3_code, year, nomenclature, agreements, partners, duty_estimates, last_updated_date])

# Create a DataFrame from the extracted data
df = pd.DataFrame(data, columns=columns)

# Display the DataFrame
print(df)
```
