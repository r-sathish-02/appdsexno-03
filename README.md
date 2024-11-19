# EX-03 Implementing Recommendation Systems
### Aim:
To Implement Recommendation Systems using the suitable data sets. 
### Algorithm
STEP 1: Load the necessary Datasets and include the python libraries.<br>
STEP 2: Use Fuzzy library for handling text data and Perform Data Preprocessing steps.<br>
STEP 3: Standardize column names for merging and Apply fuzzy matching to find similar text data between datasets.<br>
STEP 4: Perform Data transformation between datasets and Define a recommendation score using the features of the datasets.<br>
STEP 5: Sort the data by recommendation score and Export the results to a CSV file.

### Program and Output:

###### Importing necessary libraries
```Python
import pandas as pd 
import numpy as np
from fuzzywuzzy import process
from sklearn.preprocessing import MinMaxScaler
```
###### Reading the datasets  
```Python
da=pd.read_csv('emobile.csv')          
da.head()
```
![image](https://github.com/user-attachments/assets/99ece1c8-634b-4162-88b1-1671cacd00a8)
  
```Python
db=pd.read_csv('maxmobile.csv')          
db.head()
```
![image](https://github.com/user-attachments/assets/253998f4-8313-4b27-9008-ec313f0df3cb)


###### Data Cleaning and Column Renaming
```Python
da.drop_duplicates(inplace=True)
db.drop_duplicates(inplace=True)
da.columns=['Product_ID','Product_Name','Rating','Review_Count']
db.columns=['Product_ID','Product_Name','Rating','Review_Count']
```
###### Fuzzy Matching Function Definition
```Python
def matching(name,choice,l=1):
    results=process.extract(name,choice,limit=l)
    return results[0][0] if results else None
```
###### Applying Fuzzy Matching to Identify Corresponding Products
```Python
db['Matched_Product'] = db['Product_Name'].apply(lambda x: matching(x, da['Product_Name'].tolist()))
db.head()
```
![image](https://github.com/user-attachments/assets/182327ed-fd60-49dd-a85a-84858cff5b00)


###### Merging Datasets Based on Fuzzy Matches
```Python
merged=pd.merge(da,db,left_on='Product_Name',right_on='Matched_Product',
                how='inner',suffixes=('_ds1','_ds2'))
merged.head()
```
![image](https://github.com/user-attachments/assets/11a4233d-c107-40cc-ad3f-fe3c053c2e7c)



###### Calculating Combined Ratings and Review Counts
```Python
merged.columns
x=merged['Review_Count_ds1']+merged['Review_Count_ds2']
merged['Combined_Rating']=(merged['Rating_ds1']*merged['Review_Count_ds1']+
                           merged['Rating_ds2']*merged['Review_Count_ds2'])/x
merged.head()
```
![image](https://github.com/user-attachments/assets/e451bec0-1eb4-4b8d-b259-c6c2e0ac31bd)

###### Calculating Recommendation Score
```Python
merged['Recommendation_Score']=0.7*merged['Combined_Rating']+0.3*merged['Rating_ds1']
merged.head()
```
![image](https://github.com/user-attachments/assets/97a7a3b5-c0d3-4847-b093-4ada5b05c77e)


###### Identifying Top Products by Recommendation Score
```Python
best=merged.sort_values (by='Recommendation_Score', ascending=False)
print(best[['Matched_Product', 'Combined_Rating', 'Recommendation_Score']].head(5))
```
![image](https://github.com/user-attachments/assets/1e144a65-10a0-4f22-86e4-dc63c51e7349)


###### Saving the Best Recommendations
```Python
best[['Matched_Product','Combined_Rating',
      'Recommendation_Score']].to_csv('BestRecommended.csv',index=False)
new=pd.read_csv('BestRecommended.csv')
new.head()
```
![image](https://github.com/user-attachments/assets/500bf66c-5ac8-4a63-a059-aa93ebce13ae)


### Result:
Thus, the Implementation of the Recommendation System for the given dataset is completed successfully.
