- [Intro](#intro)
- [Creating of Sample](#creating-of-sample)
  - [General Sample](#general-sample)
  - [Preview Graph](#preview-graph)
  - [Test Expected Value](#test-expected-value)
- [optimizing best package](#optimizing-best-package)

# Intro

The goal of this simulation is to test the statistics of M&M and other
stacks even Chocolate lentils by color, I wanted to know, if I eat m&m
package 2 by 2, separated by color, what is the chance of my finishing
the package without mixing any color in one bite.

In addition, here are some BI incite that needed to be checked:

1.  What is the probability of M&M packages packaged fairly?
2.  What is the probability of M&M packages packaged without one color?
3.  How does the size of the package or number of colors affect this
    probability?

### 

The method is based of simulation of some M&M bags, according to the
most common sizes. Each time we sample x lentils, name them by colors
(represented as factorial numbers), and see the results for many
packages as a statistic data.

### Parameters

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.4     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
    ## 
    ## Attaching package: 'MASS'
    ## 
    ## 
    ## The following object is masked from 'package:dplyr':
    ## 
    ##     select
    ## 
    ## 
    ## 
    ## Attaching package: 'scales'
    ## 
    ## 
    ## The following object is masked from 'package:purrr':
    ## 
    ##     discard
    ## 
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     col_factor

The basic parameters (will be changed later):

``` r
#parameters
nn<- 500          #numbers of bags per sample
n_color<- 6         #unique colors of M&M
gram<- 0.91         #weight of one M&M
bag_g<- 250         #common weight of M&M package
n_unit<- bag_g/gram #M&M per package
av_per_color= n_unit/n_color
paste0("The avarage lentils per color is ", round(av_per_color,2))
```

    ## [1] "The avarage lentils per color is 45.79"

# Creating of Sample

## General Sample

create_bag is a function to create one snack package as matrix.

sample_MnM is a function to create n bags from the create_bag function.

``` r
#create bag using sample vector
create_bag<- function(x,tx)
  {
  x<- floor(x)+sample(0:1,1)
  res<- matrix(nrow = 1, ncol = tx)
  colnames(res)<- 1:tx
  S_res<- sample(tx,x,replace = T)
  while(sum(table(S_res)>x))
  {S_res<- S_res[-1]}
  for (i in 1:tx) {
    res[,i] <- sum(S_res==i)
    }
  res
}

#example
create_bag(100,6)
```

    ##       1  2  3  4  5  6
    ## [1,] 20 13 14 20 19 14

``` r
sample_MnM<-function(nn,x_units,t_colors)
  {
  res<- matrix(nrow = nn, ncol = t_colors)
  for (i in 1:nn)
    {res[i,]<- create_bag(x= x_units,tx=t_colors)}
  res
}
```

## Preview Graph

Now will be creating nn bugs of M&M

``` r
MnM_sample<- sample_MnM(nn*100,n_unit,n_color)

MnM_sample<-
  MnM_sample %>% as_data_frame() %>% 
  mutate(even_count= rowSums(across(everything()  , ~ .x %% 2 == 0)), #how many evens colors there are
         even_evens= rowSums(across(-c(even_count)  , ~ .x %% 2 == 1)) %% 2 ==0, #are the uneven colors even
         var_col=    apply(across(-c(even_count, even_evens)), 1, var), #var of candy per color/ type
         low_col=   rowSums(across(-c(even_count, even_evens,var_col), ~ .x <= 0.6*av_per_color)))

MnM_sample %>% head(6)
```

    ## # A tibble: 6 × 10
    ##      V1    V2    V3    V4    V5    V6 even_count even_evens var_col low_col
    ##   <int> <int> <int> <int> <int> <int>      <dbl> <lgl>        <dbl>   <dbl>
    ## 1    36    45    53    41    48    51          2 TRUE          40.7       0
    ## 2    53    43    44    44    42    49          3 FALSE         18.2       0
    ## 3    42    50    51    38    48    46          5 FALSE         25.0       0
    ## 4    47    43    52    46    42    44          4 TRUE          13.1       0
    ## 5    53    42    51    55    32    42          3 FALSE         76.6       0
    ## 6    34    46    49    41    48    57          3 FALSE         60.6       0

``` r
MnM_sample$low_col %>% table()
```

    ## .
    ##     0     1 
    ## 49746   254

ggplot the MnM sample sample

``` r
MnM_sample$even_count %>% 
table()
```

    ## .
    ##     0     1     2     3     4     5     6 
    ##   750  4713 11689 15536 11770  4741   801

``` r
MnM_sample %>% 
  mutate(is.even = even_count %%2 ==0) %>% 
  ggplot(aes(x= even_count, fill= is.even))+
  geom_bar()
```

![](MnM-sampling-report_files/figure-gfm/ggplot%201%20sample-1.png)<!-- -->

``` r
MnM_sample %>%
  mutate(is.even = V1 %%2 ==0) %>% 
  ggplot(aes(x= V1, fill= is.even, alpha= 0.7))+
  geom_bar()
```

![](MnM-sampling-report_files/figure-gfm/ggplot%201%20sample-2.png)<!-- -->

``` r
#summary of all colors
rbind(
MnM_sample$V1 %>% summary(),
MnM_sample$V2 %>% summary(),
MnM_sample$V3 %>% summary(),
MnM_sample$V4 %>% summary(),
MnM_sample$V5 %>% summary(),
MnM_sample$V6 %>% summary()
) %>% as.data.frame() %>% cbind(sapply(MnM_sample[,1:6],var,na.rm=1)) %>% 
  rename("Var" ="sapply(MnM_sample[, 1:6], var, na.rm = 1)")
```

    ##    Min. 1st Qu. Median     Mean 3rd Qu. Max.      Var
    ## V1   21      42     46 45.78432      50   74 38.19657
    ## V2   22      41     46 45.71300      50   71 37.97463
    ## V3   24      41     46 45.72156      50   75 38.36892
    ## V4   22      42     46 45.77164      50   75 38.15217
    ## V5   21      42     46 45.75394      50   76 38.28288
    ## V6   24      41     46 45.75534      50   75 38.22017

## Test Expected Value

to see is the mu of the lentils per color are fair, we will test it per
column with t.test.

``` r
#test mu is av_per_color
check_mean_hypothesis <- function(data, column_name, X) {
  test_result <- t.test(data[[column_name]], mu = X)
  return(test_result$p.value)
}

columns_to_test <- colnames(MnM_sample[,1:6])  # כל העמודות בטבלה
test_results <- sapply(columns_to_test, function(col) {
  check_mean_hypothesis(MnM_sample, col, av_per_color)
})

t(t(test_results))
```

    ##           [,1]
    ## V1 0.907090221
    ## V2 0.006833513
    ## V3 0.017221622
    ## V4 0.564745016
    ## V5 0.224563377
    ## V6 0.244082409

``` r
check_mean_hypothesis <- function(data, column_name, X) {
  t.test(data[[column_name]], mu = X)$p.value}

check_two_sample_t_test <- function(data, col1, col2) {
  t.test(data[[col1]], data[[col2]])$p.value}

columns_to_test <- colnames(MnM_sample[,1:6])
num_cols <- length(columns_to_test)
p_value_matrix <- matrix(NA, nrow = num_cols, ncol = num_cols, dimnames = list(columns_to_test, columns_to_test))

# מילוי המטריצה
for (i in 1:num_cols) {
  for (j in 1:num_cols) {
    if (i == j) { #diagonal test of mu
      p_value_matrix[i, j] <- check_mean_hypothesis(MnM_sample, columns_to_test[i], av_per_color)
    } else { # 2 sample t.test
      p_value_matrix[i, j] <- check_two_sample_t_test(MnM_sample, columns_to_test[i], columns_to_test[j])
    }
  }
}

round(as.data.frame(p_value_matrix),4)
```

    ##        V1     V2     V3     V4     V5     V6
    ## V1 0.9071 0.0677 0.1088 0.7456 0.4373 0.4585
    ## V2 0.0677 0.0068 0.8266 0.1329 0.2945 0.2781
    ## V3 0.1088 0.8266 0.0172 0.2005 0.4082 0.3881
    ## V4 0.7456 0.1329 0.2005 0.5647 0.6508 0.6766
    ## V5 0.4373 0.2945 0.4082 0.6508 0.2246 0.9714
    ## V6 0.4585 0.2781 0.3881 0.6766 0.9714 0.2441

``` r
MnM_sample %>%
    pivot_longer(cols = 1:6, names_to = "Variable", values_to = "pieces") %>%
    ggplot(aes(fill=Variable ,x= pieces, alpha= 0.4))+
  geom_histogram(position = "identity")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](MnM-sampling-report_files/figure-gfm/plot%202%20colors-1.png)<!-- -->

we can see that the variance distribution is Gamma like with shape and
rate as seen below

``` r
# הערכת הפרמטרים של התפלגות גמא
gamma_params <- fitdistr(MnM_sample$var_col, "gamma")$estimate
paste0("the parameters of the gamma shaped var is shape ", round(gamma_params[1],3)," and rate ", round(gamma_params[2],3) )
```

    ## [1] "the parameters of the gamma shaped var is shape 2.521 and rate 0.055"

``` r
shape_est <- gamma_params["shape"]
scale_est <- 1 / gamma_params["rate"]  # R משתמש ב-"rate" שהוא הופכי ל-scale

# יצירת גרף עם ההתפלגות של הנתונים + התפלגות גמא מותאמת
MnM_sample %>%
  ggplot(aes(x = var_col, fill = "orange")) +
  geom_density(fill= "lightblue4", alpha= 0.8) +
  geom_vline(xintercept = mean(MnM_sample$var_col), color = "blue", size=1) +
  geom_vline(xintercept = scale_est*shape_est, color = "red", linetype = "dashed", size=1) +
  stat_function(fun = dgamma, args = list(shape = shape_est, scale = scale_est), 
                color = "red", alpha= 0.7) +
  labs(title = "Density Plot with Gamma Distribution",
       x = "Variance",
       y = "") +
  scale_y_continuous(label=scales::label_percent(.1)) + 
  theme_minimal()
```

![](MnM-sampling-report_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

\#check too low color (under 10%) and sample by n number

\#use statistics to sample better low chance cases

# optimizing best package

### n\*m types of snacks

We will create a function that create sample for each number of colors
and package size we want, and then calculate some interesting parameters

``` r
mega_snack<- function(nn,n_unit,n_color)
  {
  m_sample<- length(n_unit)*length(n_color)
  nul_mat= matrix(,nrow = m_sample, ncol = 5)
  res<- cbind(rep(n_unit,length(n_color)),sort(rep(n_color,length(n_unit))),
                                               nul_mat)
  for (i in 1:(dim(res)[1]))
  {
    #print(c(res[i,1],res[i,2]))
    low_color<- 0.666*res[i,1]/(res[i,2])
    small_sample<- sample_MnM(nn,res[i,1],res[i,2])
    small_sample<-
      small_sample %>% as_data_frame() %>% 
      mutate(even_count= rowSums(across(everything()  , ~ .x %% 2 == 0))/n_color, #how many evens colors there are
             even_evens= (rowSums(across(-c(even_count)  , ~ .x %% 2 == 1)) %% 2 ==0)/n_color, #are the uneven colors even
             var_col=    apply(across(c(-even_count, even_evens)), 1, var), #var of candy per color/ type
             all_even=   rowSums(across(everything()  , ~ .x %% 2 == 0))== n_color,
             low_col=   rowSums(across(-c(even_count, even_evens,var_col,all_even), ~ .x <= low_color ))>=1)
    res[i,3]<- mean(small_sample$even_count)
    res[i,4]<- mean(small_sample$even_evens)
    res[i,5]<- mean(small_sample$var_col)
    res[i,6]<- mean(small_sample$all_even)
    res[i,7]<- mean(small_sample$low_col)
      }
  colnames(res)<- c("n_unit", "n_color", "even_count", "even_evens", "var_col","all_even","low_color")
  res
  }
```

``` r
color_op<- 2:7
grams_op<- c(25,45,150,250,330,500,750,1000)
n_unit_op<- grams_op/gram

mega_snack_1<-
  mega_snack(1000,n_unit_op,color_op) %>% as.data.frame() %>% 
  mutate(n_unit= round(n_unit,1))

mega_snack_1 %>% head(6)
```

    ##   n_unit n_color even_count even_evens     var_col all_even low_color
    ## 1   27.5       2  0.2437333  0.1268452    68.37371    0.119     0.101
    ## 2   49.5       2  0.2613905  0.1325357   214.34181    0.122     0.022
    ## 3  164.8       2  0.2682690  0.1257571  2291.23854    0.126     0.000
    ## 4  274.7       2  0.2622857  0.1384786  6331.88646    0.123     0.000
    ## 5  362.6       2  0.2616238  0.1347738 11019.91510    0.115     0.000
    ## 6  549.5       2  0.2789571  0.1399786 25274.03324    0.133     0.000

``` r
mega_snack_1 %>%
  ggplot(aes(x = factor((round( n_unit,1) )), y = factor(n_color ), fill = even_count )) +
  geom_tile() +
  scale_fill_gradient(low = "blue", high = "red4") +  # שינוי צבע לפי ערכים
  labs(title = "Heatmap of mega_snack even_count",
       x = "lentils",
       y = "No. of colors",
       fill = "even_count") +
  theme_minimal()
```

![](MnM-sampling-report_files/figure-gfm/hitmap-1.png)<!-- -->

``` r
mega_snack_1 %>%
  ggplot(aes(x = factor((round( n_unit,1) )), y = factor(n_color ), fill = even_evens )) +
  geom_tile() +
  scale_fill_gradient(low = "lightblue", high = "red") +  # שינוי צבע לפי ערכים
  labs(title = "Heatmap of mega_snack even_evens",
       x = "lentils",
       y = "No. of colors",
       fill = "even_evens") +
  theme_minimal()
```

![](MnM-sampling-report_files/figure-gfm/hitmap_2-1.png)<!-- -->

now let us see the probability of all even, and whether there is
pattern.

``` r
mega_snack_1 %>%
  ggplot(aes(x = (round( n_unit,1) ), y = n_color , color = all_even,size = all_even )) +
  geom_point() +
  scale_color_gradient(low = "orange3", high = "green4") +  # שינוי צבע לפי ערכים
  labs(title = "Heatmap of mega_snack all evens",
       x = "lentils",
       y = "No. of colors",
       color = "all_even") +
  theme_minimal()
```

![](MnM-sampling-report_files/figure-gfm/ggplot%20all%20even-1.png)<!-- -->

``` r
mega_snack_1 %>%
  ggplot(aes(x = factor(round(n_unit, 1)), y = n_color, fill = low_color))+
  geom_tile(color = "white") +
    geom_text(aes(label = sprintf("%.2f%%", low_color * 100)), color = "black", size = 4)+  # המרת ערכים לאחוזים
  scale_fill_gradient(low = "pink2", high = "red4",labels = scales::percent)+
 #scale_fill_continuous(label=scales::label_percent(.1))+
  labs(title = "Chance of Less Than 66% Color in Package",
       x = "lentils",
       y = "No. of colors",
       fill = "low color")+
  theme_minimal()
```

![](MnM-sampling-report_files/figure-gfm/ggplot%20missing%20color-1.png)<!-- -->

As we can see, only the small package (less than 50 lentils) have high
probability of at least one color to appear severely lower.

Therefore, splitting package by color on the big ones should be
relatively even.
