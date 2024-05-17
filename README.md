<h1 align="center">Electricity Usage Analysis</h1>

<img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/python/python-original.svg" height="85" alt="Python"/> <img width="12"/>
<img alt="Pandas Logo" src="https://pandas.pydata.org/static/img/pandas.svg" height="100" alt="Pandas"/> <img width="80"/>

## <h2 align="center">Key Objectives</h2>
1) Prep the data
2) Visualize the consumption over time (can be changed monthly, yearly etc.)
3) Look for daily trends through out the day
4) Look for trends throughout the week
5) Give insight into how much energy needs to be accounted for

## <h2 align="center">Process and Code</h2>
View Full Python Code File --> [Click Me](https://github.com/nmowens95/Electricity-Usage-Analysis/blob/main/Electricity%20Usage%20Analysis.ipynb)

</br>

1) Prepped the data by importing pandas and adding in additional columns:
</br>

```bash
power_cols = ["Datetime", "PowerConsumption_Zone1", "PowerConsumption_Zone2", "PowerConsumption_Zone3"]

powerconsumption = pd.read_csv(
    "powerconsumption.csv",
    usecols=power_cols,
    parse_dates=["Datetime"]
).assign(total_consumption = lambda x: x["PowerConsumption_Zone1"] + x["PowerConsumption_Zone2"] + x["PowerConsumption_Zone3"],
         hour = lambda x: x["Datetime"].dt.hour,
         day_of_week = lambda x: x["Datetime"].dt.dayofweek
)
```
</br>

- We also created a day of the week list and column that we use later for analysis:
```bash
day_of_week = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday","Sunday"]

powerconsumption["day_name"] = pd.Categorical(powerconsumption["Datetime"].dt.day_name(), categories=day_of_week)
```

</br>

2) Created an area line chart to show the electricity consumption throughout a month. The variable can easily be changed to show other months:
```bash
import seaborn as sns

(powerconsumption
 .set_index(["Datetime"])
 .resample("H")
 .mean()
 .loc["2017-01", ["PowerConsumption_Zone1", "PowerConsumption_Zone2", "PowerConsumption_Zone3"]]
 .plot
 .area(
     title="Hourly Eclectricity Consumption by Zone - Jan 2017",
     xlabel="Date",
     ylabel="Consumption (KwH)"
 )
)


sns.despine()
```
</br>

Chart:
</br>
![Electrical consumption line area](https://github.com/nmowens95/Electricity-Usage-Analysis/assets/126295718/c20dae32-4456-4321-8cde-739265edd82f)

3. Create a heatmap to show weekly/hourly trends:
```bash
sns.heatmap(
    powerconsumption.pivot_table(
        index="day_name",
        columns="hour",
        values="total_consumption",
        aggfunc="mean"
    ),
    cbar=None,
    cmap="viridis"
).set(title="Power Consumption by Day of Week and Hour",
     ylabel="Day of Week",
     xlabel="Hour");
```
</br>

Heatmap:
</br>

![Electrical consumption heatmap](https://github.com/nmowens95/Electricity-Usage-Analysis/assets/126295718/8403da01-207e-42fd-b8bf-4be33656b3f0)

## <h2 align="center">Analysis</h2>
- Based off of objectives 3 and 4 we are able to gain insight with our visuals. We see that energy consumption is heavy in early day time but drops off.
- Most of the days tend to be a similar story, but starting Friday going into the weekend we start to see larger energy consumptions early in the day.
- We'd also see from our area line chart, we need to keep the committee informed that they need to be able to handle at least 100,000 KwH at any given time. 
