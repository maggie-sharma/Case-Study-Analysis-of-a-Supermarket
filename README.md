# Case-Study-Analysis-of-a-Supermarket
This case study will utilize R and Tableau to analyze a supermarket's data

# "Data Analysis of a Supermarket in Myanmar"

## Author: Maggie Sharma
### Date: 11/26/2021

# Purpose

This case study will use R and Tableau to clean, analyze, and visualize data from three branches of a supermarket located in Myanmar. 

## Guiding Questions

* Which branch is the most profitable?
* Which product line is the most profitable?
* What trends exist between branches and customers?

### Source
This open dataset was obtained from Kaggle. [source](https://www.kaggle.com/aungpyaeap/supermarket-sales)

# Analysis using R
## Preparing the Dataset
```{r}
# install packages
install.packages("tidyverse")
install.packages("RColorBrewer")
install.packages("lubridate")
library("tidyverse")
library("RColorBrewer")
library("lubridate")

```

```{r}
# Upload data
supermarket_data <- read_csv("https://github.com/maggie-sharma/Case-Study-Analysis-of-a-Supermarket/blob/main/supermarket_store_sales.csv")
```


```{r}
# View column names
colnames(supermarket_data)

```

```{r}
#Rename columns for easier use and conformity 

rename(supermarket_data, Cost_of_Goods=cogs, Gross_income= "gross income", Product_line="Product line", Customer_type="Customer type")
```
```{r}
#View structures to assist with creating plots
str(supermarket_data)
```

# Analyzing the Data
```{r}
#View summary of data
summary(supermarket_data)
```

Throughout the dataset, no currency value was present. As it was not clear what currency was being used, monetary values were left as numeric values. 

```{r echo=TRUE, message=FALSE, warning=FALSE}
supermarket_data$`gross income` <- as.numeric(supermarket_data$`gross income`)
supermarket_data$Date <- as.Date(supermarket_data$Date, "%m/%d/%y")
year(supermarket_data$Date) <- 2019
sapply(supermarket_data, function(x) sum(is.na(x)))
```

```{r}
#Calculate min and max date to establish date range for chart labels
mindate <- min(supermarket_data$Date)
maxdate <- max(supermarket_data$Date)
```
# Analysis by Branch

```{r}
ggplot(data = supermarket_data, mapping = aes(x = Branch, y = Rating)) + geom_boxplot(notch = TRUE, mapping = aes(fill = Branch)) +
theme_linedraw() + ggtitle("Box Plot Showing Relationship between Branch and Ratings",subtitle=paste0("Data from: ", mindate, " to ", maxdate)) + xlab("Branch")+ ylab("Rating")+
geom_hline(mapping = aes(yintercept = 7.1), linetype = "dashed") + geom_hline(mapping = aes(yintercept = 6.7), linetype = "dashed") +
geom_text(mapping = aes(x = "A", y = 7.5, label = "Yangon")) + geom_text(mapping = aes(x = "B", y = 7.5, label = "Mandalay")) + geom_text(mapping = aes(x = "C", y = 7.5, label = "Naypyitaw")) +
scale_y_continuous(breaks = c(4,5.3,5.6,6.7,7.1,8.2,8.5,10)) + scale_fill_brewer(palette="Dark2")
```
![](https://github.com/maggie-sharma/Case-Study-Analysis-of-a-Supermarket/blob/main/boxplot.png)

![Gross Income by Branch bar chart created on Tableau](https://github.com/maggie-sharma/Case-Study-Analysis-of-a-Supermarket/blob/main/Gross%20income%20by%20branch.png)

```{r}
ggplot(data = supermarket_data, mapping= aes(x = Rating, y = (supermarket_data$`gross income`))) + geom_smooth () + ggtitle("Relationship Between Gross Income related to Customer Rating", subtitle=paste0("Data from: ", mindate, " to ", maxdate)) + xlab("Customer Rating") + ylab("Gross Income")
```

![](https://github.com/maggie-sharma/Case-Study-Analysis-of-a-Supermarket/blob/main/smooth.png)

```{r}
# Filtering dataset with a pipe to see each branch separately 

A <- supermarket_data %>%
  filter(Branch=="A")
B <- supermarket_data %>%
  filter(Branch=="B")
C <- supermarket_data %>%
  filter(Branch=="C")

```

```{r}
average_rating_A <- mean(A$Rating)
average_rating_A

average_rating_B <- mean(B$Rating)
average_rating_B

average_rating_C <- mean(C$Rating)
average_rating_C
```
```{r}
# Create tibble of Average Branch Rating
supermarket_data %>%
  group_by(Branch) %>%
  summarise(avg=mean(Rating))
```
## Branch Analysis Summary 

* Branch C saw the highest profits during January 2019-March 2019 compared to Branches A and B.
* Branch B had lower customer ratings compared to Branches A and C.


# Trends by Customer Gender


```{r}
p <- ggplot(data = supermarket_data, mapping = aes(x = Branch)) + geom_bar(mapping = aes(fill = supermarket_data$`Gender`)) + 
theme_linedraw() + ggtitle("Distribution of Gender in Each Branch Based on Membership",subtitle=paste0("Data from: ", mindate, " to ", maxdate)) + xlab("Branch") + ylab("Number of Customers") 

p + facet_wrap(~supermarket_data$'Customer type') + scale_y_continuous(breaks = c(0, 25, 50, 75, 100, 125, 150, 175)) + scale_fill_brewer(palette="Accent",name = "Customer Type", direction=-1)


```
![](https://github.com/maggie-sharma/Case-Study-Analysis-of-a-Supermarket/blob/main/genderbybranch.png)

![Product Line Breakdown by Gross Income and Gender created on Tableau](https://github.com/maggie-sharma/Case-Study-Analysis-of-a-Supermarket/blob/main/Gross%20Income%20by%20Product%20Line%20and%20Gender.png)


![Gross Profit by Product Line pie chart created on tableau](https://github.com/maggie-sharma/Case-Study-Analysis-of-a-Supermarket/blob/main/Gross%20Income%20by%20Product%20Line.png)

## Trends by Customer Gender Summary

* Branch C had more female customers than male customers, while  Branches A and B saw more male customers than female customers.

* Female customers spent more on Food & Beverage, Fashion accessories, Home & Lifestyle, and Sports and travel compared to male customers.

* Male customers spent more on Health & beauty than female customers. 


# Trends in Customer Types & Payment Method

```{r}
g <- ggplot(data = supermarket_data, mapping = aes(x = Branch)) + geom_bar(mapping = aes(fill = `Customer type`)) + 
theme_linedraw() + ggtitle("Distribution of Customers in Each Branch Based on Payment Mode",subtitle=paste0("Data from: ", mindate, " to ", maxdate))+ xlab("Branch") + ylab("Number of Customers") 
g + facet_wrap(~supermarket_data$Payment) + scale_y_continuous(breaks = c(0, 25, 50, 75, 100, 125, 150, 175)) +scale_fill_brewer(palette="Paired")
```
![](https://github.com/maggie-sharma/Case-Study-Analysis-of-a-Supermarket/blob/main/bypaymenttype.png) 

```{r}
customer_type_total <- supermarket_data %>% count(`Customer type`)
customer_type_total
```

![Payment Type and Customer Type chart created on Tableau](https://github.com/maggie-sharma/Case-Study-Analysis-of-a-Supermarket/blob/main/Payment%20Type%20between%20customer%20type.png)




```{r}
payment_total <- supermarket_data %>% count(Payment)
payment_total
```

```{r}
# Create dataframe for piechart
payment_data <- data.frame(group=c("Cash", "Credit card", "Ewallet"),
  value=c(344, 311, 345)
)

piechart_data <- payment_data %>% 
  arrange(desc(group)) %>%
  mutate(prop = value / sum(payment_data$value) *100) %>%
  mutate(ypos = cumsum(prop)- 0.5*prop )

# Create piechart
ggplot(piechart_data, aes(x="", y=prop, fill=group)) +
  geom_bar(stat="identity", width=1, color="white") +
  coord_polar("y", start=0) +
  theme_void() + 
  theme(legend.position="none") +
  
  geom_text(aes(y = ypos, label = group), color = "white", size=6) +
  scale_fill_brewer(palette="Paired")
```
![](https://github.com/maggie-sharma/Case-Study-Analysis-of-a-Supermarket/blob/main/piechart.png)

### Product and Payment Trends Summary 

* While distribution of gross income across product lines was similar, **Food & beverage** and **Sports & Travel** were the highest grossing product lines. 

* Amongst all three branches, the customer type was nearly equal amongst members and non-members,  but payment types differed amongst member and non-members. Members were more likely to use credit cards, while non-members were more likely to use Ewallet.

* Overall, payment types were nearly equally distributed between cash, credit card, and Ewallet as demonstrated in the pie chart. 

# Conclusions

* Branch C experienced the **highest gross income** during the January 2019-March 2019 timeframe.

* Branch B had the lowest ratings. 

* Male customers spent more on *Health & beauty,* and female customers spent more on *Food & Beverage* and *Fashion accessories.*

* Members were more likely to use credit cards, while non-members were more likely to use Ewallet.
