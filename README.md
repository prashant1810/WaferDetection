# WaferFaultDetection # 

## Problem Statement: ##
A wafer (In electronics), also called a slice or substrate, is a thin slice of semiconductor,
such as crystalline silicon (c-Si), used for the fabrication of integrated circuits and in photovoltaics,
to manufacture solar cells.

- The inputs of various sensors for different wafers have been provided.
- The goal is to build a machine-learning model that predicts whether a wafer needs to be replaced or not
  (i.e whether it is working or not) based on the inputs from various sensors.
- There are two classes: +1 and -1.
  - +1: Means that the wafer is in a working condition and it doesn't need to be replaced.
  - -1: Means that the wafer is faulty and needs to be replaced.


## Data Description ##
- The client will send data in multiple sets of files in batches at a given location.
- Data will contain Wafer names and 590 columns of different sensor values for each wafer.
- The last column will have the "Good/Bad" value for each wafer.

Apart from training files, we also require a "schema" file from the client, which contains all the
relevant information about the training files such as:

- Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, 
  Name of Columns, and their datatype.

## Data Validation ##
In this step, we perform different sets of validation on the given set of training files.

- Name Validation: We validate the name of the files based on the given name in the schema file. We have 
  created a regex pattern as per the name given in the schema file to use for validation. After validating 
  the pattern in the name, we check for the length of the date in the file name as well as the length of time 
  in the file name. If all the values are as per requirements, we move such files to "Good_Data_Folder" else
  we move such files to "Bad_Data_Folder."

- Number of Columns: We validate the number of columns present in the files, and if it doesn't match with the
  the value is given in the schema file, then the file ID moves to "Bad_Data_Folder."

- Name of Columns: The name of the columns is validated and should be the same as given in the schema file. 
  If not, then the file is moved to "Bad_Data_Folder".

- The datatype of columns: The datatype of columns is given in the schema file. This is validated when we insert
  the files into the Database. If the datatype is wrong, then the file is moved to "Bad_Data_Folder."

- Null values in columns: If any of the columns in a file have all the values as NULL or missing, we discard such
  a file and move it to "Bad_Data_Folder".

## Data Insertion in Database ##
- Database Creation and Connection: Create a database with the given name passed. If the database is already created,
  open the connection to the database.
- Table creation in the database: A table with the name - "Good_Data", is created in the database for inserting the files 
  in the "Good_Data_Folder" based on given column names and datatype in the schema file. If the table is already
  present, then the new table is not created and new files are inserted in the already present table as we want 
  training to be done on new as well as old training files.
 
- Insertion of file in the table: All the files in the "Good_Data_Folder" are inserted in the above-created table. If
  any file has an invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".

## Model Training ##
- Data Export from Db: The data in a stored database is exported as a CSV file to be used for model training.
 
- Data Preprocessing:
  - Check for null values in the columns. If present, impute the null values using the KNN imputer.
  - Check if any column has zero standard deviation, remove such columns as they don't give any information during 
    model training.
    
- Clustering: KMeans algorithm is used to create clusters in the preprocessed data. The optimum number of clusters 
  is selected
