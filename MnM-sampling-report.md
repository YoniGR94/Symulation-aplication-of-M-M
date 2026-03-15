Statistical Analysis of Random Distribution in M&M Packages
================
Yoni
16 03, 2026

# Intro

### Objective of Simulation

This simulation explores the fairness and randomness in the color
distribution of M&M-style chocolate candies. Specifically, it
investigates:

1.  How often are M&M packs evenly distributed across all colors?

2.  What’s the probability of a pack missing at least one color?

3.  How do the number of candies and available colors impact that
    probability?

While seeing some chocolate packages, I wondered:

      **Can I finish a pack of M&M eating two at a time, without ever mixing colors in a bite?**

This playful question leads to a deeper statistical exploration of
random sampling, distribution fairness, and packaging quality in candy
production.

### Methodology

Since production data from [M&M](https://www.mms.com/en-us) isn’t
publicly available, I simulate packages based on common retail sizes and
the standard 6-color set. Each simulation randomly draws a sample of
candies, assigning each a color. I repeated this process hundreds of
times to analyze statistical properties across “virtual” packages.

Through this approach, I estimate the probability of:

1.  Getting a perfect pack (i.e., equal counts of each color).

2.  Receiving a pack missing at least one color.

3.  Seeing how these probabilities change with pack size or number of
    available colors.

My hypothesis is that perfectly balanced packs are extremely rare,
especially when six colors or more are involved. Larger packs may
contain all colors more consistently but still tend to be uneven in
distribution.

### Parameters

Basic parameters:

We define a “pack” as a vector of integers representing the count of
each color.  
Each simulation uses random sampling with replacement to mimic
real-world packaging.  
Key variables:

- `n-color`: Number of distinct colors

- `n-unit`: Total candies in the pack

``` r
#parameters
n= 1000              #numbers of bags per sample
n_color= 6          #unique colors of M&M
gram= 0.91          #weight of one M&M
bag_g= 250          #common weight of M&M package
n_unit= bag_g/gram  #M&M per package
```

    ## [1] "The avarage number of lentils per color is 45.8"

# Creating the Sample

## General Sample

In order to test the theoretical data, I need to simulate it using
customize functions. here are there:

- `Create_bag`- function to create one snack package for chosen package
  size and number of colors.

- `sample_MnM`- function to create n bags from the Create_bag function.

key parameters for `sample_MnM`

- `n`: Number of packages in the sample

- `x_units`: Total candies in each package

- `n_colors`: Number of distinct colors in each package

<!-- -->

    ## [1] "One bag of 100:"

    ##       1  2  3  4  5  6
    ## [1,] 19 15 18 20 17 11

    ## [1] "3 bags of 100:"

    ##       Red Blue Green Orange Yellow Brown
    ## Bag_1  17   16    16     14     20    18
    ## Bag_2  17   14    20     20     16    14
    ## Bag_3  25   18    13     12     19    14

## Preview Graph

Now will be creating n bugs of M&M

columns:

1.  `V1:V6`- the number of lentils per color

2.  `even_count`- how many evens colors there are

3.  `even_evens`- are the uneven colors even

4.  `low_col`- sum true if one color’s count is lower than $\frac{2}{3}$
    of expected value

5.  `Variance`- variance of lentils per color

6.  `min`- the lowest color in each row

7.  `all_even`- are all colors even

here are the first rows:

| Red | Blue | Green | Orange | Yellow | Brown | even_count | even_evens | low_col | Variance | min | all_even |
|---:|---:|---:|---:|---:|---:|---:|:---|---:|---:|---:|:---|
| 40 | 49 | 45 | 40 | 54 | 47 | 3 | FALSE | 0 | 29.37 | 40 | FALSE |
| 60 | 45 | 40 | 38 | 45 | 46 | 4 | TRUE | 0 | 59.47 | 38 | FALSE |
| 45 | 46 | 47 | 40 | 45 | 51 | 2 | TRUE | 0 | 12.67 | 40 | FALSE |
| 43 | 42 | 58 | 46 | 47 | 39 | 3 | FALSE | 0 | 43.77 | 39 | FALSE |

M&M sample random rows

I summarized the sample by color bellow

|        | Min. | 1st Qu. | Median |   Mean | 3rd Qu. | Max. |   Var |
|:-------|-----:|--------:|-------:|-------:|--------:|-----:|------:|
| Red    |   28 |      42 |     46 | 45.816 |      50 |   76 | 37.32 |
| Blue   |   29 |      42 |     46 | 45.754 |      50 |   68 | 38.42 |
| Green  |   29 |      41 |     46 | 45.844 |      50 |   66 | 40.62 |
| Orange |   24 |      41 |     46 | 45.969 |      50 |   68 | 41.99 |
| Yellow |   26 |      41 |     45 | 45.386 |      49 |   65 | 35.72 |
| Brown  |   27 |      41 |     46 | 45.725 |      50 |   67 | 38.34 |

summary of all colors Distribution

Here we can see the distribution of all colors to be even and of one
example color (red)

![](MnM-sampling-report_files/figure-gfm/ggplot%20all%20colors%20and%20red-1.png)<!-- -->

## Statistics Checking of the Simulation

### Test Expected Value

In order to see is the $\mu$ of the lentils per color are fair, I will
test it per column with t.test for each color.

Here is the result, none of them bellow 5% P. value

p.value of $H_0: \mu= \frac{n-unit}{n-color}$

    ##     Red    Blue   Green  Orange  Yellow   Brown 
    ## "88.3%" "86.4%" "77.9%" "37.6%"  "3.4%" "74.9%"

Now I will do the same checking for 2 samples, to see whether there is
correlation between each 2 colors distribution.

for each row i and column j

1)  if i==j, this it the check from before of the expected value to
    n_unit/n_color

2)  if i!=j, this is two samples test of same expected value hypothesis

![](MnM-sampling-report_files/figure-gfm/P.value%20test%20res-1.png)<!-- -->
Now here Is visualization of the actual data per color

![](MnM-sampling-report_files/figure-gfm/plot%202%20colors-1.png)<!-- -->

### Variance Distribution Checking

I know that the distribution of variance is approximately Gamma
distribution:

$$f(x) = \frac{1}{\Gamma(\alpha)\theta^\alpha} x^{\alpha-1}e^{-x/\theta}$$

I can see that the variance distribution is Gamma like with shape and
rate as seen below

    ## [1] "The parameters of the gamma shaped variance is shape 2.493 and scale 18.637"

![](MnM-sampling-report_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

### Are All Even in the Sample?

![](MnM-sampling-report_files/figure-gfm/are%20all%20even-1.png)<!-- -->

## Multiple Types of Samples

I will create a function that create sample for each number of colors
and package size I want, and then calculate some interesting parameters

I will make the multiple sample. parameters:

``` r
n_color<- 2:8 #Number of distinct colors in each package option
grams_op<- c(25,45,150,250,330,500,750,1000) #Weight of each package option
n_unit_op<- grams_op/gram #Total candies in each package option
nn<- 1200 #Number of packages in the sample
```

Here is some random rows:

| n_unit | n_color | even_count | even_evens | var_col | all_even | low_color | smallest_col |
|-------:|--------:|-----------:|-----------:|--------:|---------:|----------:|-------------:|
| 1098.9 |       6 |      0.506 |      0.083 | 187.766 |    0.022 |     0.000 |          131 |
| 1098.9 |       8 |      0.498 |      0.059 | 132.436 |    0.002 |     0.000 |          102 |
|  274.7 |       6 |      0.503 |      0.085 |  42.115 |    0.018 |     0.008 |           27 |
|  824.2 |       2 |      0.491 |      0.246 | 389.284 |    0.237 |     0.000 |          369 |
|   49.5 |       2 |      0.502 |      0.253 |  26.120 |    0.255 |     0.021 |           13 |

Multiple sample example rows

### Deep Insight on the Data

Here are some insights:

![](MnM-sampling-report_files/figure-gfm/hitmap-1.png)<!-- -->

![](MnM-sampling-report_files/figure-gfm/hitmap_2-1.png)<!-- -->

Here is probability of all even, and whether there is pattern.

![](MnM-sampling-report_files/figure-gfm/ggplot%20all%20even-1.png)<!-- -->

![](MnM-sampling-report_files/figure-gfm/ggplot%20missing%20color-1.png)<!-- -->
Here we can see the smallest % of Lentils in one color as seen in my
sample:

![](MnM-sampling-report_files/figure-gfm/smallest%20color%20vs%20expected-1.png)<!-- -->

As we can see, only the small package (less than 50 lentils) have high
probability of at least one color to appear severely lower.

Therefore, splitting package by color on the big ones should be
relatively even.

### Using Regression for Correlation Check

I wanted to see if there is statistic correlation of the number of
distinct colors of package size to the probability of all colors have
equal counts of each color. as a result, I chose to check this claim
with regression. Furthermore, I did the same regression adding another
potential correlated parameter: the evenness of the number of distinct
colors.

    ## 
    ## ==============================================
    ##                       Dependent variable:     
    ##                   ----------------------------
    ##                             all_even          
    ##                        (1)            (2)     
    ## ----------------------------------------------
    ## n_color              -18.764        -9.437    
    ##                     (872.970)      (460.847)  
    ##                                               
    ## n_unit               -0.0002        -0.0002   
    ##                      (0.0005)      (0.0005)   
    ##                                               
    ## color_No2                           11.102    
    ##                                   (2,169.439) 
    ##                                               
    ## Constant              36.356         6.598    
    ##                    (1,745.940)    (2,530.912) 
    ##                                               
    ## ----------------------------------------------
    ## Observations          2,160          2,160    
    ## Log Likelihood       -126.634      -126.634   
    ## Akaike Inf. Crit.    259.267        261.267   
    ## ==============================================
    ## Note:              *p<0.1; **p<0.05; ***p<0.01

As you can see, there is no statistic clear correlation of the number of
colors to probability of all distinct colors to be even.

    ## 
    ## =======================================================
    ##                            Dependent variable:         
    ##                   -------------------------------------
    ##                   cbind(n_color, n_color - even_count) 
    ##                          (1)                (2)        
    ## -------------------------------------------------------
    ## n_color                 0.007              0.007       
    ##                        (0.007)            (0.007)      
    ##                                                        
    ## n_unit                 0.00002            0.00002      
    ##                       (0.00005)          (0.00005)     
    ##                                                        
    ## color_No2               -0.003                         
    ##                        (0.031)                         
    ##                                                        
    ## Constant               0.635***           0.633***     
    ##                        (0.055)            (0.053)      
    ##                                                        
    ## -------------------------------------------------------
    ## Observations            2,160              2,160       
    ## Log Likelihood        -3,011.702         -3,011.708    
    ## Akaike Inf. Crit.     6,031.404          6,029.416     
    ## =======================================================
    ## Note:                       *p<0.1; **p<0.05; ***p<0.01

Like the previous regression, the second regression that examines the
correlation of the number of colors and package size to the percentage
of even numbers did not find a statistically significant correlation. In
addition, visually there is no clear correlation.

![](MnM-sampling-report_files/figure-gfm/visual%20glm_even_count-1.png)<!-- -->

# Summary

### Data Structure

The simulation created a random samples of snack packs, which was proven
to be statistically random with known $\mu$ and $\sigma^2$. I created
one sample with specific size and numbers of colors using “sample_MnM”,
and costume multiple samples using “mega_snack”. Then, I check the
relevant indicators for this project.

I found out that:

- Small packages often lack at least one color, and sometimes contain
  only one color.

- As the number of colors increases, the chance that all colors have
  even counts drops significantly.

- For medium to large packages, the probability of any one color being
  significantly underrepresented (less than $\frac{2}{3}$ of its
  expected amount) is near zero.

suggestion for any random sampler factory (like candies, Lego, toys):

1.  Smaller packages need more diversity check

2.  alternatively, I would recommend calculate the amount of each type
    in small packages

### Main Q: Eating M&M by Two

Although there is no clear pattern to the right M&M package for all the
colors to have even count, different approach might find a clear reason
for more or less couples of M&M. Here is what I did found:

The general probability of all colors to be even in 6 colored pack is
1.5% for small 50g package 2.1% for big 1000g package, and overall 1.5%,
which is more than I expected.

For 5 colored pack like Skittles the average is about 2.9%

For 2 colored pack the average is 25%, so for 2 colored marshmallow bag
this will be the statistics.

See all here:

| Colors | All Even Percent |
|-------:|-----------------:|
|      2 |            25.04 |
|      3 |            12.69 |
|      4 |             6.16 |
|      5 |             3.26 |
|      6 |             1.85 |
|      7 |             0.90 |
|      8 |             0.39 |

Probability of All Colors Even by Pack Colors Number

### Conclusions

To sum it up, for each medium pack the probability of all even colors is
1.4%, or 1 in a 73 packs of 250g. So I might need to change my snack
preference to marshmallow if I want to keep this method.

This project allowed me to implement simulation methods in response to a
real (albeit silly) question, and evaluate it statistically from end to
end..

I applied:

- Simulation by demand

- Exploratory analysis

- Hypothesis testing

- Distribution checks

- Outliers detection

- Visualization using R

In addition, I created the infrastructure for similar questions with
different parameters to be checked in a reusable, structured way.

### Bonus: M&M Simulation

You can try to simulate a package and play here:

[the M&M Pack Simulation
Game](https://yonigr94.github.io/Simulation-application-of-M-M//)

Have fun!
