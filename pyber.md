
Analysis:
1. There is a relationship between city type and number of drivers, as well as between number of rides and fares. The more rides there are, the lower the fare is.
2. Drivers dominate the urban areas, as 82% drive in those areas. Drivers in rural areas are lacking, as they make up only 1% of the total. 
3. Despite the lack of drivers, rural fares still make up 6% of the total, due to the higher fares.


```python
#import dependencies
from matplotlib import pyplot as plt
from scipy import stats

import numpy as np
import pandas as pd
import os
```


```python
#read csv
city_file = os.path.join('raw_data', 'city_data.csv')
ride_file = os.path.join('raw_data', 'ride_data.csv')

city_df = pd.read_csv(city_file)
ride_df = pd.read_csv(ride_file)

#drop outlier caused issue
city_df.city.drop_duplicates(inplace=True)

#merge
pyber_df = city_df.merge(ride_df, on = 'city')

pyber_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-08-19 04:27:52</td>
      <td>5.51</td>
      <td>6246006544795</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-04-17 06:59:50</td>
      <td>5.54</td>
      <td>7466473222333</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-05-04 15:06:07</td>
      <td>30.54</td>
      <td>2140501382736</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-01-25 20:44:56</td>
      <td>12.08</td>
      <td>1896987891309</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-08-09 18:19:47</td>
      <td>17.91</td>
      <td>8784212854829</td>
    </tr>
  </tbody>
</table>
</div>



# Bubble Plot


```python
#group by city
city_group_df = pyber_df.groupby(['city'])

#find total number of rides per city
rides_per_city = city_group_df['city'].count()

#find type of city
average_per_city = city_group_df['fare'].mean()

#find average fare per city
type_of_city = city_group_df['type'].unique()

#find number of drivers
number_of_drivers = city_group_df['driver_count'].mean()

#create new df
ride_counts_df = pd.DataFrame({"Rides Per City":rides_per_city, "Average Fare": average_per_city, "Number of Drivers":number_of_drivers, "Type of City":type_of_city})

#create new dfs for city types
rural = ride_counts_df[ride_counts_df['Type of City'] == 'Rural']
suburban = ride_counts_df[ride_counts_df['Type of City'] == 'Suburban']
urban = ride_counts_df[ride_counts_df['Type of City'] == 'Urban']
```


```python
#add grid
plt.grid() 

#settings for urban
urban_plt = plt.scatter(urban['Rides Per City'], urban['Average Fare'], s = urban['Number of Drivers']*6, color = 'lightcoral', edgecolor = 'black',
            label = 'Urban', alpha = .75)
#settings for suburban
suburban_plt = ax = plt.scatter(suburban['Rides Per City'], suburban['Average Fare'], s = suburban['Number of Drivers']*6, color = 'lightskyblue', edgecolor = 'black',
           label = 'Urban', alpha = .75)
#settings for suburban
rural_plt = plt.scatter(rural['Rides Per City'], rural['Average Fare'], s = rural['Number of Drivers']*6, color = 'gold', edgecolor = 'black',
            label = 'Urban', alpha = .75, )

#change field size
plt.xlim(0, 40)
plt.ylim(15,52)

#add legend
plt.legend((rural_plt,urban_plt,suburban_plt),("Rural","Urban","Suburban"),title="City Types")

# Create a title, x label, and y label for plot
plt.title("Pyber Ride Sharing Data (2016)")
plt.xlabel("Total Number of Rides (per city)")
plt.ylabel("Average Fare ($)")

#add note
plt.annotate("Note: \nCircle size correlates with driver count per city.", xy=(30, 40), xycoords='data',xytext=(42.5, 40),)

#save figure
plt.savefig("pyberbubble.png")

plt.show()

```


![png](output_5_0.png)



```python
#setup pie chart aesthetics 
labels = ["Rural", "Surburban", "Urban"]
colors = ["gold","lightskyblue", "lightcoral"]
explode=[0,0,.1]
```

# % of Total Fares by City Type


```python
#group by city type and fare
total_fares = pyber_df.groupby(["type"])["fare"].sum()

plt.pie(total_fares, explode=explode, labels=labels, colors=colors, autopct= '%1.1f%%', shadow=True, startangle=120)

#add title
plt.title('% of Total Fares by City Type')

plt.axis("equal")

#save figure
plt.savefig("totalfarespie.png")
plt.show()
```


![png](output_8_0.png)


# % of Total Rides by City Type


```python
#group by city type and ride_id
total_rides = pyber_df.groupby(["type"])["ride_id"].count()

plt.pie(total_rides, explode=explode, labels=labels, colors=colors, autopct= '%1.1f%%', shadow=True, startangle=120)

#add title
plt.title('% of Total Rides by City Type')

plt.axis("equal")

#save figure
plt.savefig("totalridespie.png")
plt.show()

```


![png](output_10_0.png)


# % of Total Drivers by City Type


```python
#group by city type and driver_count
driver_count = city_df.groupby(["type"])["driver_count"].sum()

plt.pie(driver_count, explode=explode, labels=labels, colors=colors, autopct= '%1.1f%%',shadow=True, startangle=160)

#add title
plt.title('% of Total Drivers by City Type')

plt.axis("equal")

#save figure
plt.savefig("totaldriverspie.png")
plt.show()
```


![png](output_12_0.png)

