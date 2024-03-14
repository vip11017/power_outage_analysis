# Power Outage Analysis

## Introduction
In this project I studied the relationships between the cause of a power outage with many other features such as climate, state, electricity price, etc. The dataset I used for this was provided by Purudue University Laboratory for Advancing Sustainable Critical Infrastructure, titled Major Power Outage Risks in the U.S. This dataset has various data regarding certain characteristics of power otuages, such as location, electricity consumoption, and population. The dataset has 1534 observations and 55 features. With this dataset I based my project around the question: "What characteristics are asociated with each category of cause?". This website will provide you insights on some patterns and characteristiscs of major power outages. You will also be able to learn more about the many aspects of a power outage from location to after-effect. Some of the features in the dataset that will provide you this information are:

> `U.S._State`: Represents the state in continental U.S. where the outage occured \
`NERC.REGION`: The North American Electricity Reliability Corportation regions involved in the outage event \
`CLIMATE.REGION`: U.S. Climate Regions based by National Centers for Environmental Information \
`CLIMATE.CATEGORY`: Represents the climate episodes corresponding to years. \
`CAUSE.CATEGORY`: Categories of events causing the power outage \
`OUTAGE.DURATION`: Duration of Outage \
`DEMAND.LOSS.MW`: Amount of peak demand lost during an outage event \
`CUSTOMERS.AFFECTED`: Number of customers affected by power outage event




## Data Cleaning and Exploratory Data Analysis
#### Data Cleaning

The column `OUTAGE.START.DATE` and `OUTAGE.START.TIME` are both `pd.timestamp()` values, so we can combine them to mame a new `OUTAGE.START` column containing the start date and time for the outage
```py
df['OUTAGE.START.DATE'] = df['OUTAGE.START.DATE'].dt.strftime('%Y-%m-%d')
df['OUTAGE.START.TIME'] = df['OUTAGE.START.TIME'].astype(str)
df['OUTAGE.START'] = pd.to_datetime(df['OUTAGE.START.DATE'] + " " + df['OUTAGE.START.TIME'])
```


The column `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME` are also `pd.timestamp()`, so I did the same thing
```py
df['OUTAGE.RESTORATION.DATE'] = df['OUTAGE.RESTORATION.DATE'].dt.strftime('%Y-%m-%d')
df['OUTAGE.RESTORATION.TIME'] = df['OUTAGE.RESTORATION.TIME'].astype(str)
df['OUTAGE.RESTORATION'] = pd.to_datetime(df['OUTAGE.RESTORATION.DATE'] + " " + df['OUTAGE.RESTORATION.TIME'])
```

The columns `RES.PRICE`, `COM.PRICE`, `IND.PRICE`, `TOTAL.PRICE` are currently in the unit of cents/kilowatt-hour. So to makle it on the same unit as some of the other columns measures in dollars, I converted this column to the units of dollars/kilowatt-hour
```py
price_col = ['RES.PRICE', 'COM.PRICE', 'IND.PRICE', 'TOTAL.PRICE']
df[price_col] /= 100
```

The columns `RES.SALES`, `COM.SALES`, `IND.SALES`, `TOTAL.SALES`, and `DEMAND.LOSS.MW` were all measures in the unit of megawatt. So, to make it on the same measurement as the other features, I transformed it to kilowatts-hour
```py
sales_col = ['RES.SALES', 'COM.SALES', 'IND.SALES', 'TOTAL.SALES', 'DEMAND.LOSS.MW']
df[sales_col] *= 1000
```

The columns that are stored as a percentage(%), so to ease future mathematical operations, I converted them all into decimal format
```py
perc_col = ['RES.PERCEN', 'COM.PERCEN', 'IND.PERCEN', 'RES.CUST.PCT', 'COM.CUST.PCT', 'IND.CUST.PCT', 'PC.REALGSP.CHANGE', 'UTIL.CONTRI', 'PI.UTIL.OFUSA', 'POPPCT_URBAN', 'POPPCT_UC', 'AREAPCT_URBAN', 'AREAPCT_UC', 'PCT_LAND', 'PCT_WATER_TOT', 'PCT_WATER_INLAND']
df[perc_col] /= 100
```


```py
df.head()
```

|   YEAR |   MONTH | U.S._STATE   | POSTAL.CODE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | OUTAGE.START.DATE   | OUTAGE.START.TIME   | OUTAGE.RESTORATION.DATE   | OUTAGE.RESTORATION.TIME   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   HURRICANE.NAMES |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   RES.PRICE |   COM.PRICE |   IND.PRICE |   TOTAL.PRICE |   RES.SALES |   COM.SALES |   IND.SALES |   TOTAL.SALES |   RES.PERCEN |   COM.PERCEN |   IND.PERCEN |   RES.CUSTOMERS |   COM.CUSTOMERS |   IND.CUSTOMERS |   TOTAL.CUSTOMERS |   RES.CUST.PCT |   COM.CUST.PCT |   IND.CUST.PCT |   PC.REALGSP.STATE |   PC.REALGSP.USA |   PC.REALGSP.REL |   PC.REALGSP.CHANGE |   UTIL.REALGSP |   TOTAL.REALGSP |   UTIL.CONTRI |   PI.UTIL.OFUSA |   POPULATION |   POPPCT_URBAN |   POPPCT_UC |   POPDEN_URBAN |   POPDEN_UC |   POPDEN_RURAL |   AREAPCT_URBAN |   AREAPCT_UC |   PCT_LAND |   PCT_WATER_TOT |   PCT_WATER_INLAND | OUTAGE.START        | OUTAGE.RESTORATION   |
|-------:|--------:|:-------------|:--------------|:--------------|:-------------------|----------------:|:-------------------|:--------------------|:--------------------|:--------------------------|:--------------------------|:-------------------|:------------------------|------------------:|------------------:|-----------------:|---------------------:|------------:|------------:|------------:|--------------:|------------:|------------:|------------:|--------------:|-------------:|-------------:|-------------:|----------------:|----------------:|----------------:|------------------:|---------------:|---------------:|---------------:|-------------------:|-----------------:|-----------------:|--------------------:|---------------:|----------------:|--------------:|----------------:|-------------:|---------------:|------------:|---------------:|------------:|---------------:|----------------:|-------------:|-----------:|----------------:|-------------------:|:--------------------|:---------------------|
|   2011 |       7 | Minnesota    | MN            | MRO           | East North Central |            -0.3 | normal             | 2011-07-01          | 17:00:00            | 2011-07-03                | 20:00:00                  | severe weather     | nan                     |               nan |              3060 |              nan |                70000 |      0.116  |      0.0918 |      0.0681 |        0.0928 | 2.33292e+09 | 2.11477e+09 | 2.11329e+09 |   6.56252e+09 |     0.355491 |     0.32225  |     0.322024 |         2308736 |          276286 |           10673 |           2595696 |       0.889448 |       0.10644  |     0.00411181 |              51268 |            47586 |          1.07738 |               0.016 |           4802 |          274182 |     0.0175139 |           0.022 |      5348119 |         0.7327 |      0.1528 |           2279 |      1700.5 |           18.2 |          0.0214 |        0.006 |   0.915927 |       0.0840733 |          0.0547874 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |
|   2014 |       5 | Minnesota    | MN            | MRO           | East North Central |            -0.1 | normal             | 2014-05-11          | 18:38:00            | 2014-05-11                | 18:39:00                  | intentional attack | vandalism               |               nan |                 1 |              nan |                  nan |      0.1212 |      0.0971 |      0.0649 |        0.0928 | 1.58699e+09 | 1.80776e+09 | 1.88793e+09 |   5.28423e+09 |     0.300325 |     0.342104 |     0.357276 |         2345860 |          284978 |            9898 |           2640737 |       0.888335 |       0.107916 |     0.0037482  |              53499 |            49091 |          1.08979 |               0.019 |           5226 |          291955 |     0.0179    |           0.022 |      5457125 |         0.7327 |      0.1528 |           2279 |      1700.5 |           18.2 |          0.0214 |        0.006 |   0.915927 |       0.0840733 |          0.0547874 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |
|   2010 |      10 | Minnesota    | MN            | MRO           | East North Central |            -1.5 | cold               | 2010-10-26          | 20:00:00            | 2010-10-28                | 22:00:00                  | severe weather     | heavy wind              |               nan |              3000 |              nan |                70000 |      0.1087 |      0.0819 |      0.0607 |        0.0815 | 1.46729e+09 | 1.80168e+09 | 1.9513e+09  |   5.22212e+09 |     0.280977 |     0.34501  |     0.37366  |         2300291 |          276463 |           10150 |           2586905 |       0.889206 |       0.10687  |     0.00392361 |              50447 |            47287 |          1.06683 |               0.027 |           4571 |          267895 |     0.0170627 |           0.021 |      5310903 |         0.7327 |      0.1528 |           2279 |      1700.5 |           18.2 |          0.0214 |        0.006 |   0.915927 |       0.0840733 |          0.0547874 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |
|   2012 |       6 | Minnesota    | MN            | MRO           | East North Central |            -0.1 | normal             | 2012-06-19          | 04:30:00            | 2012-06-20                | 23:00:00                  | severe weather     | thunderstorm            |               nan |              2550 |              nan |                68200 |      0.1179 |      0.0925 |      0.0671 |        0.0919 | 1.85152e+09 | 1.94117e+09 | 1.99303e+09 |   5.78706e+09 |     0.319941 |     0.335433 |     0.344393 |         2317336 |          278466 |           11010 |           2606813 |       0.888954 |       0.106822 |     0.00422355 |              51598 |            48156 |          1.07148 |               0.006 |           5364 |          277627 |     0.0193209 |           0.022 |      5380443 |         0.7327 |      0.1528 |           2279 |      1700.5 |           18.2 |          0.0214 |        0.006 |   0.915927 |       0.0840733 |          0.0547874 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |
|   2015 |       7 | Minnesota    | MN            | MRO           | East North Central |             1.2 | warm               | 2015-07-18          | 02:00:00            | 2015-07-19                | 07:00:00                  | severe weather     | nan                     |               nan |              1740 |           250000 |               250000 |      0.1307 |      0.1016 |      0.0774 |        0.1043 | 2.02888e+09 | 2.16161e+09 | 1.77794e+09 |   5.97034e+09 |     0.339826 |     0.362059 |     0.297795 |         2374674 |          289044 |            9812 |           2673531 |       0.888216 |       0.108113 |     0.00367005 |              54431 |            49844 |          1.09203 |               0.017 |           4873 |          292023 |     0.016687  |           0.022 |      5489594 |         0.7327 |      0.1528 |           2279 |      1700.5 |           18.2 |          0.0214 |        0.006 |   0.915927 |       0.0840733 |          0.0547874 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |



#### Exploratory Data Analysis

> Distribution of `U.S._STATE`:
<iframe
  src="assets/state_distr.html"
  width='800'
  height= '418'
  frameborder='0'
></iframe>

Here we can see the number of power outages by state. Based on this histogram, we can see that California has the highest number of Power Outages with over 200 in total, and Arkansas has the least amount with barely over 0 in total. 


> Distribution of `U.S._STATE` on Map:
<iframe
  src="assets/choropleth_map.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This provides us with a visualization on the geographic areas of the power outages. You can see hrer that majority of the midwest has relatively low power outages and as you move more north east, the number of power outages per state tend to increase


> Distribution of `CUSTOMERS.AFFECTED` per `CLIMATE.CATEGORY`
<iframe
  src='assets/climate_X_customer.html'
  width="800"
  height="418"
  frameborder="0"
></iframe>

Here we can see a box and whisker plot for each unique `CLIMATE.CATEGORY`: normal, cold, and warm. All three box and whisker plots seem to each have the same or very similiar values for the lower and upper quartile, and the median. This could tell us that the distribution of `CLIMATE.CATEGORY` by `CUSTOMERS.AFFECTED` are roughly the same, meaning that there may not be a meaningful difference between the catetgories


> Pairplor of `Utility.PERCEN` features
<iframe
  src='assets/pair_percen.html'
  width="800"
  height="418"
  frameborder="0"
></iframe>

This pairplot gives us all the combinations between all the `.PERCEN` columns: `RES.PERCEN`, `COM.PERCEN`, `IND.PERCEN`. You can see here that `RES.PERCEN` by `IND.PERCEN`, `COM.PERCEN` by `IND.PERCEN` show an overall negative slope indicating that while as one of the featuers increases, the other decreases. Or, in other words, the two features have an inverse relationship.

> Pairplor of `Utility.SALES` features
<iframe
  src='assets/pair_sales.html'
  width="800"
  height="418"
  frameborder="0"
></iframe>

This pairplor gives us all the combinations between all the `.SALES` columns: `RES.SALES`, `COM.SALES`, `IND.SALES`. Here we can see every combination of features shows a strong positive correlation, suggesting that as one feature increases, the other features also increases

> Month by `CUSTOMERS.AFFECTED`
<iframe
  src='assets/month_customer.html'
  width="800"
  height="418"
  frameborder="0"
></iframe>

Here we can see a line plot where the x-axis is `month` and the y-axis is `CUSTOMERS.AFFECTED`. Here we can see some patterns wehere in month 3, March, there is a drop is `CUSTOMERS.AFFECTED`, but as summer approaches the number steadily inrceases and reached a peak in month 8, August. Then the `CUSTOMERS.AFFECTED` drops again by November

## Assessment of Missingess 
### NMAR Analysis

### Missingness Dependency


## Hypothesis Testing
We are investigating whether `CLIMATE.REGION` and `CAUSE.CATEGORY` have the same ditribution, or in other words we want to see if there is a significant association between the two features. This could tell us some more insights on the data and a potential reasoning as to what features play an important part in the `CAUSE.CATEGORY`. To do this I am going to use a permutation test on the distribution of `CLIMATE.REGION` and `CAUSE.CATEGORY` to see if there is such an association, or is it just by chance.

__Null Hypothesis (H<sub>0</sub>):__ The `CLIMATE.REGION` has no association with `CAUSE.CATEGORY` \
__Alternative Hypothesis (H<sub>1</sub>):__ `CLIMATE.REGION` is associated with `CAUSE.CATEGORY` \
__Test Statistic:__ Since we are comparing two categorical distributions, `CLIMATE.REGION` and `CAUSE.CATEGORY`, we can use total variation distance (TVD) \
__Significance Level:__ To ensure a good balance between Type I Error and Type II Error, I chose a significance level at 5% (0.05)

To start I created a contingency table with the index being `CLIMATE.REGION`, and columns being `CAUSE.CATEGORY`, and then divide by the sum to be able to convert these "counts" into proportions"
```py
count_table = df.pivot_table(index='CLIMATE.REGION', columns='CAUSE.CATEGORY', aggfunc='size')
count_table = count_table / count_table.sum()

count_table.head()
```

|   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
|           0.118644  |               0.0784314 |            0.0909091 |   0.0652174 |       0.0289855 |        0.177866  |                       0.0873016 |
|           0.0508475 |               0.0980392 |            0.0478469 |   0.0217391 |       0.0289855 |        0.137022  |                       0.0238095 |
|           0.0847458 |               0.27451   |            0.322967  |   0.0217391 |       0.057971  |        0.231884  |                       0.119048  |
|           0.0338983 |               0.0196078 |            0.212919  |   0.108696  |       0.0289855 |        0.0382082 |                       0.031746  |
|           0.169492  |               0.137255  |            0.0669856 |   0.0434783 |       0.608696  |        0.14888   |                       0.214286  |


Next, I needed to find my observed statistic. To do this, I used the pandas.DataFrame.diff() method to be able to find the d


## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis