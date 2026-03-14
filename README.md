# 🍫 M&M by Two — Color Pairing Simulation

A statistical simulation exploring whether M&M colors distribute evenly enough to eat every color in pairs
— with an interactive game to try it yourself
🟤🔴🟡🟠🟢🔵🟤


This is a data simulation of M&M packages were I am analyzing the distribution of colors and various statistical properties.

The goal is to explore simulation creation and identify potential biases or anomalies.

Look for the result here:

* [**Github Presentation MD**](https://github.com/YoniGR94/Symulation-aplication-of-M-M/blob/main/MnM-sampling-report.md)


* [**PDF Presentation file**](https://github.com/YoniGR94/Symulation-aplication-of-M-M/blob/main/MnM-sampling-report.pdf)


* [**RMD code file**](https://github.com/YoniGR94/Symulation-aplication-of-M-M/blob/main/MnM-sampling-report.Rmd)


* [**M&M Pack Simulation Game**](https://yonigr94.github.io/Simulation-application-of-M-M/)


For now, you can see the main result: Probability of all colors of snack package be in couples by size of package and number of colors:

<img src="https://github.com/YoniGR94/Symulation-aplication-of-M-M/blob/main/Heatmap_all_evens.png?raw=true" width="600"/>

### 📂 Inside the project

✅ Simulated M&M packages with different color distributions

✅ Statistical analysis (mean, variance, hypothesis testing)

✅ Visualizations to highlight trends

✅ The interesting Question: can you eat each color two by two all the way to the end?


### 🔍 Key Questions Explored

1. How evenly does colors distributed in an M&M package?

2. What is the probability of getting too little of one color?

3. Do different package sizes affect color variation?

Here is the distribution of each color of M&M from my simulation

<img src="https://github.com/YoniGR94/Symulation-aplication-of-M-M/blob/main/colored_M&M.png?raw=true" width="600"/>

### 📚 required libraries

Clone the repository and run the analysis using the Tidyverse and visualization using ggplot.

```
library(tidyverse)
library(MASS)
library(scales)
```


### Bonus: M&M Simulation Game

In order to demonstrate the simulation on more visual fun way, I created this Game below.The simulation was created via vibe coding,
showing sampling of candy package and checking whether the distinct colors are arranged in pairs in the package.

You can try to simulate a package and play here:

[**M&M Pack Simulation Game**](https://yonigr94.github.io/Simulation-application-of-M-M/)

Have fun!

### 🤝 Contributions

Feel free to fork this repository, suggest improvements, or add new analyses!

![](https://img.shields.io/github/commit-activity/m/YoniGR94/Symulation-aplication-of-M-M?label=commited&logo=git&style=plastic)
