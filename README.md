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
