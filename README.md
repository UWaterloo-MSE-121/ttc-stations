# TTC subway station usage

Typical weekday ridership at every Toronto subway and Scarborough RT station,
one small CSV file per year from 2012 to 2017. Prepared for MSE 121 at the
University of Waterloo as a first dataset for learning to work with data at the
command line and in Python.

Each file is about 74 rows. The whole thing fits on a screen, and every row is
a place you may have stood.

## Files

| file | year | rows |
|---|---|---|
| `stations-2012-2013.csv` | 2012 to 2013 | 74 |
| `stations-2014.csv` | 2014 | 74 |
| `stations-2015.csv` | 2015 | 74 |
| `stations-2016.csv` | 2016 | 74 |
| `stations-2017.csv` | 2017 | 74 |

`stations-2017.csv` is the most recent, and the one used in class.

## Format

Plain CSV: comma-separated, one header row, no quoting. No field contains a
comma, so the files can be split on commas with any tool.

```
rank,station,line,to_platform,from_platform,total
1,Bloor-Yonge,1 Yonge-University,93924,110703,204627
2,Bloor-Yonge,2 Bloor-Danforth,96309,100155,196464
3,Union,1 Yonge-University,67881,60774,128655
```

| column | meaning |
|---|---|
| `rank` | Position by `total`: 1 is the busiest station in that year. |
| `station` | Station name. |
| `line` | The line the platform is on: `1 Yonge-University`, `2 Bloor-Danforth`, `3 Scarborough`, or `4 Sheppard`. |
| `to_platform` | Riders going to the platform to board a train, on a typical weekday. |
| `from_platform` | Riders leaving the platform after getting off a train, on a typical weekday. |
| `total` | `to_platform + from_platform`. |

## Things to know before you compute anything

**A station on two lines appears twice**, once per line, because the count is
per platform. Bloor-Yonge, St. George, Sheppard-Yonge, Kennedy, and Spadina
each have two rows. To get a total for the whole station, add its rows.

**Some counts repeat from one year to the next.** In every pair of adjacent
years, two or three of the busiest interchange stations carry exactly the same
`to_platform` and `from_platform` as the year before, while every other
station changes. For example, Sheppard-Yonge on line 4 reads 45,746 in 2015,
2016, and 2017. The source does not explain this. The likely reason is that
those stations were not re-counted every year and the previous figure was
carried forward, but that is a guess. Treat any year-over-year change at a
major interchange with suspicion, and check that the numbers actually moved.

**One count is fractional.** Broadview in 2017 has 16817.5 riders to the
platform and 17278.5 from it. A half rider cannot be counted, so the figure
was computed somehow, but the source does not say how. It is left as
published, because rounding either value would break `to + from = total`.

**One station was renamed.** Downsview became Sheppard West in 2017, when line
1 was extended past it. Each file uses that year's name.

**Ranks are the source's**, not recomputed. In the 2015 file a few are out of
order by `total`.

## Source

City of Toronto open data, *TTC Ridership - Subway-Scarborough RT Station
Usage*, published by the Toronto Transit Commission and refreshed annually
until 2017, when the series stopped.

<https://open.toronto.ca/dataset/ttc-ridership-subway-scarborough-rt-station-usage/>

Licensed under the [Open Government Licence - Toronto][ogl]. *Contains
information licensed under the Open Government Licence - Toronto.*

[ogl]: https://www.toronto.ca/city-government/data-research-maps/open-data/open-data-licence/

## How these files differ from the source

The City publishes one Excel workbook per year. The files here were converted
to CSV and cleaned so that every year has the same layout. Nothing was added,
and no count was changed. What changed:

- **Titles and totals rows were removed.** Each workbook has a title block
  above the header and a grand-total row below the data. The CSVs hold only
  the header and the station rows.
- **The `line` column was made consistent.** The 2016 and 2017 workbooks have
  a line column. The 2012 to 2015 workbooks do not: they write the line into
  the station name for interchange stations, as in `St. George (line 2, B-D)`,
  and leave it out for every other station. The line was recovered for every
  row, from the name where it was given and from the station's known line
  otherwise, and checked against the 2016 and 2017 workbooks.
- **Station names were spelled one way.** The workbooks vary: `BLOOR-YONGE`,
  `Bloor-Yonge`, and `Bloor (line 1, Y-U)` all mean the same station, and
  2016 drops the period from `St. George`. Names are in title case with one
  spelling across all years.
- **Column names were made plain.** `To Trains` and `To` became
  `to_platform`; `From Trains` and `From` became `from_platform`; `Totals` and
  `Total` became `total`.

The conversion is done by a script in the course repository, and every row was
checked so that `to_platform + from_platform` equals `total`.
