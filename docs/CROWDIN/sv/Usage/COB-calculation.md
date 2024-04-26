# COB calculation

## How does AndroidAPS calculate the COB value?

### Oref0 / Oref1

Unabsorbed carbs are cut off after specified time

![Oref0 / Oref1](../images/cob_oref0_orange.png)

### AAPS, WeightedAverage

absorption is calculated to have `COB == 0` after specified time

![AAPS, WheitedAverage](../images/cob_aaps2_orange.png)

If minimal carbs absorption (min_5m_carbimpact) is used instead of value calculated from BG deviations, an orange dot appears on COB graph.

## Detection of wrong COB values

As of version 2.4, AAPS warns you if you are about to bolus with COB from a previous meal and the algorithm thinks that current COB calculation could be wrong. In this case it will give you an additional hint on the confirmation screen after usage of bolus wizard.

### How does AndroidAPS detect wrong COB values?

Normally AAPS detects carb absorption through BG deviations. In case you entered carbs but AAPS cannot see their estimated absorption through BG deviations, it will use the [min_5m_carbimpact](../Configuration/Config-Builder.md?highlight=min_5m_carbimpact#absorption-settings) method to calculate the absorption instead (so called 'fallback'). As this method calculates only the minimal carb absorption without considering BG deviations, it might lead to incorrect COB values.

![Hint on wrong COB value](../images/Calculator_SlowCarbAbsorbtion.png)

In the screenshot above, 41% of1 time the carb absorption was mathematically calculated by the min_5m_carbimpact instead of the value  detected from deviations.  This means that maybe you are having less carbs on board than calculated by the algorithm.

### How to deal with this warning?

- Consider to cancel the treatment - press Cancel instead of OK.
- Calculate your upcoming meal again with bolus wizard leaving COB unticked.
- In case you are sure you need a correction bolus, enter it manually.
- In any case be careful not to overdose!

### Why does the algorithm not detect COB correctly?

- Maybe you overestimated carbs when entering them.
- Activity / exercise after your previous meal
- I:C needs adjustment
- Value for min_5m_carbimpact is wrong (recommended is 8 with SMB, 3 with AMA)

## Manual correction of carbs entered

If you over- or underestimated carbs you can correct this though treatments tab and care portal as described [here](../Getting-Started/Screenshots#carb-correction).
