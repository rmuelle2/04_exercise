---
title: 'Weekly Exercises #4'
author: "Rylan Mueller"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    toc_float: TRUE
    df_print: paged
    code_download: true
---


```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE, error=TRUE, message=FALSE, warning=FALSE)
```

```{r libraries}
library(tidyverse)     # for data cleaning and plotting
library(lubridate)     # for date manipulation
library(openintro)     # for the abbr2state() function
library(palmerpenguins)# for Palmer penguin data
library(maps)          # for map data
library(ggmap)         # for mapping points on maps
library(gplots)        # for col2hex() function
library(RColorBrewer)  # for color palettes
library(sf)            # for working with spatial data
library(leaflet)       # for highly customizable mapping
library(carData)       # for Minneapolis police stops data
library(ggthemes)      # for more themes (including theme_map())
theme_set(theme_economist())
```

```{r data}
# Starbucks locations
Starbucks <- read_csv("https://www.macalester.edu/~ajohns24/Data/Starbucks.csv")

starbucks_us_by_state <- Starbucks %>% 
  filter(Country == "US") %>% 
  count(`State/Province`) %>% 
  mutate(state_name = str_to_lower(abbr2state(`State/Province`))) 

# Lisa's favorite St. Paul places - example for you to create your own data
favorite_stp_by_lisa <- tibble(
  place = c("Home", "Macalester College", "Adams Spanish Immersion", 
            "Spirit Gymnastics", "Bama & Bapa", "Now Bikes",
            "Dance Spectrum", "Pizza Luce", "Brunson's"),
  long = c(-93.1405743, -93.1712321, -93.1451796, 
           -93.1650563, -93.1542883, -93.1696608, 
           -93.1393172, -93.1524256, -93.0753863),
  lat = c(44.950576, 44.9378965, 44.9237914,
          44.9654609, 44.9295072, 44.9436813, 
          44.9399922, 44.9468848, 44.9700727)
  )

#COVID-19 data from the New York Times
covid19 <- read_csv("https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-states.csv")

```

## Put your homework on GitHub!

If you were not able to get set up on GitHub last week, go [here](https://github.com/llendway/github_for_collaboration/blob/master/github_for_collaboration.md) and get set up first. Then, do the following (if you get stuck on a step, don't worry, I will help! You can always get started on the homework and we can figure out the GitHub piece later):

* Create a repository on GitHub, giving it a nice name so you know it is for the 4th weekly exercise assignment (follow the instructions in the document/video).  
* Copy the repo name so you can clone it to your computer. In R Studio, go to file --> New project --> Version control --> Git and follow the instructions from the document/video.  
* Download the code from this document and save it in the repository folder/project on your computer.  
* In R Studio, you should then see the .Rmd file in the upper right corner in the Git tab (along with the .Rproj file and probably .gitignore).  
* Check all the boxes of the files in the Git tab under Stage and choose commit.  
* In the commit window, write a commit message, something like "Initial upload" would be appropriate, and commit the files.  
* Either click the green up arrow in the commit window or close the commit window and click the green up arrow in the Git tab to push your changes to GitHub.  
* Refresh your GitHub page (online) and make sure the new documents have been pushed out.  
* Back in R Studio, knit the .Rmd file. When you do that, you should have two (as long as you didn't make any changes to the .Rmd file, in which case you might have three) files show up in the Git tab - an .html file and an .md file. The .md file is something we haven't seen before and is here because I included `keep_md: TRUE` in the YAML heading. The .md file is a markdown (NOT R Markdown) file that is an interim step to creating the html file. They are displayed fairly nicely in GitHub, so we want to keep it and look at it there. Click the boxes next to these two files, commit changes (remember to include a commit message), and push them (green up arrow).  
* As you work through your homework, save and commit often, push changes occasionally (maybe after you feel finished with an exercise?), and go check to see what the .md file looks like on GitHub.  
* If you have issues, let me know! This is new to many of you and may not be intuitive at first. But, I promise, you'll get the hang of it! 


## Instructions

* Put your name at the top of the document. 

* **For ALL graphs, you should include appropriate labels.** 

* Feel free to change the default theme, which I currently have set to `theme_minimal()`. 

* Use good coding practice. Read the short sections on good code with [pipes](https://style.tidyverse.org/pipes.html) and [ggplot2](https://style.tidyverse.org/ggplot2.html). **This is part of your grade!**

* When you are finished with ALL the exercises, uncomment the options at the top so your document looks nicer. Don't do it before then, or else you might miss some important warnings and messages.


## Warm-up exercises from tutorial

These exercises will reiterate what you learned in the "Mapping data with R" tutorial. If you haven't gone through the tutorial yet, you should do that first.

### Starbucks locations (`ggmap`)

  1. Add the `Starbucks` locations to a world map. Add an aesthetic to the world map that sets the color of the points according to the ownership type. What, if anything, can you deduce from this visualization? 
  
```{r}

world <- get_stamenmap(
    bbox = c(left = -180, bottom = -57, right = 179, top = 82.1), 
    maptype = "toner-background",
    zoom = 2)
ggmap(world) +
  geom_point(data = Starbucks,
            aes(x = Longitude, y = Latitude, color = `Ownership Type`),
            size = .2) +
  theme_map()+
  labs(title = "Starbucks Locations")
```
  

  2. Construct a new map of Starbucks locations in the Twin Cities metro area (approximately the 5 county metro area).  
  
```{r}
starbucks_twin_cities <- get_stamenmap(
    bbox = c(left = -93.86, bottom = 44.63, right = -92.85, top = 45.39), 
    maptype = "terrain",
    zoom = 10
)

ggmap(starbucks_twin_cities)+
  geom_point(data = Starbucks, 
            aes(x = Longitude, y = Latitude),
            size = .6) +
  theme_map()+
  labs(title = "Starbucks in the Twin Cities Metro Area")
```
  

  3. In the Twin Cities plot, play with the zoom number. What does it do?  (just describe what it does - don't actually include more than one map).  
  
```{r}
starbucks_twin_cities <- get_stamenmap(
    bbox = c(left = -93.86, bottom = 44.63, right = -92.85, top = 45.39), 
    maptype = "terrain",
    zoom = 7
)

ggmap(starbucks_twin_cities)+
  geom_point(data = Starbucks, 
            aes(x = Longitude, y = Latitude),
            size = .6) +
  theme_map()+
  labs(title = "Starbucks in the Twin Cities Metro Area")
```
  **Zoom changes the level of detail in the map. The smaller the zoom, the less detail. The larger the zoom, the more detail in the map.**

  4. Try a couple different map types (see `get_stamenmap()` in help and look at `maptype`). Include a map with one of the other map types.  
  
```{r}
starbucks_twin_cities <- get_stamenmap(
    bbox = c(left = -93.86, bottom = 44.63, right = -92.85, top = 45.39), 
    maptype = "watercolor",
    zoom = 10
)

ggmap(starbucks_twin_cities)+
  geom_point(data = Starbucks, 
            aes(x = Longitude, y = Latitude),
            size = .6) +
  theme_map()+
  labs(title = "Starbucks in the Twin Cities Metro Area")
```
  

  5. Add a point to the map that indicates Macalester College and label it appropriately. There are many ways you can do think, but I think it's easiest with the `annotate()` function (see `ggplot2` cheatsheet).
  
```{r}
starbucks_twin_cities <- get_stamenmap(
    bbox = c(left = -93.86, bottom = 44.63, right = -92.85, top = 45.39), 
    maptype = "terrain",
    zoom = 10
)

ggmap(starbucks_twin_cities)+
  geom_point(data = Starbucks, 
            aes(x = Longitude, y = Latitude),
            size = .6) + 
  annotate("point", y=44.93923625444584, x=-93.16925707570375, color = "blue") +
  annotate("text", y=44.91923625444584, x=-93.16925707570375, color = "blue", label = "Macalester") +
  theme_map()+
  labs(title = "Starbucks in the Twin Cities Metro Area with Macalester Labeled")
```
  

### Choropleth maps with Starbucks data (`geom_map()`)

The example I showed in the tutorial did not account for population of each state in the map. In the code below, a new variable is created, `starbucks_per_10000`, that gives the number of Starbucks per 10,000 people. It is in the `starbucks_with_2018_pop_est` dataset.

```{r}
census_pop_est_2018 <- read_csv("https://www.dropbox.com/s/6txwv3b4ng7pepe/us_census_2018_state_pop_est.csv?dl=1") %>% 
  separate(state, into = c("dot","state"), extra = "merge") %>% 
  select(-dot) %>% 
  mutate(state = str_to_lower(state))

starbucks_with_2018_pop_est <-
  starbucks_us_by_state %>% 
  left_join(census_pop_est_2018,
            by = c("state_name" = "state")) %>% 
  mutate(starbucks_per_10000 = (n/est_pop_2018)*10000)
```

  6. **`dplyr` review**: Look through the code above and describe what each line of code does.
  
  **Line 193:** Creates a data set called census_pop_est_2018 by reading in a csv.
  **Line 194:** Separates the state column into a column with the dot in front of the state name and a column with just the state name.
  **Line 195:** Keeps all columns of the data set except the column with the dot.
  **Line 196:** Makes sure all characters in the state column are lowercase.
  **Line 198 - 199:** Establishes a new data set based on the starbucks_us_by_state data set.
  **Line 200-201:** Joins starbucks_us_by_state with with census_pop_est_2018 by the same state.
  **Line 202:** Creates a variable called starbucks_per_10000 by dividing the state's total Starbucks by the population of the state and multiplying by 10000.

  7. Create a choropleth map that shows the number of Starbucks per 10,000 people on a map of the US. Use a new fill color, add points for all Starbucks in the US (except Hawaii and Alaska), add an informative title for the plot, and include a caption that says who created the plot (you!). Make a conclusion about what you observe.
  
```{r}

state_us <- map_data("state")
starbucks_us <- Starbucks %>% 
  filter(Country == "US") %>% 
  filter(`State/Province` != "AK") %>% 
  filter(`State/Province` != "HI")
  
ggplot() +
  geom_map(data = starbucks_with_2018_pop_est,
           map = state_us,
           aes(map_id = state_name,
               fill = starbucks_per_10000))+
  geom_point(data = starbucks_us, 
            aes(x = Longitude, y = Latitude),
            size = .6,
            color = "red") +
  expand_limits(x = state_us$long, y = state_us$lat) + 
  scale_fill_viridis_c(option = "magma", direction = -1) +
  theme_map() +
  theme(legend.background = element_blank()) +
  labs(title = "Starbucks per 10000 People in the Contiguous United States", caption = "Map created by Rylan Mueller", fill = "")
```
  **Conclusion: Starbucks are primarily concentrated around high population metro areas. There are more Starbucks per 10,000 people along the west coast, probably because Starbucks was founded in Seattle. There are more Starbucks per 10,000 people in high population states and not many Starbucks per 10,000 people in low population states.**

### A few of your favorite things (`leaflet`)

  8. In this exercise, you are going to create a single map of some of your favorite places! The end result will be one map that satisfies the criteria below. 

  * Create a data set using the `tibble()` function that has 10-15 rows of your favorite places. The columns will be the name of the location, the latitude, the longitude, and a column that indicates if it is in your top 3 favorite locations or not. For an example of how to use `tibble()`, look at the `favorite_stp_by_lisa` I created in the data R code chunk at the beginning.  

  * Create a `leaflet` map that uses circles to indicate your favorite places. Label them with the name of the place. Choose the base map you like best. Color your 3 favorite places differently than the ones that are not in your top 3 (HINT: `colorFactor()`). Add a legend that explains what the colors mean.  
  
  * Connect all your locations together with a line in a meaningful way (you may need to order them differently in the original data).  
  
  * If there are other variables you want to add that could enhance your plot, do that now.  

```{r}
houses <- tibble(
  place = c("Evansville House", "Evansville Apartment", "Sioux City Apartment", "Dakota Dunes", "Cedar Rapids", "Campus Apartments", "Savannah", "Mum Lane", "Macktown", "River Falls House", "Mariah", "Current House", "Macalester"),
    long = c(-87.4150, -87.4857, -96.4920, -96.5023, -91.7144, -83.1857, -83.2415, -83.2533, -83.2696, -92.6363, -92.4169, -91.9610, -93.2573),
  lat = c(37.9727, 37.9860, 42.4974, 42.4993, 41.9875, 35.2967, 35.3703, 35.3608, 35.3571, 44.8354, 44.8616, 46.8282, 44.9511),
  top_three = c("No", "No", "No", "No","No", "No", "No", "No", "Yes", "No", "No", "Yes", "Yes")
  )

leaflet(data = houses) %>% 
  addTiles() %>% 
  addCircleMarkers(
    lng = ~long, 
    lat = ~lat, 
    label = ~place,
    color = col2hex("black"))%>% 
  addPolylines(
    lng = ~long, 
    lat = ~lat, 
    color = col2hex("orange"))
```
  
  
## Revisiting old datasets

This section will revisit some datasets we have used previously and bring in a mapping component. 

### Bicycle-Use Patterns

The data come from Washington, DC and cover the last quarter of 2014.

Two data tables are available:

- `Trips` contains records of individual rentals
- `Stations` gives the locations of the bike rental stations

Here is the code to read in the data. We do this a little differently than usualy, which is why it is included here rather than at the top of this file. To avoid repeatedly re-reading the files, start the data import chunk with `{r cache = TRUE}` rather than the usual `{r}`. This code reads in the large dataset right away.

```{r cache=TRUE}
data_site <- 
  "https://www.macalester.edu/~dshuman1/data/112/2014-Q4-Trips-History-Data.rds" 
Trips <- readRDS(gzcon(url(data_site)))
Stations<-read_csv("http://www.macalester.edu/~dshuman1/data/112/DC-Stations.csv")
```

  9. Use the latitude and longitude variables in `Stations` to make a visualization of the total number of departures from each station in the `Trips` data. Use either color or size to show the variation in number of departures. This time, plot the points on top of a map. Use any of the mapping tools you'd like.
  
```{r}
  
  dc_map <- get_stamenmap(
  bbox = c(left = -77.2540, bottom = 38.7856, right = -76.8979, top = 39.1396),
  maptype = "terrain-lines",
  zoom = 11
)

stations_join <- Stations %>% 
  left_join(Trips, by = c("name" = "sstation")) %>% 
  group_by(lat, long) %>% 
  summarize(sum_name = n())

ggmap(dc_map) +
  geom_point(data = stations_join, 
             aes(x = long, y = lat, color = sum_name),
             size = .5) +
  scale_color_viridis_c(option = "magma", direction = -1) +
  theme_map()+
  labs(title = "Biking around the District of Columbia", color = "Number of Departures") +
  theme(legend.background = element_blank())
```
  **There are more departurescloser to downtown DC.**
  
  10. Only 14.4% of the trips in our data are carried out by casual users. Create a plot that shows which area(s) have stations with a much higher percentage of departures by casual users. What patterns do you notice? Also plot this on top of a map. I think it will be more clear what the patterns are.
  
```{r}
  dc_map2 <- get_stamenmap(
  bbox = c(left = -77.2540, bottom = 38.7856, right = -76.8979, top = 39.1396),
  maptype = "terrain-lines",
  zoom = 11
)

trips_join <- Trips %>% 
  left_join(Stations, by = c("sstation" = "name")) %>% 
  group_by(lat, long) %>% 
  summarize(sum_name = n(), prop_client = mean(client == "Casual"))
  
  ggmap(dc_map2) +
  geom_point(data = trips_join, 
             aes(x = long, y = lat, color = prop_client),
             size = .5) +
  scale_color_viridis_c(option = "magma", direction = -1) +
  theme_map()+
  labs(title = "Biking around the District of Columbia", color = "Proportion of Casual Bikers") + 
    theme(legend.background = element_blank())
```
  **There are more casual bikers around tourist destinations and there are more registered users in the residential areas of DC.**
  
  
### COVID-19 data

The following exercises will use the COVID-19 data from the NYT.

  11. Create a map that colors the states by the most recent cumulative number of COVID-19 cases (remember, these data report cumulative numbers so you don't need to compute that). Describe what you see. What is the problem with this map?
  
```{r}

march9_covid <- covid19 %>% 
  filter(date == "2022-03-09") %>% 
  mutate(state = str_to_lower(state))


states_map <- map_data("state")
  
  ggplot() +
  geom_map(data = march9_covid,
           map = states_map,
           aes(map_id = state,
               fill = cases)) +
  expand_limits(x = states_map$long, y = states_map$lat) + 
  scale_fill_viridis_c(option = "magma", direction = -1) +
  theme_map()+
  labs(title = "Total COVID cases in each State", fill = "Total Cases")
```
  
  **This map shows the total number of cases in each state. It is deceiving because states with a greater population will tend to have more COVID cases. **
  
  12. Now add the population of each state to the dataset and color the states by most recent cumulative cases/10,000 people. See the code for doing this with the Starbucks data. You will need to make some modifications. 
  
```{r}
march9_covid <- covid19 %>% 
  filter(date == "2022-03-09") %>% 
  mutate(state = str_to_lower(state))

census_pop_est_2018 <- read_csv("https://www.dropbox.com/s/6txwv3b4ng7pepe/us_census_2018_state_pop_est.csv?dl=1") %>% 
  separate(state, into = c("dot","state"), extra = "merge") %>% 
  select(-dot) %>% 
  mutate(state = str_to_lower(state))

covid_with_population <-
  march9_covid %>% 
  left_join(census_pop_est_2018,
            by = "state") %>% 
  mutate(covid_per_10000 = (cases/est_pop_2018)*10000)


states_map <- map_data("state")
  
  ggplot() +
  geom_map(data = covid_with_population,
           map = states_map,
           aes(map_id = state,
               fill = covid_per_10000)) +
  expand_limits(x = states_map$long, y = states_map$lat) + 
  scale_fill_viridis_c(option = "magma", direction = -1) +
  theme_map()+
  labs(title = "COVID cases per 10,000 people", fill = "COVID per 10000")
```
  
  
  13. **CHALLENGE** Choose 4 dates spread over the time period of the data and create the same map as in exercise 12 for each of the dates. Display the four graphs together using faceting. What do you notice?
  
## Minneapolis police stops

These exercises use the datasets `MplsStops` and `MplsDemo` from the `carData` library. Search for them in Help to find out more information.

  14. Use the `MplsStops` dataset to find out how many stops there were for each neighborhood and the proportion of stops that were for a suspicious vehicle or person. Sort the results from most to least number of stops. Save this as a dataset called `mpls_suspicious` and display the table.  
  
```{r}
mpls_suspicious <- MplsStops %>% 
  group_by(neighborhood) %>% 
  summarize(sum_stops = n(), prop_sus = mean(problem == "suspicious")) %>% 
  arrange(desc(sum_stops))

mpls_suspicious
```
  **Note: I have had glitches with leaflet maps on the server, so Professor Milne said it is fine if I use other maps for the remaining problems instead of leaflet.**
  
  15. Use a `leaflet` map and the `MplsStops` dataset to display each of the stops on a map as a small point. Color the points differently depending on whether they were for suspicious vehicle/person or a traffic stop (the `problem` variable). HINTS: use `addCircleMarkers`, set `stroke = FAlSE`, use `colorFactor()` to create a palette.  
  
```{r}
  mpls_stops_map <- get_stamenmap(
    bbox = c(left = -93.3354, bottom = 44.8851, right = -93.1959, top = 45.0560), 
    maptype = "terrain",
    zoom = 12
)

ggmap(mpls_stops_map)+
  geom_point(data = MplsStops, 
            aes(x = long, y = lat, color = problem),
            size = .2) +
  theme_map() + 
  labs(title = "Police Stops in Minneapolis", color = "Reason for Stop") + 
    theme(legend.background = element_blank())
```
  
  
  16. Save the folder from moodle called Minneapolis_Neighborhoods into your project/repository folder for this assignment. Make sure the folder is called Minneapolis_Neighborhoods. Use the code below to read in the data and make sure to **delete the `eval=FALSE`**. Although it looks like it only links to the .sph file, you need the entire folder of files to create the `mpls_nbhd` data set. These data contain information about the geometries of the Minneapolis neighborhoods. Using the `mpls_nbhd` dataset as the base file, join the `mpls_suspicious` and `MplsDemo` datasets to it by neighborhood (careful, they are named different things in the different files). Call this new dataset `mpls_all`.

```{r}
mpls_nbhd <- st_read("Minneapolis_Neighborhoods/Minneapolis_Neighborhoods.shp", quiet = TRUE)

mpls_all_1 <- mpls_suspicious %>% 
  left_join(mpls_nbhd,
            by = c("neighborhood" = "BDNAME"))

mpls_all <- mpls_all_5 %>% 
  left_join(MplsDemo,
            by = "neighborhood")

mpls_all
```


  17. Use `leaflet` to create a map from the `mpls_all` data  that colors the neighborhoods by `prop_suspicious`. Display the neighborhood name as you scroll over it. Describe what you observe in the map.
  
```{r}

mpls_all_2 <- mpls_all %>% 
  left_join(MplsStops,
            by = "neighborhood")
 
mpls_map <- get_stamenmap(
    bbox = c(left = -93.3354, bottom = 44.8851, right = -93.1959, top = 45.0560), 
    maptype = "terrain",
    zoom = 12
)
 ggmap(mpls_map) +
   geom_polygon(data = mpls_all_2,
                aes(x = long, y = lat, color = prop_sus),
                size = .2)+
   theme_map() + 
  labs(title = "Police Stops in Minneapolis", color = "Proportion of Suspicious Stops") + 
    theme(legend.background = element_blank())

```
  
  
  18. Use `leaflet` to create a map of your own choosing. Come up with a question you want to try to answer and use the map to help answer that question. Describe what your map shows. 
  
  **Question: In Minneapolis, are there a lot of traffic stops around Starbucks locations?**
  
```{r}
traffic_stops <- MplsStops %>% 
  filter(problem == "traffic")

ggmap(mpls_map)+
  geom_point(data = Starbucks, 
            aes(x = Longitude, y = Latitude, color = "green"),
            size = .6) +
  geom_point(data = traffic_stops, 
            aes(x = long, y = lat, color = "blue"),
            size = .2) + 
  theme_map()+
  labs(title = "Starbucks and Traffic Stops", color = "Location", x = "", y = "") + 
    theme(legend.background = element_blank())

```
  **I did not realize the amount of traffic stops compared to the number of Starbucks until I made this map. However, there does not appear to be a correlation between Starbucks locations and traffic stops.**
  
  
## GitHub link

  19. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 04_exercises.Rmd, provide a link to the 04_exercises.md file, which is the one that will be most readable on GitHub.
