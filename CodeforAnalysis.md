**Reliability** ------





import pandas as pd

from semopy import Model

from graphviz import Digraph



\# Step 1: Generate Sample Dataset

filepath = "C:\\\\Users\\\\Girisha Jain\\\\What Influenced You.xlsx"



\# Load the specific sheet into a DataFrame

sheet\_name = 'OtherState'  # Replace with the name of your desired sheet

data = pd.read\_excel(filepath, sheet\_name=sheet\_name)

print(data.head())



start\_column = input("Enter the name of the starting column:")

end\_column = input("Enter the name of the ending column:")



df = data.loc\[:, start\_column:end\_column]



def cronbach\_alpha(df):

&#x20;   # 1. Transform the df into a correlation matrix

&#x20;   df\_corr = df.corr()

&#x20;   # 2.1 Calculate N

&#x20;   # The number of variables equals the number of columns in the df

&#x20;   N = df.shape\[1]

&#x20;   #2.2 Calculate R

&#x20;   # For this, we'll loop through the columns and append every

&#x20;   # relevant correlation to an array calles "r\_s". Then, we'll

&#x20;   # calculate the mean of "r\_s"

&#x20;   rs = np.array(\[])

&#x20;   for i, col in enumerate(df\_corr.columns):

&#x20;       sum\_ = df\_corr\[col]\[i+1:].values

&#x20;       rs = np.append(sum\_, rs)

&#x20;   mean\_r = np.mean(rs)

&#x20;   cronbach\_alpha = (N \* mean\_r) / (1 + (N - 1) \* mean\_r)

&#x20;   return cronbach\_alpha





\----------------------- **Exploratory Factor Analysis** ------------------------------------------

import pandas as pd

import numpy as np

from sklearn.model\_selection import train\_test\_split

from factor\_analyzer import FactorAnalyzer

from factor\_analyzer.factor\_analyzer import calculate\_kmo, calculate\_bartlett\_sphericity

import seaborn as sns

import matplotlib.pyplot as plt

from semopy import Model





df2 = pd.read\_excel('C:\\\\Users\\\\Girisha Jain\\\\MainData.xlsx')

df = df2.iloc\[:,1:32]



df.columns



\# Define the split ratio (e.g., 50% for EFA, 50% for CFA)



efa\_size = 0.5  # Adjust this ratio as needed



\# Perform the split



efa\_data, cfa\_data = train\_test\_split(df1, test\_size=efa\_size, random\_state=42)



for col in df.columns:

&#x20;   print(f"{col}: {df\[col].unique()}")

&#x20;   # Define the value mapping



response\_map = {

&#x20;   'Very Unimportant': 1,

&#x20;   'Unimportant': 2,

&#x20;   'Neutral': 3,

&#x20;   'Important': 4,

&#x20;   'Very Important': 5,

&#x20;   'No Influence at all': 1,

&#x20;   'Very little Influence': 2,

&#x20;   'Moderate Influence':3,

&#x20;   'Significant Influence':4,

&#x20;   'Extreme Influence':5

}



df1 = df.replace(response\_map)



df1.corr()



import seaborn as sns

import matplotlib.pyplot as plt

import numpy as np



\# Compute correlation matrix

corr\_matrix = df1.corr()



\# Mask upper triangle to avoid duplicate values

mask = np.triu(np.ones\_like(corr\_matrix, dtype=bool))



\# Plot heatmap

plt.figure(figsize=(12, 10))  

sns.heatmap(

&#x20;   corr\_matrix, 

&#x20;   mask=mask, 

&#x20;   annot=True, 

&#x20;   fmt=".2f", 

&#x20;   cmap="coolwarm", 

&#x20;   linewidths=0.5, 

&#x20;   cbar\_kws={"shrink": 0.8}, 

&#x20;   annot\_kws={"size":8}  # control annotation font size

)



plt.title("Correlation Matrix Heatmap", fontsize=14, weight="bold", pad=20)

plt.xticks(rotation=45, ha="right", fontsize=9)

plt.yticks(fontsize=9)

plt.tight\_layout()

plt.show()





\---------Statistical tests ------------

\# KMO Test

kmo\_all, kmo\_model = calculate\_kmo(df1)

print("KMO Model Value:", round(kmo\_model, 3))  # should be > 0.6



chi\_square\_value, p\_value = calculate\_bartlett\_sphericity(df1)

print("Bartlett’s Test p-value:", p\_value)  # should be < 0.05



\# Initialize and fit Factor Analyzer

fa = FactorAnalyzer(n\_factors=5, rotation='varimax')  # Adjust n\_factors as needed

fa.fit(efa\_data)



import matplotlib.pyplot as plt

import matplotlib as mpl

import numpy as np



\# Set font

mpl.rcParams\['font.family'] = 'Book Antiqua'

mpl.rcParams\['font.size'] = 14



\# Scree Plot

ev, \_ = fa.get\_eigenvalues()

plt.plot(range(1, len(ev)+1), ev, marker="o", color="#8e883d", label='Eigenvalues')



\# Highlight the elbow point (example: at index 3)

elbow\_idx = 3  # Change as needed (1-based index)

plt.plot(elbow\_idx, ev\[elbow\_idx - 1], marker='\*', color='#494623', markersize=10, label='Elbow Point')



\# Labels and grid

plt.xlabel("Number of Factors")

plt.ylabel("Eigenvalue")

plt.title("Scree Plot")

plt.grid(True)

plt.legend()

plt.show()



\----------------------------**Confirmatory Factor Analysis** -----------------



cfa\_data.columns



model\_desc = """

\# Measurement model

Behavioural\_Beliefs =\~ BB\_ToU + BB\_AcademicReputation + BB\_ProgramOffered + BB\_CourseCurriculum + BB\_CulturalExperience + BB\_Facilities\_resources + BB\_SafetyCampus + BB\_DressCode + BB\_Infrastructure + BB\_FacultyProfile + BB\_Discipline + BB\_CareerOpportunities + BB\_HS

Normative\_Beliefs =\~ SN\_Family + SN\_Friends + SN\_Seniors + SN\_RelativesNeighbors + SN\_Teachers + SN\_SameBehaviour + Selfesteem

Perceived\_Behavioural =\~ PB\_ScholarshipAvailability + PB\_UniversityTeamGuidance + PB\_LanguageSpoken + PB\_AccomodationCostofLiving + PB\_SafetyinCampus + PB\_UniversitylocatedCity + PB\_CourseFee + PB\_Ranking + PB\_ReputationBrand + PB\_AvailabilityofCourse + PB\_NearbytoHome 

"""



\# Step 3: Create and fit the model

model = Model(model\_desc)

model.fit(cfa\_data)



\# Step 4: Output the results

results = model.inspect()

print(results)



results.to\_csv("CFA\_results.csv", index=False)







\# Get model fit statistics

stats = calc\_stats(model)

print(stats)



for key, value in stats.items():

&#x20;   print(f"{key}: {value}")



print("Sample size:", cfa\_data.shape\[0])



mod.inspect(std\_est=True)



model\_desc = """

\# Measurement model

Behavioural\_Beliefs =\~ BB\_ToU + BB\_AcademicReputation + BB\_ProgramOffered + BB\_CourseCurriculum + BB\_CulturalExperience + BB\_Facilities\_resources + BB\_SafetyCampus + BB\_DressCode + BB\_Infrastructure + BB\_FacultyProfile + BB\_Discipline + BB\_CareerOpportunities + BB\_HS

Normative\_Beliefs =\~ SN\_Family + SN\_Friends + SN\_Seniors + SN\_RelativesNeighbors + SN\_Teachers

Perceived\_Behavioural =\~ PB\_ScholarshipAvailability + PB\_UniversityTeamGuidance + PB\_LanguageSpoken + PB\_AccomodationCostofLiving + PB\_SafetyinCampus + PB\_UniversitylocatedCity + PB\_CourseFee + PB\_Ranking + PB\_ReputationBrand + PB\_AvailabilityofCourse + PB\_NearbytoHome 

"""



\# Step 3: Create and fit the model

model = Model(model\_desc)

model.fit(cfa\_data)



\# Step 4: Output the results

results = model.inspect()

print(results)



results\_std = model.inspect(std\_est=True)

print(results\_std)



mod.inspect(std\_est=True)



\# ✅ Fit the model

model = Model(model\_desc)

model.fit(cfa\_data)



\# ✅ Create and save the path diagram (top-down layout only)

semplot(model, "cfa\_diagram\_topdown.png")



print("✅ CFA diagram saved as 'cfa\_diagram\_topdown.png'")







import pandas as pd



\# Filter loadings and error variances using updated column names

loadings = results.query('op == "\~"')\[\['lval', 'rval', 'Estimate']]

errors = results.query('op == "\~\~" and lval == rval')\[\['lval', 'Estimate']]



\# Compute CR and AVE

latent\_vars = loadings\['lval'].unique()

validity = \[]



for lv in latent\_vars:

&#x20;   lv\_loadings = loadings\[loadings\['lval'] == lv]\['Estimate'].values

&#x20;   lv\_items = loadings\[loadings\['lval'] == lv]\['rval'].values

&#x20;   lv\_errors = errors\[errors\['lval'].isin(lv\_items)]\['Estimate'].values



&#x20;   CR = (sum(lv\_loadings))\*\*2 / ((sum(lv\_loadings))\*\*2 + sum(lv\_errors))

&#x20;   AVE = sum(lv\_loadings\*\*2) / len(lv\_loadings)



&#x20;   validity.append({'Latent Variable': lv, 'CR': round(CR, 3), 'AVE': round(AVE, 3)})



cr\_ave\_df = pd.DataFrame(validity)

print("🔍 Convergent Validity:")

print(cr\_ave\_df)



\# Merge the constructs and their standardized loadings

ave\_dict = {}



for construct, items in construct\_items.items():

&#x20;   squared\_loadings = loadings\_df\[

&#x20;       (loadings\_df\['rval'] == construct) \&

&#x20;       (loadings\_df\['lval'].isin(items))

&#x20;   ]\['Est. Std'].astype(float) \*\* 2

&#x20;   

&#x20;   ave = squared\_loadings.mean()

&#x20;   ave\_dict\[construct] = ave



constructs = list(construct\_items.keys())

fl\_matrix = pd.DataFrame(index=constructs, columns=constructs)



\# Fill Fornell-Larcker matrix

for c1 in constructs:

&#x20;   for c2 in constructs:

&#x20;       if c1 == c2:

&#x20;           fl\_matrix.loc\[c1, c2] = round(np.sqrt(ave\_dict\[c1]), 3)

&#x20;       else:

&#x20;           # For between-construct correlations, extract from your latent variable correlation matrix

&#x20;           # If not available, placeholder: assume 0.5

&#x20;           fl\_matrix.loc\[c1, c2] = "Need latent correlation matrix"



print("📊 Fornell-Larcker Matrix:")

print(fl\_matrix)



def compute\_htmt(data, construct\_items):

&#x20;   constructs = list(construct\_items.keys())

&#x20;   htmt\_matrix = pd.DataFrame(index=constructs, columns=constructs)



&#x20;   for c1 in constructs:

&#x20;       for c2 in constructs:

&#x20;           if c1 == c2:

&#x20;               htmt\_matrix.loc\[c1, c2] = 1.0

&#x20;           else:

&#x20;               items1 = construct\_items\[c1]

&#x20;               items2 = construct\_items\[c2]

&#x20;               cor\_matrix = data\[items1 + items2].corr()



&#x20;               # Heterotrait-Monotrait correlations

&#x20;               het\_trait = cor\_matrix.loc\[items1, items2].abs().values.flatten()



&#x20;               # Mono-trait for construct 1

&#x20;               mt1 = cor\_matrix.loc\[items1, items1]

&#x20;               mask1 = np.triu(np.ones(mt1.shape), k=1).astype(bool)

&#x20;               mask1\_df = pd.DataFrame(mask1, index=mt1.index, columns=mt1.columns)

&#x20;               mono\_trait1 = mt1.where(mask1\_df).stack().abs().values



&#x20;               # Mono-trait for construct 2

&#x20;               mt2 = cor\_matrix.loc\[items2, items2]

&#x20;               mask2 = np.triu(np.ones(mt2.shape), k=1).astype(bool)

&#x20;               mask2\_df = pd.DataFrame(mask2, index=mt2.index, columns=mt2.columns)

&#x20;               mono\_trait2 = mt2.where(mask2\_df).stack().abs().values



&#x20;               # Combine and compute HTMT

&#x20;               denominator = np.mean(np.concatenate(\[mono\_trait1, mono\_trait2]))

&#x20;               htmt\_val = np.mean(het\_trait) / denominator if denominator != 0 else 0

&#x20;               htmt\_matrix.loc\[c1, c2] = round(htmt\_val, 3)



&#x20;   return htmt\_matrix



htmt\_result = compute\_htmt(cfa\_data, construct\_items)

print("\\n📊 HTMT Matrix:")

print(htmt\_result)



for key, value in stats.items():

&#x20;   print(f"{key}: {value}")









