Statistical Analysis of Random Distribution in M&M Packages
================
Yoni
21 04, 2025

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

    ##      1  2  3  4  5  6
    ## [1,] 7 20 15 20 17 21

    ## [1] "3 bags of 100:"

    ##       Red Blue Green Orange Yellow Brown
    ## Bag_1  16   13    22     22     11    17
    ## Bag_2  16   17    15     23     20     9
    ## Bag_3  21   15    16     15     13    20

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
| 48 | 38 | 44 | 51 | 44 | 50 | 5 | FALSE | 0 | 23.37 | 38 | FALSE |
| 46 | 39 | 44 | 47 | 46 | 53 | 3 | FALSE | 0 | 20.57 | 39 | FALSE |
| 51 | 45 | 46 | 43 | 56 | 33 | 2 | TRUE | 0 | 60.67 | 33 | FALSE |
| 45 | 40 | 42 | 62 | 37 | 49 | 3 | FALSE | 0 | 79.77 | 37 | FALSE |

M&M sample random rows

I summarized the sample by color bellow

|        | Min. | 1st Qu. | Median |   Mean | 3rd Qu. | Max. |   Var |
|:-------|-----:|--------:|-------:|-------:|--------:|-----:|------:|
| Red    |   27 |      42 |     46 | 45.747 |   50.00 |   71 | 35.51 |
| Blue   |   28 |      41 |     45 | 45.330 |   49.00 |   70 | 37.88 |
| Green  |   26 |      42 |     46 | 46.099 |   50.00 |   65 | 34.68 |
| Orange |   26 |      41 |     45 | 45.498 |   49.25 |   68 | 37.18 |
| Yellow |   26 |      41 |     45 | 45.677 |   50.00 |   64 | 38.91 |
| Brown  |   30 |      42 |     46 | 46.155 |   50.00 |   72 | 38.83 |

summary of all colors Distibution

Here we can see the distribution of all colors to be even and of one
example color (red)

![](MnM-sampling-report_files/figure-gfm/ggplot%20all%20colors%20and%20red-1.png)<!-- -->

## Statisics Checking of the Simullation

### Test Expected Value

In order to see is the $\mu$ of the lentils per color are fair, I will
test it per column with t.test for each color.

Here is the result, none of them bellow 5% P. value

p.value of $H_0: \mu= \frac{n-unit}{n-color}$

    ##     Red    Blue   Green  Orange  Yellow   Brown 
    ## "83.0%"  "1.9%"  "9.5%" "13.4%" "57.5%"  "6.2%"

Now I will do the same checking for 2 samples, to see whether there is
correlation between each 2 colors distribution.

for each row i and column j

1)  if i==j, this it the check from before of the expected value to
    n_unit/n_color

2)  if i!=j, this is two samples test of same expected value hypothesis

![](MnM-sampling-report_files/figure-gfm/P.value%20test-1.png)<!-- -->
Now here Is visualization of the actual data per color

![](MnM-sampling-report_files/figure-gfm/plot%202%20colors-1.png)<!-- -->

### Variance Distribution Checking

I know that the distribution of variance is approximately Gamma
distribution:

$$f(x) = \frac{1}{\Gamma(\alpha)\theta^\alpha} x^{\alpha-1}e^{-x/\theta}$$

I can see that the variance distribution is Gamma like with shape and
rate as seen below

    ## [1] "The parameters of the gamma shaped variance is shape 2.419 and scale 18.462"

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
|   27.5 |       6 |      0.494 |      0.079 |   4.588 |    0.013 |     0.559 |            0 |
|  164.8 |       7 |      0.496 |      0.075 |  23.557 |    0.004 |     0.073 |           11 |
|  274.7 |       7 |      0.505 |      0.070 |  39.655 |    0.012 |     0.028 |           20 |
|   49.5 |       7 |      0.501 |      0.068 |   7.298 |    0.007 |     0.278 |            0 |
|   27.5 |       2 |      0.493 |      0.251 |  13.671 |    0.244 |     0.105 |            5 |

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
    ## n_color              -18.962        -9.535    
    ##                     (869.341)      (459.099)  
    ##                                               
    ## n_unit               -0.0001        -0.0001   
    ##                      (0.0005)      (0.0005)   
    ##                                               
    ## color_No2                           11.203    
    ##                                   (2,162.952) 
    ##                                               
    ## Constant              36.897         6.842    
    ##                    (1,738.683)    (2,522.804) 
    ##                                               
    ## ----------------------------------------------
    ## Observations          2,160          2,160    
    ## Log Likelihood       -137.104      -137.104   
    ## Akaike Inf. Crit.    280.209        282.209   
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
    ## n_color                 -0.005             -0.005      
    ##                        (0.007)            (0.007)      
    ##                                                        
    ## n_unit                 0.00003            0.00003      
    ##                       (0.00005)          (0.00005)     
    ##                                                        
    ## color_No2               -0.008                         
    ##                        (0.031)                         
    ##                                                        
    ## Constant               0.704***           0.701***     
    ##                        (0.055)            (0.053)      
    ##                                                        
    ## -------------------------------------------------------
    ## Observations            2,160              2,160       
    ## Log Likelihood        -3,009.374         -3,009.407    
    ## Akaike Inf. Crit.     6,026.749          6,024.815     
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
relevand indicators fot this project.

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

2.  Althernatively, I would recomand calculate the amount of each type
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
|      2 |            24.62 |
|      3 |            12.76 |
|      4 |             6.42 |
|      5 |             3.17 |
|      6 |             1.75 |
|      7 |             0.71 |
|      8 |             0.44 |

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
Game](https://yonigr94.github.io/Symulation-aplication-of-M-M/)

Have fun!
