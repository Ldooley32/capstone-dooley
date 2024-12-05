# Predicting the Spread of Antibiotic Resistance: Data-Driven Insights from Antibiotic Usage 
___
### Author: Laura Dooley
### Date: 11/20/2024

## Project Overview
Data Anylsis Capstone Project : Antibiotic resistance is one of the most pressing health challenges . It occurs when bacteria evolve mechanisms to withstand the drugs designed to kill them. Antibiotic overuse is a signigicant driver of this phenomenon. this project explores the relationship between antibiotic usage patterns and emergence of antibiotic resistance. The data analysis and visualization is aimed to provide insight into how antibiotics affect bacteria reisistance. By using statistical techniques and  machine learning to quantify trends in resistance. 

## Folders and Files 
- `data/` - Contains raw data collected from (https://arpsp.cdc.gov/about?tab=antibiotic-resistance)
- `eda.ipynb` - Exploratory Data Analysis notebook.
- `advanced_analysis.ipynb - Decision Tree model: Antibiotic resistance prediction
- `antibiotics_sales.csv`, `avg_state.csv`, `updated_saar.csv`, and `updated_resistance.csv` - cleaned data used in eda and advanced analysis


## How to Run Project
`Powershell`
Create virtual environment
```python -m venv venv```

Activate virtual environment
```venv\Scripts\activate```

Install requirements.txt
```pip install -r requirements.txt```

commit and push updates to GitHub
```git add .
git commit -m "comment"
git push origin master```


### Data Cleaning and Preprocessing
1. Combine multiple tables into one.
       - folders in the file contains multiple tables that need to be combined
            a. 'resistance' directory
            b.  'saar' directory
2 Use function to combine files
```def combine_csv_files(input_folder, output_file):
    # Check if the output file already exists
    if os.path.exists(output_file):
        print(f"The output file '{output_file}' already exists. No changes were made.")
        return
    
    # List to hold dataframes
    dataframes = []
    
    # Iterate over all files in the input folder
    for filename in os.listdir(input_folder):
        if filename.endswith(".csv"):
            file_path = os.path.join(input_folder, filename)
            df = pd.read_csv(file_path)
            dataframes.append(df)
    
    # Concatenate all dataframes
    combined_df = pd.concat(dataframes, ignore_index=True)
    
    # Save the combined dataframe to the specified output file
    combined_df.to_csv(output_file, index=False)
    print(f"All CSV files from {input_folder} have been combined and saved to {output_file}.") ```

Summarize the 4 files that will be used for analysis using function `summarize_csv`. 

2. Removal and addition of columns
    - resistance.csv
        a. for agecat remove "All" entries, "All" combine "Adult" and "Peds" entries. Leaning "All" will double my results
        b. add columns state_id, phenotype_id, eventtype_id, and agecat_id to switch variables into intergers
```# Specify the output file path 
output_file_path = 'updated_resistance.csv'

# Check if the output file already exists
if os.path.exists(output_file_path):
    print(f"The output file '{output_file_path}' already exists. No changes were made.")
else:
    # Load the CSV file
    df = pd.read_csv(r'C:\Users\laura\OneDrive\Documents\capstone-dooley\data\resistance.csv')

    #  Convert 'State' column to state_id
    state_mapping = {
        'AL': 1, 'AK': 2, 'AZ': 3, 'AR': 4, 'CA': 5, 'CO': 6, 'CT': 7, 'DE': 8, 'DC': 9, 'FL': 10,
        'GA': 11, 'HI': 12, 'ID': 13, 'IL': 14, 'IN': 15, 'IA': 16, 'KS': 17, 'KY': 18, 'LA': 19,
        'ME': 20, 'MD': 21, 'MA': 22, 'MI': 23, 'MN': 24, 'MS': 25, 'MO': 26, 'MT': 27, 'NE': 28,
        'NV': 29, 'NH': 30, 'NJ': 31, 'NM': 32, 'NY': 33, 'NC': 34, 'ND': 35, 'OH': 36, 'OK': 37,
        'OR': 38, 'PA': 39, 'RI': 40, 'SC': 41, 'SD': 42, 'TN': 43, 'TX': 44, 'UT': 45, 'VT': 46,
        'VA': 47, 'WA': 48, 'WV': 49, 'WI': 50, 'WY': 51, 'PR': 52  # Added PR for Puerto Rico
    }

    df['state_id'] = df['State'].map(state_mapping)

    
    #  Convert 'phenotype' column to phenotype_id
    phenotype_mapping = {
        'Acinetobacter_Carbapenems': 1,
        'Acinetobacter_MDR': 2,
        'CNS_Vanc': 3,
        'CREall': 4,
        'E.faecalis_Dapto': 5,
        'E.faecalis_VRE': 6,
        'E.faecium_Dapto': 7,
        'E.faecium_VRE': 8,
        'Ecoli_CRE': 9,
        'Ecoli_ESCeph': 10,
        'Ecoli_Fluoroq': 11,
        'Ecoli_MDR': 12,
        'Entb_Cefepime': 13,
        'Entb_CRE': 14,
        'Entb_MDR': 15,
        'Klebsiella_CRE': 16,
        'Klebsiella_ESCeph': 17,
        'Klebsiella_MDR': 18,
        'MRSA': 19,
        'MRSA_Dapto': 20,
        'MRSA_Fluoroq': 21,
        'MRSA_Linezolid': 22,
        'MRSA_Vanc': 23,
        'P.aeruginosa_Amino': 24,
        'P.aeruginosa_Carbapenems': 25,
        'P.aeruginosa_ESCeph': 26,
        'P.aeruginosa_Fluoroq': 27,
        'P.aeruginosa_MDR': 28,
        'P.aeruginosa_PiPTaz': 29
    }

    df['phenotype_id'] = df['phenotype'].map(phenotype_mapping)
    
    
    # Remove rows where the 'agecat' column is "All"
    df = df[df['agecat'] != "All"]

    # Create new column 'r_present' based on 'Suppress' values
    df['r_present'] = df['Suppress'].apply(lambda x: 0 if x in ['LowCount', 'Zero'] else 1)

    # Convert 'agecat' column to agecat_id
    agecat_mapping = {
        'Adult': 0,  
        'Peds': 1
    }
    df['agecat_id'] = df['agecat'].map(agecat_mapping)

    # Convert 'eventtype' column to eventtype_id
    eventtype_mapping = {
        'All': 1,  
        'CAUTI': 2,
        'CLABSI': 3,
        'SSI': 4
    }
    df['eventtype_id'] = df['eventtype'].map(eventtype_mapping) ```

    # Step 4: Convert columns to integers
    df['pctNonSuscep'] = pd.to_numeric(df['pctNonSuscep'], errors='coerce').fillna(0).astype(int)
    df['numTested'] = pd.to_numeric(df['numTested'], errors='coerce').fillna(0).astype(int)
    df['numNonSuscep'] = pd.to_numeric(df['numNonSuscep'], errors='coerce').fillna(0).astype(int)

    - saar.csv
        a. keep columns "year" , "state", "observed antimicrobial days", and "predicted antimicrobial days"
        b. Add column "saar" which is "observed antimicrobial days"/ "predicited antimicrobial days"
        c. Change the state names to abbreviations to match updated_resistance.csv State column
```# Mapping of full state names to state abbreviations
state_abbreviations = {
    "Alabama": "AL", "Alaska": "AK", "Arizona": "AZ", "Arkansas": "AR", "California": "CA",
    "Colorado": "CO", "Connecticut": "CT", "Delaware": "DE", "Florida": "FL", "Georgia": "GA",
    "Hawaii": "HI", "Idaho": "ID", "Illinois": "IL", "Indiana": "IN", "Iowa": "IA",
    "Kansas": "KS", "Kentucky": "KY", "Louisiana": "LA", "Maine": "ME", "Maryland": "MD",
    "Massachusetts": "MA", "Michigan": "MI", "Minnesota": "MN", "Mississippi": "MS", "Missouri": "MO",
    "Montana": "MT", "Nebraska": "NE", "Nevada": "NV", "New Hampshire": "NH", "New Jersey": "NJ",
    "New Mexico": "NM", "New York": "NY", "North Carolina": "NC", "North Dakota": "ND", "Ohio": "OH",
    "Oklahoma": "OK", "Oregon": "OR", "Pennsylvania": "PA", "Rhode Island": "RI", "South Carolina": "SC",
    "South Dakota": "SD", "Tennessee": "TN", "Texas": "TX", "Utah": "UT", "Vermont": "VT",
    "Virginia": "VA", "Washington": "WA", "West Virginia": "WV", "Wisconsin": "WI", "Wyoming": "WY", "Puerto Rico":"PR", "District of Columbia": "DC"
}

# Specify the output file path
output_file = 'update_saar.csv'

# Check if the output file already exists
if os.path.exists(output_file):
    print(f"The output file '{output_file}' already exists. No changes were made.")
else:
    # Load the CSV file into a DataFrame
    df = pd.read_csv(file3)

    # Ensure there are no zero values in 'Predicted Antimicrobial Days'
    if (df['Predicted Antimicrobial Days'] == 0).any():
        print("Warning: Found zero values in 'Predicted Antimicrobial Days'. These will be replaced with NaN.")
        df['Predicted Antimicrobial Days'].replace(0, pd.NA, inplace=True)

    # Add a new column named 'SAAR' while handling division safely
    df['SAAR'] = df['Observed Antimicrobial Days'] / df['Predicted Antimicrobial Days']

    # Convert the 'SAAR' column to float with 4 decimal places
    df['SAAR'] = df['SAAR'].astype(float).round(4)

    # Keep only the specified columns
    columns_to_keep = ["Event Year", "State", "Observed Antimicrobial Days", 
                       "Predicted Antimicrobial Days", "SAAR"]
    df = df[columns_to_keep]

    # Replace state names with abbreviations in the 'State' column
    df['State'] = df['State'].map(state_abbreviations).fillna(df['State'])

    # Save the updated DataFrame to a new file
    df.to_csv(output_file, index=False)
    print(f"Processed data saved to {output_file}") ```
3. Creat a new file avg_state.csv to plot average saar value and average precent non susceptible for each state. 
```def calculate_average_per_state(file_name, column_name, state_column, existing_df=None):
    """
    Calculates the average of a specific column per state and appends it to an existing DataFrame if provided.
    """
    # Load the dataset
    df = pd.read_csv(file_name)
    
    # Ensure the specified column is numeric
    df[column_name] = pd.to_numeric(df[column_name], errors='coerce')
    
    # Handle NaN values by filling them with 0
    df[column_name] = df[column_name].fillna(0)
    
    # Calculate the average of the specified column per state
    average_per_state = df.groupby(state_column)[column_name].mean().reset_index()
    
    # Rename columns for clarity
    average_per_state.columns = [state_column, f'avg_{column_name}']
    
    # If an existing DataFrame is provided, merge with it
    if existing_df is not None:
        result_df = pd.merge(existing_df, average_per_state, on=state_column, how='outer')
    else:
        # Otherwise, use the current averages as the result
        result_df = average_per_state
    
    return result_df```
### Data Overview
Use the function to summarize data files
```def summarize_csv(file):
    # Read the CSV file
    df = pd.read_csv(file)
    
    # Get the number of rows and columns
    num_rows, num_columns = df.shape
    
    # Get column names and data types
    column_info = df.dtypes
    
    # Get summary statistics for numerical columns
    summary_stats = df.describe()
    
    # Count missing values in each column
    missing_values = df.isnull().sum()
    
    # Get percentage of missing values in each column
    missing_percentage = (missing_values / num_rows) * 100
    
    # Print the summary
    print(f"Number of rows: {num_rows}")
    print(f"Number of columns: {num_columns}")
    print("\nColumn Information:")
    print(column_info)
    print("\nSummary Statistics for Numerical Columns:")
    print(summary_stats)
    print("\nMissing Values per Column:")
    print(missing_values)
    print("\nPercentage of Missing Values per Column:")
    print(missing_percentage)```

1. antibiotics_sales.csv
Screenshot 2024-12-04 213916.png

2. updated_resistance.csv

![image](Ldooley32/capstone-dooley/Screenshot 2024-12-04 214126.png)

![image](Ldooley32/capstone-dooley/Screenshot 2024-12-04 214217.png)

![image](Ldooley32/capstone-dooley/Screenshot 2024-12-04 214310.png)
3. avg_per_state.csv

![image](Ldooley32/capstone-dooley/Screenshot 2024-12-04 214340.png)


### Visualization
1. Establish baseline antibiotic usage tends
    a. line graph using antibiotics_sales.csv
            * million kilograms of antibiotic sold for livestock and human per year 


![image](Ldooley32/capstone-dooley/Screenshot 2024-12-04 220229.png)


2. Total number of bacteria resistence per year
    a. line graph using updated_resistance.csv
            * average percent non susceptible per year


![image](Ldooley32/capstone-dooley/Screenshot 2024-12-04 220317.png)



4. Average Standardized Antimicrobial Administration Ratio (SAAR) vs Average percent non susceptible per state
     a. using update_saar.csv and updated_resistance.csv create a new file avg_state.csv which will include the average saar per state and the average precent non susceptible per state
     b. scatterplot using avg_state.csv


![image](Ldooley32/capstone-dooley/Screenshot 2024-12-04 220353.png)




## Machine learing 
1.Determine the top phenotypes in data.

![image](Ldooley32/capstone-dooley/Screenshot 2024-12-04 220511.png)

2.Decision Tree 
- load the dataset
```resistance_full = pd.read_csv('updated_resistance.csv')

top3 = resistance_full['phenotype_id'].isin([1, 8, 21])

top3_resistant_phenotype = resistance_full[top3]
other_reistant_phenotype = resistance_full[~top3]```
- Split the data 20% testing and 80% Training
```splitter = StratifiedShuffleSplit(n_splits=1, test_size=0.2,
random_state=123)

for train_indices, test_indices in splitter.split(top3_resistant_phenotype,
top3_resistant_phenotype['r_present']):
    
    train_set = top3_resistant_phenotype.iloc[train_indices]
test_set = top3_resistant_phenotype.iloc[test_indices]```

- Training Model
```# Correct column names
X = train_set[['eventtype_id', 'agecat_id', 'phenotype_id', 'state_id']] 
y = train_set['r_present']

X_test = test_set[['eventtype_id', 'agecat_id', 'phenotype_id', 'state_id']]  
y_test = test_set['r_present']

# Initialize the Decision Tree Classifier
tree_model = DecisionTreeClassifier()

# Fit the model to the training data
tree_model.fit(X, y)

# Predict using the trained model
y_pred = tree_model.predict(X)

# Results for decision tree on training data
print('Results for decision tree on training data')
print('   Default settings')
print("Confusion Matrix")
print(confusion_matrix(y, y_pred))
print('Accuracy is  ', accuracy_score(y, y_pred))
print('Precision is ', precision_score(y, y_pred, average='weighted'))  # Specify averaging method
print('Recall is    ', recall_score(y, y_pred, average='weighted'))  # Specify averaging method
print('F1 is        ', f1_score(y, y_pred, average='weighted'))  # Specify averaging method```

- Testing Model
```y_test_pred = tree_model.predict(X_test)

print('Results for decision tree on test data')
print('  Default settings')
print("Confusion Matrix ")
print(confusion_matrix(y_test, y_test_pred))
print('Accuracy is  ', accuracy_score(y_test, y_test_pred))
print('Precision is  ', precision_score(y_test, y_test_pred))
print('Recall is  ', recall_score(y_test, y_test_pred))
print('F1 is ', f1_score(y_test, y_test_pred))```



![image](Ldooley32/capstone-dooley/Screenshot 2024-12-04 231006.png)
















