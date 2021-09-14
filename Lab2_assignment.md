Lab2\_assignment
================
Amelia Marcantonio-Fields
9/13/2021

``` r
library(viridis)
library(tidyverse)
library(tidycensus)
library(dplyr)
library(sf)
library(tmap) # mapping, install if you don't have it
set.seed(717)
```

This assignment if for you to complete a short version of the lab notes,
but you have to complete a number of the steps yourself. You will then
knit this to a markdown (not an HTML) and push it to your GitHub repo.
Unlike HTML, the RMarkdown knit to `github_document` can be viewed
directly on GitHub. You will them email your lab instructor with a link
to your repo.

Steps in this assignment:

1.  Make sure you have successfully read, run, and learned from the
    `MUSA_508_Lab2_sf.Rmd` Rmarkdown

2.  Find two new variables from the 2019 ACS data to load. Use
    `vars <- load_variables(2019, "acs5")` and `View(vars)` to see all
    of the variable from that ACS. Note that you should not pick
    something really obscure like count\_38yo\_cabinetmakers because you
    will get lots of NAs.

``` r
census_api_key("9a4eb0449cd787a44a9402bccce6e20769078384",overwrite = TRUE)

vars <- load_variables(2019, "acs5")

view(vars)

lab2_acs_vars <- c("B01001_001E", # ACS total Pop Estimate
                    "B16010_041E", # Total Bachelor's Degree or Higher
                    "B19019_001E") # Median HH Income (2019 $)

myTracts2 <- c("42101000803",
               "42101000804",
               "42101001202")

acsTractsPHL.2019.sf <- get_acs(geography = "tract",
                             year = 2019, 
                             variables = lab2_acs_vars, 
                             geometry = TRUE, 
                             state = "PA", 
                             county = "Philadelphia", 
                             output = "wide") %>% 
  dplyr::select (GEOID, NAME, all_of(lab2_acs_vars)) %>%
  rename (total_pop.2019 = B01001_001E,
          total_BDorhigher.2019 = B16010_041E,
          med_HH_income.2019 = B19019_001E) %>%
  mutate(pctBDorhigher.2019 = total_BDorhigher.2019/total_pop.2019) %>%
  mutate(Rittenhouse = ifelse(GEOID %in% myTracts2, "RITTENHOUSE", "REST OF PHILADELPHIA"))
```

3.  Pick a neighborhood of the City to map. You will need to do some
    googling to figure this out. Use the [PHL Track
    Explorer](https://data-phl.opendata.arcgis.com/datasets/census-tracts-2010/explore?location=40.002759%2C-75.119097%2C11.91)
    to get the `GEOID10` number from each parcel and add them to the
    `myTracts` object below. This is just like what was done in the
    exercise, but with a different neighborhood of your choice. Remember
    that all GEOIDs need to be 10-characters long.

4.  In the first code chunk you will do that above and then edit the
    call-outs in the dplyr pipe sequence to `rename` and `mutate` your
    data.

5.  You will transform the data to `WGS84` by adding the correct EPSG
    code. This is discussed heavily in the exercise.

``` r
acsTractsPHL.2019.sf_WGS84 <- acsTractsPHL.2019.sf %>% 
  st_transform(crs = "EPSG:4326")

st_crs(acsTractsPHL.2019.sf_WGS84)
```

    ## Coordinate Reference System:
    ##   User input: EPSG:4326 
    ##   wkt:
    ## GEOGCRS["WGS 84",
    ##     DATUM["World Geodetic System 1984",
    ##         ELLIPSOID["WGS 84",6378137,298.257223563,
    ##             LENGTHUNIT["metre",1]]],
    ##     PRIMEM["Greenwich",0,
    ##         ANGLEUNIT["degree",0.0174532925199433]],
    ##     CS[ellipsoidal,2],
    ##         AXIS["geodetic latitude (Lat)",north,
    ##             ORDER[1],
    ##             ANGLEUNIT["degree",0.0174532925199433]],
    ##         AXIS["geodetic longitude (Lon)",east,
    ##             ORDER[2],
    ##             ANGLEUNIT["degree",0.0174532925199433]],
    ##     USAGE[
    ##         SCOPE["Horizontal component of 3D system."],
    ##         AREA["World."],
    ##         BBOX[-90,-180,90,180]],
    ##     ID["EPSG",4326]]

6.  You will produce a map of one of the variables you picked and
    highlight the neighborhood you picked. There are call-out within the
    `ggplot` code for you to edit.

![](Lab2_assignment_files/figure-gfm/ggplot_geom_sf-1.png)<!-- -->

7.  You can run the code chunks and lines of code as you edit to make
    sure everything works.

8.  Once you are done, hit the `knit` button at the top of the script
    window (little blue knitting ball) and you will see the output. Once
    it is what you want…

9.  Use the `Git` tab on the bottom left of right (depending on hour
    your Rstudio is laid out) and click the check box to `stage` all of
    your changes, write a commit note, hit the `commit` button, and then
    the `Push` button to push it to Github.

10. Check your Github repo to see you work in the cloud.

11. Email your lab instructor with a link!

12. Congrats! You made a map in code!
