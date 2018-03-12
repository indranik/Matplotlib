

```python
# Dependencies
import pandas as pd
import os
import matplotlib.pyplot as plt
import seaborn as sns

```


```python
# Read the datafiles
cityDataDf = pd.read_csv(os.path.join("raw_data","city_data.csv"))
rideDataDf = pd.read_csv(os.path.join("raw_data","ride_data.csv"))


```


```python
# Create your summary dataframes/data

#Summarize the ride_data
# Get the total number of rides occurred in each of the cities and the total fare collected.
sumRideDataDf = rideDataDf.groupby(['city']).agg({'fare':['sum'],'city':['count']})
sumRideDataDf.reset_index(inplace = True)
sumRideDataDf.columns=[''.join(col) for col in sumRideDataDf.columns]
pd.DataFrame(sumRideDataDf.to_records())
sumRideDataDf = sumRideDataDf.rename(columns = {'faresum': 'Total Fare Amt','citycount': 'Num Rides' })


#Create a combined Df that combines the city data with summarized ride data
combineDataDf = pd.merge(cityDataDf,sumRideDataDf,how='outer',on='city')

combineDataDf['Average Fare'] = combineDataDf['Total Fare Amt']/combineDataDf['Num Rides']
#sumRideDataDf.head()
#combineDataDf
```


```python
# Before you start plotting..set your colors.
groups = ['Rural','Suburban','Urban' ]
colors = ['gold', 'lightskyblue', 'lightcoral']
explode = (0, 0, 0.05) 
```


```python
#Bubble Chart
# Create groups based on city type
grpRural  = combineDataDf.loc[combineDataDf['type']=='Rural']
grpSubUrban  = combineDataDf.loc[combineDataDf['type']=='Suburban']
grpUrban  = combineDataDf.loc[combineDataDf['type']=='Urban']

dataGrps = (grpRural, grpSubUrban, grpUrban)

 
# Create plot
fig = plt.figure(figsize = (8,6))

ax = fig.add_subplot(1, 1, 1, axisbg="1.0")
 
for data, color, group in zip(dataGrps, colors, groups):
    x = data['Num Rides']
    y = data['Average Fare']
    ax.scatter(x, y,s=data['driver_count']*8, alpha=0.8, c=color, edgecolors='none', label=group)
    
plt.grid(axis = 'both')

plt.xlim(0, x.max()+10)

# Adding a note to explain the size of the circle
plt.text(0, 11, "Note : Circle size corelates to the driver count in each city.", fontsize = 10)

# Formatting the legend
lgnd = plt.legend(loc="best",title = 'City Type', scatterpoints=1, fontsize=10)
lgnd.legendHandles[0]._sizes = [50]
lgnd.legendHandles[1]._sizes = [50]
lgnd.legendHandles[2]._sizes = [50]

plt.xlabel("Total Number of Rides (Per City)")
plt.ylabel("Average Fare")
plt.show()

```

    C:\Users\Ind\Anaconda3\envs\PythonData_3\lib\site-packages\matplotlib\cbook\deprecation.py:106: MatplotlibDeprecationWarning: The axisbg attribute was deprecated in version 2.0. Use facecolor instead.
      warnings.warn(message, mplDeprecation, stacklevel=1)
    


![png](output_4_1.png)



```python
# Total Fares by city Type

fareByCityTypeDf = combineDataDf.groupby(['type'])['Total Fare Amt'].sum()


# Create a bar chart based off of the group series above
fareByCityPie = fareByCityTypeDf.plot(kind = 'pie',autopct='%1.1f%%',colors =colors,
                                      title = '% of Total Fares by City Type',
                                      explode =explode,startangle=90)

# Set the xlabel and ylabel using class methods
fareByCityPie.set_xlabel("City Type")
fareByCityPie.set_ylabel("")

plt.show()
```


![png](output_5_0.png)



```python
# Total Rides by city Type

ridesByCityTypeDf = combineDataDf.groupby(['type'])['Num Rides'].sum()


# Create a bar chart based off of the group series above
ridesByCityPie = ridesByCityTypeDf.plot(kind = 'pie',autopct='%1.1f%%',colors =colors,
                                      title = '% of Total Rides by City Type',
                                      explode =explode,startangle=90 )

# Set the xlabel and ylabel using class methods
ridesByCityPie.set_xlabel("City Type")
ridesByCityPie.set_ylabel("")

plt.show()
```


![png](output_6_0.png)



```python
# Total Divers by city Type

driversByCityTypeDf = combineDataDf.groupby(['type'])['driver_count'].sum()


# Create a bar chart based off of the group series above
driversByCityPie = driversByCityTypeDf.plot(kind = 'pie',autopct='%1.1f%%',colors =colors,
                                      title = '% of Total Drivers by City Type',
                                       explode =explode,startangle=90)

# Set the xlabel and ylabel using class methods
driversByCityPie.set_xlabel("City Type")
driversByCityPie.set_ylabel("")

plt.show()
```


![png](output_7_0.png)

