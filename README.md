# ==========================================
# TASK 3 - Marketing Funnel & Conversion Analysis
# ==========================================

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import os
import random

# ------------------------------------------
# Step 1: Create Dataset Folder
# ------------------------------------------
os.makedirs("data", exist_ok=True)
file_path = "data/marketing_funnel_data.csv"

# ------------------------------------------
# Step 2: Generate Dataset (if not exists)
# ------------------------------------------
if not os.path.exists(file_path):

    np.random.seed(42)

    channels = ["Social Media", "Google Ads", "Email", "Organic Search"]

    data = []

    for channel in channels:
        leads = random.randint(5000, 10000)
        prospects = int(leads * random.uniform(0.4, 0.7))
        qualified = int(prospects * random.uniform(0.5, 0.8))
        customers = int(qualified * random.uniform(0.4, 0.7))

        data.append([channel, leads, prospects, qualified, customers])

    df_create = pd.DataFrame(data, columns=[
        "Channel", "Leads", "Prospects", "Qualified_Leads", "Customers"
    ])

    df_create.to_csv(file_path, index=False)
    print("Marketing funnel dataset created!")

# ------------------------------------------
# Step 3: Load Dataset
# ------------------------------------------
df = pd.read_csv(file_path)

# Clean column names
df.columns = df.columns.str.strip().str.replace(" ", "_")

print("\nDataset Preview:\n", df)

# ------------------------------------------
# KPI 1: Overall Funnel Totals
# ------------------------------------------
total_leads = df["Leads"].sum()
total_prospects = df["Prospects"].sum()
total_qualified = df["Qualified_Leads"].sum()
total_customers = df["Customers"].sum()

print("\nTotal Leads:", total_leads)
print("Total Prospects:", total_prospects)
print("Total Qualified Leads:", total_qualified)
print("Total Customers:", total_customers)

# ------------------------------------------
# KPI 2: Conversion Rates
# ------------------------------------------
conversion_leads_to_prospects = (total_prospects / total_leads) * 100
conversion_prospects_to_qualified = (total_qualified / total_prospects) * 100
conversion_qualified_to_customers = (total_customers / total_qualified) * 100
overall_conversion = (total_customers / total_leads) * 100

print("\nConversion Rates:")
print("Leads → Prospects: {:.2f}%".format(conversion_leads_to_prospects))
print("Prospects → Qualified: {:.2f}%".format(conversion_prospects_to_qualified))
print("Qualified → Customers: {:.2f}%".format(conversion_qualified_to_customers))
print("Overall Conversion Rate: {:.2f}%".format(overall_conversion))

# ------------------------------------------
# KPI 3: Funnel Visualization
# ------------------------------------------
funnel_stages = ["Leads", "Prospects", "Qualified Leads", "Customers"]
funnel_values = [total_leads, total_prospects, total_qualified, total_customers]

plt.figure()
plt.bar(funnel_stages, funnel_values)
plt.title("Marketing Funnel Overview")
plt.xlabel("Funnel Stage")
plt.ylabel("Count")
plt.show()

# ------------------------------------------
# KPI 4: Channel Performance
# ------------------------------------------
df["Conversion_Rate_%"] = (df["Customers"] / df["Leads"]) * 100

print("\nChannel-wise Conversion Rates:\n", df[["Channel", "Conversion_Rate_%"]])

plt.figure()
plt.bar(df["Channel"], df["Conversion_Rate_%"])
plt.title("Channel-wise Conversion Rate")
plt.xlabel("Channel")
plt.ylabel("Conversion Rate (%)")
plt.xticks(rotation=45)
plt.show()

# ------------------------------------------
# KPI 5: Identify Biggest Drop-off
# ------------------------------------------
drop_off_1 = 100 - conversion_leads_to_prospects
drop_off_2 = 100 - conversion_prospects_to_qualified
drop_off_3 = 100 - conversion_qualified_to_customers

drop_offs = {
    "Leads → Prospects": drop_off_1,
    "Prospects → Qualified": drop_off_2,
    "Qualified → Customers": drop_off_3
}

biggest_drop = max(drop_offs, key=drop_offs.get)

print("\nBiggest Drop-off Stage:", biggest_drop)

print("\nTask 3 Analysis Completed Successfully!")
