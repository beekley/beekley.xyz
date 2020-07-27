Los Angeles and CA have [a housing shortage](https://en.wikipedia.org/wiki/California_housing_shortage). Since there are fewer units in supply than demand, [housing prices have outpaced incomes](https://www.citylab.com/equity/2018/05/where-the-house-price-to-income-ratio-is-most-out-of-whack/561404/). As residents are pushed to cheaper housing further from job centers, [commutes get longer](https://la.curbed.com/2019/8/15/20807275/los-angeles-commute-times-traffic) and traffic gets worse. California has a large and rapidly growing [homeless population](https://www.nytimes.com/2017/12/21/us/california-today-states-homeless-population-drives-national-increase.html) and Californians are much more likely to [share a room with another adult](https://lao.ca.gov/reports/2015/finance/housing-costs/housing-costs.pdf).

There has been [pushback](https://www.curbed.com/2020/2/7/21125100/sb-50-california-bill-fail) against increasing the allowed density of residential units. A major fear has been that increasing supply means increasing density and changing the character of single-family neighborhoods. ["Manhattanization"](https://en.wikipedia.org/wiki/Manhattanization) is often used to describe efforts to increase density.

**The analysis below shows that Los Angeles can eliminate its housing shortage without significantly changing the vast majority of residential neighborhoods.**

There are 1,790k single family homes in LA County, and 24k multifamily buildings containing 1,560k units, for a total of 3,350k units [1]. The LA housing shortage is described as [about 517k units](https://la.curbed.com/2019/5/21/18634232/los-angeles-affordable-housing-shortage-how-much-need).

Fourplexes can blend in with single-family homes due to their small size and general conformance with existing single-family zoning laws (this is the basis for [SB 902](https://www.kqed.org/news/11805850/sen-wiener-wants-to-abolish-single-family-only-neighborhoods-in-california), which aims to allow 2-4plexes across the state). A [quick image search shows fourplexes](https://www.google.com/search?tbm=isch&q=los+angeles+fourplex) look like large single family homes. **If 173k single family homes were converted to fourplexes, 519k units would be added, eliminating the housing shortage**. That's a little under 1 in 10 houses, or about one fourplex per block. This isn't a significant change to any neighborhood.

#### Graph showing the number of buildings in LA county before and after building 519k units of quadplex.
![a](https://github.com/beekley/beekley.github.io/blob/master/images/r1-rd2.png?raw=true)

But that is assuming an even distribution of supply. If we want to improve traffic, housing needs to be near jobs to lower the total people-miles of commuting. The Westside, Beverly Hills, and El Segundo are major job centers and together have about 113k single family homes [2]. We have to look denser than single-story buildings to fit the units in one region of LA:

* The highest density zone (R5) allows one unit per 200 sqft of lot size.
* The average single family home in CA is 0.15 acre, or 6500 sqft.
* In a vastly simplified calculation, each unit of single-family home could be converted to 33 units of R5 housing.
* It would take 16k home->tower conversions to address the shortage.

**We could solve the LA housing shortage building 2400 acres of residential towers**, which is about [the area west of Lincoln in Santa Monica + Venice](https://github.com/beekley/beekley.github.io/blob/master/images/area.png?raw=true). Putting high density towers near job centers could significantly change the surrounding area, the small number of changes (<1% of R1 buildings) means a vast minority of LA neighborhoods would be affected. Especially if it's spread out across the 3 million acres of LA County.

#### Graph showing the number of buildings in LA county before and after converting 16k R1 homes to R5 housing.
![a](https://github.com/beekley/beekley.github.io/blob/master/images/r1-r5.png?raw=true)

#### Graph showing the number of buildings in the "Westside" before and after converting 16k R1 homes to R5 housing.
![a](https://github.com/beekley/beekley.github.io/blob/master/images/r1-r5_sm.png?raw=true)

Of course, these are both extreme cases. Skyscrapers are never going to appear in Venice overnight (or possibly ever), nor will a hundred thousand homes get torn down for more density. Medium-density is the "missing middle" that enables walkable neighborhoods and would require much less upzoning than the first case, with a lower impact than the second.

Additionally, this only considers upzoning single-family properties. There are 215k 2-9 family buildings that could be upzoned to medium-density in areas that already have appetites for multifamily housing [1]. Many of the ongoing major housing projects were on non-residential land, like [former industrial mills](https://urbanize.la/post/595-unit-linea-development-nears-finish-line-pico-sepulveda) or [car dealerships](https://urbanize.la/post/martin-cadillac-makes-way-expo-line-adjacent-offices-and-apartments).

It'll take a combination of changes to address the housing shortage in LA. But none of them will cause LA to look anything like Manhattan.

[1] [2018 LA County Asessor Parcels Data](https://data.lacounty.gov/Parcel-/Assessor-Parcels-Data-2018/mk7y-hq5p)
```
select units, count(1) c
from Assessor_Parcels_Data_2018
where generalusetype = 'Residential'
group by units
order by units asc;
```
[2] [2018 LA County Asessor Parcels Data](https://data.lacounty.gov/Parcel-/Assessor-Parcels-Data-2018/mk7y-hq5p). Everything southwest of [34.079536, -118.404716](https://github.com/beekley/beekley.github.io/blob/master/images/westside.png?raw=true) is considered the "westside". (There are better definitions, but this is a simple query).
```
select units, count(1) c
from Assessor_Parcels_Data_2018
where generalusetype = 'Residential'
and center_lat <= 34.079536
and center_lon <= -118.404716
group by units
order by units asc;
```
