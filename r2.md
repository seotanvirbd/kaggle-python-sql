# Python SQL Data Processing Tutorial

## 1. Initial Setup and Data Download

```python
# Import required library for Kaggle API
import kaggle

# Download CSV file from Kaggle
!kaggle datasets download ankitbansal06/retail-orders -f orders.csv

# Extract file from zip
import zipfile
zip_ref = zipfile.ZipFile('orders.csv.zip') 
zip_ref.extractall()  # extract file to current directory
zip_ref.close()  # close file

# Read the CSV file into a pandas DataFrame
import pandas as pd
df = pd.read_csv('orders.csv')
```

This section:

Uses the Kaggle API to download a retail orders dataset
Extracts the downloaded zip file
Loads the data into a pandas DataFrame

## 2. Data Exploration and Analysis

 ```  ### Basic data exploration commands
df.head()  # View first few rows
df.shape  # Get dimensions of DataFrame
df.info()  # Get DataFrame information
df.describe()  # Get statistical summary
df.dtypes  # View data types of columns

# Check for null values and duplicates
df.isnull().sum()  # Count null values in each column
df.duplicated().sum()  # Count total duplicate rows

# Examine specific column duplicates
df['Ship Mode'].duplicated()  # Check duplicates in Ship Mode column
df.duplicated(['Ship Mode']).sum()  # Count duplicates in Ship Mode column
```

This section shows various ways to:

Inspect the data structure and content
Check for missing values
Identify duplicate entries

## 3. Data Cleaning and Handling Missing Values

```python
# Remove duplicates in different ways
df.drop_duplicates()  # Remove all duplicate rows
df.drop_duplicates(['Ship Mode'])  # Remove rows with duplicate Ship Mode values
df['Ship Mode'].drop_duplicates()  # Get unique values in Ship Mode column

# Handle missing and special values
# Convert 'Not Available' and 'unknown' to NaN
df1 = pd.read_csv('orders.csv', na_values=['Not Available', 'unknown'])

# Find rows with null values in Ship Mode column
df1[df1['Ship Mode'].isna()==1]

# Check unique values in Ship Mode after handling special values
df1['Ship Mode'].unique()
```
## 4. Column Renaming and Standardization
```
### Method 1: Rename specific columns
df1.rename(columns={
    'Order Id': 'order_id',
    'City': 'city'
})

### Method 2: Standardize all column names (convert to lowercase and replace spaces with underscores)
df1.columns = df1.columns.str.lower()
df1.columns = df1.columns.str.replace(' ', '_')
```
## 5. Creating New Columns and Data Transformation
```
### Calculate discount amount
```df1['discount'] = df1['list_price'] * df1['discount_percent'] * .01```

### Calculate sale price
df1['sale_price'] = df1['list_price'] - df1['discount']

### Calculate profit
```df1['profit'] = df1['sale_price'] - df1['cost_price']```

### Convert order_date to datetime
df1['order_date'] = pd.to_datetime(df1['order_date'], format="%Y-%m-%d")```

### Remove unnecessary columns
df1.drop(columns=['list_price', 'cost_price', 'discount_percent'], inplace=True)
```
## 6. Database Integration with PostgreSQL

### Install required packages (if not already installed)
```# pip install psycopg2-binary sqlalchemy```

# Set up database connection
```
import pandas as pd
from sqlalchemy import create_engine
connection_string = 'postgresql+psycopg2://postgres:1234@localhost:5432/ankit bansal'
engine = create_engine(connection_string)

# Export DataFrame to PostgreSQL - Replace mode
df1.to_sql('rough_direct_from_pd', engine, index=False, if_exists='replace')

# Export DataFrame to PostgreSQL - Append mode
df1.to_sql('afra_pd', engine, index=False, if_exists='append')
```
Key points about this code:

## Data Cleaning:

Removes duplicates using different methods
Handles missing values and special text values by converting them to NaN
Standardizes column names for consistency


## Data Transformation:

Creates new calculated columns for business metrics (discount, sale_price, profit)
Converts date strings to proper datetime objects
Removes redundant columns after calculations


## Database Integration:

Sets up a connection to PostgreSQL database
Provides two methods to save data:

'replace' mode: overwrites existing table
'append' mode: adds new records to existing table





This code demonstrates a complete ETL (Extract, Transform, Load) process:

Extracting data from a CSV file
Transforming it through various cleaning and calculation steps
Loading it into a PostgreSQL database
