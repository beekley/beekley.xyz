Failed is a strong word and possibly inappropriate here. "Unproven, yet," may be better.

There's a lot of talk in CA about housing and about making zoning changes to address its high expense and low availability. It can sometimes feel like the discussion focuses on [current renters and hopeful first-time buyers](https://cayimby.org/) supporting upzoning, while [existing single-family homeowners](https://www.lewis.ucla.edu/opposition-to-new-housing/) oppose and would be harmed by it. I fall into the former category, but believe that there is a financial benefit from upzoning to existing homeowners that isn't talked about much. My hypothesis:

Increasing the options of what can be built on a property (e.g. through relaxed zoning) increases the property's value.

It feels intuitive. If you have two identical assets, but you are legally allowed to do an additional thing with one of them, the "free-er" one is more valuable. I first tested this hypothesis by comparing the values of the apartment property I lived in and the single-family home next door. This should control variables: both were corner properties of similar-ish size on the same block of the same neighborhood. One was zoned R3 (multi-unit) and the other R1 (single-family residence).

From Redfin (lightly rounded):

| Property | Parcel Size (sqft) | Value (million $) | Value ($/sqft)| Value, without building ($/sqft)|
|----------|--------------------|-------------------|---------------|---------------------------------|
| R3       | 11750              | 11.0              |  936          | 527                             |
| R1       | 7150               | 2.8               |  391          | 289                             |

Each square foot of lot of the R3 property is worth 2.4x the R1 property next door. However, this ignores the value/cost of the building itself, [which may be around $200/sqft in LA](https://www.biggerpockets.com/forums/24/topics/160796-new-construction-costs-for-apartment-buildings-in-la-county) (note: this is a strong assumption, but I feel it favors the R1 property, which is 15 years newer and subjectively "nicer"-- therefore likely more expensive per sqft). When we subtract the cost to build the building, the increase becomes about 1.8x. Still a huge gain just for being zoned higher. 

Now, to apply the same principle to a larger data set. The [LA County Assessor provides data](https://data.lacounty.gov/api/views/) on every parcel of land in LA. I grabbed the Santa Monica data set (`Assessor_Parcels_Data_-_2015`) since it was a manageable ~24k rows, and loaded it into mysql. It didn't actually contain parcel size and the assessed values were form 2015, so I wrote [a small script](https://github.com/beekley/sandbox/blob/master/zone-values/index_2.js) to get the parcel details in batches from the assessor's API. The data was filtered for residential properties (`GeneralUseType = 'Residential'`) and non-condos (`PropertyType != 'CND'`). In the end, there were about 7321 single-family residences (`Units = 1`) and 4048 multi-unit properties (`Units > 1`), with the following key data for each parcel:

```
Units - Number of units on the parcel
SitusZipCode - 9 digit ZIP
CurrentRoll_LandValue - Assessed value of the parcel's land without any building
SqftLot - Size of parcel
```

I first looked at the average land value / sqft for single and multi unit properties by ZIP code. The 9 digit ZIP would hopefully control for the location-related variables that affect property value. The following charts compare average values for ZIPs where there are at least 3 of each single and multi unit properties.

![Average land value by ZIP](https://github.com/beekley/beekley.github.io/blob/master/images/lotValue.png?raw=true)

![Comparison](https://github.com/beekley/beekley.github.io/blob/master/images/lotValueDiff.png?raw=true)

Unfortunately, this doesn't really support my hypothesis. There is no significant effect of unit count on lot value.

One simple explanation is that the assessors don't consider zoning in their land value assessment, which would make this experiment moot. Another explanation is that higher-density buildings are built on lower-value land (e.g. near freeways or on major streets), wiping out any gains for zone. Or that the value data is noisy since it might be decades since a parcel was last assessed. Finally, it could just be that the sample size (as low as n=3 for some ZIPs) is too small to make meaningful conclusions.

This isn't necessarily the end of the story for me and the hypothesis. I think if I'm going to work on it more, I'd want to look at larger data sets (e.g. the 1+ million row LA county set) and different sources for "value".

BTW, here's the SQL to get the comparison and generate those charts. It's a little ugly but does the job.

```
SELECT 
    *
FROM
    (SELECT 
        SitusZipCode,
            COUNT(*) AS singleCount,
            AVG(CurrentRoll_LandValue / SqftLot) AS singleAvg,
            STDDEV(CurrentRoll_LandValue / SqftLot) AS singleStdev
    FROM
        sandbox.sm_assessor_2015
    WHERE
        GeneralUseType = 'Residential'
            AND Units = 1
            AND PropertyType != 'CND'
            AND SitusZipCode IS NOT NULL
            AND SitusZipCode != ''
    GROUP BY SitusZipCode) AS single
        INNER JOIN
    (SELECT 
        SitusZipCode,
            COUNT(*) AS multiCount,
            AVG(CurrentRoll_LandValue / SqftLot) AS multiAvg,
            STDDEV(CurrentRoll_LandValue / SqftLot) AS multiStdev
    FROM
        sandbox.sm_assessor_2015
    WHERE
        GeneralUseType = 'Residential'
            AND Units > 1
            AND PropertyType != 'CND'
            AND SitusZipCode IS NOT NULL
            AND SitusZipCode != ''
    GROUP BY SitusZipCode) AS multi ON multi.SitusZipCode = single.SitusZipCode
WHERE
    multiCount >= 3 AND singleCount >= 3
```
