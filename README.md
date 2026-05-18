# Ex.No: 02 LINEAR AND POLYNOMIAL TREND ESTIMATION
Date:16-05-2026
### AIM:
To Implement Linear and Polynomial Trend Estiamtion Using Python.

### ALGORITHM:
### Step-1:
Import necessary libraries (NumPy, Matplotlib)
### Step-2:
Load the dataset
### Step-3:
Calculate the linear trend values using least square method
### Step-4:
Calculate the polynomial trend values using least square method
### Step-5:
End the program

### PROGRAM:
A - LINEAR TREND ESTIMATION
B- POLYNOMIAL TREND ESTIMATION
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Load dataset
df = pd.read_csv("wb_commodity_price_intelligence_1960_2026.csv")

# Clean column names
df.columns = df.columns.str.strip().str.lower()

# Create proper datetime column from year + month
df['date'] = pd.to_datetime(
    df['year'].astype(str) + '-' + df['month'].astype(str) + '-01'
)

# OPTIONAL:
# Filter one commodity only
commodity = "Aluminum"
df = df[df['commodity_name'] == commodity]

# Set datetime index
df.set_index('date', inplace=True)

# Use actual price column
df = df[['price_nominal_usd']]

# Resample yearly average
resampled_data = df.resample('YE').mean()

# Convert index to year integer
resampled_data.index = resampled_data.index.year

# Reset index
resampled_data.reset_index(inplace=True)

# Rename columns
resampled_data.rename(columns={
    'date': 'Year',
    'price_nominal_usd': 'Price'
}, inplace=True)

# Prepare variables
years = resampled_data['date'].tolist() \
    if 'date' in resampled_data.columns \
    else resampled_data.iloc[:,0].tolist()

price = resampled_data['Price'].tolist()

# Center X values
X = [i - years[len(years)//2] for i in years]

n = len(X)

if n < 2:
    print("Not enough data for trend analysis")

else:

    # Linear Trend (Manual)
    x2 = [i**2 for i in X]
    xy = [i*j for i, j in zip(X, price)]

    b = (n*sum(xy) - sum(price)*sum(X)) / \
        (n*sum(x2) - (sum(X)**2))

    a = (sum(price) - b*sum(X)) / n

    linear_trend = [a + b*x for x in X]

    # Polynomial Trend
    coeffs = np.polyfit(X, price, 2)

    a_poly, b_poly, c_poly = coeffs

    poly_trend = [
        a_poly*(x**2) + b_poly*x + c_poly
        for x in X
    ]

    # Print equations
    print(f"Linear Trend: y = {a:.2f} + {b:.2f}x")
    print(
        f"Polynomial Trend: "
        f"y = {a_poly:.4f}x² + {b_poly:.4f}x + {c_poly:.2f}"
    )

    # Add trends
    resampled_data['Linear Trend'] = linear_trend
    resampled_data['Polynomial Trend'] = poly_trend

    # Set plotting index
    resampled_data.set_index(
        resampled_data.columns[0],
        inplace=True
    )

    # Plot
    plt.figure(figsize=(12,6))

    plt.plot(
        resampled_data.index,
        resampled_data['Price'],
        marker='o',
        label='Actual Price'
    )

    plt.plot(
        resampled_data.index,
        resampled_data['Linear Trend'],
        linestyle='--',
        linewidth=2,
        label='Linear Trend'
    )

    plt.plot(
        resampled_data.index,
        resampled_data['Polynomial Trend'],
        linewidth=2,
        label='Polynomial Trend'
    )

    plt.title(f'{commodity} Price Trend Analysis')
    plt.xlabel('Year')
    plt.ylabel('Price (USD)')
    plt.legend()
    plt.grid(True)

    plt.show()
```
### OUTPUT
A - LINEAR TREND ESTIMATION
B- POLYNOMIAL TREND ESTIMATION
<img width="796" height="473" alt="image" src="https://github.com/user-attachments/assets/1a33bfee-9316-4fed-9fd5-692893906226" />

### RESULT:
Thus the python program for linear and Polynomial Trend Estiamtion has been executed successfully.
