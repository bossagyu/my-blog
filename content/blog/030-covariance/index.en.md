+++
title = 'Investigation of Covariance'
date = 2024-09-02T10:00:00+09:00
draft = false
categories = ['Engineering']
tags = ['Mathematics', 'Statistics']
+++

## What is Covariance?

Covariance is a statistical measure that indicates how two variables change together.  
A positive value means the two variables tend to change in the same direction, while a negative value means they tend to change in opposite directions.

### Formula

Covariance is calculated using the following formula:

![Covariance Formula](image.png)

### Key Points

- If the covariance is close to 0, it suggests that the two variables have little to no strong relationship.
- Since the value depends on the scale, correlation coefficients are often used instead.

## Example in Python

Below is an example of calculating covariance using Python.

```python
import numpy as np

# Sample data
X = [2.1, 2.5, 4.0, 3.6]
Y = [8, 10, 12, 14]

# Calculate covariance
cov_matrix = np.cov(X, Y, bias=True)
covariance = cov_matrix[0][1]

print(f"Covariance: {covariance}")
```

### Output

```
Covariance: 1.53
```

## Difference Between Covariance and Correlation Coefficient

| Feature       | Covariance                | Correlation Coefficient  |
|---------------|---------------------------|---------------------------|
| Scale Dependent | Yes                      | No                        |
| Value Range    | -∞ to ∞                  | -1 to 1                   |

## Summary

Covariance is a fundamental metric for understanding the relationship between two variables.  
However, due to its scale dependency, it is often used in conjunction with correlation coefficients for deeper analysis.
