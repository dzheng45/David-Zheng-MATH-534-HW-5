MATH 534 HW 4
================
David Zheng
2025-09-27

- [Q2](#q2)
- [Q3](#q3)
- [Q4](#q4)
- [Q4.1](#q41)
- [Q4.2](#q42)
- [Q4.3](#q43)
- [Q4.4](#q44)
- [Q4.5](#q45)
- [Q5](#q5)
- [Q5a](#q5a)
- [Q5b](#q5b)
- [Q5c](#q5c)
- [Q6](#q6)

# Q2

How many rows are available in the Measurements table of the Smith
College Wideband Auditory Immittance database?

``` r
library(RMariaDB)
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.2     ✔ tibble    3.3.0
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(mdsr)

con <- dbConnect(
  MariaDB(), host = "scidb.smith.edu",
  user = "waiuser", password = "smith_waiDB", 
  dbname = "wai"
)
Measurements <- tbl(con, "Measurements")

dbGetQuery(con, "
  SELECT COUNT(*) AS n_rows
  FROM Measurements;
")
```

    ##    n_rows
    ## 1 5052304

There are 5052304 rows available in the Measurements table of the Smith
College Wideband Auditory Immittance database.

# Q3

Identify what years of data are available in the flights table of the
airlines database.

``` r
con <- dbConnect_scidb("airlines")

flights <- tbl(con, "flights")

dbGetQuery(con, "
  SELECT DISTINCT year
  FROM flights
  ORDER BY year;
")
```

    ##   year
    ## 1 2013
    ## 2 2014
    ## 3 2015

The years of data that are available in the flights table of the
airlines database are 2013, 2014, and 2015.

# Q4

Use the dbConnect_scidb function to connect to the airlines database to
answer the following problem.

``` r
con <- dbConnect_scidb("airlines")

dbListTables(con)
```

    ## [1] "airports"        "planes"          "carriers"        "flights_summary"
    ## [5] "flights"

``` r
flights  <- tbl(con, "flights")
airports <- tbl(con, "airports")
carriers <- tbl(con, "carriers")

flights
```

    ## # Source:   table<`flights`> [?? x 21]
    ## # Database: mysql  [mdsr_public@mdsr.crcbo51tmesf.us-east-2.rds.amazonaws.com:3306/airlines]
    ##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
    ##    <int> <int> <int>    <int>          <int>     <int>    <int>          <int>
    ##  1  2013    10     1        2             10        -8      453            505
    ##  2  2013    10     1        4           2359         5      730            729
    ##  3  2013    10     1       11             15        -4      528            530
    ##  4  2013    10     1       14           2355        19      544            540
    ##  5  2013    10     1       16             17        -1      515            525
    ##  6  2013    10     1       22             20         2      552            554
    ##  7  2013    10     1       29             35        -6      808            816
    ##  8  2013    10     1       29             35        -6      449            458
    ##  9  2013    10     1       31             30         1      519            538
    ## 10  2013    10     1       32             33        -1      557            606
    ## # ℹ more rows
    ## # ℹ 13 more variables: arr_delay <int>, carrier <chr>, tailnum <chr>,
    ## #   flight <int>, origin <chr>, dest <chr>, air_time <int>, distance <int>,
    ## #   cancelled <int>, diverted <int>, hour <int>, minute <int>, time_hour <dttm>

``` r
airports
```

    ## # Source:   table<`airports`> [?? x 9]
    ## # Database: mysql  [mdsr_public@mdsr.crcbo51tmesf.us-east-2.rds.amazonaws.com:3306/airlines]
    ##    faa   name                         lat    lon   alt    tz dst   city  country
    ##    <chr> <chr>                      <dbl>  <dbl> <int> <int> <chr> <chr> <chr>  
    ##  1 04G   Lansdowne Airport           41.1  -80.6  1044    -5 A     Youn… United…
    ##  2 06A   Moton Field Municipal Air…  32.5  -85.7   264    -6 A     Tusk… United…
    ##  3 06C   Schaumburg Regional         42.0  -88.1   801    -6 A     Scha… United…
    ##  4 06N   Randall Airport             41.4  -74.4   523    -5 A     Midd… United…
    ##  5 09J   Jekyll Island Airport       31.1  -81.4    11    -5 A     Jeky… United…
    ##  6 0A9   Elizabethton Municipal Ai…  36.4  -82.2  1593    -5 A     Eliz… United…
    ##  7 0G6   Williams County Airport     41.5  -84.5   730    -5 A     Bryan United…
    ##  8 0G7   Finger Lakes Regional Air…  42.9  -76.8   492    -5 A     Sene… United…
    ##  9 0P2   Shoestring Aviation Airfi…  39.8  -76.6  1000    -5 U     Stew… United…
    ## 10 0S9   Jefferson County Intl       48.1 -123.    108    -8 A     Port… United…
    ## # ℹ more rows

``` r
carriers
```

    ## # Source:   table<`carriers`> [?? x 2]
    ## # Database: mysql  [mdsr_public@mdsr.crcbo51tmesf.us-east-2.rds.amazonaws.com:3306/airlines]
    ##    carrier name                                        
    ##    <chr>   <chr>                                       
    ##  1 02Q     Titan Airways                               
    ##  2 04Q     Tradewind Aviation                          
    ##  3 05Q     Comlux Aviation, AG                         
    ##  4 06Q     Master Top Linhas Aereas Ltd.               
    ##  5 07Q     Flair Airlines Ltd.                         
    ##  6 09Q     Swift Air, LLC                              
    ##  7 0BQ     DCA                                         
    ##  8 0CQ     ACM AIR CHARTER GmbH                        
    ##  9 0GQ     Inter Island Airways, d/b/a Inter Island Air
    ## 10 0HQ     Polar Airlines de Mexico d/b/a Nova Air     
    ## # ℹ more rows

# Q4.1

How many domestic flights flew into Dallas-Fort Worth (DFW) on May 14,
2015?

``` r
dbGetQuery(con, "
  SELECT COUNT(*) AS domestic_flights
  FROM flights
  WHERE year=2015 AND month=5 AND day=14
    AND dest='DFW'
    AND origin IN (
      SELECT faa FROM airports
      WHERE country IN ('United States','USA')
    );
")
```

    ##   domestic_flights
    ## 1              736

736 domestic flights flew into Dallas-Fort Worth (DFW) on May 14, 2015.

# Q4.2

Of all the destinations from Chicago O’Hare (ORD), which were the most
common in 2015?

``` r
dbGetQuery(con, "
  SELECT dest, name AS airport, COUNT(*) AS arrivals_from_chicago
  FROM flights 
  LEFT JOIN airports ON dest = faa
  WHERE year = 2015 AND origin = 'ORD'
  GROUP BY dest, airport
  ORDER BY arrivals_from_chicago DESC
  LIMIT 10;
")
```

    ##    dest                            airport arrivals_from_chicago
    ## 1   LGA                         La Guardia                 10492
    ## 2   LAX                   Los Angeles Intl                  8720
    ## 3   DFW             Dallas Fort Worth Intl                  8384
    ## 4   SFO                 San Francisco Intl                  8156
    ## 5   BOS General Edward Lawrence Logan Intl                  7240
    ## 6   ATL    Hartsfield Jackson Atlanta Intl                  7104
    ## 7   MSP           Minneapolis St Paul Intl                  6955
    ## 8   DCA      Ronald Reagan Washington Natl                  6495
    ## 9   DEN                        Denver Intl                  6120
    ## 10  MKE              General Mitchell Intl                  5274

Of all the destinations from Chicago O’Hare (ORD), La Guardia (LGA) was
the most common in 2015.

# Q4.3

Which airport had the highest average arrival delay time in 2015?

``` r
dbGetQuery(con, "
  SELECT dest, name AS airport, AVG(arr_delay) AS average_arrival_delay
  FROM flights
  LEFT JOIN airports ON dest = faa
  WHERE year = 2015 AND arr_delay IS NOT NULL
  GROUP BY dest, airport
  ORDER BY average_arrival_delay DESC
  LIMIT 1;
")
```

    ##   dest                      airport average_arrival_delay
    ## 1  STC Saint Cloud Regional Airport                21.622

Saint Cloud Regional Airport had the highest average arrival delay time
in 2015.

# Q4.4

How many domestic flights came into or flew out of Bradley Airport (BDL)
in 2015?

``` r
dbGetQuery(con, "
  SELECT COUNT(*) AS domestic_flights
  FROM flights
  WHERE year = 2015
    AND (origin = 'BDL' OR dest = 'BDL')
    AND origin IN (SELECT faa FROM airports WHERE country IN ('United States','USA'))
    AND dest IN (SELECT faa FROM airports WHERE country IN ('United States','USA'));
")
```

    ##   domestic_flights
    ## 1            40051

There were 40051 domestic flights that came into or flew out of Bradley
Airport (BDL) in 2015.

# Q4.5

List the airline and flight number for all flights between LAX and JFK
on September 26th, 2015.

``` r
dbGetQuery(con, "
  SELECT c.name AS airline, f.carrier, f.flight, f.origin, f.dest
  FROM flights AS f
  LEFT JOIN carriers AS c ON f.carrier = c.carrier
  WHERE f.year = 2015 AND f.month = 9 AND f.day = 26
    AND ((f.origin='LAX' AND f.dest='JFK') OR (f.origin='JFK' AND f.dest='LAX'))
  ORDER BY f.origin, f.dest, airline, f.flight;
")
```

    ##                   airline carrier flight origin dest
    ## 1  American Airlines Inc.      AA      3    JFK  LAX
    ## 2  American Airlines Inc.      AA     19    JFK  LAX
    ## 3  American Airlines Inc.      AA     21    JFK  LAX
    ## 4  American Airlines Inc.      AA     33    JFK  LAX
    ## 5  American Airlines Inc.      AA    117    JFK  LAX
    ## 6  American Airlines Inc.      AA    255    JFK  LAX
    ## 7  American Airlines Inc.      AA    293    JFK  LAX
    ## 8    Delta Air Lines Inc.      DL    420    JFK  LAX
    ## 9    Delta Air Lines Inc.      DL    423    JFK  LAX
    ## 10   Delta Air Lines Inc.      DL    447    JFK  LAX
    ## 11   Delta Air Lines Inc.      DL    464    JFK  LAX
    ## 12   Delta Air Lines Inc.      DL    472    JFK  LAX
    ## 13   Delta Air Lines Inc.      DL    477    JFK  LAX
    ## 14        JetBlue Airways      B6     23    JFK  LAX
    ## 15        JetBlue Airways      B6    123    JFK  LAX
    ## 16        JetBlue Airways      B6    223    JFK  LAX
    ## 17        JetBlue Airways      B6    323    JFK  LAX
    ## 18        JetBlue Airways      B6    423    JFK  LAX
    ## 19        JetBlue Airways      B6    523    JFK  LAX
    ## 20        JetBlue Airways      B6    623    JFK  LAX
    ## 21  United Air Lines Inc.      UA    441    JFK  LAX
    ## 22  United Air Lines Inc.      UA    535    JFK  LAX
    ## 23  United Air Lines Inc.      UA    703    JFK  LAX
    ## 24  United Air Lines Inc.      UA    841    JFK  LAX
    ## 25  United Air Lines Inc.      UA   1985    JFK  LAX
    ## 26         Virgin America      VX    399    JFK  LAX
    ## 27         Virgin America      VX    407    JFK  LAX
    ## 28         Virgin America      VX    411    JFK  LAX
    ## 29         Virgin America      VX    413    JFK  LAX
    ## 30         Virgin America      VX    415    JFK  LAX
    ## 31 American Airlines Inc.      AA      2    LAX  JFK
    ## 32 American Airlines Inc.      AA      4    LAX  JFK
    ## 33 American Airlines Inc.      AA     22    LAX  JFK
    ## 34 American Airlines Inc.      AA     30    LAX  JFK
    ## 35 American Airlines Inc.      AA     32    LAX  JFK
    ## 36 American Airlines Inc.      AA     34    LAX  JFK
    ## 37 American Airlines Inc.      AA    118    LAX  JFK
    ## 38 American Airlines Inc.      AA    180    LAX  JFK
    ## 39   Delta Air Lines Inc.      DL    412    LAX  JFK
    ## 40   Delta Air Lines Inc.      DL    476    LAX  JFK
    ## 41   Delta Air Lines Inc.      DL    920    LAX  JFK
    ## 42   Delta Air Lines Inc.      DL   1162    LAX  JFK
    ## 43   Delta Air Lines Inc.      DL   1262    LAX  JFK
    ## 44   Delta Air Lines Inc.      DL   1908    LAX  JFK
    ## 45        JetBlue Airways      B6     24    LAX  JFK
    ## 46        JetBlue Airways      B6    124    LAX  JFK
    ## 47        JetBlue Airways      B6    224    LAX  JFK
    ## 48        JetBlue Airways      B6    324    LAX  JFK
    ## 49        JetBlue Airways      B6    424    LAX  JFK
    ## 50        JetBlue Airways      B6    524    LAX  JFK
    ## 51        JetBlue Airways      B6    624    LAX  JFK
    ## 52  United Air Lines Inc.      UA    275    LAX  JFK
    ## 53  United Air Lines Inc.      UA    779    LAX  JFK
    ## 54  United Air Lines Inc.      UA    912    LAX  JFK
    ## 55  United Air Lines Inc.      UA   1752    LAX  JFK
    ## 56         Virgin America      VX    404    LAX  JFK
    ## 57         Virgin America      VX    406    LAX  JFK
    ## 58         Virgin America      VX    412    LAX  JFK
    ## 59         Virgin America      VX    416    LAX  JFK
    ## 60         Virgin America      VX    420    LAX  JFK

# Q5

Wideband acoustic immittance (WAI) is an area of biomedical research
that strives to develop WAI measurements as noninvasive auditory
diagnostic tools. WAI measurements are reported in many related formats,
including absorbance, admittance, impedance, power reflectance, and
pressure reflectance.

``` r
library(RMariaDB)
db <- dbConnect(
  MariaDB(), 
  user = "waiuser", 
  password = "smith_waiDB", 
  host = "scidb.smith.edu", 
  dbname = "wai"
)

dbListTables(db)
```

    ## [1] "Codebook"             "Measurements"         "Measurements_pre2020"
    ## [4] "PI_Info"              "PI_Info_OLD"          "Subjects"            
    ## [7] "Subjects_pre2020"

``` r
Subjects <- tbl(db, "Subjects")
Measurements <- tbl(db, "Measurements")

Subjects
```

    ## # Source:   table<`Subjects`> [?? x 11]
    ## # Database: mysql  [waiuser@scidb.smith.edu:3306/wai]
    ##    Identifier  SubjectNumber SessionTotal AgeFirstMeasurement
    ##    <chr>               <int>        <int>               <dbl>
    ##  1 Abur_2014               1            7            20      
    ##  2 Abur_2014               3            8            19      
    ##  3 Abur_2014               4            7            21      
    ##  4 Abur_2014               6            8            21      
    ##  5 Abur_2014               7            5            20      
    ##  6 Abur_2014               8            5            19      
    ##  7 Abur_2014              10            5            19      
    ##  8 Aithal_2013             1            1            NA      
    ##  9 Aithal_2013             2            1             0.00744
    ## 10 Aithal_2013             3            1            NA      
    ## # ℹ more rows
    ## # ℹ 7 more variables: AgeCategoryFirstMeasurement <chr>, Sex <chr>, Race <chr>,
    ## #   Ethnicity <chr>, LeftEarStatusFirstMeasurement <chr>,
    ## #   RightEarStatusFirstMeasurement <chr>, SubjectNotes <chr>

``` r
Measurements
```

    ## # Source:   table<`Measurements`> [?? x 16]
    ## # Database: mysql  [waiuser@scidb.smith.edu:3306/wai]
    ##    Identifier SubjectNumber Session Ear   Instrument   Age AgeCategory EarStatus
    ##    <chr>              <int>   <int> <chr> <chr>      <dbl> <chr>       <chr>    
    ##  1 Abur_2014              1       1 Left  HearID        20 Adult       Normal   
    ##  2 Abur_2014              1       1 Left  HearID        20 Adult       Normal   
    ##  3 Abur_2014              1       1 Left  HearID        20 Adult       Normal   
    ##  4 Abur_2014              1       1 Left  HearID        20 Adult       Normal   
    ##  5 Abur_2014              1       1 Left  HearID        20 Adult       Normal   
    ##  6 Abur_2014              1       1 Left  HearID        20 Adult       Normal   
    ##  7 Abur_2014              1       1 Left  HearID        20 Adult       Normal   
    ##  8 Abur_2014              1       1 Left  HearID        20 Adult       Normal   
    ##  9 Abur_2014              1       1 Left  HearID        20 Adult       Normal   
    ## 10 Abur_2014              1       1 Left  HearID        20 Adult       Normal   
    ## # ℹ more rows
    ## # ℹ 8 more variables: TPP <dbl>, AreaCanal <dbl>, PressureCanal <dbl>,
    ## #   SweepDirection <chr>, Frequency <dbl>, Absorbance <dbl>, Zmag <dbl>,
    ## #   Zang <dbl>

# Q5a

How many female subjects are there in total across all studies?

``` r
dbGetQuery(db, "
  SELECT COUNT(*) AS n_female
  FROM Subjects
  WHERE Sex = 'Female';
")
```

    ##   n_female
    ## 1     4727

There were 4727 females in total across all studies.

# Q5b

Find the average absorbance for participants for each study, ordered by
highest to lowest value.

``` r
dbGetQuery(db, "
  SELECT Identifier AS study,
         AVG(Absorbance) AS avg_absorbance
  FROM Measurements
  GROUP BY Identifier
  ORDER BY avg_absorbance DESC;
")
```

    ##             study avg_absorbance
    ## 1      Keefe_2003     0.75639774
    ## 2   Merchant_2010     0.69463692
    ## 3     Aithal_2022     0.59916577
    ## 4       Voss_2016     0.59155708
    ## 5     Aithal_2013     0.58986439
    ## 6    Shahnaz_2006     0.58277188
    ## 7       Voss_2010     0.58104418
    ## 8    Aithal_2017a     0.57391283
    ## 9  AlMakadma_2021     0.56706402
    ## 10    Pitaro_2016     0.55980564
    ## 11    Werner_2010     0.55051614
    ## 12    Aithal_2014     0.54973516
    ## 13  Rosowski_2012     0.53602115
    ## 14      Abur_2014     0.53518763
    ## 15       Sun_2023     0.52863273
    ## 16  Merchant_2015     0.52558405
    ## 17    Aithal_2015     0.52496602
    ## 18     Sliwa_2020     0.52173963
    ## 19     Myers_2018     0.51853195
    ## 20   Aithal_2014b     0.51603367
    ## 21  Nakajima_2012     0.50872624
    ## 22  Merchant_2020     0.48905205
    ## 23   Downing_2022     0.48731569
    ## 24    Hunter_2016     0.48492314
    ## 25   Aithal_2019a     0.46932590
    ## 26   Aithal_2017b     0.46117866
    ## 27     Lewis_2018     0.44771641
    ## 28       Liu_2008     0.43156744
    ## 29     Groon_2015     0.42415414
    ## 30    Shaver_2013     0.42185850
    ## 31       Sun_2016     0.41817281
    ## 32   Aithal_2019b     0.41569045
    ## 33 Scheperle_2020     0.41187745
    ## 34  Merchant_2021     0.41104207
    ## 35   Sanford_2009     0.40507324
    ## 36   Aithal_2020b     0.39401147
    ## 37    Feeney_2017     0.36650951
    ## 38   Sanford_2014     0.34979781
    ## 39     Keefe_2017     0.33199957
    ## 40   Ellison_2012     0.32621005
    ## 41   Aithal_2020a     0.32115980
    ## 42     Keefe_2012     0.31703928
    ## 43      Voss_1994     0.27106546
    ## 44     Lewis_2015     0.06627515

# Q5c

Write a query to count all the measurements with a calculated absorbance
of less than 0.

``` r
dbGetQuery(db, "
  SELECT COUNT(*) AS n_negative_absorbance
  FROM Measurements
  WHERE absorbance < 0;
")
```

    ##   n_negative_absorbance
    ## 1                 28694

There were 28694 measurements with a calculated absorbance of less than
0.

# Q6

The following open-ended question may require more than one query and a
thoughtful response. Based on data from 2013 only, and assuming that
transportation to the airport is not an issue, would you rather fly out
of JFK, LaGuardia (LGA), or Newark (EWR)? Why or why not? Use the
dbConnect_scidb function to connect to the airlines database.

``` r
con <- dbConnect_scidb("airlines")
flights <- tbl(con, "flights")
```

``` r
dbGetQuery(con, "
  SELECT origin,
         COUNT(*) AS n_flights,
         AVG(dep_delay) AS avg_delay,
         AVG(CASE WHEN dep_delay IS NULL AND arr_delay IS NULL THEN 1 ELSE 0 END) AS cancel_rate,
         AVG(CASE WHEN dep_delay <= 15 THEN 1 ELSE 0 END) AS on_time_rate
  FROM flights
  WHERE year = 2013
    AND origin IN ('JFK', 'LGA', 'EWR')
  GROUP BY origin
  ORDER BY avg_delay;
")
```

    ##   origin n_flights avg_delay cancel_rate on_time_rate
    ## 1    LGA    104662   10.0352           0       0.8167
    ## 2    JFK    111279   11.9094           0       0.7965
    ## 3    EWR    120835   14.7030           0       0.7605

Based on the 2013 data, I would choose to fly out of LaGuardia (LGA)
airport out of all the airports in the NYC area. It had the highest
on-time departure rate at 81.67% and the lowest average departure delay
of 10.0352 minutes compared to the other New York area airports. All
three airports had no cancellations. While Newark (EWR) offered more
flights than LaGuardia at 120835 flights for Newark compared to 104662
flights for LaGuardia, I find punctuality and cancellation rate more
valuable than the number of flights offered. Therefore, LaGuardia (LGA)
would be my preferred choice.
