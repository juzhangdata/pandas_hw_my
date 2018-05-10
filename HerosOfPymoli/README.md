
# Heroes Of Pymoli Data Analysis

OBSERVED TREND 1: The normalized total values for women vs. men(3.83 dollars vs. 4.02 dollars) are very close, although male players make up a higher percentage of total players than female(81.15% vs 17.45%).

OBSERVED TREND 2: Players between 20 and 24 years old spend the most (978.77 dollars) among all ages. However, their normalized total value is the lowest (3.78 dollars). Players elder than 40 years old has the highest normalized total value(4.89 dollars). 

OBSERVED TREND 3: Item with ID 39 and name "Betrayal, Whisper of Grieving Widows" and item with ID 84 and name "Arcane Gem" are the most popular items, with 11 purchases each. Item with ID 34 and name "Retribution Axe" is the most profitable item, bringing in 37.26 dollars of revenue.


```python
# Import dependencies
import pandas as pd
import numpy as np

#set max rows viewable
pd.options.display.max_rows = 400

# Read the .json file
file_path = "./purchase_data.json"
game_df = pd.read_json(file_path, orient = "columns", encoding = "ISO-8859-1")
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
gender_analysis["Normalized Totals"] = gender_analysis["Total Purchase Value"]/gender_df["Total\xa0Count"]

# Format columns to two decimals, and add $
gender_analysis["Total Purchase Value"] = gender_analysis["Total Purchase Value"].map("${:.2f}".format)
gender_analysis["Average Purchase Price"] = gender_analysis["Average Purchase Price"].map("${:.2f}".format)
gender_analysis["Normalized Totals"] = gender_analysis["Normalized Totals"].map("${:.2f}".format)

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
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>



# Age Demographics


```python
# Find out the mininmum and maximum age
# print(game_df["Age"].min(), game_df["Age"].max())

# Generate bins and labels based on min and max
bins = [0, 9, 14, 19, 24, 29, 34, 39, 46]
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]

# Cut Age and place the values into bins
age_demo = pd.DataFrame(game_df.groupby("SN")["Age"].mean())
age_demo["Age Summary"] = pd.cut(age_demo["Age"], bins, labels=group_names)

# Using value_counts to get number of players in each age bin
age_demo = pd.DataFrame(age_demo["Age Summary"].value_counts())

# Rename column
age_demo = age_demo.rename(columns = {"Age Summary" : "Total Count"})

# Calculate percentage, format cells to %, arrange column display, and sort dataframe index
age_demo["Percentages of Players"] = age_demo["Total Count"] / age_demo["Total Count"].sum()
age_demo["Percentages of Players"] = (age_demo["Percentages of Players"] *100).map("{:.2f}%".format)
age_demo[["Percentages of Players","Total Count"]]
age_demo.sort_index()
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
      <th>Total Count</th>
      <th>Percentages of Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>19</td>
      <td>3.32%</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>23</td>
      <td>4.01%</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>100</td>
      <td>17.45%</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>259</td>
      <td>45.20%</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>87</td>
      <td>15.18%</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>47</td>
      <td>8.20%</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>27</td>
      <td>4.71%</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>11</td>
      <td>1.92%</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Age)


```python
# Find out the mininmum and maximum age
# print(game_df["Age"].min(), game_df["Age"].max())

# Generate bins and labels based on min and max
bins = [0, 9, 14, 19, 24, 29, 34, 39, 46]
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]

# Cut Age and place the values into bins
game_df["Age Summary"] = pd.cut(game_df["Age"], bins, labels=group_names)
age_group = game_df.groupby("Age Summary")

# Get data series of purchase_count and data frame of total_purchase_value, and create a new dataframe
purchase_count = game_df["Age Summary"].value_counts()
total_purchase_value = age_group["Price"].sum()
age_analysis = pd.DataFrame(
    {
        "Purchase Count" : purchase_count,
        "Total Purchase Value" : total_purchase_value
    }
)

# Calculate average and normalized value:
age_analysis["Average Purchase Price"] = age_analysis["Total Purchase Value"] / age_analysis["Purchase Count"]
age_analysis["Normalized Totals"] = age_analysis["Total Purchase Value"]/ age_demo["Total Count"]

# Format columns to two decimals, and add $
age_analysis["Total Purchase Value"] = age_analysis["Total Purchase Value"].map("${:.2f}".format)
age_analysis["Average Purchase Price"] = age_analysis["Average Purchase Price"].map("${:.2f}".format)
age_analysis["Normalized Totals"] = age_analysis["Normalized Totals"].map("${:.2f}".format)

# Arrange rows and columns for viewing
age_analysis[["Purchase Count", "Average Purchase Price", "Total Purchase Value","Normalized Totals"]]
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
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$4.39</td>
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
top_spenders = top_spenders[["Purchase Count", "Average Purchase Price", "Total Purchase Value"]]

# Sort dataframe by purchase count
top_spenders.sort_values(by = ["Purchase Count"],ascending=False).head()
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Qarwen67</th>
      <td>4</td>
      <td>$2.49</td>
      <td>$9.97</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Sondastan54</th>
      <td>4</td>
      <td>$2.56</td>
      <td>$10.24</td>
    </tr>
  </tbody>
</table>
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


