---
external: false
title: "A Time-Series Analysis of Inflation and Unemployment"
description: "Does the Phillips Curve exists in Indonesia?"
date: 2023-12-06
---

By [Abdurrahman Fadhil](https://www.linkedin.com/in/rahmanfadhil/) and [Richita Hongo](https://www.linkedin.com/in/richita-hongo-0097b8251/)

We just realized how hard it is to come up with a good econometric model. The thing is that you never know whether or not your explanatory variables do a good job explaining your dependent variable until you regress it. Imagine spending weeks reading papers and textbooks, collecting data, cleaning it, and the moment you regress it you realize that what you did doesn’t really matter. 🙃

That’s what pretty much happened to us. However, no lesson is more valuable than experience. We learned an awful lot of things since the day we started this project, which is what we’re about to share with you all today!

## The Phillips Curve

One of the basic macroeconomic theories says that, in the short run, inflation and unemployment simultaneously go together in the opposite direction. At times of low unemployment, inflation tends to be high. This also works the other way around. High unemployment years tend have to low inflation.

This might seem a little bit counterintuitive at first glance. One of the easiest explanation for this phenomenon is that as the economy gets closer to full employment, the firms have no choice but to hire workers with high reservation wage, that is the minimum amount of wage that the workers is willing to accept for a job. This results in a higher cost of producing goods and services, which leaves the firms with no choice but to raise their prices, in other words, inflation.

However, is it always true that inflation is negatively correlated with inflation? What is the case in Indonesia? These are the questions we asked ourselves a couple of weeks ago, and we decided to figure it out by ourselves.

After reading our textbooks, the simplest model we can find to prove this theory is this:

𝚫inflation{% sub %}t{% /sub %} = β{% sub %}0{% /sub %} + β{% sub %}1{% /sub %} unemployment{% sub %}t{% /sub %}

This model is called the _Static Phillips Curve_ model, and we took it from Jeff Wooldridge's _Introductory Econometrics 7th edition_ book chapter 10. Here, we want to estimate whether or not inflation is really correlated with unemployment. We’re not quite sure at this point why this model uses the first difference of inflation rather than the actual inflation. After we did some digging, one possible explanation we can come up with is that inflation tends to be not stationary, i.e. have a trend, but we’re not very confident about it.

### Preparing our data

We collected Indonesia’s inflation and unemployment data from the World Bank and Badan Pusat Statistik (BPS). All of the data, source code, graphs, and regression output shown in this article can be accessed freely through [this link](https://drive.google.com/drive/folders/1Or4jm4am0U1-QKsYTG9dYflqSI1nScJC).

Let's open the `project 1 bps.dta` file to your stata.

![open stata](/images/phillips-curve/image15.png)

By using the `br` syntax, we can observe the raw (unmanipulated data).

| inflation | unemployment | time   |
| --------- | ------------ | ------ |
| -.17      | 10.26        | 2019m2 |
| .55       | 11.24        | 2019m8 |
| .58       | 10.45        | 2018m2 |
| .33       | 10.28        | 2018m8 |
| .62       | 9.75         | 2017m2 |
| .75       | 9.11         | 2017m8 |
| ...       | ...          | ...    |

The `tsset` command tells Stata to treat your dataset as a time series using the variable time as the time variable. The argument delta (6 months) indicates that the time interval between observations are 6 months.

```stata
tsset time, delta(6 months)
```

If you run the command, you will see something like this:

```txt
Time variable: time, 2005m2 to 2019m8
        Delta: 6 months
```

Currently, the inflation data is in percentage, but we want to transform it into 𝚫inflation.

```stata
gen delta_inflation = inflation - inflation[_n-1]
```

The above command creates a new variable called `delta_inflation`. This variable represents the change in inflation between consecutive time periods in the dataset. By using this command, you can measure the changes in inflation between time periods and analyze the trends or patterns of these changes in your Stata dataset.

By using the `br` command, we can review our latest data:

| inflation | unemployment | time   | delta_inflation |
| --------- | ------------ | ------ | --------------- |
| -.08      | 4.98         | 2005m2 |                 |
| .12       | 5.23         | 2005m8 | .2              |
| .17       | 5.1          | 2006m2 | .05             |
| -.05      | 5.3          | 2006m8 | -.22            |
| .23       | 5.33         | 2007m2 | .28             |
| -.07      | 5.5          | 2007m8 | -.3             |
| ...       | ...          | ...    | ...             |

### Run the regression

```stata
regress delta_inflation unemployment
```

The command above performs linear regression in Stata, with `delta_inflation` as the dependent variable (y) and unemployment as the independent variable (x). In other words, this command produces a simple linear regression model where you attempt to predict changes in inflation (`delta_inflation`) based on the unemployment rate (unemployment).

```txt
      Source |       SS           df       MS      Number of obs   =        29
-------------+----------------------------------   F(1, 27)        =      0.03
       Model |  .009432878         1  .009432878   Prob > F        =    0.8555
    Residual |    7.530381        27     .278903   R-squared       =    0.0013
-------------+----------------------------------   Adj R-squared   =   -0.0357
       Total |  7.53981388        28  .269279067   Root MSE        =    .52811

------------------------------------------------------------------------------
delta_infl~n | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
-------------+----------------------------------------------------------------
unemployment |  -.0099355    .054025    -0.18   0.855    -.1207856    .1009146
       _cons |   .0932221   .4009531     0.23   0.818    -.7294656    .9159099
------------------------------------------------------------------------------
```

The output from this command will provide information about regression coefficients, significance test statistics, and model performance evaluation metrics. This information can help you understand the relationship between changes in inflation and the unemployment rate in your dataset.

```stata
predict delta_inflation_hat
```

The command above serves to predict the values of the dependent variable that have been estimated by the regression model. In this case, the dependent variable being estimated is `delta_inflation`, and the predicted results will be stored in a new variable called `delta_inflation_hat`. The `delta_inflation_hat` variable for further analysis or to compare the model predictions with the actual values of the dependent variable.

By using the `br` syntax, we can review our latest data once more:

| inflation | unemployment | time   | delta_inflation | delta_inflation_hat |
| --------- | ------------ | ------ | --------------- | ------------------- |
| -.08      | 4.98         | 2005m2 |                 | .0437433            |
| .12       | 5.23         | 2005m8 | .2              | .0412594            |
| .17       | 5.1          | 2006m2 | .05             | .042551             |
| -.05      | 5.3          | 2006m8 | -.22            | .0405639            |
| .23       | 5.33         | 2007m2 | .28             | .0402659            |
| -.07      | 5.5          | 2007m8 | -.3             | .0385768            |
| ...       | ...          | ...    | ...             | ...                 |

### Visualize the Phillips Curve

```stata
graph twoway (lfit delta_inflation_hat unemployment) (scatter delta_inflation unemployment), ytitle("Change in Inflation (%)") xtitle("Unemployment rate (%)") legend(label(1 "Regression line") label(2 "Real data point")) title("Static Model")
```

The provided Stata syntax is a command for creating a two-dimensional graph, particularly a scatter plot with an added linear regression line. It incorporates two sets of data, where `delta_inflation` represents the dependent variable, and `unemployment` is the independent variable. The `ytitle` and `xtitle` commands designate the y-axis and x-axis titles, indicating the percentage change in inflation and the unemployment rate, respectively. The legend command adds a legend to the graph, labeling the regression line as "Regression line" and the data points as "Real data point." The overall graph is titled "Static Model." This syntax facilitates the visualization of the relationship between `delta_inflation` and `unemployment` by combining real data points with a fitted regression line for analysis

![phillips curve graph](</images/phillips-curve/static model bps.jpg>)

## Forecasting Inflation

At this point, we thought we needed to level up our game a little bit. So, we did a little more digging and we found out that the Phillips Curve can also be used to forecast future inflation! 🤯

𝚫inflation{% sub %}t{% /sub %} = β{% sub %}0{% /sub %} + β{% sub %}1{% /sub %} 𝚫inflation{% sub %}t-1{% /sub %} + β{% sub %}2{% /sub %} 𝚫inflation{% sub %}t-2{% /sub %} + β{% sub %}3{% /sub %} 𝚫inflation{% sub %}t-3{% /sub %} + β{% sub %}4{% /sub %} 𝚫inflation{% sub %}t-4{% /sub %} + β{% sub %}5{% /sub %} unemployment{% sub %}t-1{% /sub %} + β{% sub %}6{% /sub %} unemployment{% sub %}t-2{% /sub %} + β{% sub %}7{% /sub %} unemployment{% sub %}t-3{% /sub %} + β{% sub %}8{% /sub %} unemployment{% sub %}t-4{% /sub %}

We took this model from Stock & Watson's _Introduction to Econometrics 3rd edition_ book chapter 15. This is called the autoregressive distributed lag model, or ADL in short. The idea is that the change in inflation at year t can be predicted using the lagged values of change in inflation and unemployment level.

```stata
regress delta_inflation L(1/4).delta_inflation L(1/4).unemployment
```

The above command is used to perform linear regression with an autoregressive distributed lag (ADL) model to forecast inflation by accounting for the time distribution effect. The syntax involves `L(1/4)`, indicating that we divide it into 4 quarters, utilizing lag values from 1 to 4 (`L1.delta_inflation`, `L2.delta_inflation`, `L3.delta_inflation`, `L4.delta_inflation`) as predictors.

```txt
      Source |       SS           df       MS      Number of obs   =        25
-------------+----------------------------------   F(8, 16)        =      3.73
       Model |  4.80627802         8  .600784753   Prob > F        =    0.0120
    Residual |  2.57382606        16  .160864129   R-squared       =    0.6512
-------------+----------------------------------   Adj R-squared   =    0.4769
       Total |  7.38010409        24  .307504337   Root MSE        =    .40108

---------------------------------------------------------------------------------
delta_inflation | Coefficient  Std. err.      t    P>|t|     [95% conf. interval]
----------------+----------------------------------------------------------------
delta_inflation |
            L1. |   -.915848   .2533513    -3.61   0.002    -1.452929   -.3787673
            L2. |  -.1359489   .3437331    -0.40   0.698    -.8646304    .5927326
            L3. |  -.0121858   .3717164    -0.03   0.974    -.8001895    .7758178
            L4. |   .0172217   .2760882     0.06   0.951    -.5680591    .6025025
                |
   unemployment |
            L1. |  -.2049554   .2242571    -0.91   0.374    -.6803592    .2704483
            L2. |  -.0946311   .2461721    -0.38   0.706    -.6164927    .4272305
            L3. |   .1977935   .2317352     0.85   0.406    -.2934632    .6890503
            L4. |   .0914296   .2872475     0.32   0.754    -.5175079     .700367
                |
          _cons |   .2019221   .4716677     0.43   0.674    -.7979687    1.201813
---------------------------------------------------------------------------------
```

This syntax is used after creating a regression model to obtain predictions for the values that will occur in the following year.

```stata
predict forecast_delta_inflation
```

We can see the result of this command by running the `br` command.

| inflation | unemployment | time   | delta_inflation | delta_inflation_hat | forecast_delta_inflation |
| --------- | ------------ | ------ | --------------- | ------------------- | ------------------------ |
| -.08      | 4.98         | 2005m2 |                 | .0437433            |                          |
| .12       | 5.23         | 2005m8 | .2              | .0412594            |                          |
| .17       | 5.1          | 2006m2 | .05             | .042551             |                          |
| -.05      | 5.3          | 2006m8 | -.22            | .0405639            |                          |
| .23       | 5.33         | 2007m2 | .28             | .0402659            |                          |
| -.07      | 5.5          | 2007m8 | -.3             | .0385768            | -.1288051                |
| -.09      | 5.5          | 2008m2 | -.02            | .0385768            | .3251108                 |
| -.02      | 5.61         | 2008m8 | .07             | .0374839            | .1449133                 |
| -.36      | 5.81         | 2009m2 | -.34            | .0354968            | .0539226                 |
| .39       | 6.18         | 2009m8 | .75             | .0318207            | .3679268                 |
| .26       | 5.7          | 2010m2 | -.13            | .0365897            | -.6438855                |
| .47       | 5.94         | 2010m8 | .21             | .0342052            | .1334037                 |
| ...       | ...          | ...    | ...             | ...                 | ....                     |

This function is used to create a line plot. We can visualize the predictions from the ADL(4,4) model on changes in inflation to aid in understanding how well the model successfully predicts changes in inflation based on predictor variables and how well these predictions align with actual data.

```stata
line forecast_delta_inflation delta_inflation time, ytitle("Change in Inflation (%)") xtitle("Time") legend(label(1 "Our forecast") label(2 "Change in Inflation")) title("ADL(4,4) Model")
```

![adl model visualization graph](</images/phillips-curve/adl model bps.jpg>)

## Findings

The more we dig into it, the more we realize we don’t know anything. Up to this point, we have no idea why the adjusted R-squared of the static Phillips Curve regression model is negative. We have never seen something like this before, so based on our current understanding, there is no easy way to interpret it.

Another weird thing we found is that when we plot the data from BPS and World Bank side by side, the fluctuations are different, even when we use the same time span. We thought the World Bank collects the data from BPS, but it seems there’s something off here, and we have no clue.

So, we presented our projects to our lecturers with so many questions. One of our lecturers said that the problem with these regressions is that they omit many variables that could influence inflation. He points out that some of the predictions missed too far from the actual value, making it practically unusable for real-world forecasting. He also recommends we try the VAR(p) model and see how it would perform, but it’s quite an advanced topic that one might learn in a higher-level econometrics course.

Meanwhile, the other lecturer, who happens to be a professor, doesn’t bother with the omitted variable problem. However, he questions the quality of our regressions due to a lack of observations. He argues that a time-series model won’t perform very well without at least 60 observations, which is twice the number of samples we have on hand. One of the tricks to get around this, he explains, is to use interpolation to fill the missing data points between the range of our observations. However, it is not a recommended approach since there’s no guarantee that the interpolated data is close to the real values.

We have so many questions, but too little time. We have to finish this project before the final exam, so we just present this to you as it is. Hopefully, you can learn a thing or two from this absolute chaotic experiment. But that’s exactly what we do here as a community! [Student Tech Enthusiast](/) takes a quite radical approach on learning: we build things, documenting the entire process, and releasing it to the internet for everyone to read, replicate, and criticize.

See you next time, cheers! 😉
