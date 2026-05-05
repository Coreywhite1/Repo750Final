# Repo750Final
This is a repository for SIS 750 Final exam project documents.

# Exemplary Code Chunk
Below is a sample of exemplary work in `RStudio`. using the following code, I was able to recreate a plot on white vs black voter turnout data in Louisiana. I loaded my packages and data and began cleaning. I created mutated datasets that filter for black and white registration rates grouped by the dummy variable `claustype` which accounts for groups being treated or controlled. I summarized the data by grouping by `year` and `clausestype` and creating variables that were averages of the voter registration rates across the measured timefrme.


I next created several plots. I created individual plots for black and white voter registration using `ggplot`. I plotted the individual datapoints and then connected them using `geom_line`. I cleaned the visual product by expanding the data limits and customizing the breaks on the `x` axis before using the `theme` function to further customize the product. by combining the tables, I was able to compare black and white voter registration data based on a given treatment condition and present a clean visual product. 


## Loading packages and data
```{r}
library(tidyverse)
library(dplyr)
library(ggplot2)
library(patchwork)
library(haven)

df<-read.csv("la_turnout_basic.csv")
```
## Cleaning the data
```{r}
dfmutate <- df |>
mutate(
  clausetype = if_else(understandingclause2 == 0, 'control', 'treated')
)
dfmutate1 <- dfmutate |>
select(year, blackregrate, whiteregrate, clausetype) |>
filter(!is.na(blackregrate)) |>
filter(!is.na(whiteregrate)) |>
group_by(clausetype)
dfmutate2 <- dfmutate1 |>
  group_by(year, clausetype) |>
  summarise(
    avgblackreg = mean(blackregrate, na.rm = TRUE),
    avgwhitereg = mean(whiteregrate, na.rm = TRUE)
  )
```
## Making the plots for black and white voters. 
```{r}
atable <- dfmutate2 |>
ggplot(aes(x = year, y = avgblackreg)) +
  geom_point(aes(shape = clausetype))+
  geom_line(aes(color = clausetype)) +
  theme_classic()+
  scale_x_continuous(
    limits = c(1950, 1970), 
    breaks = seq(1950, 1975, 5),
    expand = expansion(mult = c(.05, .1))
  ) +
  scale_y_continuous(
    limits = c(0, .65),
    expand = expansion(mult = c(0, .01))
  )+
  scale_color_manual(
    values = c('purple4', 'yellow2')
  )+
  theme(
    panel.grid.major = element_line(color = 'grey'),
    panel.grid.minor = element_blank(),
    panel.background = element_rect(fill = 'white'),
    legend.position = 'right',
    plot.background = element_rect(fill= 'white'),
    legend.title = element_blank(),
    panel.border = element_rect(fill = NA, colour = 'black')
  ) +
labs(
  y = 'Black Registration Rate',
  plot.title = 'Suppressing Black Votes:',
  plot.subtitle = 'A Historical Case Study of Voting Restrictions in Louisiana',
  x= 'Year
  (a) Black Registration'
)
```
```{r}
btable <- dfmutate2 |>
  ggplot(aes(x = year, y = avgwhitereg)) +
  geom_point(aes(shape = clausetype))+
  geom_line(aes(color = clausetype, group = clausetype))+
  theme_classic()+
  scale_x_continuous(
    limits = c(1950, 1970), 
    breaks = seq(1950, 1975, 5),
    expand = expansion(mult = c(.05, .1))
  ) +
  scale_y_continuous(
    limits = c(.55, 1)
  ) +
  scale_color_manual(
    values = c('purple4', 'yellow2')
  )+
  theme(
    panel.grid.major = element_line(color = 'grey'),
    panel.grid.minor = element_blank(),
    panel.background = element_rect(fill = 'white'),
    legend.position = 'right',
    legend.title = element_blank(),
    panel.border = element_rect(fill = NA, colour = 'black')
  ) +
  labs(
    y = 'White Registration Rate',
    plot.title = 'Suppressing Black Votes:',
    plot.subtitle = 'A Historical Case Study of Voting Restrictions in Louisiana',
    x= 'Year
  (b) White Registration'
  )
```
## Combining the tables
```{r}
ctable <- atable + btable
```
## Output
<img width="1013" height="693" alt="image" src="https://github.com/user-attachments/assets/17c22cb3-b195-48b4-b1be-7aed4058583c" />

