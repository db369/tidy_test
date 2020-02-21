# TidyTuesday
See https://github.com/rfordatascience/tidytuesday for more information.

### Gender differences in taxable income in Australia
![Top 25 occupations for females, by income](tt4/top_25.png)

![Top and bottom 15 by difference in income](tt4/top_bottom.png)


### County-level American Community Survey (5-year estimates) 2015
![First](tt5/hexplot.jpg)

![Second version](tt5/hexplot2.jpg)

![Third version](tt5/hexplot_alt.jpg)


<details>
  <summary> . . . click here for code . . . </summary>
```r
library(tidyverse)
library(geojsonio)
library(broom)
library(rgeos)
library(wesanderson)
library(ggthemes)

df <- read.csv("acs2015_county_data.csv")

# From the excellent r-graph-gallery: https://www.r-graph-gallery.com/328-hexbin-map-of-the-usa/
spdf <- geojson_read("us_states_hexgrid.geojson.json",  what = "sp")

spdf@data = spdf@data %>% mutate(google_name = gsub(" \\(United States\\)", "", google_name))
spdf_fortified <- tidy(spdf, region = "google_name")
centers <- cbind.data.frame(data.frame(gCentroid(spdf, byid=TRUE), id=spdf@data$iso3166_2))

spdf_fortified$bin = cut(spdf_fortified$Unemployment , breaks=c(seq(0,16,2), Inf), labels=c(paste0(" ",seq(0,14,2),"-",seq(2,16,2)),"16+"), include.lowest = TRUE)

# color pallete experiments
pal1 <- wes_palette(9, name = "Moonrise2", type = "continuous")
pal2 <- wes_palette(9, name = "Darjeeling2", type = "continuous")
pal_alt <- tableau_seq_gradient_pal("Orange")(seq(0,1,length=9))

pal <- pal_alt

hex_plot <- ggplot() +
  geom_polygon(data = spdf_fortified, aes(fill = bin, x = long, y = lat, group = group) , size=0.7, alpha=0.9, col="#f5f5f2") +
  geom_text(data=centers, aes(x=x, y=y, label=id), color="white", size=3, alpha=0.6) +
  theme_void() +
  scale_fill_manual(values=pal, name="Unemployment rates %", guide = guide_legend(keyheight = unit(2, units = "mm"),
                                                                                  keywidth = unit(8, units = "mm"),
                                                                                  title.position = "top", label.position = "bottom",
                                                                                  title.hjust = 0.5, label.hjust = 0, nrow = 1) ) +
  labs(title = "Unemployment Rates By State",
       subtitle = "2015 American Community Survey 5-year estimates",
       caption = "\nBy @DaveBloom11  |  Source: 2015 American Community Survey " )

hex_plot +
  theme(
    legend.position = c(0.5, 0.9),
    legend.title = element_text(size = 8),
    legend.text = element_text(size = 7),
    text = element_text(color = "#22211d"),
    plot.background = element_rect(fill = "#f5f5f2", color = NA),
    panel.background = element_rect(fill = "#f5f5f2", color = NA),
    legend.background = element_rect(fill = "#f5f5f200", color = NA),
    plot.title = element_text(size= 22, hjust=0.5, color = "#4e4d47", margin = margin(b = -0.1, t = 0.4, l = 2, unit = "cm")),
    plot.subtitle = element_text(size= 12, hjust=0.5, color = "#4e4d47", margin = margin(b = -0.1, t = 0.4, l = 2, unit = "cm")),
    plot.caption = element_text(size = 7),
    plot.margin=unit(c(0,0,0,0),"mm")
  ) +
  coord_map()

```
</details>
