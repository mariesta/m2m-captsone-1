# Capstone Project #1: Sleep Analysis

## Dataset details
This dataset comes courtesy of M2M Connect. It contains 373 records of people including their sleep quality, duration, occupation, and other data. Based on this, we can derive a few information.

```python
import pandas as pd
from bokeh.plotting import figure, show
from bokeh.models import ColumnDataSource
from bokeh.io import output_notebook
from bokeh.models import HoverTool
from bokeh.layouts import row, column
from bokeh.palettes import Bokeh4, TolRainbow11

output_notebook()

sleep_lifestyle_df = pd.read_csv("Sleep_health_and_lifestyle_dataset.csv");

# Clean data
sleep_lifestyle_df['Sleep Disorder'] = sleep_lifestyle_df['Sleep Disorder'].fillna('No Disorder')
sleep_lifestyle_df = sleep_lifestyle_df.replace('Normal Weight', 'Normal');


print(sleep_lifestyle_df);
```

## Sleep and Gender
The first thing we can try to determine is if gender has something to do with sleep duration or quality.

Based on the data, there isn't a big difference in sleep duration between genders with both sleeping on average 7 hours.

Women, however, seem to have a better sleep quality at 7.7 compared to 7 on average for men.

```python
gender_groups = sleep_lifestyle_df.groupby('Gender')

# Dataset per gender
male_df = gender_groups.get_group('Male')
female_df = gender_groups.get_group('Female')

genders = ['Male', 'Female']
color = ['#F6A91B', '#892889']

# Calculating sleep average per gender
avg_sleep_female = female_df['Sleep Duration'].mean()
avg_sleep_male = male_df['Sleep Duration'].mean()
avgs = [avg_sleep_male, avg_sleep_female]

# Calculating sleep quality per gender
avg_sleep_quality_female = female_df['Quality of Sleep'].mean()
avg_sleep_quality_male = male_df['Quality of Sleep'].mean()
avgs_quality = [avg_sleep_quality_male, avg_sleep_quality_female]

# Plotting Average Sleep Duration per Gender
source = ColumnDataSource(data=dict(genders=genders, avgs=avgs, color=color))
gender_sleep_chart = figure(x_range=genders, y_range=(0,9), height=350, title="Average Sleep Duration by Gender", toolbar_location=None, tools="")
gender_sleep_chart.vbar(x='genders', top='avgs', width=0.5, color='color', legend_field="genders", source=source)

gender_sleep_chart.xgrid.grid_line_color = None
gender_sleep_chart.y_range.start = 0
gender_sleep_chart.legend.orientation = "horizontal"
gender_sleep_chart.legend.location = "top_center"

gender_sleep_chart.add_tools(HoverTool(
    tooltips=[
        ('Avg', '@avgs{1.1} hrs'),
    ]))

# Plotting Quality of Sleep per Gender
source_quality = ColumnDataSource(data=dict(genders=genders, avgs=avgs_quality, color=color))
gender_sleep_quality_chart = figure(x_range=genders, y_range=(0,9), height=350, title="Average Quality of Sleep by Gender", toolbar_location=None, tools="")
gender_sleep_quality_chart.vbar(x='genders', top='avgs', width=0.5, color='color', legend_field="genders", source=source_quality)

gender_sleep_quality_chart.xgrid.grid_line_color = None
gender_sleep_quality_chart.y_range.start = 0
gender_sleep_quality_chart.legend.orientation = "horizontal"
gender_sleep_quality_chart.legend.location = "top_center"

gender_sleep_quality_chart.add_tools(HoverTool(
    tooltips=[
        ('Avg', '@avgs{1.1}'),
    ]))

show(row(gender_sleep_chart, gender_sleep_quality_chart))
```
<img width="593" alt="Screenshot 2025-02-28 at 9 54 45 PM" src="https://github.com/user-attachments/assets/e6221d18-b454-4492-9bde-e5195efb3530" />
<img width="591" alt="Screenshot 2025-02-28 at 9 55 00 PM" src="https://github.com/user-attachments/assets/03ef5ccd-deaf-4ac5-84c9-23ce45b0be6a" />

## Sleep and Age
Age is also one data point in the dataset. Thanks to this, we can group records by decades and see if, at different ages, people sleep better.

Based on our dataset, we can see people in their 50s not only sleep longer averaging 7.6 hrs but also have a better sleep quality at 8.2.

The decade that sleeps the worst is the youngest group, those in their 20s. They sleep on average 6.5 hours and have a sleep quality averaging 5.8.
```python
age_groups = sleep_lifestyle_df.groupby(pd.cut(sleep_lifestyle_df['Age'], [20, 29, 39, 49, 59]))
age_labels = ['20-29', '30-39', '40-49', '50-59']

# Calculating sleep duration and quality average per age group
avgs_age_sleep_duration = []
avgs_age_sleep_quality = []
for _, group in age_groups:
  avg_sleep = group['Sleep Duration'].mean()
  avg_sleep_quality = group['Quality of Sleep'].mean()
  avgs_age_sleep_duration.append(avg_sleep)
  avgs_age_sleep_quality.append(avg_sleep_quality)

# Plotting Average Sleep Duration per Age
source_sleep_age = ColumnDataSource(data=dict(ages=age_labels, avgs=avgs_age_sleep_duration, color=Bokeh4))
age_sleep_chart = figure(x_range=age_labels, y_range=(0,9), height=350, title="Average Sleep Duration by Age Range", toolbar_location=None, tools="")
age_sleep_chart.vbar(x='ages', top='avgs', width=0.5, color='color', legend_field="ages", source=source_sleep_age)

age_sleep_chart.xgrid.grid_line_color = None
age_sleep_chart.y_range.start = 0
age_sleep_chart.legend.orientation = "horizontal"
age_sleep_chart.legend.location = "top_center"

age_sleep_chart.add_tools(HoverTool(
    tooltips=[
        ('Avg', '@avgs{1.1} hrs'),
    ]))

# Plotting Average Sleep per Age
source_sleep_age_quality = ColumnDataSource(data=dict(ages=age_labels, avgs=avgs_age_sleep_quality, color=Bokeh4))
age_sleep_quality_chart = figure(x_range=age_labels, y_range=(0,9), height=350, title="Average Sleep Quality by Age Range", toolbar_location=None, tools="")
age_sleep_quality_chart.vbar(x='ages', top='avgs', width=0.5, color='color', legend_field="ages", source=source_sleep_age_quality)

age_sleep_quality_chart.xgrid.grid_line_color = None
age_sleep_quality_chart.y_range.start = 0
age_sleep_quality_chart.legend.orientation = "horizontal"
age_sleep_quality_chart.legend.location = "top_center"

age_sleep_quality_chart.add_tools(HoverTool(
    tooltips=[
        ('Avg', '@avgs{1.1}'),
    ]))

show(row(age_sleep_chart, age_sleep_quality_chart))
```
<img width="592" alt="Screenshot 2025-02-28 at 10 11 14 PM" src="https://github.com/user-attachments/assets/59c49ecf-e035-401e-a262-c92736d2417a" />
<img width="596" alt="Screenshot 2025-02-28 at 10 11 01 PM" src="https://github.com/user-attachments/assets/da756075-128c-4675-a2ec-232a01e80208" />

## Sleep and Occupation
Does occupation have an impact on the duration or quality of sleep? Based on our dataset, we can determine whether that is true.

According to our dataset, we can see that engineers sleep the most with 8 hours of sleep on average. Far more than the sales representatives at 5.9 hours.

Engineers have the best quality of sleep. Again, far more than the sales representatives.
```python
sleep_occupation_groups = sleep_lifestyle_df.groupby('Occupation')
occupation_labels = []
occupation_sleep_duration = []
occupation_sleep_quality = []

for _, group in sleep_occupation_groups:
  occupation_labels.append(group['Occupation'].iloc[0])
  occupation_sleep_duration.append(group['Sleep Duration'].mean())
  occupation_sleep_quality.append(group['Quality of Sleep'].mean())

# Plotting Averages Sleep per Occupation
source_sleep_occupation = ColumnDataSource(data=dict(occupation=occupation_labels, avgs=occupation_sleep_duration, color=TolRainbow11))
occupation_sleep_chart = figure(x_range=occupation_labels, y_range=(0,12), height=400, width=1250, title="Average Sleep Duration by Occupation", toolbar_location=None, tools="")
occupation_sleep_chart.vbar(x='occupation', top='avgs', width=0.5, color='color', legend_field="occupation", source=source_sleep_occupation)

occupation_sleep_chart.xgrid.grid_line_color = None
occupation_sleep_chart.y_range.start = 0
occupation_sleep_chart.legend.orientation = "horizontal"
occupation_sleep_chart.legend.location = "top_center"

occupation_sleep_chart.add_tools(HoverTool(
    tooltips=[
        ('Avg', '@avgs{1.1} hrs'),
    ]))

# Plotting Average Sleep Quality per Occupation
source_sleep_occupation_quality = ColumnDataSource(data=dict(occupation=occupation_labels, avgs=occupation_sleep_quality, color=TolRainbow11))
occupation_sleep_quality_chart = figure(x_range=occupation_labels, y_range=(0,12), height=400, width=1250, title="Average Sleep Quality by Occupation", toolbar_location=None, tools="")
occupation_sleep_quality_chart.vbar(x='occupation', top='avgs', width=0.5, color='color', legend_field="occupation", source=source_sleep_occupation_quality)

occupation_sleep_quality_chart.xgrid.grid_line_color = None
occupation_sleep_quality_chart.y_range.start = 0
occupation_sleep_quality_chart.legend.orientation = "horizontal"
occupation_sleep_quality_chart.legend.location = "top_center"

occupation_sleep_quality_chart.add_tools(HoverTool(
    tooltips=[
        ('Avg', '@avgs{1.1}'),
    ]))

show(column(occupation_sleep_chart, occupation_sleep_quality_chart))
```
<img width="1245" alt="Screenshot 2025-02-28 at 10 17 33 PM" src="https://github.com/user-attachments/assets/64a47ff3-a55b-45be-85f9-a4cac103ea53" />
<img width="1243" alt="Screenshot 2025-02-28 at 10 15 10 PM" src="https://github.com/user-attachments/assets/008e7aee-9d1d-4a6c-b469-9ec62c93460c" />

## Sleep Disorders
More than half of the respondents do not suffer from sleep disorders. From the ones that do, it's evenly split between sleep apnea and insomnia.

One of the factors affecting sleep disorders is weight as 90% who have one are also overweight or obese.

Women are also disproportionally affected by sleep disorders compared to men.

```python
from math import pi
from bokeh.palettes import YlGnBu, RdPu
from bokeh.transform import cumsum

sleep_disorders_groups = sleep_lifestyle_df.groupby('Sleep Disorder')

x = {
    'Sleep Apnea': sleep_disorders_groups.get_group('Sleep Apnea')['Person ID'].count(),
    'Insomnia': sleep_disorders_groups.get_group('Insomnia')['Person ID'].count(),
    'No Disorder': sleep_disorders_groups.get_group('No Disorder')['Person ID'].count(),
}

data = pd.Series(x).reset_index(name='value').rename(columns={'index': 'disorder'})
data['angle'] = data['value']/data['value'].sum() * 2*pi
data['color'] = YlGnBu[len(x)]

sleep_disorder_chart = figure(height=350, title="Sleep Disorders", toolbar_location=None,
           tools="hover", tooltips="@disorder: @value", x_range=(-0.5, 1.0))

sleep_disorder_chart.wedge(x=0, y=1, radius=0.4,
        start_angle=cumsum('angle', include_zero=True), end_angle=cumsum('angle'),
        line_color="white", fill_color='color', legend_field='disorder', source=data)

sleep_disorder_chart.axis.axis_label = None
sleep_disorder_chart.axis.visible = False
sleep_disorder_chart.grid.grid_line_color = None

# Get BMI for Sleep Apnea and Insomnia
disorders_df = sleep_lifestyle_df.loc[sleep_lifestyle_df['Sleep Disorder'].isin(['Sleep Apnea', 'Insomnia'])]
disorders_bmi_groups = disorders_df.groupby('BMI Category')

disorder_bmi_dict = {
    'Normal': disorders_bmi_groups.get_group('Normal')['Person ID'].count(),
    'Overweight': disorders_bmi_groups.get_group('Overweight')['Person ID'].count(),
    'Obese': disorders_bmi_groups.get_group('Obese')['Person ID'].count()
}
data_disorder_bmi = pd.Series(disorder_bmi_dict).reset_index(name='value').rename(columns={'index': 'bmi'})
data_disorder_bmi['angle'] = data_disorder_bmi['value']/data_disorder_bmi['value'].sum() * 2*pi
data_disorder_bmi['color'] = RdPu[len(disorder_bmi_dict)]

disorder_bmi_chart = figure(height=350, title="BMI for Sleep Disorders", toolbar_location=None,
           tools="hover", tooltips="@bmi: @value", x_range=(-0.5, 1.0))
 
disorder_bmi_chart.wedge(x=0, y=1, radius=0.4,
        start_angle=cumsum('angle', include_zero=True), end_angle=cumsum('angle'),
        line_color="white", fill_color='color', legend_field='bmi', source=data_disorder_bmi)

disorder_bmi_chart.axis.axis_label = None
disorder_bmi_chart.axis.visible = False
disorder_bmi_chart.grid.grid_line_color = None

show(row(sleep_disorder_chart, disorder_bmi_chart))

# Get gender for sleep disorders
disorders_gender_groups = disorders_df.groupby('Gender')

disorder_gender_dict = {
    'Male': disorders_gender_groups.get_group('Male')['Person ID'].count(),
    'Female': disorders_gender_groups.get_group('Female')['Person ID'].count(),
}
data_disorder_gender = pd.Series(disorder_gender_dict).reset_index(name='value').rename(columns={'index': 'gender'})
data_disorder_gender['angle'] = data_disorder_gender['value']/data_disorder_gender['value'].sum() * 2*pi
data_disorder_gender['color'] = ('#F6A91B', '#892889')

disorder_gender_chart = figure(height=350, title="Gender for Sleep Disorders", toolbar_location=None,
           tools="hover", tooltips="@gender: @value", x_range=(-0.5, 1.0))
 
disorder_gender_chart.wedge(x=0, y=1, radius=0.4,
        start_angle=cumsum('angle', include_zero=True), end_angle=cumsum('angle'),
        line_color="white", fill_color='color', legend_field='gender', source=data_disorder_gender)

disorder_gender_chart.axis.axis_label = None
disorder_gender_chart.axis.visible = False
disorder_gender_chart.grid.grid_line_color = None

show(disorder_gender_chart)
```
<img width="593" alt="Screenshot 2025-02-28 at 10 21 27 PM" src="https://github.com/user-attachments/assets/4ca4576f-ad63-4a25-a220-390071ee2576" />
<img width="598" alt="Screenshot 2025-02-28 at 10 21 12 PM" src="https://github.com/user-attachments/assets/2f298246-479b-4993-a17c-339a2e1a1732" />
<img width="595" alt="Screenshot 2025-02-28 at 10 21 04 PM" src="https://github.com/user-attachments/assets/7b617194-72a6-49de-8a0b-cc2ce2198004" />

