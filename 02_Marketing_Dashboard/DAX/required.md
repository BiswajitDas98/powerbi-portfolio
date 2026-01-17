# Used Measures – DAX
- Impressions: How many times Ads was shown today.
- Clicks: How many people clicked the ad.
- Acquisitions/Conversions: How many people filled form / purchased.
- Revenue: Those sales generated money
- CTR (Click Through Rate): Out of the people who saw the ad, how many clicked?
- CPC (Cost Per Click): How much did one click cost me?
- CPA (Cost Per Acquisition): How much did I pay for one customer/lead?
- ROI (Return on Investment): Did I earn more than I spent?

## Pseudo DAX Logic
```DAX
Total Acquisitions = SUM ('CAMPAIGN_DAILY_PERFORMANCE'[Acquisitions])
Total Clicks = SUM ('CAMPAIGN_DAILY_PERFORMANCE'[Clicks])
Acquisition Rate% = DIVIDE ( [Total Acquisitions], [Total Clicks] )
Total Budget = SUM ('CAMPAIGN_BUDGET'[Monthly_Budget])
Total Spends = SUM ('CAMPAIGN_DAILY_PERFORMANCE'[Daily_Spend])
Budget Variance = [Total Budget] - [Total Spends]
Profit = [Total Revenue] - [Total Spends]
Daily CTR% = 
DIVIDE (
    SUM ( 'CAMPAIGN_DAILY_PERFORMANCE'[Clicks] ),
    SUM ( CAMPAIGN_DAILY_PERFORMANCE[Impressions] )
)
ROI% = DIVIDE ( [Profit], [Total Spends]) / 100
CPA = DIVIDE ( [Total Spends], [Total Acquisitions] )
CPC = DIVIDE ([Total Spends], [Total Clicks] )
CPM = DIVIDE ( [Total Spends], [Total Impressions] ) * 1000
CTR% = DIVIDE ( [Total Clicks], [Total Impressions] )
