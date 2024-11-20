---
title: "Statistical Experimentation with R Internal Datasets"
author: "Elisha Veriwa"
date: "Last updated: `r Sys.Date()`"
format:
  html:
    toc: true
    toc-depth: 3
    number-sections: true
    code-fold: true
editor: visual
---

# Introduction
Statistical experimentation explores relationships between variables and tests hypotheses. This notebook demonstrates concepts using R internal datasets such as `mtcars`, `iris`, and `PlantGrowth`.

> **Objectives:**
> - Apply statistical methods using R datasets.
> - Understand experimental design, hypothesis testing, and errors.

#### 2. **Basics of Statistical Experimentation**

# Basics of Statistical Experimentation

## Example: The `PlantGrowth` Dataset
The `PlantGrowth` dataset contains data on the effect of three treatments on plant growth.

### Data Description
```{r}
data("PlantGrowth")
head(PlantGrowth)
summary(PlantGrowth)
```

### Visualization
```{r}
library(ggplot2)
ggplot(PlantGrowth, aes(x = group, y = weight, fill = group)) +
  geom_boxplot() +
  theme_minimal() +
  labs(title = "Effect of Treatments on Plant Growth", x = "Treatment Group", y = "Plant Weight")
```

#### 3. **Designing Experiments**
# Designing Experiments

## Example: One-Way ANOVA

We test if the plant weights differ significantly across treatment groups.

### Hypotheses
- \(H_0\): Mean weights are equal across groups.
- \(H_1\): At least one group has a different mean.

### Performing ANOVA
```{r}
# ANOVA Test
anova_result <- aov(weight ~ group, data = PlantGrowth)
summary(anova_result)
```

### Post-hoc Analysis
If the ANOVA test is significant, perform pairwise comparisons:

```{r}
# Tukey's HSD Test
TukeyHSD(anova_result)
```

### Interpretation
- Check the p-values to determine significance.
- Identify which groups differ significantly.

#### 4. **Hypothesis Testing**

# Hypothesis Testing

## Example: Testing Correlation in the `mtcars` Dataset

The `mtcars` dataset contains information on car specifications. We'll test if there's a significant correlation between weight (`wt`) and miles per gallon (`mpg`).

### Hypotheses
- \(H_0\): No correlation (\(r = 0\)).
- \(H_1\): Significant correlation (\(r \neq 0\)).

### Scatterplot
```{r}
ggplot(mtcars, aes(x = wt, y = mpg)) +
  geom_point() +
  geom_smooth(method = "lm", col = "blue") +
  theme_minimal() +
  labs(title = "Scatterplot of Weight vs. MPG", x = "Weight (1000 lbs)", y = "Miles Per Gallon")
```

### Correlation Test
```{r}
cor_test <- cor.test(mtcars$wt, mtcars$mpg)
cor_test
```

### Interpretation
- If \(p < 0.05\), there's a significant correlation.
- Check the correlation coefficient (\(r\)) for strength and direction.

#### 5. **Errors and Bias in Experiments**

# Errors and Bias in Experiments

## Example: Simulating Errors Using `iris` Dataset

The `iris` dataset contains measurements of flowers from three species. We'll simulate Type I and Type II errors.

### Simulation: Comparing Petal Lengths
```{r}
setosa <- iris$Petal.Length[iris$Species == "setosa"]
versicolor <- iris$Petal.Length[iris$Species == "versicolor"]

# Perform T-test
t_test <- t.test(setosa, versicolor, alternative = "two.sided")
t_test
```

### Error Analysis
- Type I Error: False rejection of \(H_0\).
- Type II Error: Failure to reject \(H_0\) when it’s false.

Simulating Type I Error:

```{r}
# Simulate Type I Error
simulate_type1 <- function(n = 10) {
  group1 <- rnorm(n, mean = 0, sd = 1)
  group2 <- rnorm(n, mean = 0, sd = 1) # Same mean
  t.test(group1, group2)$p.value < 0.05
}
mean(replicate(1000, simulate_type1()))
```

Interpretation:
- The proportion of significant tests approximates the chosen significance level (\( \alpha = 0.05 \))

#### 6. **Advanced Topics**

# Advanced Topics

## Example: Factorial Design with `mtcars`

Factorial designs test multiple factors. Here, we analyze the interaction of transmission (`am`) and number of cylinders (`cyl`) on mileage (`mpg`).

### Two-Way ANOVA

```{r}
# Two-way ANOVA
mtcars$am <- factor(mtcars$am, labels = c("Automatic", "Manual"))
mtcars$cyl <- factor(mtcars$cyl)

two_way <- aov(mpg ~ am * cyl, data = mtcars)
summary(two_way)
```

### Interaction Plot

```{r}
interaction.plot(mtcars$cyl, mtcars$am, mtcars$mpg,
                 col = c("red", "blue"), legend = TRUE,
                 main = "Interaction Plot: Transmission and Cylinders",
                 xlab = "Number of Cylinders", ylab = "Miles Per Gallon")
```

### Interpretation
- Significant interaction term (\(p < 0.05\)) suggests dependency between factors.

### **Render Your Notebook**
Run the following command in your terminal:
```
quarto render statistical-experimentation.qmd
```
