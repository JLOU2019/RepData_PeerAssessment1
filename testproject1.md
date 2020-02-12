reproducible research project 1
test 110220
first R Markdown file

title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true

load data

```{r}
library(datasets)
data(airquality)
summary(airquality)
```

make a pairs plot

```{r}
pairs(airquality)
```
