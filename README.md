# Financial Statements Analysis and Interpretation (Pershing Square Holdings Limited and Abrdn Plc)

## Table of Contents
- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning and Preparation](#data-cleaning-and-preparation)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results and Findings](#results-and-findings)
- [Recommendation](#recommendation)
- [Reference](#reference)

### Project Overview
There are many uses and users of company’s financial statements. Majority of the users lack understanding of the financial statements as contained in the company’s annual report. Hence, negative outcomes such as poor decision-making, ineffective financial planning, increased fraud, limited access to capital, regulatory compliance issues and stock market crises are worsened. Companies dress up their financial reports to only highlight what they want the public to see.  So, a mere look at the audited financial report of any company is inadequate for accurate business decision making. For example, there are reportedly approximately $620 billion of mark-to-market losses on banks’ security portfolios that are not currently reflected on bank’s financial statements.
This project aims to; 
1.	analyse and critique the financial statements of Pershing Square Holdings Ltd (PSH) and Abrdn Plc for the 2021 and 2022 financial years in the context of business decision-making,
2.	accurately present and communicate the analysis results to proffer appropriate solutions for management decision problems, and
3.	critically evaluate, apply and communicate data analytical solutions for decision making to practical financial and business case applications. 
### Data Sources
The analysis is based on audited financial statements: statement of financial position (balance sheet), statement of comprehensive income (profit and loss statement), statement of cashflow and market data (share values) for year 2021 and 2022 of Pershing Square Holdings Limited and Abrdn Plc respectively. The financial statements were obtained from both FAME [download here](https://fame-r1-bvdinfo-com.plymouth.idm.oclc.org/version-20230918-3-0/fame/1/Companies/Report) and the companies’ websites. The share prices were obtained from Yahoo Finance using the "getSymbols.yahoo" function in R.
### Tools
- Microsoft Excel - used for data preparation and normalization
- Microsoft Word - used to present the report
- R/Rstudio - used for data extraction, analysis and visualisation using the following packages: plyr, dplyr, XBRL, finreportr, devtools, ggplot2, cowplot, quantmod, Rmisc, gridExtra, finstr

### Data Cleaning and Preparation
- The extracted financial report was aggregated using Excel
- The aggregated spread sheet was loaded to R/Rstudio for validation and accuracy
### Exploratory Data Analysis
**1.** **Pershing Square Holdings Limited (PSH)**

From the summarised balance sheet and income statements presented below, PSH’s total asset reduced from $15,808.37bn in 2021 to $12,951.81bn in 2022. Its revenue declined from $3,251.14bn in 2021 to ($968.97m) in 2022, from gain of $2,436.31bn to a loss of $1,172.08bn. However, PSH trailing 12-month (TTM) market capitalization, revenue and net income are $8.12bn, $3.10bn and $2.76bn respectively making PSH the fourth largest investment company among other seven peers.

<img width="334" alt="PSH Balance sheet" src="https://github.com/user-attachments/assets/7225047b-e400-472a-a1bf-aaad71f2ab14" />

<img width="307" alt="PSH Income statement" src="https://github.com/user-attachments/assets/80e59ca9-eb01-4430-89cb-7e7ea40e319f" />

**2.** **Abrdn Plc (ABDN)**
As at June 30, 2023, its AUM was £496bn, total assets of £9,247bn y-end 2022, reduced revenue of £1,538bn as against £1,685bn in 2021, from a gain of £994m to a loss of £550m. However, ABRDN’s trailing 12-month (TTM) market capitalization, revenue and net income were £3.17bn, £1.57bn and (£406.00m) respectively. It’s the fourth largest investment company among its nine peers.
<img width="413" alt="ABRDN Balance sheet" src="https://github.com/user-attachments/assets/26ca67c6-eb6e-4616-a083-d8d3f150c0d5" />
<img width="412" alt="ABRDN Income state" src="https://github.com/user-attachments/assets/587c215d-2d51-48df-ab39-1b0886fb843b" />
### Data Analysis
We performed vertical analysis (common-size statement), horizontal analysis (comparative statement), ratio analysis and Capital Asset Pricing Model (CAPM).

**1.** **Vertical and Horizontal analysis of Balance Sheet and Income statement**
```R
PSH.bs = make.statement(template='bs_template.csv', skeleton="bs_skeleton.R", digits = 2) #balance sheet
PSH.is = make.statement(template='is_template.csv', skeleton="is_skeleton.R", digits = 2) #Income Statement
#check statements
check_statement(PSH.bs)
check_statement(PSH.is)
PSH.bss <- simplify.bs(bsf=PSH.bs) #simplify the balance sheet
# plot the two statements
plot.bsf(bsf=PSH.bss, prop = F, psave = F)
plot.isf(isf=PSH.is, dates=c(1,2), prop = F, psave = F)
## horizontal and vertical analyses of balance sheet (Statement of Financial Position)
horizonal.analysis(fs=PSH.bs, type='bs', units = 1000000, export = TRUE)
vertical.analysis(fs=PSH.bs, type = 'bs', total = NULL, export = TRUE)
## horizontal and vertical analyses of income statement
horizonal.analysis(fs=PSH.is, type='is', units = 1000000, export = TRUE)
vertical.analysis(fs=PSH.is, type = 'is', total = NULL, export = TRUE)
```
<img width="388" alt="vertical analysis" src="https://github.com/user-attachments/assets/e99fd3fa-3f56-4ff7-aceb-1dd79c860cd8" />
<img width="412" alt="Horizontal analysis" src="https://github.com/user-attachments/assets/1e00e8a7-9c2f-4893-b332-2ba7946309eb" />


**2.** **Ratio Analysis of Key Financial Metrics**
```R
PSH.statements = merge(PSH.bs, PSH.is)  
PSH.ratios <- fin.ratios(fstats=PSH.statements, ratios=c(profit.margins, bsf.ratios, isf.ratios))
```
<img width="284" alt="Ratio analysis" src="https://github.com/user-attachments/assets/c65978cd-3fcc-47d2-a90e-997d40b18de7" />


**3.** **Analysis of market data using Capital Asset Pricing Model (CAPM)**
```R
# list symbols of the two companies and other related companies 
uk.symbols <- c('III.L', 'ABDN.L', 'FCIT.L', 'PSH.L', 'HVPE.L', 'JGGI.L', 'AV.L', 'MNG.L', 'CCH.L', 'UU.L', 'LGEN.L', 'HL.L', 'RR.L', 'SDR.L', 'HGT.L', 'REL.L', 'JUP.L', 'HMCX.L', 'SMT.L', 'HUKX.L')
# download stock price from Yahoo finance
tmp.uk <- new.env()
getSymbols.yahoo(uk.symbols, env = tmp.uk, from = '2019-01-02')
ls(tmp.uk) # return the symbols of downloaded data 
# capm analysis
capm.uk = capm.fit(x=tmp.uk, mkt='HUKX.L')
names(capm.uk)
capm.uk$capm
```
<img width="482" alt="capm analysis" src="https://github.com/user-attachments/assets/03fd2f99-a8e8-46ed-9a49-c2681bf4a2f1" />
<img width="230" alt="capm table" src="https://github.com/user-attachments/assets/266efb53-7a03-4479-9d34-0abd9457bb30" />

### Results and Findings
1. ABRDN cost of goods sold decreased by 42.3% in 2022, an indication that ABRDN has undertaken cost-cutting measures, improved supplier terms, and increased operational efficiency. This might be a good sign, reflecting the company's capacity to properly manage costs. Though there was a significant drop in cost of production for both companies, the net profit wasn’t positively impacted as the net profit for PSH and ABRDN decreased by 148.1% and 155.3% respectively, attributable to low revenue.
2. Year 2022 was not particularly a bullish year for both PSH and ABRDN as all profitability metrics had a downturn resulting in loss. A negative gross and net profit margins as a proportion of revenue imply poor profitability and inefficiency in operations.
3. Total assets of PSH decreased by 18.7% in 2022 while that of ABRDN PLC also decreased by 19.0%. A look at the quick and current ratio shows an increase to 1.57 and 2.26 in 2022 for PSH then 0.82 and 1.52 for ABRDN both signifying an increase. The positive quick ratio demonstrates the company's ability to satisfy immediate financial obligations without relying largely on inventory. The positive quick and current ratios indicate that the companies have high liquidity which means ability to cover short-term obligations as they fall due.
4. The decline in total assets as shown in the horizontal analysis, on the other hand, indicates significant worries about the company's general financial health or a shift in asset composition, prompting a detailed investigation into the reasons for the decrease.
5. Again, both companies experienced an increase in trade receivables in 2022 which already implies that there may be a delay in collecting payments from customers, potentially leading to a cash flow challenge. It may also indicate a need for more rigorous credit risk assessment or enhanced collection strategies to ensure timely cash inflows in both companies. Furthermore, an increase in trade receivables may indicate increasing sales on credit, which may boost revenue but necessitates careful management to avoid the related cash flow risks.
6. The analysis further reveals diminishing balance of non-current tangible assets, that shows either asset write-downs, sales or disposals, a lack of reinvestment, or value deterioration. ABRDN long-term debt increased by 7% suggesting that the company borrowed more money to fund expansion, capital projects and meet operational needs.

The overall performmance position of both companies is shown in the table below;
<img width="366" alt="Performance indices" src="https://github.com/user-attachments/assets/0c37e525-0c87-49cc-b148-99fb515ae2e9" />

### Recommendation
In view of the foregoing, we recommend that in general, interested stakeholders in the financial statements of PSH Limited and ABRDN Plc should take key interest in observing the companies’ performances in 2023 before making any business decisions.  

Specifically, for the purpose of investment, Pershing Square Holdings Limited is preferred considering its average positive returns on stock (see fig 5).  

Management of PSH should consider restructuring its asset classification by holding a good percentage of its total asset in current asset. This would ensure enough available fund to take on business opportunities for bigger revenue generation. 

ABRDN should review its account receivable collections. Collection days above 30 days is out of standard and could bring about uncontrollable bad debts with a negative effect on liquidity. 

Creditors should be comfortable lending to the companies as they both have the capacity to borrow more money for business purpose and repay as and when due without any impairment.

### Limitation
This analysis was based solely on the companies' published financial statements and market data.

### Reference
1. Hanif, M., & Mukherjee, A. (2020). Accountancy: Analysis of financial statements: analysis of financial statements. New Central Book Agency. Created from Plymouth on 2023-11-09 15:00:40
2. Holmes, G. Andrew, Gee, P., & Sugden, A. (2008). Interpreting company reports and accounts. 10th ed. Harlow, England: Financial Times Prentice Hall.
3. McGowan, C., Gardner, J. C., & Moeller, S. E. (2014). The fundamentals of financial statement analysis as applied to the coca-cola company. Business Expert Press. Created from Plymouth on 2023-11-10 21:06:42
4. Stickney, C. P, Brown, P. R., & Wahlen, J. M. (2007). Financial reporting, financial statement analysis, and valuation: a strategic perspective. 6th ed. Mason, OH: Thomson/South-Western.
5. Walton, P. J. (2000). Financial statement analysis: an international perspective. London: Business press.
6. [FAME](https://fame-r1-bvdinfo-com.plymouth.idm.oclc.org/version-20230918-3-0/fame/1/Companies/Report)
7. [HBR-CAPM](https://hbr.org/1982/01/does-the-capital-asset-pricing-model-work) 

 


