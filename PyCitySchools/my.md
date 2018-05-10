
# PyCity Schools Analysis

Observed Trends:
* 1 Large schools have a smaller overall passing rate (76.36%) than small and medium schools (94.82% and 95.20%, separately). 
* 2 Schools with higher spending budget per student has a lower overall passing rate.
* 3 Charter schools has a higher overall passing rate than District schools.


```python
# Import dependencies
import pandas as pd
import numpy as np

# Using pd to read files as dataframes
students_file = "./raw_data/students_complete.csv"
schools_file = "./raw_data/schools_complete.csv"
students_df = pd.read_csv(students_file)
schools_df = pd.read_csv(schools_file)
```

## District Summary


```python
# Merge two dataframes on the school name column
df = pd.merge(students_df, schools_df, left_on = "school", right_on = "name", how="left", suffixes=("_students", "_schools"))
df.head()
# Calculations
total_schools = len(schools_df["School ID"].unique())
total_students = len(students_df["Student ID"].unique())
total_budget = schools_df["budget"].sum()
average_math_score = students_df["math_score"].mean()
average_reading_score = students_df["reading_score"].mean()
percent_passing_math = 100 * len(students_df[students_df["math_score"] >= 70]) / total_students
percent_passing_reading = 100 * len(students_df[students_df["reading_score"] >= 70]) / total_students
overall_passing = (percent_passing_math + percent_passing_reading) / 2

# Generate a dataframe
district_summary = pd.DataFrame(
{
    "Total Schools" : [total_schools],
    "Total Students" : [total_students],
    "Total Budget" : [total_budget],
    "Average Math Score" : [average_math_score],
    "Average Reading Score" : [average_reading_score],
    "% Passing Math" : [percent_passing_math],
    "% Passing Reading" : [percent_passing_reading],
    "% Overall Passing Rate" : [overall_passing]
})

# Arrange columns in dataframe
columns = ["Total Schools", "Total Students", "Total Budget", "Average Math Score", "Average Reading Score", "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]
district_summary = district_summary[columns]
district_summary = district_summary.round(2)
district_summary.head()
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
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>24649428</td>
      <td>78.99</td>
      <td>81.88</td>
      <td>74.98</td>
      <td>85.81</td>
      <td>80.39</td>
    </tr>
  </tbody>
</table>
</div>



## School Summary


```python
# Create a school groupby object
school_group = df.groupby("school")
school_group.min().head()

# Create a data series of students in each school
students_in_schools = df["school"].value_counts()

# Calculate percent passing math and percent passing reading, and overall passing rate
math_pass_df = df[df["math_score"] >= 70]
percent_passing_math = 100 * math_pass_df["school"].value_counts() / students_in_schools
reading_pass_df = df[df["reading_score"] >= 70]
percent_passing_reading = 100 * reading_pass_df["school"].value_counts() / students_in_schools
overall_passing_rate = (percent_passing_math + percent_passing_reading) / 2

# Generate a dataframe based on data series
school_summary = pd.DataFrame(
{
    "School Type" : school_group["type"].max(),
    "Total Students" : students_in_schools,
    "Total School Budget" : school_group["budget"].mean(),
    "Per Student Budget" : (school_group["budget"].mean()) / students_in_schools,
    "Average Math Score" : (school_group["math_score"].sum()) / students_in_schools,
    "Average Reading Score" : (school_group["reading_score"].sum()) / students_in_schools,
    "% Passing Math" : percent_passing_math,
    "% Passing Reading" : percent_passing_reading,
    "% Overall Passing Rate" : overall_passing_rate
})

# Format budget into $ and decimals, arrange column display for dataframe
school_summary["Total School Budget"] = school_summary["Total School Budget"].map("${:,.2f}".format)
school_summary["Per Student Budget"] = school_summary["Per Student Budget"].map("${:.2f}".format)
columns = ["School Type", "Total Students", "Total School Budget", "Per Student Budget", "Average Math Score", "Average Reading Score", "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]
school_summary = school_summary[columns]
school_summary = school_summary.round(2)
school_summary.head()
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
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>District</td>
      <td>4976</td>
      <td>$3,124,928.00</td>
      <td>$628.00</td>
      <td>77.05</td>
      <td>81.03</td>
      <td>66.68</td>
      <td>81.93</td>
      <td>74.31</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>$1,081,356.00</td>
      <td>$582.00</td>
      <td>83.06</td>
      <td>83.98</td>
      <td>94.13</td>
      <td>97.04</td>
      <td>95.59</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>$1,884,411.00</td>
      <td>$639.00</td>
      <td>76.71</td>
      <td>81.16</td>
      <td>65.99</td>
      <td>80.74</td>
      <td>73.36</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>$1,763,916.00</td>
      <td>$644.00</td>
      <td>77.10</td>
      <td>80.75</td>
      <td>68.31</td>
      <td>79.30</td>
      <td>73.80</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>$917,500.00</td>
      <td>$625.00</td>
      <td>83.35</td>
      <td>83.82</td>
      <td>93.39</td>
      <td>97.14</td>
      <td>95.27</td>
    </tr>
  </tbody>
</table>
</div>



## Top Performing Schools (By Passing Rate)


```python
# Sort schools by overall passing rate, from high to low
school_summary.sort_values("% Overall Passing Rate", ascending = False).head()
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
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>$1,081,356.00</td>
      <td>$582.00</td>
      <td>83.06</td>
      <td>83.98</td>
      <td>94.13</td>
      <td>97.04</td>
      <td>95.59</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>$1,043,130.00</td>
      <td>$638.00</td>
      <td>83.42</td>
      <td>83.85</td>
      <td>93.27</td>
      <td>97.31</td>
      <td>95.29</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>$917,500.00</td>
      <td>$625.00</td>
      <td>83.35</td>
      <td>83.82</td>
      <td>93.39</td>
      <td>97.14</td>
      <td>95.27</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>$585,858.00</td>
      <td>$609.00</td>
      <td>83.84</td>
      <td>84.04</td>
      <td>94.59</td>
      <td>95.95</td>
      <td>95.27</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>$1,319,574.00</td>
      <td>$578.00</td>
      <td>83.27</td>
      <td>83.99</td>
      <td>93.87</td>
      <td>96.54</td>
      <td>95.20</td>
    </tr>
  </tbody>
</table>
</div>



## Bottom Performing Schools (By Passing Rate)


```python
# Sort schools by overall passing rate, from low to high
school_summary.sort_values("% Overall Passing Rate", ascending = True).head()
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
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>$2,547,363.00</td>
      <td>$637.00</td>
      <td>76.84</td>
      <td>80.74</td>
      <td>66.37</td>
      <td>80.22</td>
      <td>73.29</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>$1,884,411.00</td>
      <td>$639.00</td>
      <td>76.71</td>
      <td>81.16</td>
      <td>65.99</td>
      <td>80.74</td>
      <td>73.36</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>$1,910,635.00</td>
      <td>$655.00</td>
      <td>76.63</td>
      <td>81.18</td>
      <td>65.68</td>
      <td>81.32</td>
      <td>73.50</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>$3,094,650.00</td>
      <td>$650.00</td>
      <td>77.07</td>
      <td>80.97</td>
      <td>66.06</td>
      <td>81.22</td>
      <td>73.64</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>$1,763,916.00</td>
      <td>$644.00</td>
      <td>77.10</td>
      <td>80.75</td>
      <td>68.31</td>
      <td>79.30</td>
      <td>73.80</td>
    </tr>
  </tbody>
</table>
</div>



## Math Scores by Grade


```python
# Define a function to get the average math score for a certain grade in schools
def math(grade):
    return df[df["grade"] == grade].groupby("school")["math_score"].mean()

# Generate a dataframe based on math scores by grade in schools
math_by_grade = pd.DataFrame(
{
    "9th" : math("9th"),
    "10th" : math("10th"),
    "11th" : math("11th"),
    "12th" : math("12th")
})

# Arrange columns of dataframe
columns = ["9th", "10th", "11th", "12th"]
math_by_grade = math_by_grade[columns]
math_by_grade = math_by_grade.round(2)
math_by_grade.head()
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
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.08</td>
      <td>77.00</td>
      <td>77.52</td>
      <td>76.49</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.09</td>
      <td>83.15</td>
      <td>82.77</td>
      <td>83.28</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.40</td>
      <td>76.54</td>
      <td>76.88</td>
      <td>77.15</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.36</td>
      <td>77.67</td>
      <td>76.92</td>
      <td>76.18</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.04</td>
      <td>84.23</td>
      <td>83.84</td>
      <td>83.36</td>
    </tr>
  </tbody>
</table>
</div>



## Reading Score by Grade 


```python
# Define a function to get the average reading score for a certain grade in schools
def reading(grade):
    return df[df["grade"] == grade].groupby("school")["reading_score"].mean()

# Generate a dataframe based on reading scores by grade in schools
reading_by_grade = pd.DataFrame(
{
    "9th" : reading("9th"),
    "10th" : reading("10th"),
    "11th" : reading("11th"),
    "12th" : reading("12th")
})

# Arrange columns of dataframe
columns = ["9th", "10th", "11th", "12th"]
reading_by_grade = reading_by_grade[columns]

reading_by_grade = reading_by_grade.round(2)

reading_by_grade.head()
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
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.30</td>
      <td>80.91</td>
      <td>80.95</td>
      <td>80.91</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.68</td>
      <td>84.25</td>
      <td>83.79</td>
      <td>84.29</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.20</td>
      <td>81.41</td>
      <td>80.64</td>
      <td>81.38</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.63</td>
      <td>81.26</td>
      <td>80.40</td>
      <td>80.66</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.37</td>
      <td>83.71</td>
      <td>84.29</td>
      <td>84.01</td>
    </tr>
  </tbody>
</table>
</div>



## Scores by School Spending


```python
# Cut values of per student budget, and place them into bins
bins = [0, 585, 615, 645, 675]
labels = ["<$585", "$585-615", "$615-645", "$645-675"]

# Calcute the spending per student again(their values in school_summary are formatted, so I redo the calculations)
spending_per_student = (school_group["budget"].mean()) / students_in_schools

# Creating a group based off of the bins, and use groupby on the dataframe
school_summary["Spending Ranges (Per Student)"] = pd.cut(spending_per_student, bins, labels = labels)
scores_by_spending = school_summary.groupby("Spending Ranges (Per Student)").mean()

# Arrange columns of dataframe
columns = ["Average Math Score", "Average Reading Score", "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]
scores_by_spending = scores_by_spending[columns]
scores_by_spending = scores_by_spending.round(2)

scores_by_spending
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>Spending Ranges (Per Student)</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;$585</th>
      <td>83.45</td>
      <td>83.94</td>
      <td>93.46</td>
      <td>96.61</td>
      <td>95.04</td>
    </tr>
    <tr>
      <th>$585-615</th>
      <td>83.60</td>
      <td>83.88</td>
      <td>94.23</td>
      <td>95.90</td>
      <td>95.06</td>
    </tr>
    <tr>
      <th>$615-645</th>
      <td>79.08</td>
      <td>81.89</td>
      <td>75.67</td>
      <td>86.11</td>
      <td>80.89</td>
    </tr>
    <tr>
      <th>$645-675</th>
      <td>77.00</td>
      <td>81.03</td>
      <td>66.16</td>
      <td>81.13</td>
      <td>73.65</td>
    </tr>
  </tbody>
</table>
</div>



## Scores by School Size


```python
# Cut values of total students and place them into bins
bins = [0, 1000, 2000, 5000]
labels = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]

# Creating a group based off of the bins, and use groupby on the dataframe
school_summary["School Size"] = pd.cut(school_summary["Total Students"], bins, labels = labels)
scores_by_size = school_summary.groupby("School Size").mean()

# Arrange columns of dataframe
columns = ["Average Math Score", "Average Reading Score", "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]
scores_by_size = scores_by_size[columns]
scores_by_size = scores_by_size.round(2)
scores_by_size
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small (&lt;1000)</th>
      <td>83.82</td>
      <td>83.93</td>
      <td>93.55</td>
      <td>96.10</td>
      <td>94.82</td>
    </tr>
    <tr>
      <th>Medium (1000-2000)</th>
      <td>83.37</td>
      <td>83.87</td>
      <td>93.60</td>
      <td>96.79</td>
      <td>95.20</td>
    </tr>
    <tr>
      <th>Large (2000-5000)</th>
      <td>77.74</td>
      <td>81.34</td>
      <td>69.96</td>
      <td>82.77</td>
      <td>76.36</td>
    </tr>
  </tbody>
</table>
</div>



## Scores by School Type


```python
# Using groupby, get summary for each school type
scores_by_type = pd.DataFrame(school_summary.groupby("School Type").mean())

# Arrange columns of dataframe and format
columns = ["Average Math Score", "Average Reading Score", "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]
scores_by_type = scores_by_type[columns]
scores_by_type["Average Math Score"] = scores_by_type["Average Math Score"].map("{:.2f}".format)
scores_by_type["Average Reading Score"] = scores_by_type["Average Reading Score"].map("{:.2f}".format)
scores_by_type["% Passing Math"] = scores_by_type["% Passing Math"].map("{:.2f}".format)
scores_by_type["% Passing Reading"] = scores_by_type["% Passing Reading"].map("{:.2f}".format)
scores_by_type["% Overall Passing Rate"] = scores_by_type["% Overall Passing Rate"].map("{:.2f}".format)
scores_by_type
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.47</td>
      <td>83.90</td>
      <td>93.62</td>
      <td>96.59</td>
      <td>95.10</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.96</td>
      <td>80.97</td>
      <td>66.55</td>
      <td>80.80</td>
      <td>73.67</td>
    </tr>
  </tbody>
</table>
</div>


