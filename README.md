# Earnings_Manipulation_Analytics
## Objective: 
Applying financial methods such as benford's law, Beneish Earnings Manipulation Detection model, accruals model and operation cashflow prediction to detect the earnings manipulation behaviors in corporates.
           
## Dataset: 
From Compustat, a database of financial, statistical and market information on active and inactive global companies, spanning from year 1988–2017

## Tools&Packages: 
R (dplyr, ggplot2, bendford.analysis, plm, data.table etc)

## Data Cleaning Process:  
### (1)Taking the following variables out of 1000+ variables:   
	gvkey (company identifier),   
	datadate,   
	fyear (fiscal year),   
	revt(total revenue),   
	rect(Receivables Total),   
	ppegt(Property, Plant and Equipment - Total (Gross)),   
	Epspi(Earnings Per Share (Basic) Including Extraordinary Items),  
	Ni(Net Income (Loss)),   
	At(total asset),  
	Oancf(Operating Activities Net Cash Flow),   
	Sic(Standard Industry Classification Code),  
	Rdq(firm's earnings announcement date)  

### (2) Restrict sample to firms with fiscal years 1988 to 2017 inclusive.  
### (3) Drop any observation with missing assets, revenue, net income, EPS, account receivables, and operating cash flow.  
### (4) If PPE is missing, set it to zero.  
### (5) Drop any observation where revenue and account receivables are negative.  
### (6) Drop duplicate observation based on all columns except the column rdq.  

*My final dataset have approximately 248,289 observations.   
*I'll upload cleaned data to save the time and you can refer to my file "Data_Cleaning.Rmd" for detailed commands

## Analysis:  
### 1. Descriptive Findings:   
	i. average total asset increase drastically compared to revenue and net income  
	ii. Firm-reported YoY EPS change is more  asymmetric than ROA, suggesting a stronger incentive to manage the number of shares 	outstanding (EPS denominator) by issuing more shares or repurchasing them back.  
### 2. Methods Findings:  
#### 1). Benford's Law (first-digit law):   
	1) Comparing the distribution of first-digit in actual data and benford value, if there are huge deviation, it might be a sign of earning management.  
	2) Calculating the residual to benford value and compare it with industry standard to find evidence of earning management  
#### 2). Accrual Model:  
##### a. Discretionary Accruals Model  
	Findings:   
	□ the absolute value of residuals is called Unsigned Discretionary Accrual which is an evidence of earning management. Since firms whose earning is more deviated from the industry standard, has higher possibility of manipulation.  
	
	□ Model: Accruals_it = β_0 + β_1Cash Revenue Growth_it + β2Gross PPE_it + ψSIC_i + ε_it  
	□ Accrual = net income - operating cash flow  
	□ cash revenue growth = ∆ revenue - ∆ rect  
	□ all variables above (except SIC) is scaled by prior year’s total assets which means the OLS model is trying to test the predictive power of previous performance metrics on the this-year accruals.  
	□ SIC is the industry classifier, which means that for different industries, there might be different groups and levels of estimates that might grant different accruals.  
	
##### b. Unsigned Discretionary Accrual Model  
	Findings:  
	□ Firms that tend to earnings manage also tend to delay their financial reporting.   
	□ If there are statistical significant correlation between delay and unsigned accruals it means the same firm appears to be delaying when they simultaneously have more accruals (either positive or negative). One interpretation is that they are using the extra time to manage their accounting reports.  
	□ Model: Delay_it = β_0 + β_1Unsigned Discretionary Accrual_it + ψFirm_i + ε_it  
				
##### c. Operation Cashflow Prediction Model  
	□ Model:  operating cash flows_{i,t+1} = β_{0} + β_{1accrualsi},t + β_{2operating cash flows_{i,t} + ε_{i,t+1}  
	□ Findings: Interpretation of Beta one:   
		® If β1 = 0 then the level of accruals are completely uncorrelated with future cash flows ceteris paraibus. This could be true if accruals are complete noise and do not reflect fundamental performance and/or fundamental performance is iid across time.   
		® If positive or negative and significant, then there is some predictability in the accruals value.   
		® If positive, then it implies that greater accruals is associated with greater cash flows in the future. This could be true if   
			◊ a) accruals correctly capture performance that will materialize in cash flows in the future or   
			◊ b) accruals correctly capture contemporaneous performance and firm performance is non-iid (firms that do well this period tend to do well in the future period).   
		® If negative, then it implies that greater accruals is negatively associated with future cash flows. This could be true if poor performance firms are managing earnings (hence greater accruals). This story also requires the assumption that firms’ performance is persistent over time, hence poor performance today implies poor performance in the future but also induce earnings management (greater accruals) today, which leads to the predictability signal.   
			


