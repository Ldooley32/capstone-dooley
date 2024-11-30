# Predicting the Spread of Antibiotic Resistance: Data-Driven Insights from Antibiotic Usage 

## Author: Laura Dooley
## Date: 11/20/2024

Data Anylsis Capstone Project 

Create virtual environment
`python -m venv venv`

Activate virtual environment
`venv\Scripts\activate`

Install requirements.txt
`pip install -r requirements.txt`

commit and push updates to GitHub
`git add .`
`git commit -m "comment"`
`git push origin master`



Combine multiple tables into one.
    1. original tables in 'data' directory
    2. folders in the file contains multiple tables that need to be combined
        a. 'resistance' directory
        b.  'saar' directory
    2 Use function `def combine_csv_files(input_folder, output_file):` to combine files 

Summarize the 4 files that will be used for analysis using function `summarize_csv`. 

Decide which Column and rows to keep for resistance.csv and saar.csv
    1. resistance.csv
        a. for agecat remove "All" entries, "All" combine "Adult" and "Peds" entries. Leaning "All" will double my results
        b. add columns state_id, phenotype_id, eventtype_id, and agecat_id to switch variables into intergers 
    2. saar.csv
        a. keep columns "year" , "state", "observed antimicrobial days", and "predicted antimicrobial days"
        b. Add column "saar" which is "observed antimicrobial days"/ "predicited antimicrobial days"
        c. Change the state names to abbreviations to match updated_resistance.csv State column

Creat a new file avg_state.csv to plot average saar value and average precent non susceptible for each state. 


Machine learning
graph which phenotypes have the highest percent non susceptiblity.

Use the top three phenotype to predict their precent non susceptibility for 2025-2030. 


### support project goals
1. Establish baseline antibiotic usage tends
    a. line graph using antibiotics_sales.csv
            * million kilograms of antibiotic sold for livestock and human per year 

2. Total number of bacteria resistence per year
    a. line graph using updated_resistance.csv
            * average percent non susceptible per year

3. Compare the trends of both line graphs to each other. 

4. Average Standardized Antimicrobial Administration Ratio (SAAR) vs Average percent non susceptible per state
     a. using update_saar.csv and updated_resistance.csv create a new file avg_state.csv which will include the average saar per state and the average precent non susceptible per state
     b. scatterplot using avg_state.csv

5. Analyize the scatterplot


### Machine learing 
1. Use Decision Tree as a regression model. 
