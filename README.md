# Module-5-challenge
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import scipy.stats as st

# Study data files
mouse_metadata_path = "data/Mouse_metadata.csv"
study_results_path = "data/Study_results.csv"

# Read the mouse data and the study results
mouse_metadata = pd.read_csv(mouse_metadata_path)
study_results = pd.read_csv(study_results_path)

# Combine the data into a single dataset
data_merge = pd.merge(study_results, mouse_metadata, how='outer', on=["Mouse ID"])

# Display the data table for preview
data_merge.head()

# Checking the number of mice.
mice_number = data_merge["Mouse ID"].nunique()
mice_number

# Getting the duplicate mice by ID number that shows up for Mouse ID and Timepoint. 
dup_mice = data_merge.loc[data_merge.duplicated(subset=['Mouse ID', 'Timepoint']),'Mouse ID'].unique()
dup_mice

# Optional: Get all the data for the duplicate mouse ID. 
dup_mice = data_merge.loc[data_merge["Mouse ID"] =="g989"]
dup_mice

# Create a clean DataFrame by dropping the duplicate mouse by its ID.
clean_data = data_merge[data_merge["Mouse ID"].isin(dup_mice)==False]
clean_data.head()

# Checking the number of mice in the clean DataFrame.
unique_mice = data_merge["Mouse ID"].nunique()
unique_mice

# Generate a summary statistics table of mean, median, variance, standard deviation, and SEM of the tumor volume for each regimen
# Use groupby and summary statistical methods to calculate the following properties of each drug regimen: 
# mean, median, variance, standard deviation, and SEM of the tumor volume.

mean = clean_data.groupby(["Drug Regimen"])["Tumor Volume (mm3)"].mean()
median = clean_data.groupby(["Drug Regimen"])["Tumor Volume (mm3)"].median()
var = clean_data.groupby(["Drug Regimen"])["Tumor Volume (mm3)"].var()
std = clean_data.groupby(["Drug Regimen"])["Tumor Volume (mm3)"].std()
sem = clean_data.groupby(["Drug Regimen"])["Tumor Volume (mm3)"].sem()

# Assemble the resulting series into a single summary DataFrame.

summary_data = pd.DataFrame({"Mean Tumor Volume":mean, 
                            "Median Tumor Volume":median, 
                           "Tumor Volume Variance":var, 
                           "Tumor Volume Std. Dev.":std, 
                           "Tumor Volume Std. Err.":sem})
summary_data

# Generate a summary statistics table of mean, median, variance, standard deviation, 
# and SEM of the tumor volume for each regimen

# Using the aggregation method, produce the same summary statistics in a single line.
stat_summary_table = clean_data.groupby(["Drug Regimen"])[["Tumor Volume (mm3)"]].agg(["mean", "median", "var", "std", "sem"])

stat_summary_table

# Generate a bar plot showing the total number of timepoints for all mice tested for each drug regimen using Pandas.

mice_total = clean_data["Drug Regimen"].value_counts()

bar_plot = mice_total.plot.bar(color='b')
plt.xlabel("Drug Regimen")
plt.ylabel("Number of Mice")
plt.title("Number of Mice per Treatment")

# mice per regimen 
mice_test = clean_data["Drug Regimen"].value_counts()
mice_test

# Generate a bar plot showing the total number of timepoints for all mice tested for each drug regimen using pyplot.

x_axis = mice_test.index.values
y_axis = mice_test.values

plt.bar(x_axis, y_axis, color='r', alpha=0.8, align='center')

plt.title("Number of Mice Tested per Treatment")
plt.xlabel("Drug Regimen")
plt.ylabel("Number of Mice")
plt.xticks(rotation="vertical")

plt.show()

# Generate a pie plot showing the distribution of female versus male mice using Pandas
gender_data = clean_data["Sex"].value_counts()
plt.title("Female vs. Male Mice")
gender_data.plot.pie(autopct= "%1.1f%%")
plt.show()

# Generate a pie plot showing the distribution of female versus male mice using pyplot
labels = ['Female', 'Male']
sizes = [49.7999197, 50.200803]
plot = gender_data.plot.pie(y='Total Count', autopct="%1.1f%%")
plt.title('Male vs Female Mouse Population')
plt.ylabel('Sex')
plt.show()

# Calculate the final tumor volume of each mouse across four of the treatment regimens:  
# Capomulin, Ramicane, Infubinol, and Ceftamin
Capomulin = clean_data.loc[clean_data["Drug Regimen"] == "Capomulin",:]
Ramicane = clean_data.loc[clean_data["Drug Regimen"] == "Ramicane", :]
Infubinol = clean_data.loc[clean_data["Drug Regimen"] == "Infubinol", :]
Ceftamin = clean_data.loc[clean_data["Drug Regimen"] == "Ceftamin", :]

# Start by getting the last (greatest) timepoint for each mouse
Capomulin_last = Capomulin.groupby('Mouse ID').max()['Timepoint']
Capomulin_vol = pd.DataFrame(Capomulin_last)
Capomulin_merge = pd.merge(Capomulin_vol, clean_data, on=("Mouse ID","Timepoint"),how="left")
Capomulin_merge.head()

