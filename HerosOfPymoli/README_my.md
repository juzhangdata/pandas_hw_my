
# Heroes Of Pymoli Data Analysis

•	OBSERVED TREND 1: The percentage of female and male in players (17.45% and 81.15%) is similar to the percentage of purchases made by female and male(16.74 and 81.69%).  

•	OBSERVED TREND 2: Item with ID 39 and name "Betrayal, Whisper of Grieving Widows" and item with ID 84 and name "Arcane Gem" are the most popular items, with 11 purchases each.  

•	OBSERVED TREND 3: Item with ID 34 and name "Retribution Axe" is the most profitable item, bringing in $37.26 revenue.


```python
# Import dependencies
import pandas as pd
import numpy as np

#set max rows viewable
pd.options.display.max_rows = 400

# Read the .json file
file_path = "./purchase_data.json"
game_df = pd.read_json(file_path, orient = "columns", encoding = "UTF-8")

# Optional: explore the data
# print(game_df.count().head(), "\n", "\n",
# game_df.columns, "\n", "\n",
# game_df["Item ID"].value_counts().head(), "\n", "\n",
# game_df["Item Name"].value_counts().head(), "\n", "\n",
# game_df["SN"].value_counts().head(), "\n", "\n",
# game_df.head())
```

# Player Count


```python
# Count unique players
total_players = len(game_df["SN"].unique())

# Convert the value into a DataFrame
pd.DataFrame(
    {
        "Total Players":[str(total_players)]
    }
)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Total)


```python
# Calculations
number_of_unique_items = len(game_df["Item ID"].unique())
total_revenue = game_df["Price"].sum()
average_purchase_price = total_revenue / len(game_df)
total_number_of_purchases = len(game_df)

# Save results into a dataframe
analysis_total_df = pd.DataFrame(
    {
    "Number of Unique Items" : [str(number_of_unique_items)],
    "Average Purchase Price" : ["$" + ("{:.2f}".format(average_purchase_price))],
    "Total Number of Purchases" : [str(total_number_of_purchases)],
    "Total Revenue" : ["$" + str(total_revenue)]
    }
)

# Arrange columns of the dataframe
analysis_total_df[["Number of Unique Items","Average Purchase Price","Total Number of Purchases","Total Revenue"]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Purchase Price</th>
      <th>Total Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>



# Gender Demographics


```python
# Group the dataframe by players, and convert the data series into a dataframe
sn_group = game_df.groupby(game_df["SN"])
sn_df = pd.DataFrame(sn_group.max())

# Count males and females, and calculate percentage of males and females
gender_df = pd.DataFrame(sn_df["Gender"].value_counts())
gender_df["Percentage of Players"] = gender_df["Gender"] / total_players * 100

# Rename the Gender column
gender_df = gender_df.rename(columns = {"Gender" : "Total Count"})

# Format percentage, and view in the desired column sequence
gender_df["Percentage of Players"] = gender_df["Percentage of Players"].map("{:.2f}".format)

# Arrange columns for viewing
gender_df[["Percentage of Players","Total\xa0Count"]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Gender)


```python
# Using GroupBy in order to separate the data into fields according to "Gender" values
gender_group = game_df.groupby(["Gender"])
gender_group.count()
purchase_count = gender_group["Price"].count()
purchase_total = gender_group["Price"].sum()

# Creating a new DataFrame using both values and count
gender_analysis = pd.DataFrame(
    {
        "Purchase Count" : purchase_count,
        "Total Purchase Value" : purchase_total
    }
)

# Calculate average purchase price, and normalized totals
gender_analysis["Average Purchase Price"] = purchase_total / purchase_count
a = gender_analysis["Total Purchase Value"]
gender_analysis["Normalized Totals"] = (a-a.min())/(a.max()-a.min())

# Format columns to two decimals, and add $
gender_analysis["Total Purchase Value"] = gender_analysis["Total Purchase Value"].map("${:.2f}".format)
gender_analysis["Average Purchase Price"] = gender_analysis["Average Purchase Price"].map("${:.2f}".format)
gender_analysis["Normalized Totals"] = gender_analysis["Normalized Totals"].map("{:.2f}".format)

# Arrange columns for viewing
gender_analysis[["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>0.19</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>0.00</td>
    </tr>
  </tbody>
</table>
</div>



# Age Demographics


```python
# Find out the mininmum and maximum age
# print(game_df["Age"].min(), game_df["Age"].max())

# Generate bins and labels based on min and max
bins = []
for i in range(1,13):
    bins.append(4 * i)
group_names = ["04-08", "08-12", "12-16", "16-20", "20-24", "24-28", "28-32", "32-36", "36-40", "40-44", "44-48"]

# Cut Age and place the values into bins
game_df["Age Summary"] = pd.cut(game_df["Age"], bins, labels=group_names)
age_group = game_df.groupby("Age Summary")

# Get data series of purchase_count and data frame of total_purchase_value, and create a new dataframe
purchase_count = game_df["Age Summary"].value_counts()
total_purchase_value = age_group["Price"].sum()
age_demo = pd.DataFrame(
    {
        "Purchase Count" : purchase_count,
        "Total Purchase Value" : total_purchase_value
    }
)

# Calculate average and normalized value:
age_demo["Average Purchase Price"] = age_demo["Total Purchase Value"] / age_demo["Purchase Count"]
a = age_demo["Total Purchase Value"]
age_demo["Normalized Totals"] = (a-a.min())/(a.max()-a.min())

# Format columns to two decimals, and add $
age_demo["Total Purchase Value"] = age_demo["Total Purchase Value"].map("${:.2f}".format)
age_demo["Average Purchase Price"] = age_demo["Average Purchase Price"].map("${:.2f}".format)
age_demo["Normalized Totals"] = age_demo["Normalized Totals"].map("{:.2f}".format)

# Arrange rows and columns for viewing
age_demo[["Purchase Count", "Average Purchase Price", "Total Purchase Value","Normalized Totals"]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>04-08</th>
      <td>22</td>
      <td>$2.79</td>
      <td>$61.34</td>
      <td>0.08</td>
    </tr>
    <tr>
      <th>08-12</th>
      <td>24</td>
      <td>$3.39</td>
      <td>$81.25</td>
      <td>0.11</td>
    </tr>
    <tr>
      <th>12-16</th>
      <td>87</td>
      <td>$2.75</td>
      <td>$238.89</td>
      <td>0.34</td>
    </tr>
    <tr>
      <th>16-20</th>
      <td>161</td>
      <td>$2.91</td>
      <td>$468.03</td>
      <td>0.67</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>238</td>
      <td>$2.92</td>
      <td>$696.09</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>24-28</th>
      <td>104</td>
      <td>$2.97</td>
      <td>$309.37</td>
      <td>0.44</td>
    </tr>
    <tr>
      <th>28-32</th>
      <td>66</td>
      <td>$3.06</td>
      <td>$202.09</td>
      <td>0.29</td>
    </tr>
    <tr>
      <th>32-36</th>
      <td>38</td>
      <td>$2.98</td>
      <td>$113.28</td>
      <td>0.16</td>
    </tr>
    <tr>
      <th>36-40</th>
      <td>37</td>
      <td>$2.90</td>
      <td>$107.35</td>
      <td>0.15</td>
    </tr>
    <tr>
      <th>40-44</th>
      <td>2</td>
      <td>$2.96</td>
      <td>$5.92</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>44-48</th>
      <td>1</td>
      <td>$2.72</td>
      <td>$2.72</td>
      <td>0.00</td>
    </tr>
  </tbody>
</table>
</div>



# Top Spenders


```python
# Groupby and get data series of purchase_count and total_purchase_value
purchase_count = game_df["SN"].value_counts()
total_purchase_value = sn_group["Price"].sum()

# Create a new dataframe from data series
top_spenders = pd.DataFrame(
    {
        "Purchase Count" : purchase_count,
        "Total Purchase Value" : total_purchase_value
    }
)

#calculate average
top_spenders["Average Purchase Price"] = total_purchase_value / purchase_count

#format into decimals and add $
top_spenders["Average Purchase Price"] = top_spenders["Average Purchase Price"].map("${:.2f}".format)
top_spenders["Total Purchase Value"] = top_spenders["Total Purchase Value"].map("${:.2f}".format)

# Arrange columns for viewing
top_spenders[["Purchase Count", "Average Purchase Price", "Total Purchase Value"]]

# Sort dataframe by purchase count
top_spenders.sort_values(by = ["Purchase Count"],ascending=False)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Average Purchase Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$17.06</td>
      <td>$3.41</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$12.74</td>
      <td>$3.18</td>
    </tr>
    <tr>
      <th>Qarwen67</th>
      <td>4</td>
      <td>$9.97</td>
      <td>$2.49</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$13.56</td>
      <td>$3.39</td>
    </tr>
    <tr>
      <th>Sondastan54</th>
      <td>4</td>
      <td>$10.24</td>
      <td>$2.56</td>
    </tr>
    <tr>
      <th>Hailaphos89</th>
      <td>4</td>
      <td>$5.87</td>
      <td>$1.47</td>
    </tr>
    <tr>
      <th>Yadanun74</th>
      <td>3</td>
      <td>$9.09</td>
      <td>$3.03</td>
    </tr>
    <tr>
      <th>Lirtosia72</th>
      <td>3</td>
      <td>$8.37</td>
      <td>$2.79</td>
    </tr>
    <tr>
      <th>Seorithstilis90</th>
      <td>3</td>
      <td>$8.39</td>
      <td>$2.80</td>
    </tr>
    <tr>
      <th>Eusri70</th>
      <td>3</td>
      <td>$10.55</td>
      <td>$3.52</td>
    </tr>
    <tr>
      <th>Chanastsda67</th>
      <td>3</td>
      <td>$8.96</td>
      <td>$2.99</td>
    </tr>
    <tr>
      <th>Haerith37</th>
      <td>3</td>
      <td>$10.14</td>
      <td>$3.38</td>
    </tr>
    <tr>
      <th>Mindirra92</th>
      <td>3</td>
      <td>$9.01</td>
      <td>$3.00</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$12.73</td>
      <td>$4.24</td>
    </tr>
    <tr>
      <th>Frichaya88</th>
      <td>3</td>
      <td>$9.16</td>
      <td>$3.05</td>
    </tr>
    <tr>
      <th>Frichosiala98</th>
      <td>3</td>
      <td>$8.94</td>
      <td>$2.98</td>
    </tr>
    <tr>
      <th>Chamistast30</th>
      <td>3</td>
      <td>$4.49</td>
      <td>$1.50</td>
    </tr>
    <tr>
      <th>Isursti83</th>
      <td>3</td>
      <td>$11.05</td>
      <td>$3.68</td>
    </tr>
    <tr>
      <th>Isurria36</th>
      <td>3</td>
      <td>$11.01</td>
      <td>$3.67</td>
    </tr>
    <tr>
      <th>Tillyrin30</th>
      <td>3</td>
      <td>$9.19</td>
      <td>$3.06</td>
    </tr>
    <tr>
      <th>Aduephos78</th>
      <td>3</td>
      <td>$6.70</td>
      <td>$2.23</td>
    </tr>
    <tr>
      <th>Aerithllora36</th>
      <td>3</td>
      <td>$10.45</td>
      <td>$3.48</td>
    </tr>
    <tr>
      <th>Sondim43</th>
      <td>3</td>
      <td>$9.38</td>
      <td>$3.13</td>
    </tr>
    <tr>
      <th>Aeduera68</th>
      <td>3</td>
      <td>$5.80</td>
      <td>$1.93</td>
    </tr>
    <tr>
      <th>Ila44</th>
      <td>3</td>
      <td>$7.84</td>
      <td>$2.61</td>
    </tr>
    <tr>
      <th>Chadossa56</th>
      <td>3</td>
      <td>$7.85</td>
      <td>$2.62</td>
    </tr>
    <tr>
      <th>Chadjask77</th>
      <td>3</td>
      <td>$10.01</td>
      <td>$3.34</td>
    </tr>
    <tr>
      <th>Yasriphos60</th>
      <td>3</td>
      <td>$10.40</td>
      <td>$3.47</td>
    </tr>
    <tr>
      <th>Lisossa63</th>
      <td>3</td>
      <td>$5.92</td>
      <td>$1.97</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$11.58</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>Lisistaya47</th>
      <td>3</td>
      <td>$9.19</td>
      <td>$3.06</td>
    </tr>
    <tr>
      <th>Ialistidru50</th>
      <td>3</td>
      <td>$7.34</td>
      <td>$2.45</td>
    </tr>
    <tr>
      <th>Saellyra72</th>
      <td>2</td>
      <td>$7.18</td>
      <td>$3.59</td>
    </tr>
    <tr>
      <th>Iathenudil29</th>
      <td>2</td>
      <td>$4.88</td>
      <td>$2.44</td>
    </tr>
    <tr>
      <th>Iaralrgue74</th>
      <td>2</td>
      <td>$6.36</td>
      <td>$3.18</td>
    </tr>
    <tr>
      <th>Hilaerin92</th>
      <td>2</td>
      <td>$6.87</td>
      <td>$3.44</td>
    </tr>
    <tr>
      <th>Haedasu65</th>
      <td>2</td>
      <td>$8.49</td>
      <td>$4.25</td>
    </tr>
    <tr>
      <th>Saistyphos30</th>
      <td>2</td>
      <td>$5.98</td>
      <td>$2.99</td>
    </tr>
    <tr>
      <th>Iadueria43</th>
      <td>2</td>
      <td>$4.50</td>
      <td>$2.25</td>
    </tr>
    <tr>
      <th>Seosri62</th>
      <td>2</td>
      <td>$5.53</td>
      <td>$2.76</td>
    </tr>
    <tr>
      <th>Saida58</th>
      <td>2</td>
      <td>$6.74</td>
      <td>$3.37</td>
    </tr>
    <tr>
      <th>Sausosia74</th>
      <td>2</td>
      <td>$5.59</td>
      <td>$2.79</td>
    </tr>
    <tr>
      <th>Shaidanu32</th>
      <td>2</td>
      <td>$8.25</td>
      <td>$4.12</td>
    </tr>
    <tr>
      <th>Lassassasda30</th>
      <td>2</td>
      <td>$6.07</td>
      <td>$3.04</td>
    </tr>
    <tr>
      <th>Frichistasta59</th>
      <td>2</td>
      <td>$4.17</td>
      <td>$2.08</td>
    </tr>
    <tr>
      <th>Sida61</th>
      <td>2</td>
      <td>$6.34</td>
      <td>$3.17</td>
    </tr>
    <tr>
      <th>Eoralphos86</th>
      <td>2</td>
      <td>$4.25</td>
      <td>$2.12</td>
    </tr>
    <tr>
      <th>Sundast29</th>
      <td>2</td>
      <td>$4.42</td>
      <td>$2.21</td>
    </tr>
    <tr>
      <th>Eosur70</th>
      <td>2</td>
      <td>$5.85</td>
      <td>$2.92</td>
    </tr>
    <tr>
      <th>Eosurdru76</th>
      <td>2</td>
      <td>$7.24</td>
      <td>$3.62</td>
    </tr>
    <tr>
      <th>Stryanastip77</th>
      <td>2</td>
      <td>$4.46</td>
      <td>$2.23</td>
    </tr>
    <tr>
      <th>Erudrion71</th>
      <td>2</td>
      <td>$2.66</td>
      <td>$1.33</td>
    </tr>
    <tr>
      <th>Streural92</th>
      <td>2</td>
      <td>$5.06</td>
      <td>$2.53</td>
    </tr>
    <tr>
      <th>Ethruard50</th>
      <td>2</td>
      <td>$5.70</td>
      <td>$2.85</td>
    </tr>
    <tr>
      <th>Ethrusuard41</th>
      <td>2</td>
      <td>$7.93</td>
      <td>$3.96</td>
    </tr>
    <tr>
      <th>Sondossa55</th>
      <td>2</td>
      <td>$7.44</td>
      <td>$3.72</td>
    </tr>
    <tr>
      <th>Eulidru49</th>
      <td>2</td>
      <td>$3.33</td>
      <td>$1.67</td>
    </tr>
    <tr>
      <th>Eural50</th>
      <td>2</td>
      <td>$7.68</td>
      <td>$3.84</td>
    </tr>
    <tr>
      <th>Eurallo89</th>
      <td>2</td>
      <td>$5.60</td>
      <td>$2.80</td>
    </tr>
    <tr>
      <th>Sondilsa40</th>
      <td>2</td>
      <td>$3.80</td>
      <td>$1.90</td>
    </tr>
    <tr>
      <th>Eurisuru25</th>
      <td>2</td>
      <td>$8.29</td>
      <td>$4.14</td>
    </tr>
    <tr>
      <th>Eusur90</th>
      <td>2</td>
      <td>$6.06</td>
      <td>$3.03</td>
    </tr>
    <tr>
      <th>Farenon57</th>
      <td>2</td>
      <td>$6.64</td>
      <td>$3.32</td>
    </tr>
    <tr>
      <th>Farusrian86</th>
      <td>2</td>
      <td>$6.81</td>
      <td>$3.41</td>
    </tr>
    <tr>
      <th>Filrion44</th>
      <td>2</td>
      <td>$6.11</td>
      <td>$3.06</td>
    </tr>
    <tr>
      <th>Ryanara76</th>
      <td>2</td>
      <td>$7.00</td>
      <td>$3.50</td>
    </tr>
    <tr>
      <th>Frichassala85</th>
      <td>2</td>
      <td>$2.85</td>
      <td>$1.43</td>
    </tr>
    <tr>
      <th>Idai61</th>
      <td>2</td>
      <td>$3.60</td>
      <td>$1.80</td>
    </tr>
    <tr>
      <th>Ilogha82</th>
      <td>2</td>
      <td>$8.61</td>
      <td>$4.30</td>
    </tr>
    <tr>
      <th>Rithe77</th>
      <td>2</td>
      <td>$3.51</td>
      <td>$1.75</td>
    </tr>
    <tr>
      <th>Mindilsa60</th>
      <td>2</td>
      <td>$6.37</td>
      <td>$3.19</td>
    </tr>
    <tr>
      <th>Lassast89</th>
      <td>2</td>
      <td>$6.78</td>
      <td>$3.39</td>
    </tr>
    <tr>
      <th>Lassilsa41</th>
      <td>2</td>
      <td>$4.42</td>
      <td>$2.21</td>
    </tr>
    <tr>
      <th>Lassjask63</th>
      <td>2</td>
      <td>$4.80</td>
      <td>$2.40</td>
    </tr>
    <tr>
      <th>Lassjaskan73</th>
      <td>2</td>
      <td>$7.98</td>
      <td>$3.99</td>
    </tr>
    <tr>
      <th>Liri91</th>
      <td>2</td>
      <td>$5.59</td>
      <td>$2.79</td>
    </tr>
    <tr>
      <th>Lirtassa47</th>
      <td>2</td>
      <td>$2.09</td>
      <td>$1.04</td>
    </tr>
    <tr>
      <th>Narirra38</th>
      <td>2</td>
      <td>$8.42</td>
      <td>$4.21</td>
    </tr>
    <tr>
      <th>Lirtistasta79</th>
      <td>2</td>
      <td>$3.76</td>
      <td>$1.88</td>
    </tr>
    <tr>
      <th>Lirtossa78</th>
      <td>2</td>
      <td>$3.58</td>
      <td>$1.79</td>
    </tr>
    <tr>
      <th>Lisasi93</th>
      <td>2</td>
      <td>$6.42</td>
      <td>$3.21</td>
    </tr>
    <tr>
      <th>Lisassasta50</th>
      <td>2</td>
      <td>$6.59</td>
      <td>$3.29</td>
    </tr>
    <tr>
      <th>Pharithdil38</th>
      <td>2</td>
      <td>$5.96</td>
      <td>$2.98</td>
    </tr>
    <tr>
      <th>Meosridil82</th>
      <td>2</td>
      <td>$3.82</td>
      <td>$1.91</td>
    </tr>
    <tr>
      <th>Lisirra55</th>
      <td>2</td>
      <td>$5.79</td>
      <td>$2.90</td>
    </tr>
    <tr>
      <th>Marirrasta50</th>
      <td>2</td>
      <td>$6.22</td>
      <td>$3.11</td>
    </tr>
    <tr>
      <th>Marim28</th>
      <td>2</td>
      <td>$6.56</td>
      <td>$3.28</td>
    </tr>
    <tr>
      <th>Lisjaskan36</th>
      <td>2</td>
      <td>$7.68</td>
      <td>$3.84</td>
    </tr>
    <tr>
      <th>Lisjasksda68</th>
      <td>2</td>
      <td>$4.05</td>
      <td>$2.02</td>
    </tr>
    <tr>
      <th>Lisosiast26</th>
      <td>2</td>
      <td>$6.74</td>
      <td>$3.37</td>
    </tr>
    <tr>
      <th>Lisossa25</th>
      <td>2</td>
      <td>$3.44</td>
      <td>$1.72</td>
    </tr>
    <tr>
      <th>Lisossan98</th>
      <td>2</td>
      <td>$9.10</td>
      <td>$4.55</td>
    </tr>
    <tr>
      <th>Lisovynya38</th>
      <td>2</td>
      <td>$5.15</td>
      <td>$2.58</td>
    </tr>
    <tr>
      <th>Lassassast73</th>
      <td>2</td>
      <td>$5.11</td>
      <td>$2.55</td>
    </tr>
    <tr>
      <th>Jiskossa51</th>
      <td>2</td>
      <td>$8.14</td>
      <td>$4.07</td>
    </tr>
    <tr>
      <th>Iladarla40</th>
      <td>2</td>
      <td>$3.91</td>
      <td>$1.96</td>
    </tr>
    <tr>
      <th>Raerithsti62</th>
      <td>2</td>
      <td>$6.77</td>
      <td>$3.38</td>
    </tr>
    <tr>
      <th>Rinallorap73</th>
      <td>2</td>
      <td>$6.32</td>
      <td>$3.16</td>
    </tr>
    <tr>
      <th>Reuthelis39</th>
      <td>2</td>
      <td>$3.87</td>
      <td>$1.94</td>
    </tr>
    <tr>
      <th>Ililsa62</th>
      <td>2</td>
      <td>$7.40</td>
      <td>$3.70</td>
    </tr>
    <tr>
      <th>Reula64</th>
      <td>2</td>
      <td>$7.57</td>
      <td>$3.79</td>
    </tr>
    <tr>
      <th>Rathellorin54</th>
      <td>2</td>
      <td>$2.99</td>
      <td>$1.50</td>
    </tr>
    <tr>
      <th>Indcil77</th>
      <td>2</td>
      <td>$8.53</td>
      <td>$4.27</td>
    </tr>
    <tr>
      <th>Ingatcil75</th>
      <td>2</td>
      <td>$8.44</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>Raillydeu47</th>
      <td>2</td>
      <td>$6.02</td>
      <td>$3.01</td>
    </tr>
    <tr>
      <th>Raesursurap33</th>
      <td>2</td>
      <td>$5.20</td>
      <td>$2.60</td>
    </tr>
    <tr>
      <th>Raesty92</th>
      <td>2</td>
      <td>$5.46</td>
      <td>$2.73</td>
    </tr>
    <tr>
      <th>Iskadarya95</th>
      <td>2</td>
      <td>$8.79</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>Jiskassa76</th>
      <td>2</td>
      <td>$3.83</td>
      <td>$1.92</td>
    </tr>
    <tr>
      <th>Iskimnya76</th>
      <td>2</td>
      <td>$5.82</td>
      <td>$2.91</td>
    </tr>
    <tr>
      <th>Queusurra38</th>
      <td>2</td>
      <td>$5.40</td>
      <td>$2.70</td>
    </tr>
    <tr>
      <th>Iskista88</th>
      <td>2</td>
      <td>$5.07</td>
      <td>$2.54</td>
    </tr>
    <tr>
      <th>Quarusrion32</th>
      <td>2</td>
      <td>$2.83</td>
      <td>$1.42</td>
    </tr>
    <tr>
      <th>Iskosia51</th>
      <td>2</td>
      <td>$6.70</td>
      <td>$3.35</td>
    </tr>
    <tr>
      <th>Iskossa88</th>
      <td>2</td>
      <td>$5.45</td>
      <td>$2.72</td>
    </tr>
    <tr>
      <th>Iskossan49</th>
      <td>2</td>
      <td>$6.18</td>
      <td>$3.09</td>
    </tr>
    <tr>
      <th>Iskossaya95</th>
      <td>2</td>
      <td>$3.13</td>
      <td>$1.56</td>
    </tr>
    <tr>
      <th>Qilatie51</th>
      <td>2</td>
      <td>$6.81</td>
      <td>$3.41</td>
    </tr>
    <tr>
      <th>Ithergue48</th>
      <td>2</td>
      <td>$6.42</td>
      <td>$3.21</td>
    </tr>
    <tr>
      <th>Sundaststa26</th>
      <td>2</td>
      <td>$5.80</td>
      <td>$2.90</td>
    </tr>
    <tr>
      <th>Malunil62</th>
      <td>2</td>
      <td>$8.89</td>
      <td>$4.45</td>
    </tr>
    <tr>
      <th>Chadanto83</th>
      <td>2</td>
      <td>$6.23</td>
      <td>$3.12</td>
    </tr>
    <tr>
      <th>Tyananurgue44</th>
      <td>2</td>
      <td>$6.55</td>
      <td>$3.28</td>
    </tr>
    <tr>
      <th>Chanirrala39</th>
      <td>2</td>
      <td>$5.56</td>
      <td>$2.78</td>
    </tr>
    <tr>
      <th>Yarithsurgue62</th>
      <td>2</td>
      <td>$4.81</td>
      <td>$2.41</td>
    </tr>
    <tr>
      <th>Assastnya25</th>
      <td>2</td>
      <td>$4.75</td>
      <td>$2.38</td>
    </tr>
    <tr>
      <th>Chanastst38</th>
      <td>2</td>
      <td>$6.42</td>
      <td>$3.21</td>
    </tr>
    <tr>
      <th>Alaephos75</th>
      <td>2</td>
      <td>$6.63</td>
      <td>$3.31</td>
    </tr>
    <tr>
      <th>Aeri84</th>
      <td>2</td>
      <td>$6.60</td>
      <td>$3.30</td>
    </tr>
    <tr>
      <th>Tyaelo67</th>
      <td>2</td>
      <td>$5.48</td>
      <td>$2.74</td>
    </tr>
    <tr>
      <th>Aidain51</th>
      <td>2</td>
      <td>$6.84</td>
      <td>$3.42</td>
    </tr>
    <tr>
      <th>Chanadar44</th>
      <td>2</td>
      <td>$7.19</td>
      <td>$3.59</td>
    </tr>
    <tr>
      <th>Asur53</th>
      <td>2</td>
      <td>$4.04</td>
      <td>$2.02</td>
    </tr>
    <tr>
      <th>Tyeosri53</th>
      <td>2</td>
      <td>$5.21</td>
      <td>$2.60</td>
    </tr>
    <tr>
      <th>Aerithnucal56</th>
      <td>2</td>
      <td>$3.18</td>
      <td>$1.59</td>
    </tr>
    <tr>
      <th>Thryallym62</th>
      <td>2</td>
      <td>$5.00</td>
      <td>$2.50</td>
    </tr>
    <tr>
      <th>Assylla81</th>
      <td>2</td>
      <td>$6.60</td>
      <td>$3.30</td>
    </tr>
    <tr>
      <th>Chamilsala65</th>
      <td>2</td>
      <td>$5.52</td>
      <td>$2.76</td>
    </tr>
    <tr>
      <th>Chamosia29</th>
      <td>2</td>
      <td>$6.16</td>
      <td>$3.08</td>
    </tr>
    <tr>
      <th>Airi27</th>
      <td>2</td>
      <td>$7.57</td>
      <td>$3.79</td>
    </tr>
    <tr>
      <th>Chamjasknya65</th>
      <td>2</td>
      <td>$3.80</td>
      <td>$1.90</td>
    </tr>
    <tr>
      <th>Yaralnura48</th>
      <td>2</td>
      <td>$4.19</td>
      <td>$2.10</td>
    </tr>
    <tr>
      <th>Chamilsan75</th>
      <td>2</td>
      <td>$5.64</td>
      <td>$2.82</td>
    </tr>
    <tr>
      <th>Chamirraya83</th>
      <td>2</td>
      <td>$4.83</td>
      <td>$2.42</td>
    </tr>
    <tr>
      <th>Tyaelistidru84</th>
      <td>2</td>
      <td>$5.97</td>
      <td>$2.98</td>
    </tr>
    <tr>
      <th>Tyadaru49</th>
      <td>2</td>
      <td>$7.36</td>
      <td>$3.68</td>
    </tr>
    <tr>
      <th>Chanjask65</th>
      <td>2</td>
      <td>$3.39</td>
      <td>$1.69</td>
    </tr>
    <tr>
      <th>Chanirrasta87</th>
      <td>2</td>
      <td>$3.36</td>
      <td>$1.68</td>
    </tr>
    <tr>
      <th>Chanjaskan89</th>
      <td>2</td>
      <td>$2.91</td>
      <td>$1.46</td>
    </tr>
    <tr>
      <th>Sweecossa42</th>
      <td>2</td>
      <td>$4.81</td>
      <td>$2.41</td>
    </tr>
    <tr>
      <th>Aeliriam77</th>
      <td>2</td>
      <td>$6.72</td>
      <td>$3.36</td>
    </tr>
    <tr>
      <th>Tyaeristi78</th>
      <td>2</td>
      <td>$3.51</td>
      <td>$1.75</td>
    </tr>
    <tr>
      <th>Assassa38</th>
      <td>2</td>
      <td>$5.61</td>
      <td>$2.80</td>
    </tr>
    <tr>
      <th>Tyirithnu40</th>
      <td>2</td>
      <td>$5.61</td>
      <td>$2.81</td>
    </tr>
    <tr>
      <th>Aeliru63</th>
      <td>2</td>
      <td>$8.98</td>
      <td>$4.49</td>
    </tr>
    <tr>
      <th>Aelalis34</th>
      <td>2</td>
      <td>$5.06</td>
      <td>$2.53</td>
    </tr>
    <tr>
      <th>Deural48</th>
      <td>2</td>
      <td>$7.27</td>
      <td>$3.63</td>
    </tr>
    <tr>
      <th>Chanosiaya39</th>
      <td>2</td>
      <td>$6.75</td>
      <td>$3.38</td>
    </tr>
    <tr>
      <th>Chanosiast43</th>
      <td>2</td>
      <td>$5.92</td>
      <td>$2.96</td>
    </tr>
    <tr>
      <th>Tyisriphos58</th>
      <td>2</td>
      <td>$9.18</td>
      <td>$4.59</td>
    </tr>
    <tr>
      <th>Chamadar61</th>
      <td>2</td>
      <td>$5.67</td>
      <td>$2.83</td>
    </tr>
    <tr>
      <th>Syasriria69</th>
      <td>2</td>
      <td>$3.98</td>
      <td>$1.99</td>
    </tr>
    <tr>
      <th>Yadaisuir65</th>
      <td>2</td>
      <td>$8.56</td>
      <td>$4.28</td>
    </tr>
    <tr>
      <th>Chanosia60</th>
      <td>2</td>
      <td>$6.74</td>
      <td>$3.37</td>
    </tr>
    <tr>
      <th>Assassasta79</th>
      <td>2</td>
      <td>$9.12</td>
      <td>$4.56</td>
    </tr>
    <tr>
      <th>Zhisrisu83</th>
      <td>2</td>
      <td>$2.46</td>
      <td>$1.23</td>
    </tr>
    <tr>
      <th>Yarolwen77</th>
      <td>2</td>
      <td>$6.98</td>
      <td>$3.49</td>
    </tr>
    <tr>
      <th>Yathecal72</th>
      <td>2</td>
      <td>$7.77</td>
      <td>$3.88</td>
    </tr>
    <tr>
      <th>Pheodai94</th>
      <td>1</td>
      <td>$1.48</td>
      <td>$1.48</td>
    </tr>
    <tr>
      <th>Phenastya51</th>
      <td>1</td>
      <td>$4.75</td>
      <td>$4.75</td>
    </tr>
    <tr>
      <th>Pheusrical25</th>
      <td>1</td>
      <td>$1.36</td>
      <td>$1.36</td>
    </tr>
    <tr>
      <th>Undjasksya56</th>
      <td>1</td>
      <td>$4.53</td>
      <td>$4.53</td>
    </tr>
    <tr>
      <th>Undotesta33</th>
      <td>1</td>
      <td>$3.90</td>
      <td>$3.90</td>
    </tr>
    <tr>
      <th>Phistym51</th>
      <td>1</td>
      <td>$3.79</td>
      <td>$3.79</td>
    </tr>
    <tr>
      <th>Philodil43</th>
      <td>1</td>
      <td>$1.49</td>
      <td>$1.49</td>
    </tr>
    <tr>
      <th>Philistirap41</th>
      <td>1</td>
      <td>$1.69</td>
      <td>$1.69</td>
    </tr>
    <tr>
      <th>Phially37</th>
      <td>1</td>
      <td>$2.88</td>
      <td>$2.88</td>
    </tr>
    <tr>
      <th>Phyali88</th>
      <td>1</td>
      <td>$2.29</td>
      <td>$2.29</td>
    </tr>
    <tr>
      <th>Sundista85</th>
      <td>1</td>
      <td>$1.14</td>
      <td>$1.14</td>
    </tr>
    <tr>
      <th>Qaronon57</th>
      <td>1</td>
      <td>$3.57</td>
      <td>$3.57</td>
    </tr>
    <tr>
      <th>Undast38</th>
      <td>1</td>
      <td>$2.04</td>
      <td>$2.04</td>
    </tr>
    <tr>
      <th>Raedalis34</th>
      <td>1</td>
      <td>$2.55</td>
      <td>$2.55</td>
    </tr>
    <tr>
      <th>Quinarap53</th>
      <td>1</td>
      <td>$2.46</td>
      <td>$2.46</td>
    </tr>
    <tr>
      <th>Umuard36</th>
      <td>1</td>
      <td>$3.55</td>
      <td>$3.55</td>
    </tr>
    <tr>
      <th>Undadar97</th>
      <td>1</td>
      <td>$3.91</td>
      <td>$3.91</td>
    </tr>
    <tr>
      <th>Undadarla37</th>
      <td>1</td>
      <td>$2.35</td>
      <td>$2.35</td>
    </tr>
    <tr>
      <th>Quelaton80</th>
      <td>1</td>
      <td>$1.72</td>
      <td>$1.72</td>
    </tr>
    <tr>
      <th>Quelatarn54</th>
      <td>1</td>
      <td>$4.33</td>
      <td>$4.33</td>
    </tr>
    <tr>
      <th>Undare39</th>
      <td>1</td>
      <td>$2.46</td>
      <td>$2.46</td>
    </tr>
    <tr>
      <th>Quarunarn52</th>
      <td>1</td>
      <td>$4.75</td>
      <td>$4.75</td>
    </tr>
    <tr>
      <th>Quanenrian83</th>
      <td>1</td>
      <td>$3.79</td>
      <td>$3.79</td>
    </tr>
    <tr>
      <th>Qarrian82</th>
      <td>1</td>
      <td>$4.45</td>
      <td>$4.45</td>
    </tr>
    <tr>
      <th>Undirra73</th>
      <td>1</td>
      <td>$4.15</td>
      <td>$4.15</td>
    </tr>
    <tr>
      <th>Qilunan34</th>
      <td>1</td>
      <td>$2.32</td>
      <td>$2.32</td>
    </tr>
    <tr>
      <th>Wailin72</th>
      <td>1</td>
      <td>$2.04</td>
      <td>$2.04</td>
    </tr>
    <tr>
      <th>Qiluard68</th>
      <td>1</td>
      <td>$4.95</td>
      <td>$4.95</td>
    </tr>
    <tr>
      <th>Undirrasta89</th>
      <td>1</td>
      <td>$2.28</td>
      <td>$2.28</td>
    </tr>
    <tr>
      <th>Qilanrion65</th>
      <td>1</td>
      <td>$2.98</td>
      <td>$2.98</td>
    </tr>
    <tr>
      <th>Undistasta86</th>
      <td>1</td>
      <td>$3.02</td>
      <td>$3.02</td>
    </tr>
    <tr>
      <th>Undiwinya88</th>
      <td>1</td>
      <td>$2.77</td>
      <td>$2.77</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Frichast72</th>
      <td>1</td>
      <td>$2.20</td>
      <td>$2.20</td>
    </tr>
    <tr>
      <th>Frichadar89</th>
      <td>1</td>
      <td>$4.23</td>
      <td>$4.23</td>
    </tr>
    <tr>
      <th>Fironon91</th>
      <td>1</td>
      <td>$2.46</td>
      <td>$2.46</td>
    </tr>
    <tr>
      <th>Filrion59</th>
      <td>1</td>
      <td>$2.07</td>
      <td>$2.07</td>
    </tr>
    <tr>
      <th>Filon68</th>
      <td>1</td>
      <td>$2.34</td>
      <td>$2.34</td>
    </tr>
    <tr>
      <th>Faralcil63</th>
      <td>1</td>
      <td>$3.27</td>
      <td>$3.27</td>
    </tr>
    <tr>
      <th>Eyircil84</th>
      <td>1</td>
      <td>$1.06</td>
      <td>$1.06</td>
    </tr>
    <tr>
      <th>Eustyria89</th>
      <td>1</td>
      <td>$4.75</td>
      <td>$4.75</td>
    </tr>
    <tr>
      <th>Eusri44</th>
      <td>1</td>
      <td>$2.55</td>
      <td>$2.55</td>
    </tr>
    <tr>
      <th>Eusri26</th>
      <td>1</td>
      <td>$2.91</td>
      <td>$2.91</td>
    </tr>
    <tr>
      <th>Eurith26</th>
      <td>1</td>
      <td>$4.37</td>
      <td>$4.37</td>
    </tr>
    <tr>
      <th>Eurinu48</th>
      <td>1</td>
      <td>$3.25</td>
      <td>$3.25</td>
    </tr>
    <tr>
      <th>Euna48</th>
      <td>1</td>
      <td>$4.25</td>
      <td>$4.25</td>
    </tr>
    <tr>
      <th>Eullydru35</th>
      <td>1</td>
      <td>$2.35</td>
      <td>$2.35</td>
    </tr>
    <tr>
      <th>Euliria52</th>
      <td>1</td>
      <td>$2.04</td>
      <td>$2.04</td>
    </tr>
    <tr>
      <th>Eulaeria40</th>
      <td>1</td>
      <td>$3.73</td>
      <td>$3.73</td>
    </tr>
    <tr>
      <th>Eula35</th>
      <td>1</td>
      <td>$3.62</td>
      <td>$3.62</td>
    </tr>
    <tr>
      <th>Eudasu82</th>
      <td>1</td>
      <td>$3.51</td>
      <td>$3.51</td>
    </tr>
    <tr>
      <th>Ethralista69</th>
      <td>1</td>
      <td>$4.39</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>Ethralan59</th>
      <td>1</td>
      <td>$1.85</td>
      <td>$1.85</td>
    </tr>
    <tr>
      <th>Eryon48</th>
      <td>1</td>
      <td>$2.11</td>
      <td>$2.11</td>
    </tr>
    <tr>
      <th>Ermol76</th>
      <td>1</td>
      <td>$1.79</td>
      <td>$1.79</td>
    </tr>
    <tr>
      <th>Eratiel90</th>
      <td>1</td>
      <td>$2.29</td>
      <td>$2.29</td>
    </tr>
    <tr>
      <th>Eothe56</th>
      <td>1</td>
      <td>$2.60</td>
      <td>$2.60</td>
    </tr>
    <tr>
      <th>Eosursurap97</th>
      <td>1</td>
      <td>$2.23</td>
      <td>$2.23</td>
    </tr>
    <tr>
      <th>Chadadarya31</th>
      <td>1</td>
      <td>$1.82</td>
      <td>$1.82</td>
    </tr>
    <tr>
      <th>Billysu76</th>
      <td>1</td>
      <td>$2.86</td>
      <td>$2.86</td>
    </tr>
    <tr>
      <th>Bartassaya73</th>
      <td>1</td>
      <td>$3.91</td>
      <td>$3.91</td>
    </tr>
    <tr>
      <th>Aillyriadru65</th>
      <td>1</td>
      <td>$1.84</td>
      <td>$1.84</td>
    </tr>
    <tr>
      <th>Ailaesuir66</th>
      <td>1</td>
      <td>$1.88</td>
      <td>$1.88</td>
    </tr>
    <tr>
      <th>Aiduesu83</th>
      <td>1</td>
      <td>$2.63</td>
      <td>$2.63</td>
    </tr>
    <tr>
      <th>Aiduecal76</th>
      <td>1</td>
      <td>$2.33</td>
      <td>$2.33</td>
    </tr>
    <tr>
      <th>Aidaira48</th>
      <td>1</td>
      <td>$1.73</td>
      <td>$1.73</td>
    </tr>
    <tr>
      <th>Aidaira26</th>
      <td>1</td>
      <td>$1.72</td>
      <td>$1.72</td>
    </tr>
    <tr>
      <th>Aethedru70</th>
      <td>1</td>
      <td>$2.97</td>
      <td>$2.97</td>
    </tr>
    <tr>
      <th>Aethe80</th>
      <td>1</td>
      <td>$2.32</td>
      <td>$2.32</td>
    </tr>
    <tr>
      <th>Aesur96</th>
      <td>1</td>
      <td>$4.66</td>
      <td>$4.66</td>
    </tr>
    <tr>
      <th>Aesty51</th>
      <td>1</td>
      <td>$1.82</td>
      <td>$1.82</td>
    </tr>
    <tr>
      <th>Aerithriaphos45</th>
      <td>1</td>
      <td>$2.38</td>
      <td>$2.38</td>
    </tr>
    <tr>
      <th>Aerithnuphos61</th>
      <td>1</td>
      <td>$1.69</td>
      <td>$1.69</td>
    </tr>
    <tr>
      <th>Aerillorin70</th>
      <td>1</td>
      <td>$1.88</td>
      <td>$1.88</td>
    </tr>
    <tr>
      <th>Aeral97</th>
      <td>1</td>
      <td>$2.35</td>
      <td>$2.35</td>
    </tr>
    <tr>
      <th>Aeral85</th>
      <td>1</td>
      <td>$4.25</td>
      <td>$4.25</td>
    </tr>
    <tr>
      <th>Aeral43</th>
      <td>1</td>
      <td>$2.72</td>
      <td>$2.72</td>
    </tr>
    <tr>
      <th>Aenasu69</th>
      <td>1</td>
      <td>$3.27</td>
      <td>$3.27</td>
    </tr>
    <tr>
      <th>Aenarap34</th>
      <td>1</td>
      <td>$1.65</td>
      <td>$1.65</td>
    </tr>
    <tr>
      <th>Aelollo59</th>
      <td>1</td>
      <td>$1.55</td>
      <td>$1.55</td>
    </tr>
    <tr>
      <th>Aellysup38</th>
      <td>1</td>
      <td>$3.61</td>
      <td>$3.61</td>
    </tr>
    <tr>
      <th>Aellyrialis39</th>
      <td>1</td>
      <td>$3.15</td>
      <td>$3.15</td>
    </tr>
    <tr>
      <th>Aellyria80</th>
      <td>1</td>
      <td>$4.32</td>
      <td>$4.32</td>
    </tr>
    <tr>
      <th>Aeliriarin93</th>
      <td>1</td>
      <td>$2.04</td>
      <td>$2.04</td>
    </tr>
    <tr>
      <th>Aelin32</th>
      <td>1</td>
      <td>$3.14</td>
      <td>$3.14</td>
    </tr>
    <tr>
      <th>Aela59</th>
      <td>1</td>
      <td>$1.27</td>
      <td>$1.27</td>
    </tr>
    <tr>
      <th>Aela49</th>
      <td>1</td>
      <td>$2.46</td>
      <td>$2.46</td>
    </tr>
    <tr>
      <th>Aillycal84</th>
      <td>1</td>
      <td>$4.00</td>
      <td>$4.00</td>
    </tr>
    <tr>
      <th>Aina42</th>
      <td>1</td>
      <td>$3.01</td>
      <td>$3.01</td>
    </tr>
    <tr>
      <th>Baelollodeu94</th>
      <td>1</td>
      <td>$2.77</td>
      <td>$2.77</td>
    </tr>
    <tr>
      <th>Aina43</th>
      <td>1</td>
      <td>$2.46</td>
      <td>$2.46</td>
    </tr>
    <tr>
      <th>Astydil38</th>
      <td>1</td>
      <td>$3.21</td>
      <td>$3.21</td>
    </tr>
    <tr>
      <th>Assossa43</th>
      <td>1</td>
      <td>$4.89</td>
      <td>$4.89</td>
    </tr>
    <tr>
      <th>Assosiasta83</th>
      <td>1</td>
      <td>$2.82</td>
      <td>$2.82</td>
    </tr>
    <tr>
      <th>Assosia38</th>
      <td>1</td>
      <td>$1.93</td>
      <td>$1.93</td>
    </tr>
    <tr>
      <th>Assithasta65</th>
      <td>1</td>
      <td>$3.61</td>
      <td>$3.61</td>
    </tr>
    <tr>
      <th>Assistast50</th>
      <td>1</td>
      <td>$3.55</td>
      <td>$3.55</td>
    </tr>
    <tr>
      <th>Assistasda90</th>
      <td>1</td>
      <td>$4.15</td>
      <td>$4.15</td>
    </tr>
    <tr>
      <th>Assilsan72</th>
      <td>1</td>
      <td>$3.58</td>
      <td>$3.58</td>
    </tr>
    <tr>
      <th>Assesi91</th>
      <td>1</td>
      <td>$1.73</td>
      <td>$1.73</td>
    </tr>
    <tr>
      <th>Assassasda84</th>
      <td>1</td>
      <td>$3.89</td>
      <td>$3.89</td>
    </tr>
    <tr>
      <th>Assassa43</th>
      <td>1</td>
      <td>$3.89</td>
      <td>$3.89</td>
    </tr>
    <tr>
      <th>Arithllorin55</th>
      <td>1</td>
      <td>$3.74</td>
      <td>$3.74</td>
    </tr>
    <tr>
      <th>Anallorgue57</th>
      <td>1</td>
      <td>$4.87</td>
      <td>$4.87</td>
    </tr>
    <tr>
      <th>Alo67</th>
      <td>1</td>
      <td>$2.11</td>
      <td>$2.11</td>
    </tr>
    <tr>
      <th>Alim85</th>
      <td>1</td>
      <td>$3.61</td>
      <td>$3.61</td>
    </tr>
    <tr>
      <th>Alarap40</th>
      <td>1</td>
      <td>$2.72</td>
      <td>$2.72</td>
    </tr>
    <tr>
      <th>Alallo58</th>
      <td>1</td>
      <td>$4.14</td>
      <td>$4.14</td>
    </tr>
    <tr>
      <th>Alaesu91</th>
      <td>1</td>
      <td>$1.03</td>
      <td>$1.03</td>
    </tr>
    <tr>
      <th>Alaesu77</th>
      <td>1</td>
      <td>$2.33</td>
      <td>$2.33</td>
    </tr>
    <tr>
      <th>Aithelis62</th>
      <td>1</td>
      <td>$3.79</td>
      <td>$3.79</td>
    </tr>
    <tr>
      <th>Aisurphos78</th>
      <td>1</td>
      <td>$3.73</td>
      <td>$3.73</td>
    </tr>
    <tr>
      <th>Aisur51</th>
      <td>1</td>
      <td>$1.49</td>
      <td>$1.49</td>
    </tr>
    <tr>
      <th>Airithrin43</th>
      <td>1</td>
      <td>$3.57</td>
      <td>$3.57</td>
    </tr>
    <tr>
      <th>Airidil41</th>
      <td>1</td>
      <td>$3.59</td>
      <td>$3.59</td>
    </tr>
    <tr>
      <th>Airal46</th>
      <td>1</td>
      <td>$2.67</td>
      <td>$2.67</td>
    </tr>
    <tr>
      <th>Frichaststa61</th>
      <td>1</td>
      <td>$4.95</td>
      <td>$4.95</td>
    </tr>
    <tr>
      <th>Frichilsasya78</th>
      <td>1</td>
      <td>$3.74</td>
      <td>$3.74</td>
    </tr>
    <tr>
      <th>Frichim27</th>
      <td>1</td>
      <td>$4.56</td>
      <td>$4.56</td>
    </tr>
    <tr>
      <th>Lassilsala30</th>
      <td>1</td>
      <td>$3.21</td>
      <td>$3.21</td>
    </tr>
    <tr>
      <th>Lassadarsda57</th>
      <td>1</td>
      <td>$1.03</td>
      <td>$1.03</td>
    </tr>
    <tr>
      <th>Lamyon68</th>
      <td>1</td>
      <td>$1.55</td>
      <td>$1.55</td>
    </tr>
    <tr>
      <th>Lamon28</th>
      <td>1</td>
      <td>$4.39</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>Lamil79</th>
      <td>1</td>
      <td>$2.34</td>
      <td>$2.34</td>
    </tr>
    <tr>
      <th>Lamil70</th>
      <td>1</td>
      <td>$1.49</td>
      <td>$1.49</td>
    </tr>
    <tr>
      <th>Koikirra25</th>
      <td>1</td>
      <td>$2.23</td>
      <td>$2.23</td>
    </tr>
    <tr>
      <th>Jiskosiala43</th>
      <td>1</td>
      <td>$3.66</td>
      <td>$3.66</td>
    </tr>
    <tr>
      <th>Jiskjask80</th>
      <td>1</td>
      <td>$2.91</td>
      <td>$2.91</td>
    </tr>
    <tr>
      <th>Jiskirran77</th>
      <td>1</td>
      <td>$3.96</td>
      <td>$3.96</td>
    </tr>
    <tr>
      <th>Jiskimsda56</th>
      <td>1</td>
      <td>$3.91</td>
      <td>$3.91</td>
    </tr>
    <tr>
      <th>Jiskilsa35</th>
      <td>1</td>
      <td>$3.81</td>
      <td>$3.81</td>
    </tr>
    <tr>
      <th>Jeyciman68</th>
      <td>1</td>
      <td>$3.15</td>
      <td>$3.15</td>
    </tr>
    <tr>
      <th>Ithesuphos68</th>
      <td>1</td>
      <td>$2.78</td>
      <td>$2.78</td>
    </tr>
    <tr>
      <th>Isurriarap71</th>
      <td>1</td>
      <td>$4.16</td>
      <td>$4.16</td>
    </tr>
    <tr>
      <th>Isrirgue68</th>
      <td>1</td>
      <td>$4.36</td>
      <td>$4.36</td>
    </tr>
    <tr>
      <th>Isri59</th>
      <td>1</td>
      <td>$3.74</td>
      <td>$3.74</td>
    </tr>
    <tr>
      <th>Isri49</th>
      <td>1</td>
      <td>$1.91</td>
      <td>$1.91</td>
    </tr>
    <tr>
      <th>Iskossasda43</th>
      <td>1</td>
      <td>$2.41</td>
      <td>$2.41</td>
    </tr>
    <tr>
      <th>Iskosian40</th>
      <td>1</td>
      <td>$3.39</td>
      <td>$3.39</td>
    </tr>
    <tr>
      <th>Iskjaskst81</th>
      <td>1</td>
      <td>$3.71</td>
      <td>$3.71</td>
    </tr>
    <tr>
      <th>Iskjaskan81</th>
      <td>1</td>
      <td>$2.38</td>
      <td>$2.38</td>
    </tr>
    <tr>
      <th>Iskistasda86</th>
      <td>1</td>
      <td>$4.14</td>
      <td>$4.14</td>
    </tr>
    <tr>
      <th>Iskista96</th>
      <td>1</td>
      <td>$4.83</td>
      <td>$4.83</td>
    </tr>
    <tr>
      <th>Iskirra45</th>
      <td>1</td>
      <td>$2.29</td>
      <td>$2.29</td>
    </tr>
    <tr>
      <th>Iskichinya81</th>
      <td>1</td>
      <td>$4.67</td>
      <td>$4.67</td>
    </tr>
    <tr>
      <th>Lassilsa63</th>
      <td>1</td>
      <td>$1.27</td>
      <td>$1.27</td>
    </tr>
    <tr>
      <th>Lassimla92</th>
      <td>1</td>
      <td>$2.17</td>
      <td>$2.17</td>
    </tr>
    <tr>
      <th>Iskassa50</th>
      <td>1</td>
      <td>$1.79</td>
      <td>$1.79</td>
    </tr>
    <tr>
      <th>Lassista97</th>
      <td>1</td>
      <td>$1.03</td>
      <td>$1.03</td>
    </tr>
    <tr>
      <th>Lisosianya62</th>
      <td>1</td>
      <td>$1.55</td>
      <td>$1.55</td>
    </tr>
    <tr>
      <th>Lisjaskya84</th>
      <td>1</td>
      <td>$3.11</td>
      <td>$3.11</td>
    </tr>
    <tr>
      <th>Lisistasya93</th>
      <td>1</td>
      <td>$3.91</td>
      <td>$3.91</td>
    </tr>
    <tr>
      <th>Lisista27</th>
      <td>1</td>
      <td>$2.11</td>
      <td>$2.11</td>
    </tr>
    <tr>
      <th>Lisirraya76</th>
      <td>1</td>
      <td>$4.10</td>
      <td>$4.10</td>
    </tr>
    <tr>
      <th>Lisirrast82</th>
      <td>1</td>
      <td>$1.59</td>
      <td>$1.59</td>
    </tr>
    <tr>
      <th>Lisiriya82</th>
      <td>1</td>
      <td>$3.51</td>
      <td>$3.51</td>
    </tr>
    <tr>
      <th>Lisimsda29</th>
      <td>1</td>
      <td>$3.62</td>
      <td>$3.62</td>
    </tr>
    <tr>
      <th>Lisico81</th>
      <td>1</td>
      <td>$1.59</td>
      <td>$1.59</td>
    </tr>
    <tr>
      <th>Lisassa49</th>
      <td>1</td>
      <td>$2.11</td>
      <td>$2.11</td>
    </tr>
    <tr>
      <th>Lisassa39</th>
      <td>1</td>
      <td>$1.56</td>
      <td>$1.56</td>
    </tr>
    <tr>
      <th>Lisassa26</th>
      <td>1</td>
      <td>$4.25</td>
      <td>$4.25</td>
    </tr>
    <tr>
      <th>Lisadar44</th>
      <td>1</td>
      <td>$2.31</td>
      <td>$2.31</td>
    </tr>
    <tr>
      <th>Lirtyrdesta65</th>
      <td>1</td>
      <td>$1.84</td>
      <td>$1.84</td>
    </tr>
    <tr>
      <th>Lirtossanya27</th>
      <td>1</td>
      <td>$1.79</td>
      <td>$1.79</td>
    </tr>
    <tr>
      <th>Lirtossan50</th>
      <td>1</td>
      <td>$3.51</td>
      <td>$3.51</td>
    </tr>
    <tr>
      <th>Lirtossa84</th>
      <td>1</td>
      <td>$4.62</td>
      <td>$4.62</td>
    </tr>
    <tr>
      <th>Lirtistanya48</th>
      <td>1</td>
      <td>$3.42</td>
      <td>$3.42</td>
    </tr>
    <tr>
      <th>Lirtista72</th>
      <td>1</td>
      <td>$4.36</td>
      <td>$4.36</td>
    </tr>
    <tr>
      <th>Lirtilsan89</th>
      <td>1</td>
      <td>$4.07</td>
      <td>$4.07</td>
    </tr>
    <tr>
      <th>Lirtast83</th>
      <td>1</td>
      <td>$3.55</td>
      <td>$3.55</td>
    </tr>
    <tr>
      <th>Liawista80</th>
      <td>1</td>
      <td>$3.27</td>
      <td>$3.27</td>
    </tr>
    <tr>
      <th>Leyirra83</th>
      <td>1</td>
      <td>$4.45</td>
      <td>$4.45</td>
    </tr>
    <tr>
      <th>Leulaesti78</th>
      <td>1</td>
      <td>$3.42</td>
      <td>$3.42</td>
    </tr>
    <tr>
      <th>Layjask75</th>
      <td>1</td>
      <td>$4.16</td>
      <td>$4.16</td>
    </tr>
    <tr>
      <th>Isketo41</th>
      <td>1</td>
      <td>$1.32</td>
      <td>$1.32</td>
    </tr>
    <tr>
      <th>Irithrap69</th>
      <td>1</td>
      <td>$4.75</td>
      <td>$4.75</td>
    </tr>
    <tr>
      <th>Frichim77</th>
      <td>1</td>
      <td>$3.96</td>
      <td>$3.96</td>
    </tr>
    <tr>
      <th>Iarilis73</th>
      <td>1</td>
      <td>$1.93</td>
      <td>$1.93</td>
    </tr>
    <tr>
      <th>Ialo60</th>
      <td>1</td>
      <td>$4.08</td>
      <td>$4.08</td>
    </tr>
    <tr>
      <th>Iallyphos37</th>
      <td>1</td>
      <td>$2.34</td>
      <td>$2.34</td>
    </tr>
    <tr>
      <th>Ialidru40</th>
      <td>1</td>
      <td>$1.24</td>
      <td>$1.24</td>
    </tr>
    <tr>
      <th>Ialallo29</th>
      <td>1</td>
      <td>$3.15</td>
      <td>$3.15</td>
    </tr>
    <tr>
      <th>Hirirap39</th>
      <td>1</td>
      <td>$2.33</td>
      <td>$2.33</td>
    </tr>
    <tr>
      <th>Hiral75</th>
      <td>1</td>
      <td>$1.45</td>
      <td>$1.45</td>
    </tr>
    <tr>
      <th>Hiasur92</th>
      <td>1</td>
      <td>$4.87</td>
      <td>$4.87</td>
    </tr>
    <tr>
      <th>Hiasri33</th>
      <td>1</td>
      <td>$2.28</td>
      <td>$2.28</td>
    </tr>
    <tr>
      <th>Hiarideu73</th>
      <td>1</td>
      <td>$2.23</td>
      <td>$2.23</td>
    </tr>
    <tr>
      <th>Hiadanurin36</th>
      <td>1</td>
      <td>$3.96</td>
      <td>$3.96</td>
    </tr>
    <tr>
      <th>Heuralsti66</th>
      <td>1</td>
      <td>$4.75</td>
      <td>$4.75</td>
    </tr>
    <tr>
      <th>Heunadil74</th>
      <td>1</td>
      <td>$3.82</td>
      <td>$3.82</td>
    </tr>
    <tr>
      <th>Heuli25</th>
      <td>1</td>
      <td>$3.48</td>
      <td>$3.48</td>
    </tr>
    <tr>
      <th>Heosurnuru52</th>
      <td>1</td>
      <td>$1.73</td>
      <td>$1.73</td>
    </tr>
    <tr>
      <th>Heosrisuir72</th>
      <td>1</td>
      <td>$4.82</td>
      <td>$4.82</td>
    </tr>
    <tr>
      <th>Heolo60</th>
      <td>1</td>
      <td>$1.24</td>
      <td>$1.24</td>
    </tr>
    <tr>
      <th>Hallysucal81</th>
      <td>1</td>
      <td>$1.59</td>
      <td>$1.59</td>
    </tr>
    <tr>
      <th>Hala31</th>
      <td>1</td>
      <td>$1.72</td>
      <td>$1.72</td>
    </tr>
    <tr>
      <th>Hainaria90</th>
      <td>1</td>
      <td>$3.39</td>
      <td>$3.39</td>
    </tr>
    <tr>
      <th>Haerithp41</th>
      <td>1</td>
      <td>$3.20</td>
      <td>$3.20</td>
    </tr>
    <tr>
      <th>Frichossast86</th>
      <td>1</td>
      <td>$3.62</td>
      <td>$3.62</td>
    </tr>
    <tr>
      <th>Frichossast75</th>
      <td>1</td>
      <td>$3.61</td>
      <td>$3.61</td>
    </tr>
    <tr>
      <th>Frichjaskan98</th>
      <td>1</td>
      <td>$3.68</td>
      <td>$3.68</td>
    </tr>
    <tr>
      <th>Frichjask31</th>
      <td>1</td>
      <td>$2.17</td>
      <td>$2.17</td>
    </tr>
    <tr>
      <th>Frichistast39</th>
      <td>1</td>
      <td>$3.56</td>
      <td>$3.56</td>
    </tr>
    <tr>
      <th>Iaralsuir44</th>
      <td>1</td>
      <td>$3.73</td>
      <td>$3.73</td>
    </tr>
    <tr>
      <th>Iarithdil76</th>
      <td>1</td>
      <td>$3.81</td>
      <td>$3.81</td>
    </tr>
    <tr>
      <th>Irith83</th>
      <td>1</td>
      <td>$2.33</td>
      <td>$2.33</td>
    </tr>
    <tr>
      <th>Iasur80</th>
      <td>1</td>
      <td>$4.87</td>
      <td>$4.87</td>
    </tr>
    <tr>
      <th>Irillo49</th>
      <td>1</td>
      <td>$4.32</td>
      <td>$4.32</td>
    </tr>
    <tr>
      <th>Iri67</th>
      <td>1</td>
      <td>$4.00</td>
      <td>$4.00</td>
    </tr>
    <tr>
      <th>Iral74</th>
      <td>1</td>
      <td>$3.58</td>
      <td>$3.58</td>
    </tr>
    <tr>
      <th>Inguron55</th>
      <td>1</td>
      <td>$2.23</td>
      <td>$2.23</td>
    </tr>
    <tr>
      <th>Inguard95</th>
      <td>1</td>
      <td>$3.96</td>
      <td>$3.96</td>
    </tr>
    <tr>
      <th>Ingonon91</th>
      <td>1</td>
      <td>$2.88</td>
      <td>$2.88</td>
    </tr>
    <tr>
      <th>Indonmol95</th>
      <td>1</td>
      <td>$2.29</td>
      <td>$2.29</td>
    </tr>
    <tr>
      <th>Indirrian56</th>
      <td>1</td>
      <td>$2.46</td>
      <td>$2.46</td>
    </tr>
    <tr>
      <th>Ina92</th>
      <td>1</td>
      <td>$4.54</td>
      <td>$4.54</td>
    </tr>
    <tr>
      <th>Ilrian97</th>
      <td>1</td>
      <td>$1.24</td>
      <td>$1.24</td>
    </tr>
    <tr>
      <th>Ilosu82</th>
      <td>1</td>
      <td>$2.82</td>
      <td>$2.82</td>
    </tr>
    <tr>
      <th>Ilosia37</th>
      <td>1</td>
      <td>$1.48</td>
      <td>$1.48</td>
    </tr>
    <tr>
      <th>Ilophos58</th>
      <td>1</td>
      <td>$4.87</td>
      <td>$4.87</td>
    </tr>
    <tr>
      <th>Ilirrasda54</th>
      <td>1</td>
      <td>$3.61</td>
      <td>$3.61</td>
    </tr>
    <tr>
      <th>Ilimya66</th>
      <td>1</td>
      <td>$3.48</td>
      <td>$3.48</td>
    </tr>
    <tr>
      <th>Ililsan66</th>
      <td>1</td>
      <td>$1.03</td>
      <td>$1.03</td>
    </tr>
    <tr>
      <th>Iliel92</th>
      <td>1</td>
      <td>$3.70</td>
      <td>$3.70</td>
    </tr>
    <tr>
      <th>Ilaststa70</th>
      <td>1</td>
      <td>$1.27</td>
      <td>$1.27</td>
    </tr>
    <tr>
      <th>Ilast79</th>
      <td>1</td>
      <td>$1.20</td>
      <td>$1.20</td>
    </tr>
    <tr>
      <th>Ilassa51</th>
      <td>1</td>
      <td>$2.21</td>
      <td>$2.21</td>
    </tr>
    <tr>
      <th>Ilariarin45</th>
      <td>1</td>
      <td>$4.23</td>
      <td>$4.23</td>
    </tr>
    <tr>
      <th>Ilaesudil92</th>
      <td>1</td>
      <td>$2.28</td>
      <td>$2.28</td>
    </tr>
    <tr>
      <th>Iduedru67</th>
      <td>1</td>
      <td>$3.32</td>
      <td>$3.32</td>
    </tr>
    <tr>
      <th>Idaria87</th>
      <td>1</td>
      <td>$4.87</td>
      <td>$4.87</td>
    </tr>
    <tr>
      <th>Idairin80</th>
      <td>1</td>
      <td>$4.30</td>
      <td>$4.30</td>
    </tr>
    <tr>
      <th>Zontibe81</th>
      <td>1</td>
      <td>$3.71</td>
      <td>$3.71</td>
    </tr>
  </tbody>
</table>
<p>573 rows × 3 columns</p>
</div>



# Most Popular Items


```python
# Using groupby to get data series of purchase count, item price and total purchase value
item_group = game_df.groupby(["Item ID", "Item Name"])
purchase_count = item_group["Gender"].count()
item_price = item_group["Price"].mean()
total_purchase_value = item_group["Price"].sum()

# Create a dataframe using data series
popular_items = pd.DataFrame(
    {
        "Purchase Count" : purchase_count,
        "Item Price" : item_price,
        "Total Purchase Value" : total_purchase_value
    }
)

# Sort the dataframe by purchase count, and format
popular_items = popular_items.sort_values("Purchase Count", ascending = False)
popular_items["Item Price"] = popular_items["Item Price"].map("${:.2f}".format)
popular_items["Total Purchase Value"] = popular_items["Total Purchase Value"].map("${:.2f}".format)

# Arrange columns for viewing
columns = ["Purchase Count", "Item Price", "Total Purchase Value"]
popular_items[columns].head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>



# Most Profitable Items


```python
# Using groupby to get data series of purchase count, item price and total purchase value
item_group = game_df.groupby(["Item ID", "Item Name"])
purchase_count = item_group["Gender"].count()
item_price = item_group["Price"].mean()
total_purchase_value = item_group["Price"].sum()

# Create a dataframe using data series
popular_items = pd.DataFrame(
    {
        "Purchase Count" : purchase_count,
        "Item Price" : item_price,
        "Total Purchase Value" : total_purchase_value
    }
)

# Sort the dataframe by total purchase value, and format
popular_items = popular_items.sort_values("Total Purchase Value", ascending = False)
popular_items["Item Price"] = popular_items["Item Price"].map("${:.2f}".format)
popular_items["Total Purchase Value"] = popular_items["Total Purchase Value"].map("${:.2f}".format)

# Arrange columns for viewing
columns = ["Purchase Count", "Item Price", "Total Purchase Value"]
popular_items[columns].head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


