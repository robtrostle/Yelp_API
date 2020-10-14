# Yelp

![](https://www.pinclipart.com/picdir/middle/200-2008266_livingston-bagel-yelp-review3-yelp-reviews-logo-clipart.png)

## Documentation: https://www.yelp.com/developers/documentation/v3/authentication

### Client ID
RsrUSB_o5o9q1Y91jGRt8g

### API Key
1GuEZTGk6H6bzI8Z8IMPifQNOZ6s_pxx2XI3hPGdXIKCrKsmt7_MNPVCR4wfq6WeS6ggSiCpWEIO0Ob2KOQ3m-4bM1_CejFLA8pGom5FZ6SeDtMDd0Ulsyr0v9N8X3Yx


```python
import pandas as pd
import requests,json
api_url = "https://api.yelp.com/v3/businesses/search"
api_key = "1GuEZTGk6H6bzI8Z8IMPifQNOZ6s_pxx2XI3hPGdXIKCrKsmt7_MNPVCR4wfq6WeS6ggSiCpWEIO0Ob2KOQ3m-4bM1_CejFLA8pGom5FZ6SeDtMDd0Ulsyr0v9N8X3Yx"
#set up parameter dictionary according to documentation
params = {"term": "bakeries",
         "location": "Pittsburgh",
         "sort_by": "rating",
         "limit": 50}
#set up header dictionary w/ api key according to documentation
headers = {"Authorization": "Bearer {}".format(api_key)}
#call the api
response = requests.get(api_url, params = params, headers = headers)
```


```python
#isolate the JSON data from the response object. These are the different parameters to search by. I.E term: Bakeries
data = response.json()
top_50 = pd.DataFrame(data["businesses"])
print(top_50.dtypes)
```

    id                object
    alias             object
    name              object
    image_url         object
    is_closed           bool
    url               object
    review_count       int64
    categories        object
    rating           float64
    coordinates       object
    transactions      object
    price             object
    location          object
    phone             object
    display_phone     object
    distance         float64
    dtype: object
    


```python
from pandas.io.json import json_normalize
#flatten data and load to data frame, with _separators. This will get us more detail to choose from. i.e. location_zip
top_50 = pd.json_normalize(data["businesses"], sep="_")
print(list(top_50))
print(len(top_50))
```

    ['id', 'alias', 'name', 'image_url', 'is_closed', 'url', 'review_count', 'categories', 'rating', 'transactions', 'price', 'phone', 'display_phone', 'distance', 'coordinates_latitude', 'coordinates_longitude', 'location_address1', 'location_address2', 'location_address3', 'location_city', 'location_zip_code', 'location_country', 'location_state', 'location_display_address']
    50
    


```python
#Sort by the highest rated cafes in the burgh. Print the top 50 and use col name, rating, review_count
print(top_50[["name","rating","review_count"]].sort_values(by=['rating'], ascending = False).head(50))
```

                                              name  rating  review_count
    0                 Five Points Artisan Bakeshop     5.0           122
    24                               Dana's Bakery     5.0            23
    38                     Cibrone M & Sons Bakery     5.0            20
    37                                 350° Bakery     5.0            18
    28                                 KaFair Cafe     5.0            17
    1                               Redhawk Coffee     5.0           153
    25                            Mancini's Bakery     5.0            23
    4                             Nancy B's Bakery     5.0            75
    26                  Moio's Italian Pastry Shop     4.5            99
    8                               Fortuitea Cafe     4.5            90
    29                        Flour & Sugar Cakery     4.5            36
    30                     Good L'Oven Cookie Shop     4.5            30
    31                             Treats ‘N Beans     4.5            41
    32                            The Speckled Egg     4.5            39
    33                         Nothing Bundt Cakes     4.5            37
    34                            Grandview Bakery     4.5           102
    36                                 Good L'oven     4.5            36
    3         Jean-Marc Chatellier's French Bakery     4.5           169
    2          La Gourmandine Bakery & Pastry Shop     4.5           455
    39                          Kretchmar's Bakery     4.5            66
    41                      Great Harvest Bread Co     4.5            43
    42                     Mancini's Bread Company     4.5            41
    44                        La Gourmadine Bakery     4.5            22
    46                           Le Cupcake Shoppe     4.5            24
    27              The Butterwood Bake Consortium     4.5           196
    7                        La Gourmandine Bakery     4.5            67
    5                              Page Dairy Mart     4.5           231
    16                           Better-Maid Donut     4.5            68
    9                               Mediterra Cafe     4.5           108
    10                      Le Petit Cafe & Grille     4.5            60
    11                            DiAnoia's Eatery     4.5           691
    12                              Oakmont Bakery     4.5           447
    13                 Madeleine Bakery And Bistro     4.5            67
    6                           Colangelo's Bakery     4.5            74
    15                                 Waffallonia     4.5           316
    14                              Cafe Chocolade     4.5            77
    17                          Food Glorious Food     4.5            58
    18         La Gourmandine Bakery & Pastry Shop     4.5            94
    19                                  BreadWorks     4.5            38
    20                              Minerva Bakery     4.5            32
    21                       Allegro Hearth Bakery     4.5            79
    22                               Sumi's Cakery     4.5            86
    23                          Commonplace Coffee     4.5           194
    35                               Gaby Et Jules     4.0           231
    40                        Pink Box Bakery Cafe     4.0           115
    43  Peace, Love and Little Donuts - Pittsburgh     4.0           280
    45             Sinful Sweets Chocolate Company     4.0           131
    47                        Priory Fine Pastries     4.0            52
    48                             Party Cake Shop     4.0            37
    49                               Lola's Eatery     4.0            37
    


```python
#Check the different categories of aliases to choose from
flat_cafes = pd.json_normalize(data["businesses"], sep = "_", record_path="categories")
print(flat_cafes.head(30))
```

                   alias                      title
    0           bakeries                   Bakeries
    1           desserts                   Desserts
    2             coffee               Coffee & Tea
    3         foodtrucks                Food Trucks
    4           cakeshop       Patisserie/Cake Shop
    5           bakeries                   Bakeries
    6           desserts                   Desserts
    7           macarons                   Macarons
    8           bakeries                   Bakeries
    9           icecream  Ice Cream & Frozen Yogurt
    10          desserts                   Desserts
    11          bakeries                   Bakeries
    12             cafes                      Cafes
    13          bakeries                   Bakeries
    14  breakfast_brunch         Breakfast & Brunch
    15          bakeries                   Bakeries
    16            coffee               Coffee & Tea
    17             vegan                      Vegan
    18             cafes                      Cafes
    19          bakeries                   Bakeries
    20         chocolate       Chocolatiers & Shops
    21  breakfast_brunch         Breakfast & Brunch
    22            coffee               Coffee & Tea
    23             delis                      Delis
    24           italian                    Italian
    25  breakfast_brunch         Breakfast & Brunch
    26          bakeries                   Bakeries
    27          cupcakes                   Cupcakes
    28            donuts                     Donuts
    29          bakeries                   Bakeries
    


```python
#Further normalizing the date within the json file to get more detail such as co-ordinates
flat_cafes = pd.json_normalize(data["businesses"], sep = "_", record_path="categories", meta = ["name", "alias","rating",["coordinates","latitude"],["coordinates","longitude"]],meta_prefix="biz_")
print(flat_cafes.head(2))
print(flat_cafes[["alias","title"]].sort_values(by=["alias"], ascending = False).head(3))
```

          alias     title                      biz_name  \
    0  bakeries  Bakeries  Five Points Artisan Bakeshop   
    1  desserts  Desserts  Five Points Artisan Bakeshop   
    
                                     biz_alias biz_rating  \
    0  five-points-artisan-bakeshop-pittsburgh          5   
    1  five-points-artisan-bakeshop-pittsburgh          5   
    
      biz_coordinates_latitude biz_coordinates_longitude  
    0                  40.4443                  -79.9168  
    1                  40.4443                  -79.9168  
          alias    title
    69  waffles  Waffles
    34  waffles  Waffles
    17    vegan    Vegan
    


```python
#set up parameter dictionary according to documentation to start at record number 50 and append to the top 50 data. 
params = {"term": "bakeries",
         "location": "Pittsburgh",
         "sort_by": "rating",
         "limit": 50,
         "offset": 50}

#call the api
result = requests.get(api_url, params = params, headers = headers)
next_50 = pd.json_normalize(result.json()["businesses"])

#append the results, setting ignore index to renumber rows
top_100 = top_50.append(next_50, ignore_index = True)

#print the bottom 10 rated cafes in pittsburgh
print(top_100[["name","rating","review_count"]].sort_values(by=['rating'], ascending = False).tail(10))
print(top_100.shape)
```

                                 name  rating  review_count
    68     Everyday's A Sunday & Cafe     4.0            54
    71          Chateau Café & Coffee     4.0            54
    72                Gluuteny Bakery     4.0           106
    47           Priory Fine Pastries     4.0            52
    76                 Lincoln Bakery     4.0            56
    77  Batter & Dough Baking Company     4.0            43
    78                 My Sweet Lilly     4.0            47
    73                Prantl's Bakery     4.0           251
    89                Prantl's Bakery     3.5            98
    97    Bartram House Bakery & Cafe     3.5           113
    (100, 34)
    


```python

```
