# CO EV Model
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/bgalecki/CO-EV-Model/master?filepath=CO%20EV%20Model.ipynb)

Click the badge above to launch the model in an interactive virtual machine. This might take a minute to load. 

Using simulated EV load data from NREL's EVI-Pro model, this tool visualizes various scenarios of EV adoption and charging to forecast the impact on overall CSU system demand. This paper will briefly explain the methodology and purpose of the tool. 

EVI-Pro uses GPS trip data from 32.9 million trips in the Columbus, Ohio region (the majority of which are from non-EVs), complemented with public transit data from California and Massachusetts. This data is used to provide a bottom-up simulation of the habits and patterns of drivers to estimate the time, type, volume, and location of EV charging that would be needed to support a higher penetration of electric vehicles. 

The number of EVs is the first parameter in generating an EV load profile. The methodology used by this model is best suited for estimating medium-term adoption of EVs. Coinciding with this time period is the Colorado Energy Office’s EV Market Study (2015). This study estimated various scenarios of EV adoption in Colorado by 2030 with low (38,056), medium (302,429) and high (940,000) sensitivities. These volumes serve well as inputs for this methodology, with the medium scenario acting as the default scenario. 

Three charging behaviors were modeled natively within EVI-Pro (Percent No Delay, Percent Max Delay, and Percent Min Power), and two were imputed for the purposes of estimating the impact on PSCo’s system load (Percent TOU and Percent Shiftable). 
Percent No Delay represents the proportion of EV owners who do not coordinate their charging. This likely involves immediately plugging into charge when arriving at home in addition to utilizing uncoordinated workplace charging systems, public chargers, and fast chargers. This can be thought of as the default charging behavior of most EV owners.
Percent Max Delay represents the proportion of EV owners who input a future time when they will require their EV to be fully charged. This capability is programmable in some EVs and EVSEs. The charger then self delays to begin charging at the point at which a full charge can be delivered just in time. This behaviour is similar to a TOU rate, however it assumes that users will always delay their charging to the maximum extent. This is not a realistic charging behavior to expect without providing customers an incentive, but it offers a useful heuristic when estimating TOU and shiftable charging behaviours. This parameter assumes that the proportion of EV owners defined by the input percentage always delay their charging when feasible (i.e. home, workplace, some public charging) but not in other situations (i.e. DCFC).

*Percent Min Power* represents the proportion of EV owners who charge at the lowest power setting available on chargers. EVSEs can allow the user to reduce the charger's output wattage. This is useful in some cases for prolonging the battery life (i.e. excessive DCFC can degrade the batteries of fleet vehicles), or to avoid demand charges during certain business hours. From a power planning perspective, charging at a minimum rate––particularly overnight––can help avoid ramps. This is not a realistic charging behavior to expect without providing customers an incentive, but it offers a useful heuristic when estimating TOU and shiftable charging behaviours. This parameter assumes that the proportion of EV owners defined by the input percentage always reduce their charging to minimum levels when feasible (i.e. home, workplace, some public charging) but not in other situations (i.e. DCFC).

*Percent TOU* represents the proportion of EV owners that subscribe to time of use rates which incentivize them to charge between the off-peak hours of 9PM to 9AM. Some EVs with programmable systems, specialized EVSEs, or manual operation can delay charging, however EV owners receive no additional benefit for staggering their charging during the off-peak period and so they are likely to begin charging as soon as the period begins. This can lead to a significant ramp at 9PM if the TOU period has a large number of participants. This parameter assumes that the proportion of EV owners defined by the input percentage are subscribed to the TOU rate, they will only charge at home (i.e. they will not use public or workplace chargers), and they will only charge during the TOU period.

*Percent Shiftable* represents the proportion of EV owners who opt-in to allow the utility to control their load when charging. Historical system lambda data––the marginal cost of generation for the balancing authority–– is used to determine which times it would be most economic to shift load to. Load is shifted to hours that have the lowest system lambda costs, while also ensuring that ramping is avoided, and that total load during a given hour will not surpass the mean hourly load. Other strategies could be used by the balancing authority to determine the procedure for shifting load, such as minute-to-minute variations based on real-time wind generation. For this purpose, a figure is created by the tool to display modeled wind data from the location of the Rush Creek wind project to visualize how the selected EV charging behaviour compares with estimated wind output. This parameter assumes that the proportion of EV owners defined by the input percentage will only charge at home (i.e. they will not use public or workplace chargers) and they will allow the utility to schedule all of their home charging (overnight, with a guarantee that they will be fully charged by 7 AM).

In addition to these five charging methods, the driving and charging behavior of EV owners is dependent on the day of the week. Weekdays exhibit ‘peakier’ charging characteristics as EV owners return home from work or school and begin charging. Weekends see lower charging volume with less concentration during the evening. The tool allows the user to select between weekday only, weekend only, and a proportional blend (70% weekday, 30% weekend) in generating EV load profiles. 

Once the user has input valid inputs for all parameters the input percentage of each charging strategy and the number of EVs are multiplied by a vector containing EV load with fifteen-minute granularity for both weekday and weekends. The sum of all EV load is then calculated.

To calculate overall system load, data from the 5 highest demand days for CSU's system in summer 2018 were averaged. Because EVs are often a flexible load––that is to say that EV owners are regularly capable of leaving their cars to charge overnight, and less often require on-demand power––it is prudent to consider how to offset their charging so that it does not coincide with the overall system peak thus requiring additional generation, transmission, and distribution investment. 

While system load is useful for examining the periods when EVs should not be charging, system lambda is useful for considering the periods when it is most economic for EVs to be charging. System lambda––the real-time marginal cost of generation for PSCo––is also reported in FERC 714. For the Percent Shiftable charging strategy, hours with the lowest system lambda are chosen to shift EV load to. System lambda is pushed down by low system load (which allows for expensive peaker power plants to not be utilized) and high zero-marginal cost generation, such as that provided by solar PV and wind generators. Because the volume of wind capacity in PSCo territory is much higher than that of solar PV, periods with low system-lambda values are likely to coincide with periods of high wind production. System lambda values are also useful for defining TOU periods in order to incentivize customers avoid using electricity when it is most expensive to generate. To provide this information, the mean system lambda was calculated. Vertical dashed lines used throughout the tool indicate the time at which the system lambda value crosses its own mean.

The tool then provides five customized visualizations displaying how an average day will look given the EV load presented by the user defined scenario. 

1. Average system load with the new EV load added by location of charging (Home L1, Home L2, Work L1, Work L2, Public L2, DCFC) 
2. The new EV load by location of charging absent the existing system load
3. EV load by charging behavior (no delay, max delay, min power, TOU, shiftable)
4. EV load as a contribution to overall system load, measured as a percent
5. PSCo system lambda ($/MWh) and an arbitrary volume of wind using modeled data from two measurement sites located close to the Rush Creek wind site. 
