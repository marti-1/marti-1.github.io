---
layout: post
title: How Good are Earnings Forecasts?
---

Earnings forecasts made by analysts play a significant role in the financial world, as they provide investors and stakeholders with information about a company's expected performance. These forecasts are often used to inform investment decisions and can have a significant impact on a company's stock price. Almost all of the stock valuation models incorporate them. However, nobody has a crystal ball, so we turn to the next best thing - oracles, aka analysts.

Since these forecasts play such an important part, I wonder how accurate they tend to be. The following is a meta-study of various scientific papers that have explored this question.

## Bullish bias

We can start with De Bondt et al. (1990) who took yearly forecasts from 1976-1984. They note that the actual earnings are 65% of what was predicted at the start of the year, and they drop to 46% for the 2 year horizon. Loh et al. (2003) similarly found that actual earnings represent only 64% of the one year forecasts in their study of earnings forecasts during the Asian crisis. They also split forecasts into pre and post-crisis and ran a regression ($$AC_t = \beta FC_t + \epsilon_t$$) to see how much the actual earnings change was represented by the forecasted change. The resulting $$\beta$$ was 0.98 during normal times and 0.028 during the crisis. So the **analysts got the actual change on the money during normal times, and completely missed during the crisis period**. Loh et al. (2003) also showed that during the analyzed period from 1990 to 1999, 7 out of 10 years yearly forecasts were positively biased, mostly during the crisis years:

<img src="/assets/img/how_accurate_earnings_forecasts/forecast_error_by_year_loh_2013.png" width="400">

We saw how forecasters did during the Asian crisis, but maybe things improved afterwards? Hutira (2016) analyzed earnings forecasts for the U.S. based companies for the 2000-2013 period using various forecasting horizons. For every horizon he plotted an average forecasting error (see the chart below). Again, we can observe that during the crisis errors spike and have to be revised downwards the most:

<img src="/assets/img/how_accurate_earnings_forecasts/average_forecast_error_by_horizon_hutira_2016.png" width="500">

Sidhu et al. (2011) have studied analysts' forecasts starting with the dot-com bubble bottom in 2003 all the way to the start of the 2008 crash. What they have found was that both for quarterly and yearly forecasts analysts grew increasingly more bullish as the bubble grew. The chart below shows how the buy signal grew and the sell signal shrank even as the bubble started to pop.

<img src="/assets/img/how_accurate_earnings_forecasts/buy_sell_recommendations_sidhu_2011.png" width="400">

The asymmetry of buy and sell signal proportions is also noteworthy. The percentage of analysts suggesting to “buy” was in the 40-60% range throughout the bull run, whilst the “sell” camp stayed around 10%.

## No better than weigthed past episodes

It is my long time suspicion that quantitatively the best tool humanity has when it comes to predicting the future is a linear combination of past episodes. In addition to that, Daniel Kahneman and Amos Tversky (1973) found that people tend to overweight the most recent episode and underweight long-termm averages.

Whilst it is true that for short term horizons (< quarter), analysts tend to be more accurate than simple time-series models, the results become much more interesting as we try to test for larger horizons. For instance, Bradshaw et al. (2012) compared a simple random walk model ($$E_T(\text{EPS}_{T+\tau}) = EPS_T \in \tau = \{1,2,3\}$$) for 1,2 and 3 year horizons with analysts forecasts. They found that for all three time horizons the absolute error is identical. The only thing that is different is the bias: analysts are overoptimistic, random walk model overpessimistic:

<img src="/assets/img/how_accurate_earnings_forecasts/descriptive_statistics_bradshaw_2012.png" width="500">

So a simple lag function is just as good as professional analysts once we try to expand our horizon to a one year mark and beyond.

It that for yearly and further horizons we can live without the analysts, but what about smaller time horizons, how about a quarter? Lorek et al. (2014) found that an [ARIMA](https://en.wikipedia.org/wiki/Autoregressive_integrated_moving_average) model makes forecasts that are just or more accurate than those made by analysts **39.4%** of the time. The accuracy increases if one goes from one to three quarters.


## Conclusion

To summarize, it seems that analysts can not beat simple time-series models for time horizons larger than a quarter. Their half-year and yearly forecasts tend to have largest errors during market crashes. However, as depicted by Hutira (2016), forecasts with shorter than a quarter horizon tend to be quite accurate.

## Reference

* De Bondt, W. F., & Thaler, R. H. (1990). Do security analysts overreact?. The American economic review, 52-57.
* Loh, R. K., & Mian, M. (2003). The quality of analysts’ earnings forecasts during the Asian crisis: evidence from Singapore. Journal of Business Finance & Accounting, 30(5‐6), 749-770.
* Sidhu, B., & Tan, H. C. (2011). The performance of equity analysts during the global financial crisis. Australian Accounting Review, 21(1), 32-43.
* Lorek, S. K., & Pagach, D. P. (2014). Analysts versus time-series forecasts of quarterly earnings: A maintained hypothesis revisited. Recuperado de https://ssrn. com/abstract, 2406013.
* Bradshaw, M. T., Drake, M. S., Myers, J. N., & Myers, L. A. (2012). A re-examination of analysts’ superiority over time-series forecasts of annual earnings. Review of Accounting Studies, 17(4), 944-968.
* Hutira, S. (2016). Determinants of analyst forecasting accuracy.
* Kahneman, D., & Tversky, A. (1973). On the psychology of prediction. Psychological review, 80(4), 237.
