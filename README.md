# RDD
1. Github repo: https://github.com/xiaohan-sun/RDD


2. In this paper, the author tests the effect of harsher punishments and sanctions on driving under the influence (DUI). For the data, he used the administrative records on 512,964 DUI stops from the state of Washington (WA) to determine the thresholds that determine both the current as well as potential future punishments for drunk drivers. To specific, in WA BAC measured above 0.08 is considered a DUI while a BAC above 0.15 is considered an aggravated DUI. In the paper, regression discontinuity approach is used to deliver consistent estimates. And the results suggest that the additional sanctions experienced by drunk drivers at BAC thresholds are effective in reducing repeat drunk driving. 
    
	quietly clear
	quietly cd ~/Desktop/CI_RDD
	quietly use hansen_dwi, clear


3. Create a dummy equaling 1 if bac1>= 0.08 and 0 otherwise in your do file or R file.

	quietly gen d = 2
	quietly replace d = 1 if bac1>= 0.08
	quietly replace d = 0 if bac1< 0.08


4. If people were capable of manipulating their blood alcohol content (bac1), describe the test we would use to check for this. 
We can draw a histogram of the distribution of BAC to see whether people manipulate their blood alcohol content.

    quietly hist bac1, freq discrete
    quietly graph export hist_bac.png

![Figure 1. BAC Distribution](hist_bac.png){width=100%}

From the graph we can see that the data of bac1 is normally distributed, which means that there is no evidence for sorting on the running variable. Also this result is smae as Hansen found.


5. Are the covariates balanced at the cutoff?

    quietly eststo clear
    quietly eststo: rdrobust white bac1, c(0.08) h(0.05) kernel(uniform)
    quietly eststo: rdrobust male bac1, c(0.08) h(0.05) kernel(uniform)
    quietly eststo: rdrobust aged bac1, c(0.08) h(0.05) kernel(uniform)
    quietly eststo: rdrobust acc bac1, c(0.08) h(0.05) kernel(uniform)
    esttab,se title(Table 2—Regression Discontinuity Estimates for the Effect of Exceeding BAC Thresholds on Predetermined Characteristics)

The table shows that the number of the obs is same, so the covariate is balance.


6. Fit both linear and quadratic with confidence intervals。

    quietly cmogram acc bac1 if bac1<=0.2,title(Panel A. Accident at scene - linear CI) scatter cutpoint(0.08) lineat(0.08) lfitci
    quietly graph export Al.png

![](Al.png)

    quietly cmogram acc bac1 if bac1<=0.2,title(Panel A. Accident at scene - quadratic CI) scatter cutpoint(0.08) lineat(0.08) qfitci
    graph export Aq.png

![](Aq.png)

    quietly cmogram male bac1 if bac1<=0.2,title(Panel B. Male - linear CI) scatter cutpoint(0.08) lineat(0.08) lfitci
    graph export Bl.png

![](Bl.png)

    quietly cmogram male bac1 if bac1<=0.2,title(Panel B. Male - quadratic CI) scatter cutpoint(0.08) lineat(0.08) qfitci
    graph export Bq.png

![](Bq.png)

    quietly cmogram aged bac1 if bac1<=0.2,title(Panel C. Age - linear CI) scatter cutpoint(0.08) lineat(0.08) lfitci
    graph export Cl.png

![](Cl.png)    

    quietly cmogram aged bac1 if bac1<=0.2,title(Panel C. Age - quadratic CI) scatter cutpoint(0.08) lineat(0.08) qfitci
    graph export Cq.png

![](Cq.png)

    quietly cmogram white bac1 if bac1<=0.2,title(Panel D. White - linear CI) scatter cutpoint(0.08) lineat(0.08) lfitci
    graph export Dl.png

![](Dl.png)  

    quietly cmogram white bac1 if bac1<=0.2,title(Panel D. White - quadratic CI) scatter cutpoint(0.08) lineat(0.08) qfitci
    graph export Dq.png

![](Dq.png)

From the graphs above, we find that our results are similar to those in Hansen's finding. In detail, in the panel A, the beginning of the trend in the BAC<0.08 is higher than that in the paper. In the panel B, there is a little difference between our result and Hansen’s result, the trend experiences a increase in our graph, but decreases in the paper(when BAC<0.08). 


7. Estimate equation (1) with recidivism (recid) as the outcome. 

    quietly eststo clear
    quietly eststo:reg recidivism bac1 male white acc aged if bac1 > 0.03 & bac1 < 0.13,robust
    quietly eststo:rdrobust recidivism bac1, c(0.08) h(0.03 0.13) kernel(uniform) covs(male white acc aged) p(1) 
    quietly eststo:rdrobust recidivism bac1, c(0.08) h(0.03 0.13) kernel(uniform) covs(male white acc aged) p(2)
    esttab,keep(bac1 RD_Estimate) se title(Table 2—Regression Discontinuity Estimates for the Effect of Exceeding BAC Thresholds on Predetermined Characteristics)


8. Recreate the top panel of Figure 3

    quietly cmogram recidivism bac1 if bac1<=0.15,title(Panel A. All offenders) scatter cutpoint(0.08) lfit lineat(0.08)
    graph export ol.png

![](ol.png)

    quietly cmogram recidivism bac1 if bac1<=0.15,title(Panel A. All offenders) scatter cutpoint(0.08) qfit lineat(0.08)
    graph export oq.png

![](oq.png)


9. In this replication, I learned how to use RDD method to estimate the effect of a policy, which reminds me the DID method we have learned in econometrics class. Maybe RDD method focus on the effect around the threshold, but the DID method focus on the difference between the test group and control group. At first, we test the randomness of BAC, which show that there is little evidence of manipulation. From the graphs and tables we replicate, we notice that the result of us is pretty similar to the results that draw from Hansen, which confident me the Hansen’s original conclusion.     
