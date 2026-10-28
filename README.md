import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Load dataset
df = pd.read_csv("online_retail.csv", encoding="latin-1")

# Clean data
df = df.dropna(subset=["Description", "CustomerID", "InvoiceDate"])
df["InvoiceDate"] = pd.to_datetime(df["InvoiceDate"])
df = df[df["Quantity"] > 0]
df["TotalSales"] = df["Quantity"] * df["UnitPrice"]

# Add month column
df["Month"] = df["InvoiceDate"].dt.to_period("M").astype(str)

# Total revenue
monthly_sales = df.groupby("Month")["TotalSales"].sum().reset_index()

# Top products
top_products = df.groupby("Description")["TotalSales"].sum().sort_values(ascending=False).head(10)

# Country-wise revenue
country_sales = df.groupby("Country")["TotalSales"].sum().sort_values(ascending=False)

# Visualizations
plt.figure(figsize=(12, 6))
sns.lineplot(data=monthly_sales, x="Month", y="TotalSales")
plt.xticks(rotation=45)
plt.title("Monthly Sales Trend")
plt.show()

plt.figure(figsize=(10, 6))
top_products.plot(kind="bar")
plt.title("Top 10 Products by Revenue")
plt.ylabel("Revenue")
plt.show()

plt.figure(figsize=(10, 8))
country_sales.head(10).plot(kind="barh", color="skyblue")
plt.title("Top Countries by Sales")
plt.xlabel("Revenue")
plt.show()
