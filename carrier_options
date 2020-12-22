%matplotlib inline
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import seaborn as sns

sns.set(
    rc={'figure.figsize': (7,5)},
    style='whitegrid'
)
sns.set_style("ticks")

## Segments a dataframe into appropriate bins and returns the altered dataframe.
def segment_df(df):
    bins = [200, 500, 1000, 1500, 2500]
    total_shipments = []
    total_freight = []
    total_discount = []
    total_discount_per = []
    total_fuel = []
    total_fuel_per = []
    n = 0
    while n < len(bins):
        if n == 0:
            shipments = df.loc[:bins[n]]["Shipments"].sum()
            freight = df.loc[:bins[n]]["BuyFreight"].sum()
            discount = df.loc[:bins[n]]["BuyDiscount"].sum()
            discount_per = round((abs(discount) / freight) * 100, 2)
            fuel = df.loc[:bins[n]]["BuyFule"].sum()
            fuel_per = round((fuel / freight) * 100, 2)
        elif n == len(bins) - 1:
            shipments = df.loc[bins[n]+1:]["Shipments"].sum()
            freight = df.loc[bins[n]+1:]["BuyFreight"].sum()
            discount = df.loc[bins[n]+1:]["BuyDiscount"].sum()
            discount_per = round((abs(discount) / freight) * 100, 2)
            fuel = df.loc[bins[n]+1:]["BuyFule"].sum()
            fuel_per = round((fuel / freight) * 100, 2)
        else:
            shipments = df.loc[bins[n-1]+1:bins[n]]["Shipments"].sum()
            freight = df.loc[bins[n-1]+1:bins[n]]["BuyFreight"].sum()
            discount = df.loc[bins[n-1]+1:bins[n]]["BuyDiscount"].sum()
            discount_per = round((abs(discount) / freight) * 100, 2)
            fuel = df.loc[bins[n-1]+1:bins[n]]["BuyFule"].sum()
            fuel_per = round((fuel / freight) * 100, 2)
        total_shipments.append(shipments)
        total_freight.append(freight)
        total_discount.append(discount)
        total_discount_per.append(discount_per)
        total_fuel.append(fuel)
        total_fuel_per.append(fuel_per)
        n = n + 1
    
    weight_bins_df = pd.DataFrame({'Shipments': total_shipments,'Freight': total_freight, 'Discount': total_discount, 'Discount (%)': total_discount_per, 
                                   'Fuel': total_fuel, 'Fuel (%)':  total_fuel_per},
                                  index=[f"0-{bins[0]}", f"{bins[0]}-{bins[1]}", f"{bins[1]}-{bins[2]}", f"{bins[2]}-{bins[3]}", f"{bins[4]}-All"])
    
    return weight_bins_df
    

## filter through carrier, n=1 for discount and n=2 for fuel.
def carrier_op(carrier, n):
    
    shipment = pd.read_csv('FreightShipmentDetails(All).csv')
    shipment['miles'] = shipment['miles'].astype('float64')
    shipment["Class"].fillna("50", inplace=True)

    shipment.head(10)

    shipment["PickupDate"] = pd.to_datetime(shipment["PickupDate"])
    if carrier in shipment["CarrierCode"].values:
        exla_shipment = shipment[shipment["CarrierCode"] == carrier]
        exla = exla_shipment.groupby("TOTALWEIGHT").aggregate({'TOTALWEIGHT': 'count', 'BuyFreight': 'sum', 
                                                        'BuyDiscount': 'sum', 'BuyFule': 'sum'})
        exla.rename(columns={'TOTALWEIGHT': 'Shipments'}, inplace=True)

        weight_bins = segment_df(exla)   ## calling the above function
        weight_bins = weight_bins.reset_index().rename(columns={'index': 'Weights'})
        weight_bins.set_index('Weights', inplace=True)


        if n == 1:
            fig, ax = plt.subplots(figsize=(13, 9))
            weight_bins["Shipments"].plot(kind='bar', color='tab:blue', width=0.4, position=1 ,ax=ax, rot=0, label='No. of Shipments')
            ax1 = ax.twinx()
            weight_bins["Discount (%)"].plot(kind='bar', color='tab:orange', width=0.4, position=0 ,ax=ax1, rot=0, label='Discount')
            ax.set_ylabel('Shipments');
            ax1.set_ylabel('Discount (%)');
            ax.set_title(carrier)
            ax.legend(loc=2);
            ax1.legend();
           # plt.savefig('Charts-2/d3.png');

        if n == 2:
            fig, ax = plt.subplots(figsize=(13, 9))
            weight_bins["Shipments"].plot(kind='bar', color='tab:blue', width=0.4, position=1 ,ax=ax, rot=0, label='No. of Shipments')
            ax1 = ax.twinx()
            weight_bins["Fuel (%)"].plot(kind='bar', color='tab:orange', width=0.4, position=0 ,ax=ax1, rot=0, label='Fuel')
            ax.set_ylabel('Shipments');
            ax1.set_ylabel('Fuel (%)');
            ax.set_title(carrier)
            ax.legend(loc=2);
            ax1.legend();
           # plt.savefig('Charts-2/f3.png');
    else:
        print("No such carrier found")

## Example
carrier_op("AVRT", 1) # discount
carrier_op("AVRT", 2) # fuel
