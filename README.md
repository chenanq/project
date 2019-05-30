# project
import pandas as pd
import numpy as np
%matplotlib inline

import matplotlib.pyplot as plt

df_death=pd.read_csv("./data/deathcount.csv",usecols=["DeathCount","StateCode"])
df_case=pd.read_csv("./data/casecount.csv",usecols=["CaseCount","StateCode"])

df_death.set_index("StateCode",inplace=True)
df_case.set_index("StateCode",inplace=True)

df_rate=pd.read_csv("./data/Rate.csv",usecols=["StateCode","IndividualRate"])

df_mean_rate=df_rate.groupby(by="StateCode").mean()

#Rate和CaseCount相关性

df_rate_case=df_mean_rate.merge(df_case,how="left",left_index=True,
                                right_index=True)

#皮尔森相关系数
pearson_rate_case=round(df_rate_case.corr().loc["IndividualRate","CaseCount"],2)
pearson_rate_case

-0.22

txt=df_rate_case.index.tolist()
df_rate_case.plot(x="IndividualRate",y="CaseCount",kind="scatter",figsize=(8,8))
plt.title("Pearson=%s" % pearson_rate_case)
for i in range(len(df_rate_case)):
    plt.annotate(txt[i], xy = (df_rate_case["IndividualRate"].iloc[i],df_rate_case["CaseCount"].iloc[i]), 
                 xytext = (df_rate_case["IndividualRate"].iloc[i]+200,df_rate_case["CaseCount"].iloc[i]))


df_rate_case["IndividualRate"].iloc[0]

7024.934674098841

#Rate和DeathCount相关性

df_rate_death=df_mean_rate.merge(df_death,how="left",left_index=True,
                                 right_index=True)

#皮尔逊相关系数
pearson_rate_death=round(df_rate_death.corr().loc["IndividualRate","DeathCount"],2)
pearson_rate_death

-0.21

txt_2=df_rate_death.index.tolist()
df_rate_death.plot(x="IndividualRate",y="DeathCount",kind="scatter",figsize=(8,8))
plt.title("Pearson=%s" % pearson_rate_death)
for i in range(len(df_rate_death)):
    plt.annotate(txt_2[i], xy = (df_rate_death["IndividualRate"].iloc[i],df_rate_death["DeathCount"].iloc[i]), xytext = (df_rate_death["IndividualRate"].iloc[i]+200,df_rate_death["DeathCount"].iloc[i]))




import pandas as pd
import numpy as np

import plotly.offline as py
import plotly.graph_objs as go
from plotly.offline import init_notebook_mode
import cufflinks as cf
init_notebook_mode(connected=True)
cf.set_config_file(offline=True)
#数据导入

df=pd.read_csv("./data/Rate.csv",usecols=["StateCode","Age","IndividualRate"])
#各州人均IndividualRate

df_mean=df.groupby(by="StateCode").mean()

scl = [
    [0.0, 'rgb(242,240,247)'],
    [0.2, 'rgb(218,218,235)'],
    [0.4, 'rgb(188,189,220)'],
    [0.6, 'rgb(158,154,200)'],
    [0.8, 'rgb(117,107,177)'],
    [1.0, 'rgb(84,39,143)']
]

data = [go.Choropleth(
   colorscale = scl,
    autocolorscale = False,
    locations = df_mean.index.tolist(),
    z = [x[0] for x in df_mean.values.tolist()],
    zmin=0,
    zmax=20000,
    locationmode = 'USA-states',
    text = df_mean.index.astype(str).tolist(),
   marker = go.choropleth.Marker(
        line = go.choropleth.marker.Line(
            color = 'rgb(255,255,255)',
            width = 2
        
        )),
    colorbar = go.choropleth.ColorBar(
        title = "Average<br>Individualrate")
)]

layout = go.Layout(
    autosize=True,
    hovermode='closest',
    title = go.layout.Title(
        text = 'Average Individualrate of different States'
    ),
    geo = go.layout.Geo(
        scope = 'usa',
    
        projection = go.layout.geo.Projection(type = 'albers usa'),
        lakecolor = 'rgb(255, 255, 255)'),
)

fig = go.Figure(data = data, layout = layout)
py.iplot(fig, filename = 'avg_low')


df["age"]=df["Age"].map(lambda x :x if (x=="0-20" or x=="65 and over") else(np.nan if x=="Family Option" else "21-64"))

df_age_mean=df[["StateCode","IndividualRate","age"]]

df_age_mean.dropna(how="any",inplace=True)
E:\software\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame

See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
  """Entry point for launching an IPython kernel.

df_age_group=df_age_mean.groupby(by=["age","StateCode"]).mean()
#0-20岁年龄段各州平均Individualrate

df_low=df_age_group.loc["0-20"]              #0-20岁年龄段数据
df_old=df_age_group.loc["65 and over"]       #大于65岁的人

scl = [
    [0.0, 'rgb(242,240,247)'],
    [0.2, 'rgb(218,218,235)'],
    [0.4, 'rgb(188,189,220)'],
    [0.6, 'rgb(158,154,200)'],
    [0.8, 'rgb(117,107,177)'],
    [1.0, 'rgb(84,39,143)']
]

data = [go.Choropleth(
   colorscale = scl,
    autocolorscale = False,
    locations = df_low.index.tolist(),
    z = [x[0] for x in df_low.values.tolist()],
    zmin=50,
    zmax=250,
    locationmode = 'USA-states',
    text = df_mean.index.astype(str).tolist(),
   marker = go.choropleth.Marker(
        line = go.choropleth.marker.Line(
            color = 'rgb(255,255,255)',
            width = 2
        
        )),
    colorbar = go.choropleth.ColorBar(
        title = "Average<br>Individualrate")
)]

layout = go.Layout(
    autosize=True,
    hovermode='closest',
    title = go.layout.Title(
        text = 'Average Individualrate of different States<br>0-20'
    ),
    geo = go.layout.Geo(
        scope = 'usa',
    
        projection = go.layout.geo.Projection(type = 'albers usa'),
        lakecolor = 'rgb(255, 255, 255)'),
)

fig = go.Figure(data = data, layout = layout)
py.iplot(fig, filename = 'avg_low')

AverageIndividualrate
Average Individualrate of different States0-20
#21-64岁年龄段各州平均Individualrate

df_mid=df_age_group.loc["21-64"]             #21-64年龄段数据

scl = [
    [0.0, 'rgb(242,240,247)'],
    [0.2, 'rgb(218,218,235)'],
    [0.4, 'rgb(188,189,220)'],
    [0.6, 'rgb(158,154,200)'],
    [0.8, 'rgb(117,107,177)'],
    [1.0, 'rgb(84,39,143)']
]

data = [go.Choropleth(
   colorscale = scl,
    autocolorscale = False,
    locations = df_mid.index.tolist(),
    z = [x[0] for x in df_mid.values.tolist()],
    zmin=0,
    zmax=20000,
    locationmode = 'USA-states',
    text = df_mean.index.astype(str).tolist(),
   marker = go.choropleth.Marker(
        line = go.choropleth.marker.Line(
            color = 'rgb(255,255,255)',
            width = 2
        
        )),
    colorbar = go.choropleth.ColorBar(
        title = "Average<br>Individualrate")
)]

layout = go.Layout(
    autosize=True,
    hovermode='closest',
    title = go.layout.Title(
        text = 'Average Individualrate of different States<br>21-64'
    ),
    geo = go.layout.Geo(
        scope = 'usa',
    
        projection = go.layout.geo.Projection(type = 'albers usa'),
        lakecolor = 'rgb(255, 255, 255)'),
)

fig = go.Figure(data = data, layout = layout)
py.iplot(fig, filename = 'avg_mid')



#65及以上年龄段各州平均Individualrate

df_old=df_age_group.loc["65 and over"]       #大于65岁的人

scl = [
    [0.0, 'rgb(242,240,247)'],
    [0.2, 'rgb(218,218,235)'],
    [0.4, 'rgb(188,189,220)'],
    [0.6, 'rgb(158,154,200)'],
    [0.8, 'rgb(117,107,177)'],
    [1.0, 'rgb(84,39,143)']
]

data = [go.Choropleth(
   colorscale = scl,
    autocolorscale = False,
    locations = df_low.index.tolist(),
    z = [x[0] for x in df_old.values.tolist()],
    zmin=0,
    zmax=20000,
    locationmode = 'USA-states',
    text = df_mean.index.astype(str).tolist(),
   marker = go.choropleth.Marker(
        line = go.choropleth.marker.Line(
            color = 'rgb(255,255,255)',
            width = 2
        
        )),
    colorbar = go.choropleth.ColorBar(
        title = "Average<br>Individualrate")
)]

layout = go.Layout(
    autosize=True,
    hovermode='closest',
    title = go.layout.Title(
        text = 'Average Individualrate of different States<br>65 and over'
    ),
    geo = go.layout.Geo(
        scope = 'usa',
    
        projection = go.layout.geo.Projection(type = 'albers usa'),
        lakecolor = 'rgb(255, 255, 255)'),
)

fig = go.Figure(data = data, layout = layout)
py.iplot(fig, filename = 'avg_low')
