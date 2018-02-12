

```python
# Import the Pandas library
import pandas as pd
import os
import numpy

```


```python

# Create a reference the JSON file desired
json_path = "HW/purchase_data.json"

# Read the JSON into a Pandas DataFrame
Heroes_df = pd.read_json(json_path)

# Print the first five rows of data to the screen
Heroes_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>




```python
total_count = Heroes_df['SN'].nunique()
print("Total Players = "  + str(total_count))
```

    Total Players = 573



```python
Unique_items = pd.DataFrame(Heroes_df['Item ID'].unique())
unique = Unique_items.count()
```


```python
avg_purchase_price = Heroes_df['Price'].mean()
tot_purchases = Heroes_df['Item ID'].count()
Total_Revenue = Heroes_df['Price'].sum()
print("Items = " + str(total_count))
print("Average Purchase Price = " + str(avg_purchase_price))
print("Total Purchases = " + str(tot_purchases))
print("Total Revenue = " + str(Total_Revenue))

```

    Items = 573
    Average Purchase Price = 2.931192307692303
    Total Purchases = 780
    Total Revenue = 2286.3299999999963



```python
test = Heroes_df.groupby('Gender').agg({'SN':'count'})
gender_df = pd.DataFrame(test)
gender_df['Percentage of Players'] = gender_df['SN'] / gender_df['SN'].sum() * 100
gender_re = gender_df.rename(columns = {'SN': 'Total_Count'})
print(str(gender_re))
```

                           Total_Count  Percentage of Players
    Gender                                                   
    Female                         136              17.435897
    Male                           633              81.153846
    Other / Non-Disclosed           11               1.410256



```python
gender_table = Heroes_df.groupby('Gender').agg({'Price': ['count','sum','mean']})
gender_df = pd.DataFrame(gender_table)
gender_df.rename(columns={'count':'Purchase Count'})
gender_df = gender_df.xs('Price',axis=1,drop_level=True)
#gender_df['Normalized Totals'] = gender_df['sum'] / gender_df['count'] 
test = gender_df.rename(columns={'count':'Purchase Count','sum':'Total Purchase Value','mean':'Average Purchase Price'}) 
test['Normalized Totals'] = test['Total Purchase Value'] / test['Purchase Count'] 
print(test)
```

                           Purchase Count  Total Purchase Value  \
    Gender                                                        
    Female                            136                382.91   
    Male                              633               1867.68   
    Other / Non-Disclosed              11                 35.74   
    
                           Average Purchase Price  Normalized Totals  
    Gender                                                            
    Female                               2.815515           2.815515  
    Male                                 2.950521           2.950521  
    Other / Non-Disclosed                3.249091           3.249091  



```python


bins = [0,10, 14, 19, 24, 29,34,39,100]
labels = ['<10','10-14','15-19','20-24','25-29','30-34','35-39','40+']
Heroes_df['Binned'] = pd.cut(Heroes_df['Age'],bins=bins, labels=labels)
Heroes_df_bin = Heroes_df.groupby('Binned').agg({'SN':'count'})
binn_df = pd.DataFrame(Heroes_df_bin)
binn_df['Percentage of Players'] = binn_df['SN'] / binn_df['SN'].sum() * 100
binn_final = binn_df.rename(columns = {'SN': 'Total_Count'})
print(binn_final)

```

            Total_Count  Percentage of Players
    Binned                                    
    <10              32               4.102564
    10-14            31               3.974359
    15-19           133              17.051282
    20-24           336              43.076923
    25-29           125              16.025641
    30-34            64               8.205128
    35-39            42               5.384615
    40+              17               2.179487



```python
binn_table = Heroes_df.groupby('Binned').agg({'Price': ['count','sum','mean']})
binn2_df = pd.DataFrame(binn_table)
binn2_df = binn2_df.xs('Price',axis=1,drop_level=True)
#gender_df['Normalized Totals'] = gender_df['sum'] / gender_df['count'] 
binn_tst = binn2_df.rename(columns={'count':'Purchase Count','sum':'Total Purchase Value','mean':'Average Purchase Price'}) 
binn_tst['Normalized Totals'] = binn_tst['Total Purchase Value'] / binn_tst['Purchase Count'] 
print(binn_tst)
```

            Purchase Count  Total Purchase Value  Average Purchase Price  \
    Binned                                                                 
    <10                 32                 96.62                3.019375   
    10-14               31                 83.79                2.702903   
    15-19              133                386.42                2.905414   
    20-24              336                978.77                2.913006   
    25-29              125                370.33                2.962640   
    30-34               64                197.25                3.082031   
    35-39               42                119.40                2.842857   
    40+                 17                 53.75                3.161765   
    
            Normalized Totals  
    Binned                     
    <10              3.019375  
    10-14            2.702903  
    15-19            2.905414  
    20-24            2.913006  
    25-29            2.962640  
    30-34            3.082031  
    35-39            2.842857  
    40+              3.161765  



```python
players = Heroes_df.groupby('SN').agg({'Price': ['count','sum','mean']})
tot_df = pd.DataFrame(players)
tot_df = tot_df.xs('Price',axis=1,drop_level=True)
tot_tst = tot_df.rename(columns={'count':'Purchase Count','sum':'Total Purchase Value','mean':'Average Purchase Price'}) 
tot_test = tot_tst.sort_values('Total Purchase Value', ascending=False)
print(tot_test.head())
```

                 Purchase Count  Total Purchase Value  Average Purchase Price
    SN                                                                       
    Undirrala66               5                 17.06                3.412000
    Saedue76                  4                 13.56                3.390000
    Mindimnya67               4                 12.74                3.185000
    Haellysu29                3                 12.73                4.243333
    Eoda93                    3                 11.58                3.860000



```python
item_group = Heroes_df.groupby(['Item ID','Item Name']).agg({'Price': ['count','mean','sum']})
item_df = pd.DataFrame(item_group)
item_df = item_df.xs('Price',axis=1,drop_level=True)
item_tst = item_df.rename(columns={'count':'Purchase Count','sum':'Total Purchase Value','mean':'Item Price'}) 
item_test = item_tst.sort_values('Purchase Count', ascending=False)
print(str(item_test.head()))
```

                                                  Purchase Count  Item Price  \
    Item ID Item Name                                                          
    39      Betrayal, Whisper of Grieving Widows              11        2.35   
    84      Arcane Gem                                        11        2.23   
    31      Trickster                                          9        2.07   
    175     Woeful Adamantite Claymore                         9        1.24   
    13      Serenity                                           9        1.49   
    
                                                  Total Purchase Value  
    Item ID Item Name                                                   
    39      Betrayal, Whisper of Grieving Widows                 25.85  
    84      Arcane Gem                                           24.53  
    31      Trickster                                            18.63  
    175     Woeful Adamantite Claymore                           11.16  
    13      Serenity                                             13.41  



```python
item_group = Heroes_df.groupby(['Item ID','Item Name']).agg({'Price': ['count','mean','sum']})
item_df = pd.DataFrame(item_group)
item_df = item_df.xs('Price',axis=1,drop_level=True)
item_tst = item_df.rename(columns={'count':'Purchase Count','sum':'Total Purchase Value','mean':'Item Price'}) 
item_test = item_tst.sort_values('Total Purchase Value', ascending=False)
print(str(item_test.head()))
```

                                        Purchase Count  Item Price  \
    Item ID Item Name                                                
    34      Retribution Axe                          9        4.14   
    115     Spectral Diamond Doomblade               7        4.25   
    32      Orenmir                                  6        4.95   
    103     Singed Scalpel                           6        4.87   
    107     Splitter, Foe Of Subtlety                8        3.61   
    
                                        Total Purchase Value  
    Item ID Item Name                                         
    34      Retribution Axe                            37.26  
    115     Spectral Diamond Doomblade                 29.75  
    32      Orenmir                                    29.70  
    103     Singed Scalpel                             29.22  
    107     Splitter, Foe Of Subtlety                  28.88  

