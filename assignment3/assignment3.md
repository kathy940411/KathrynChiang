
# Assignment 3

The US Department of Agriculture publishes price estimates for fruits and vegetables [online](https://www.ers.usda.gov/data-products/fruit-and-vegetable-prices/fruit-and-vegetable-prices/). The most recent estimates are based on a 2013 survey of US retail stores.

The estimates are provided as a collection of MS Excel files, with one file per fruit or vegetable. The `assignment3_data.zip` file contains the fruit and vegetable files in the directories `fruit` and `vegetables`, respectively.

__Exercise 1.1.__ Use pandas to extract the "Fresh" row(s) from the <strong style="color:#B0B">fruit</strong> Excel files. Combine the data into a single data frame. Your data frame should look something like this:

type       | food       | form   | price_per_lb | yield | lb_per_cup | price_per_cup
---------- | ---------- | ------ | ------------ | ----- | ---------- | -------------
fruit      | watermelon | Fresh1 | 0.333412     | 0.52  | 0.330693   | 0.212033
fruit      | cantaloupe | Fresh1 | 0.535874     | 0.51  | 0.374786   | 0.3938
vegetables | onions     | Fresh1 | 1.03811      | 0.9   | 0.35274    | 0.406868
...        |            |        |              |       |            |


It's okay if the rows and columns of your data frame are in a different order. These modules are especially relevant:

* [`str` methods](https://docs.python.org/2/library/stdtypes.html#string-methods)
* [`os`](https://docs.python.org/2/library/os.html)
* [`os.path`](https://docs.python.org/2/library/os.path.html)
* [pandas](http://pandas.pydata.org/pandas-docs/stable/): `read_excel()`, `concat()`, `.fillna()`, `.str`, plotting methods

Ask questions and search the documentation/web to find the functions you need.



```python
from matplotlib import pyplot as plt
import matplotlib
matplotlib.style.use('ggplot')
import os
import os.path
import warnings
import pandas as pd
import numpy as np
path = "/Users/KathrynChiang/Downloads/assignment3_data/fruit"
def get_fresh(path):
    """Input: path to get the files 
    Note: no / at the end of the path
    Output: extract all the Fresh1 and Fresh row to a DataFrame 
    """
    files = [os.path.join(path,fname) for fname in os.listdir(path)]
    file_name = [fname for fname in os.listdir(path)]

    fruit_name = []
    for i in range(len(file_name)):
        fruit_name.append(file_name[i].split('.xlsx')[0])

    infile = os.path.basename(path)
    fresh = pd.DataFrame() #empty dataframe

    for i in range(len(files)):
        f = pd.read_excel(files[i])

        s = pd.Series(f[list(f)[0]])
        sw = s.loc[s.str.startswith('Fresh', na = False)]
        index = []
        for j in sw.index.values:
            index.append(j)
        for k in index:
            fresh1 = f.loc[f[list(f)[0]] == str(f[list(f)[0]][k])]
            fresh1 = fresh1.rename(columns = {f.columns[0]:'form'})
            fresh1['food'] = fruit_name[i]
            fresh1.insert(0, 'type', infile)
            fresh = fresh.append(fresh1,ignore_index = True)
      
        """fresh2 = f.loc[f[list(f)[0]] == ('Fresh')]
        fresh2 = fresh2.rename(columns = {f.columns[0]:'form'})
        fresh2['food'] = fruit_name[i]
        fresh2.insert(0, 'type', infile)
        fresh = fresh.append(fresh2,ignore_index = True)
       """ 
    fresh = fresh.rename(columns = {'Unnamed: 1':'price_per_lb', 
                         'Unnamed: 3':'yield',
                         'Unnamed: 4':'lb_per_cup',
                         'Unnamed: 6':'price_per_cup'}) #rename colnames

    fresh = fresh.drop('Unnamed: 5', axis =1) #remove unwanted cols.
    fresh = fresh.drop('Unnamed: 2', axis =1)

    cols = ['type','food','form','price_per_lb','yield','lb_per_cup','price_per_cup']
    fresh = fresh[cols]

    return fresh
get_fresh(path).head(5)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>food</th>
      <th>form</th>
      <th>price_per_lb</th>
      <th>yield</th>
      <th>lb_per_cup</th>
      <th>price_per_cup</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>fruit</td>
      <td>apples</td>
      <td>Fresh1</td>
      <td>1.56752</td>
      <td>0.9</td>
      <td>0.242508</td>
      <td>0.422373</td>
    </tr>
    <tr>
      <th>1</th>
      <td>fruit</td>
      <td>apricots</td>
      <td>Fresh1</td>
      <td>3.04007</td>
      <td>0.93</td>
      <td>0.363763</td>
      <td>1.1891</td>
    </tr>
    <tr>
      <th>2</th>
      <td>fruit</td>
      <td>bananas</td>
      <td>Fresh1</td>
      <td>0.566983</td>
      <td>0.64</td>
      <td>0.330693</td>
      <td>0.292965</td>
    </tr>
    <tr>
      <th>3</th>
      <td>fruit</td>
      <td>blackberries</td>
      <td>Fresh1</td>
      <td>5.77471</td>
      <td>0.96</td>
      <td>0.31967</td>
      <td>1.92292</td>
    </tr>
    <tr>
      <th>4</th>
      <td>fruit</td>
      <td>blueberries</td>
      <td>Fresh1</td>
      <td>4.73462</td>
      <td>0.95</td>
      <td>0.31967</td>
      <td>1.59318</td>
    </tr>
  </tbody>
</table>
</div>



__Exercise 1.2.__ Reuse your code from exercise 1.1 to extract the "Fresh" row(s) from the <strong style="color:#B0B">vegetable</strong> Excel files.

Does your code produce the correct prices for tomatoes? If not, why not? Do any other files have the same problem as the tomatoes file?

You don't need to extract the prices for these problem files. However, make sure the prices are extracted for files like asparagus that don't have this problem.


```python
path = "/Users/KathrynChiang/Downloads/assignment3_data/vegetables"
print "There are no prices for tomatoes.\nIn the tomato file, the Fresh row does not have any values, so it will return NaA or an empty cell in stead of some numbers.\nYes, carrots, cauliflower, celery, lettuce_romaine, mushrooms, and spinach files have the same problem as the tomatoes file."
get_fresh(path).head(5)
```

    There are no prices for tomatoes.
    In the tomato file, the Fresh row does not have any values, so it will return NaA or an empty cell in stead of some numbers.
    Yes, carrots, cauliflower, celery, lettuce_romaine, mushrooms, and spinach files have the same problem as the tomatoes file.





<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>food</th>
      <th>form</th>
      <th>price_per_lb</th>
      <th>yield</th>
      <th>lb_per_cup</th>
      <th>price_per_cup</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>vegetables</td>
      <td>acorn_squash</td>
      <td>Fresh1</td>
      <td>1.17225</td>
      <td>0.458554</td>
      <td>0.451948</td>
      <td>1.15536</td>
    </tr>
    <tr>
      <th>1</th>
      <td>vegetables</td>
      <td>artichoke</td>
      <td>Fresh1</td>
      <td>2.21305</td>
      <td>0.375309</td>
      <td>0.385809</td>
      <td>2.27497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>vegetables</td>
      <td>asparagus</td>
      <td>Fresh1</td>
      <td>3.21349</td>
      <td>0.493835</td>
      <td>0.396832</td>
      <td>2.58227</td>
    </tr>
    <tr>
      <th>3</th>
      <td>vegetables</td>
      <td>avocados</td>
      <td>Fresh1</td>
      <td>2.23587</td>
      <td>0.740753</td>
      <td>0.31967</td>
      <td>0.964886</td>
    </tr>
    <tr>
      <th>4</th>
      <td>vegetables</td>
      <td>broccoli</td>
      <td>Fresh</td>
      <td></td>
      <td></td>
      <td></td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



__Exercise 1.3.__ Remove rows without a price from the vegetable data frame and then combine the fruit and vegetable data frames. Make sure all columns of numbers are numeric (not strings).


```python
fruit_path = "/Users/KathrynChiang/Downloads/assignment3_data/fruit"
vege_path = "/Users/KathrynChiang/Downloads/assignment3_data/vegetables"
fruit = get_fresh(fruit_path)
veget = get_fresh(vege_path)
veget = veget.dropna()
fruit_n_vege = fruit.append(veget,ignore_index=True)
fruit_n_vege
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>food</th>
      <th>form</th>
      <th>price_per_lb</th>
      <th>yield</th>
      <th>lb_per_cup</th>
      <th>price_per_cup</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>fruit</td>
      <td>apples</td>
      <td>Fresh1</td>
      <td>1.56752</td>
      <td>0.9</td>
      <td>0.242508</td>
      <td>0.422373</td>
    </tr>
    <tr>
      <th>1</th>
      <td>fruit</td>
      <td>apricots</td>
      <td>Fresh1</td>
      <td>3.04007</td>
      <td>0.93</td>
      <td>0.363763</td>
      <td>1.1891</td>
    </tr>
    <tr>
      <th>2</th>
      <td>fruit</td>
      <td>bananas</td>
      <td>Fresh1</td>
      <td>0.566983</td>
      <td>0.64</td>
      <td>0.330693</td>
      <td>0.292965</td>
    </tr>
    <tr>
      <th>3</th>
      <td>fruit</td>
      <td>blackberries</td>
      <td>Fresh1</td>
      <td>5.77471</td>
      <td>0.96</td>
      <td>0.31967</td>
      <td>1.92292</td>
    </tr>
    <tr>
      <th>4</th>
      <td>fruit</td>
      <td>blueberries</td>
      <td>Fresh1</td>
      <td>4.73462</td>
      <td>0.95</td>
      <td>0.31967</td>
      <td>1.59318</td>
    </tr>
    <tr>
      <th>5</th>
      <td>fruit</td>
      <td>cantaloupe</td>
      <td>Fresh1</td>
      <td>0.535874</td>
      <td>0.51</td>
      <td>0.374786</td>
      <td>0.3938</td>
    </tr>
    <tr>
      <th>6</th>
      <td>fruit</td>
      <td>cherries</td>
      <td>Fresh1</td>
      <td>3.59299</td>
      <td>0.92</td>
      <td>0.341717</td>
      <td>1.33455</td>
    </tr>
    <tr>
      <th>7</th>
      <td>fruit</td>
      <td>grapefruit</td>
      <td>Fresh1</td>
      <td>0.897802</td>
      <td>0.49</td>
      <td>0.462971</td>
      <td>0.848278</td>
    </tr>
    <tr>
      <th>8</th>
      <td>fruit</td>
      <td>grapes</td>
      <td>Fresh1</td>
      <td>2.09383</td>
      <td>0.96</td>
      <td>0.330693</td>
      <td>0.721266</td>
    </tr>
    <tr>
      <th>9</th>
      <td>fruit</td>
      <td>honeydew</td>
      <td>Fresh1</td>
      <td>0.796656</td>
      <td>0.46</td>
      <td>0.374786</td>
      <td>0.649077</td>
    </tr>
    <tr>
      <th>10</th>
      <td>fruit</td>
      <td>kiwi</td>
      <td>Fresh1</td>
      <td>2.04468</td>
      <td>0.76</td>
      <td>0.385809</td>
      <td>1.03797</td>
    </tr>
    <tr>
      <th>11</th>
      <td>fruit</td>
      <td>mangoes</td>
      <td>Fresh1</td>
      <td>1.37756</td>
      <td>0.71</td>
      <td>0.363763</td>
      <td>0.705783</td>
    </tr>
    <tr>
      <th>12</th>
      <td>fruit</td>
      <td>nectarines</td>
      <td>Fresh1</td>
      <td>1.76115</td>
      <td>0.91</td>
      <td>0.31967</td>
      <td>0.618667</td>
    </tr>
    <tr>
      <th>13</th>
      <td>fruit</td>
      <td>oranges</td>
      <td>Fresh1</td>
      <td>1.03517</td>
      <td>0.73</td>
      <td>0.407855</td>
      <td>0.578357</td>
    </tr>
    <tr>
      <th>14</th>
      <td>fruit</td>
      <td>papaya</td>
      <td>Fresh1</td>
      <td>1.29801</td>
      <td>0.62</td>
      <td>0.308647</td>
      <td>0.646174</td>
    </tr>
    <tr>
      <th>15</th>
      <td>fruit</td>
      <td>peaches</td>
      <td>Fresh1</td>
      <td>1.59119</td>
      <td>0.96</td>
      <td>0.341717</td>
      <td>0.56639</td>
    </tr>
    <tr>
      <th>16</th>
      <td>fruit</td>
      <td>pears</td>
      <td>Fresh1</td>
      <td>1.46157</td>
      <td>0.9</td>
      <td>0.363763</td>
      <td>0.59074</td>
    </tr>
    <tr>
      <th>17</th>
      <td>fruit</td>
      <td>pineapple</td>
      <td>Fresh1</td>
      <td>0.627662</td>
      <td>0.51</td>
      <td>0.363763</td>
      <td>0.447686</td>
    </tr>
    <tr>
      <th>18</th>
      <td>fruit</td>
      <td>plums</td>
      <td>Fresh1</td>
      <td>1.82742</td>
      <td>0.94</td>
      <td>0.363763</td>
      <td>0.707176</td>
    </tr>
    <tr>
      <th>19</th>
      <td>fruit</td>
      <td>pomegranate</td>
      <td>Fresh1</td>
      <td>2.17359</td>
      <td>0.56</td>
      <td>0.341717</td>
      <td>1.32634</td>
    </tr>
    <tr>
      <th>20</th>
      <td>fruit</td>
      <td>raspberries</td>
      <td>Fresh1</td>
      <td>6.97581</td>
      <td>0.96</td>
      <td>0.31967</td>
      <td>2.32287</td>
    </tr>
    <tr>
      <th>21</th>
      <td>fruit</td>
      <td>strawberries</td>
      <td>Fresh1</td>
      <td>2.35881</td>
      <td>0.94</td>
      <td>0.31967</td>
      <td>0.802171</td>
    </tr>
    <tr>
      <th>22</th>
      <td>fruit</td>
      <td>tangerines</td>
      <td>Fresh1</td>
      <td>1.37796</td>
      <td>0.74</td>
      <td>0.407855</td>
      <td>0.759471</td>
    </tr>
    <tr>
      <th>23</th>
      <td>fruit</td>
      <td>watermelon</td>
      <td>Fresh1</td>
      <td>0.333412</td>
      <td>0.52</td>
      <td>0.330693</td>
      <td>0.212033</td>
    </tr>
    <tr>
      <th>24</th>
      <td>vegetables</td>
      <td>acorn_squash</td>
      <td>Fresh1</td>
      <td>1.17225</td>
      <td>0.458554</td>
      <td>0.451948</td>
      <td>1.15536</td>
    </tr>
    <tr>
      <th>25</th>
      <td>vegetables</td>
      <td>artichoke</td>
      <td>Fresh1</td>
      <td>2.21305</td>
      <td>0.375309</td>
      <td>0.385809</td>
      <td>2.27497</td>
    </tr>
    <tr>
      <th>26</th>
      <td>vegetables</td>
      <td>asparagus</td>
      <td>Fresh1</td>
      <td>3.21349</td>
      <td>0.493835</td>
      <td>0.396832</td>
      <td>2.58227</td>
    </tr>
    <tr>
      <th>27</th>
      <td>vegetables</td>
      <td>avocados</td>
      <td>Fresh1</td>
      <td>2.23587</td>
      <td>0.740753</td>
      <td>0.31967</td>
      <td>0.964886</td>
    </tr>
    <tr>
      <th>28</th>
      <td>vegetables</td>
      <td>brussels_sprouts</td>
      <td>Fresh1</td>
      <td>2.76355</td>
      <td>1.06</td>
      <td>0.341717</td>
      <td>0.890898</td>
    </tr>
    <tr>
      <th>29</th>
      <td>vegetables</td>
      <td>butternut_squash</td>
      <td>Fresh1</td>
      <td>1.24474</td>
      <td>0.714</td>
      <td>0.451948</td>
      <td>0.787893</td>
    </tr>
    <tr>
      <th>30</th>
      <td>vegetables</td>
      <td>cabbage</td>
      <td>Fresh green cabbage1</td>
      <td>0.579208</td>
      <td>0.778797</td>
      <td>0.330693</td>
      <td>0.245944</td>
    </tr>
    <tr>
      <th>31</th>
      <td>vegetables</td>
      <td>cabbage</td>
      <td>Fresh red cabbage1</td>
      <td>1.05645</td>
      <td>0.779107</td>
      <td>0.330693</td>
      <td>0.448412</td>
    </tr>
    <tr>
      <th>32</th>
      <td>vegetables</td>
      <td>collard_greens</td>
      <td>Fresh1</td>
      <td>2.63084</td>
      <td>1.16</td>
      <td>0.286601</td>
      <td>0.650001</td>
    </tr>
    <tr>
      <th>33</th>
      <td>vegetables</td>
      <td>corn_sweet</td>
      <td>Fresh1</td>
      <td>2.69062</td>
      <td>0.54</td>
      <td>0.363763</td>
      <td>1.8125</td>
    </tr>
    <tr>
      <th>34</th>
      <td>vegetables</td>
      <td>cucumbers</td>
      <td>Fresh, consumed with peel1</td>
      <td>1.29593</td>
      <td>0.97</td>
      <td>0.264555</td>
      <td>0.353448</td>
    </tr>
    <tr>
      <th>35</th>
      <td>vegetables</td>
      <td>cucumbers</td>
      <td>Fresh, peeled1</td>
      <td>1.29593</td>
      <td>0.73</td>
      <td>0.264555</td>
      <td>0.46965</td>
    </tr>
    <tr>
      <th>36</th>
      <td>vegetables</td>
      <td>green_beans</td>
      <td>Fresh1</td>
      <td>2.13997</td>
      <td>0.846575</td>
      <td>0.275578</td>
      <td>0.696606</td>
    </tr>
    <tr>
      <th>37</th>
      <td>vegetables</td>
      <td>green_peppers</td>
      <td>Fresh1</td>
      <td>1.41036</td>
      <td>0.82</td>
      <td>0.264555</td>
      <td>0.455022</td>
    </tr>
    <tr>
      <th>38</th>
      <td>vegetables</td>
      <td>kale</td>
      <td>Fresh1</td>
      <td>2.8073</td>
      <td>1.05</td>
      <td>0.286601</td>
      <td>0.766262</td>
    </tr>
    <tr>
      <th>39</th>
      <td>vegetables</td>
      <td>lettuce_iceberg</td>
      <td>Fresh1</td>
      <td>1.21304</td>
      <td>0.95</td>
      <td>0.242508</td>
      <td>0.309655</td>
    </tr>
    <tr>
      <th>40</th>
      <td>vegetables</td>
      <td>mustard_greens</td>
      <td>Fresh1</td>
      <td>2.56924</td>
      <td>0.84</td>
      <td>0.308647</td>
      <td>0.944032</td>
    </tr>
    <tr>
      <th>41</th>
      <td>vegetables</td>
      <td>okra</td>
      <td>Fresh1</td>
      <td>3.21355</td>
      <td>0.769474</td>
      <td>0.35274</td>
      <td>1.47315</td>
    </tr>
    <tr>
      <th>42</th>
      <td>vegetables</td>
      <td>onions</td>
      <td>Fresh1</td>
      <td>1.03811</td>
      <td>0.9</td>
      <td>0.35274</td>
      <td>0.406868</td>
    </tr>
    <tr>
      <th>43</th>
      <td>vegetables</td>
      <td>potatoes</td>
      <td>Fresh1</td>
      <td>0.56432</td>
      <td>0.811301</td>
      <td>0.264555</td>
      <td>0.184017</td>
    </tr>
    <tr>
      <th>44</th>
      <td>vegetables</td>
      <td>radish</td>
      <td>Fresh1</td>
      <td>1.31163</td>
      <td>0.9</td>
      <td>0.275578</td>
      <td>0.401618</td>
    </tr>
    <tr>
      <th>45</th>
      <td>vegetables</td>
      <td>red_peppers</td>
      <td>Fresh1</td>
      <td>2.27794</td>
      <td>0.82</td>
      <td>0.264555</td>
      <td>0.734926</td>
    </tr>
    <tr>
      <th>46</th>
      <td>vegetables</td>
      <td>summer_squash</td>
      <td>Fresh1</td>
      <td>1.63948</td>
      <td>0.7695</td>
      <td>0.396832</td>
      <td>0.84548</td>
    </tr>
    <tr>
      <th>47</th>
      <td>vegetables</td>
      <td>sweet_potatoes</td>
      <td>Fresh1</td>
      <td>0.918897</td>
      <td>0.811301</td>
      <td>0.440925</td>
      <td>0.4994</td>
    </tr>
    <tr>
      <th>48</th>
      <td>vegetables</td>
      <td>turnip_greens</td>
      <td>Fresh1</td>
      <td>2.47175</td>
      <td>0.75</td>
      <td>0.31967</td>
      <td>1.05353</td>
    </tr>
  </tbody>
</table>
</div>



__Exercise 1.4.__ Discuss the questions below (a paragraph each is sufficient). Use plots to support your ideas.

* What kinds of fruits are the most expensive (per pound)? What kinds are the least expensive?
* How do the price distributions compare for fruit and vegetables?
* Which foods are the best value for the price?
* What's something surprising about this data set?
* Which foods do you expect to provide the best combination of price, yield, and nutrition? A future assignment may combine this data set with another so you can check your hypothesis.


```python
#What kinds of fruits are the most expensive (per pound)? What kinds are the least expensive?
def max_n_min(dataframe, colname_list):
    """input: a dataframe that you want to do Max and Min with
    Output: Max and Min of that column
    """
    for i in range(len(dataframe)):
        if dataframe[colname_list][i] == dataframe[colname_list].max():
            max_price_per_lb = dataframe['food'][i]
        if dataframe[colname_list][i] == dataframe[colname_list].min():
            min_price_per_lb = dataframe['food'][i]
            return max_price_per_lb, min_price_per_lb
maxmin_pplb = max_n_min(fruit_n_vege,'price_per_lb')
print "%s are the most expensive (per pound).\n%s are the least expensive (per pound)." % (maxmin_pplb[0],maxmin_pplb[1])
```

    raspberries are the most expensive (per pound).
    watermelon are the least expensive (per pound).



```python
#How do the price distributions compare for fruit and vegetables?
print "From the histograms of price per pound for two catagories Fruits and Vegetables, we can see that the histogram for fruits is skewed to the right and the histogram for vegetables is a bimodal distribution which means that we can divide vegetables into two groups, one group is a lot more expensive than the other group. Fruits are mostly fall on $1-$1.5 per lb, and Vegetables are mostly fall on $1 per lb or $2-$3 per lb. From the summaries below we can observe that the range of fruits are from 0.33 to 6.98. It's mean is 2.08, and the range of Vegetables are smaller than the Fruits from 0.56 to 3.21. It has a smaller mean of 1.84 compare to the Fruits."
plt.hist(fruit['price_per_lb'].dropna(),bins=30,alpha=.4,label='Price Per Pound for Fruits')
plt.hist(veget['price_per_lb'].dropna(),bins=10,alpha=.4,label='Price Per Pound for Vegetables')
plt.legend()
plt.show()
warnings.filterwarnings("ignore")
fruit['price_per_lb'] = fruit['price_per_lb'].convert_objects(convert_numeric=True)
print 'fruit summary\n', fruit['price_per_lb'].describe()
veget['price_per_lb'] = veget['price_per_lb'].convert_objects(convert_numeric=True)
print 'vegetables summary\n', veget['price_per_lb'].describe()
```

    From the histograms of price per pound for two catagories Fruits and Vegetables, we can see that the histogram for fruits is skewed to the right and the histogram for vegetables is a bimodal distribution which means that we can divide vegetables into two groups, one group is a lot more expensive than the other group. Fruits are mostly fall on $1-$1.5 per lb, and Vegetables are mostly fall on $1 per lb or $2-$3 per lb. From the summaries below we can observe that the range of fruits are from 0.33 to 6.98. It's mean is 2.08, and the range of Vegetables are smaller than the Fruits from 0.56 to 3.21. It has a smaller mean of 1.84 compare to the Fruits.



![png](output_9_1.png)


    fruit summary
    count    24.000000
    mean      2.076877
    std       1.675687
    min       0.333412
    25%       1.000830
    50%       1.579351
    75%       2.219895
    max       6.975811
    Name: price_per_lb, dtype: float64
    vegetables summary
    count    25.000000
    mean      1.838701
    std       0.817285
    min       0.564320
    25%       1.213039
    50%       1.639477
    75%       2.569235
    max       3.213552
    Name: price_per_lb, dtype: float64



```python
#Which foods are the best value for the price
best_val = fruit_n_vege[['price_per_lb','yield']].groupby(level=0).sum()
#best_val.plot(y='price_per_lb')
best_val.plot(secondary_y='yield')
plt.show()
print "I think watermelon has the best value for the price because you get the least price with the most pounds of that item."
```


![png](output_10_0.png)


    I think watermelon has the best value for the price because you get the least price with the most pounds of that item.



```python
#What's something surprising about this data set
print "I am surprised that some fruits are a lot more expensive than vegetables. For example, on the histogram, the red bars that are to the right of the blue bars. Those red bars indicate the fruits that are more expensive than the most expensive vegetable. I am also surprised that the distribution for the vegetables is bimodal distribution, since I expect vegetables to have a normal distribution."
```

    I am surprised that some fruits are a lot more expensive than vegetables. For example, on the histogram, the red bars that are to the right of the blue bars. Those red bars indicate the fruits that are more expensive than the most expensive vegetable. I am also surprised that the distribution for the vegetables is bimodal distribution, since I expect vegetables to have a normal distribution.



```python
#Which foods do you expect to provide the best combination of price, yield, and nutrition?
value = fruit_n_vege['yield']/fruit_n_vege['price_per_lb']
def best_value(value):    
    for i in range(len(value)):
            if value[i] == value.max():
                max_value = value[i]
                return fruit_n_vege['food'][i],max_value

fig, ax = plt.subplots(1, 1)
def scatter(group):
    plt.plot(group["yield"], group["price_per_lb"], "o", label = group.name)
fruit_n_vege.groupby("type").apply(scatter)
ax.set(xlabel = "yield", ylabel = "price per pound")
ax.legend(loc = 4)
plt.show()
print "I assume that if the food with the most yield and lowest price per pound, it would has the best value. It would be more towards the right down corner of the scattered plot. Thus, I divide the yield column to the price_per_lb column to get the value for yield when each foods is 1 unit of price per pound. So, we get %s as the best value for the price with the value of %f, since it has the highest value of yield with 1 unit of price per pound." % (best_value(value)[0], best_value(value)[1])
```


![png](output_12_0.png)


    I assume that if the food with the most yield and lowest price per pound, it would has the best value. It would be more towards the right down corner of the scattered plot. Thus, I divide the yield column to the price_per_lb column to get the value for yield when each foods is 1 unit of price per pound. So, we get watermelon as the best value for the price with the value of 1.559632, since it has the highest value of yield with 1 unit of price per pound.



```python

```
