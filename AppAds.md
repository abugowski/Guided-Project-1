# Analysis of Free Applications
In this project I will analyse free applications available in Apple Store.
Based on this anlysis I would like to recomend to my development team which applications they shoud create to get as much as possible sponsors with Ads


```python
def explore_data(dataset, start, end, rows_and_columns = False):
    dataset_slice = dataset[start:end]
    rows = 0
    columns = 0
    for row in dataset_slice:
        print(row)
        print('\n')
        
    if rows_and_columns:
        print('Number of rows:', len(dataset))
        print('Number of columns:', len(dataset[0]))
```


```python
import csv
openfile = open('AppleStore.csv', encoding='utf8')
handler = csv.reader(openfile)
datasetApple = list(handler)

openfile = open('googleplaystore.csv')
handler = csv.reader(openfile)
datasetGoogle = list(handler)
```


```python
explore_data(datasetGoogle, 0, 2, True)
explore_data(datasetApple, 0, 2, True)
```

    ['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']
    
    
    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    Number of rows: 10842
    Number of columns: 13
    ['id', 'track_name', 'size_bytes', 'currency', 'price', 'rating_count_tot', 'rating_count_ver', 'user_rating', 'user_rating_ver', 'ver', 'cont_rating', 'prime_genre', 'sup_devices.num', 'ipadSc_urls.num', 'lang.num', 'vpp_lic']
    
    
    ['284882215', 'Facebook', '389879808', 'USD', '0.0', '2974676', '212', '3.5', '3.5', '95.0', '4+', 'Social Networking', '37', '1', '29', '1']
    
    
    Number of rows: 7198
    Number of columns: 16


We have two data sets for Apple's and Google apps.
I downloaded it from bellow links:

[Apple Store](https://dq-content.s3.amazonaws.com/350/AppleStore.csv)

[Google Play](https://dq-content.s3.amazonaws.com/350/googleplaystore.csv)

Apple file contains 16 columns. For my analysis I use: 'track_name', 'price', 'prime_genre' and 'user_rating'

Google app file contains 13 columns. I use 'App', 'Genres', 'Rating', 'Reviews' and 'Price'


```python
print(datasetGoogle[0], '\n', datasetGoogle[10473])
```

    ['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver'] 
     ['Life Made WI-Fi Touchscreen Photo Frame', '1.9', '19', '3.0M', '1,000+', 'Free', '0', 'Everyone', '', 'February 11, 2018', '1.0.19', '4.0 and up']



```python
uniqueApps = []
nonUniqueApps = []

for row in datasetGoogle[1:]:
    name = row[0]
    if name in uniqueApps:
        nonUniqueApps.append(name)
    else:
        uniqueApps.append(name)
        
print(len(uniqueApps))
print(len(nonUniqueApps))

```

    9660
    1181


Based on discussion forum about this dataset I identified one row wiht wrong data. I decide to delte this row.


```python
del datasetGoogle[10473]
```

In my detaset I found duplicate entries. I decided to remove duplicates based on number of reivews. This value is unique and is better for my project to anlayse data based on more reviews. 

I create empty directory, which will be hold name of application and number of reviews. I loop through dataset and look for this values


```python
reviews_max = {}
for row in datasetGoogle[1:]:
    name = row[0]
    n_reviews = float(row[3])
    if name in reviews_max and reviews_max[name] < n_reviews:
        reviews_max[name] = n_reviews
    elif name is not reviews_max:
        reviews_max[name] = n_reviews
        
print(len(reviews_max))
```

    9659


Now I create two empty lists. android_clean will hold clean data set. "already_added" is control list to sotre names of apps which I add recently to "android_clean".

I loop through orginal data set, extract app name and number of reivews. If this numbers are match with dictionary, I record it in android_clean, additionaly application name should be not exist in our control list. In the same step I add app name to control list arleady_added.


```python
android_clean = []
already_added = []

for row in datasetGoogle[1:]:
    name = row[0]
    n_reviews = float(row[3])
    if n_reviews == reviews_max[name] and name not in already_added:
        android_clean.append(row)
        already_added.append(name)

print(len(android_clean))
```

    9659


Now I create function which is looking for non English characters in application names


```python
def check_English(app_name):
    special_char = 0
    for character in app_name:
        if ord(character) > 127 and special_char > 2:
            return False
        elif ord(character) > 127:
            special_char += 1
    return True
```


```python
check_English('Docs To Go™™™™ Free Office Suite')
```




    False




```python
index_id = []
for row in android_clean:
    if check_English(row[0]) != True:
        index_id.append(android_clean.index(row))
        
index_id.reverse()
for row in index_id:
    android_clean.pop(row)
```


```python
for row in android_clean:
    if check_English(row[0]) != True:
        print("Do usunięcia")
```


```python
index_id = []
for row in datasetApple:
    if check_English(row[1]) != True:
        index_id.append(datasetApple.index(row))
        
index_id.reverse()
for row in index_id:
    datasetApple.pop(row)
    
for row in datasetApple:
    if check_English(row[1]) != True:
        print("Do usnięcia")
```

I clean datasets from non Engish applications.
I list number of applications in each datasets.


```python
print("Apple App Dataset length: ", len(datasetApple[1:]))
print("Android Dataset length: ", len(android_clean))
```

    Apple App Dataset length:  6183
    Android Dataset length:  9614



```python
android_free = []
for row in android_clean:
    if row[6] == 'Free':
        android_free.append(row)
```


```python
apple_free = []
for row in datasetApple[1:]:
    if float(row[4]) == 0.0:
        apple_free.append(row)
```


```python
print("Apple Free Apps length: ", len(apple_free))
print("Android Free Apps length: ", len(android_free))
```

    Apple Free Apps length:  3222
    Android Free Apps length:  8863


I would like to find app profile which has good response from users on both markets.

I will analyse which genres are most common on each of markets. I will build frequency table for a few columns in our data sets.


```python
datasetApple[0:2]
```




    [['id',
      'track_name',
      'size_bytes',
      'currency',
      'price',
      'rating_count_tot',
      'rating_count_ver',
      'user_rating',
      'user_rating_ver',
      'ver',
      'cont_rating',
      'prime_genre',
      'sup_devices.num',
      'ipadSc_urls.num',
      'lang.num',
      'vpp_lic'],
     ['284882215',
      'Facebook',
      '389879808',
      'USD',
      '0.0',
      '2974676',
      '212',
      '3.5',
      '3.5',
      '95.0',
      '4+',
      'Social Networking',
      '37',
      '1',
      '29',
      '1']]



To genereate frequecny table and find most common genre in each market I identified columns which I should use to do this:

In Apple Store

rating_count_tot: 5

user_rating: 7

prime_genre: 11

In Google Play

Rating: 2

Reviews: 3

Genres: 9

Category: 1



```python
def freq_table(dataset, index):
    genre_dict = {}
    for row in dataset:
        if row[index] in genre_dict:
            genre_dict[row[index]] += 1
        else:
            genre_dict[row[index]] = 1
    
    genre_freq = {}
    for genre_name in genre_dict:
        genre_freq[genre_name] = round(genre_dict[genre_name]/len(dataset) * 100, 2)
        
        
    return genre_freq

#test1 = freq_table(apple_free, 11)
```


```python
def display_table(dataset, index):
    table = freq_table(dataset, index)
    table_display = []
    for key in table:
        key_val_as_tuple = (table[key], key)
        table_display.append(key_val_as_tuple)

    table_sorted = sorted(table_display, reverse = True)
    for entry in table_sorted:
        print(entry[1], ':', entry[0])
```


```python
print("Frequency table for Apple Apps by Genre")
display_table(apple_free, 11)
print("")
print("Frequency table for Google Apps by Genre")
display_table(android_free, 9)
print("")
display_table(android_free, 1)

```

    Frequency table for Apple Apps by Genre
    Games : 58.16
    Entertainment : 7.88
    Photo & Video : 4.97
    Education : 3.66
    Social Networking : 3.29
    Shopping : 2.61
    Utilities : 2.51
    Sports : 2.14
    Music : 2.05
    Health & Fitness : 2.02
    Productivity : 1.74
    Lifestyle : 1.58
    News : 1.33
    Travel : 1.24
    Finance : 1.12
    Weather : 0.87
    Food & Drink : 0.81
    Reference : 0.56
    Business : 0.53
    Book : 0.43
    Navigation : 0.19
    Medical : 0.19
    Catalogs : 0.12
    
    Frequency table for Google Apps by Genre
    Tools : 8.45
    Entertainment : 6.07
    Education : 5.35
    Business : 4.58
    Productivity : 3.89
    Lifestyle : 3.89
    Finance : 3.7
    Medical : 3.54
    Sports : 3.46
    Personalization : 3.32
    Communication : 3.25
    Action : 3.1
    Health & Fitness : 3.07
    Photography : 2.94
    News & Magazines : 2.8
    Social : 2.66
    Travel & Local : 2.32
    Shopping : 2.25
    Books & Reference : 2.14
    Simulation : 2.04
    Dating : 1.86
    Arcade : 1.86
    Video Players & Editors : 1.78
    Casual : 1.75
    Maps & Navigation : 1.4
    Food & Drink : 1.24
    Puzzle : 1.13
    Racing : 0.99
    Role Playing : 0.94
    Libraries & Demo : 0.94
    Auto & Vehicles : 0.93
    Strategy : 0.9
    House & Home : 0.82
    Weather : 0.8
    Events : 0.71
    Adventure : 0.68
    Comics : 0.61
    Beauty : 0.6
    Art & Design : 0.6
    Parenting : 0.5
    Card : 0.44
    Casino : 0.43
    Trivia : 0.42
    Educational;Education : 0.39
    Board : 0.38
    Educational : 0.37
    Education;Education : 0.34
    Word : 0.26
    Casual;Pretend Play : 0.24
    Music : 0.2
    Racing;Action & Adventure : 0.17
    Puzzle;Brain Games : 0.17
    Entertainment;Music & Video : 0.17
    Casual;Brain Games : 0.14
    Casual;Action & Adventure : 0.14
    Arcade;Action & Adventure : 0.12
    Action;Action & Adventure : 0.1
    Educational;Pretend Play : 0.09
    Simulation;Action & Adventure : 0.08
    Parenting;Education : 0.08
    Entertainment;Brain Games : 0.08
    Board;Brain Games : 0.08
    Parenting;Music & Video : 0.07
    Educational;Brain Games : 0.07
    Casual;Creativity : 0.07
    Art & Design;Creativity : 0.07
    Education;Pretend Play : 0.06
    Role Playing;Pretend Play : 0.05
    Education;Creativity : 0.05
    Role Playing;Action & Adventure : 0.03
    Puzzle;Action & Adventure : 0.03
    Entertainment;Creativity : 0.03
    Entertainment;Action & Adventure : 0.03
    Educational;Creativity : 0.03
    Educational;Action & Adventure : 0.03
    Education;Music & Video : 0.03
    Education;Brain Games : 0.03
    Education;Action & Adventure : 0.03
    Adventure;Action & Adventure : 0.03
    Video Players & Editors;Music & Video : 0.02
    Sports;Action & Adventure : 0.02
    Simulation;Pretend Play : 0.02
    Puzzle;Creativity : 0.02
    Music;Music & Video : 0.02
    Entertainment;Pretend Play : 0.02
    Casual;Education : 0.02
    Board;Action & Adventure : 0.02
    Trivia;Education : 0.01
    Travel & Local;Action & Adventure : 0.01
    Tools;Education : 0.01
    Strategy;Education : 0.01
    Strategy;Creativity : 0.01
    Strategy;Action & Adventure : 0.01
    Simulation;Education : 0.01
    Role Playing;Brain Games : 0.01
    Racing;Pretend Play : 0.01
    Puzzle;Education : 0.01
    Parenting;Brain Games : 0.01
    Music & Audio;Music & Video : 0.01
    Lifestyle;Pretend Play : 0.01
    Lifestyle;Education : 0.01
    Health & Fitness;Education : 0.01
    Health & Fitness;Action & Adventure : 0.01
    Entertainment;Education : 0.01
    Communication;Creativity : 0.01
    Comics;Creativity : 0.01
    Casual;Music & Video : 0.01
    Card;Brain Games : 0.01
    Card;Action & Adventure : 0.01
    Books & Reference;Education : 0.01
    Art & Design;Pretend Play : 0.01
    Art & Design;Action & Adventure : 0.01
    Arcade;Pretend Play : 0.01
    Adventure;Education : 0.01
    
    FAMILY : 19.21
    GAME : 9.51
    TOOLS : 8.46
    BUSINESS : 4.58
    LIFESTYLE : 3.9
    PRODUCTIVITY : 3.89
    FINANCE : 3.7
    MEDICAL : 3.54
    SPORTS : 3.42
    PERSONALIZATION : 3.32
    COMMUNICATION : 3.25
    HEALTH_AND_FITNESS : 3.07
    PHOTOGRAPHY : 2.94
    NEWS_AND_MAGAZINES : 2.8
    SOCIAL : 2.66
    TRAVEL_AND_LOCAL : 2.34
    SHOPPING : 2.25
    BOOKS_AND_REFERENCE : 2.14
    DATING : 1.86
    VIDEO_PLAYERS : 1.78
    MAPS_AND_NAVIGATION : 1.4
    FOOD_AND_DRINK : 1.24
    EDUCATION : 1.13
    LIBRARIES_AND_DEMO : 0.94
    AUTO_AND_VEHICLES : 0.93
    ENTERTAINMENT : 0.88
    HOUSE_AND_HOME : 0.82
    WEATHER : 0.8
    EVENTS : 0.71
    PARENTING : 0.65
    ART_AND_DESIGN : 0.64
    COMICS : 0.62
    BEAUTY : 0.6



```python
display_table(apple_free, 11)
```

    Games : 58.16
    Entertainment : 7.88
    Photo & Video : 4.97
    Education : 3.66
    Social Networking : 3.29
    Shopping : 2.61
    Utilities : 2.51
    Sports : 2.14
    Music : 2.05
    Health & Fitness : 2.02
    Productivity : 1.74
    Lifestyle : 1.58
    News : 1.33
    Travel : 1.24
    Finance : 1.12
    Weather : 0.87
    Food & Drink : 0.81
    Reference : 0.56
    Business : 0.53
    Book : 0.43
    Navigation : 0.19
    Medical : 0.19
    Catalogs : 0.12


Most common genre in App Store is Games which is 58.16% of all apps. Entertainment and Photo & Video are most popular too.
Most apps are disigned for games, photo and video. 

We can recomend for App Store Games genre application.


```python
display_table(android_free, 1)
```

    FAMILY : 19.21
    GAME : 9.51
    TOOLS : 8.46
    BUSINESS : 4.58
    LIFESTYLE : 3.9
    PRODUCTIVITY : 3.89
    FINANCE : 3.7
    MEDICAL : 3.54
    SPORTS : 3.42
    PERSONALIZATION : 3.32
    COMMUNICATION : 3.25
    HEALTH_AND_FITNESS : 3.07
    PHOTOGRAPHY : 2.94
    NEWS_AND_MAGAZINES : 2.8
    SOCIAL : 2.66
    TRAVEL_AND_LOCAL : 2.34
    SHOPPING : 2.25
    BOOKS_AND_REFERENCE : 2.14
    DATING : 1.86
    VIDEO_PLAYERS : 1.78
    MAPS_AND_NAVIGATION : 1.4
    FOOD_AND_DRINK : 1.24
    EDUCATION : 1.13
    LIBRARIES_AND_DEMO : 0.94
    AUTO_AND_VEHICLES : 0.93
    ENTERTAINMENT : 0.88
    HOUSE_AND_HOME : 0.82
    WEATHER : 0.8
    EVENTS : 0.71
    PARENTING : 0.65
    ART_AND_DESIGN : 0.64
    COMICS : 0.62
    BEAUTY : 0.6


For Android apps most popular genre is Family. Games and Tools are the next popular type of applications. We observ similar situation like in Apple application. Genre is from games, entertaiment. 

I am recomending Family genre app.

In general after this analysis we can say that fun application are most frequently used.


```python
freq_table(apple_free, 11)
```




    {'Social Networking': 3.29,
     'Photo & Video': 4.97,
     'Games': 58.16,
     'Music': 2.05,
     'Reference': 0.56,
     'Health & Fitness': 2.02,
     'Weather': 0.87,
     'Utilities': 2.51,
     'Travel': 1.24,
     'Shopping': 2.61,
     'News': 1.33,
     'Navigation': 0.19,
     'Lifestyle': 1.58,
     'Entertainment': 7.88,
     'Food & Drink': 0.81,
     'Sports': 2.14,
     'Book': 0.43,
     'Finance': 1.12,
     'Education': 3.66,
     'Productivity': 1.74,
     'Business': 0.53,
     'Catalogs': 0.12,
     'Medical': 0.19}




```python
freq_table_apple = freq_table(apple_free, 11)
```


```python
freq_table_apple
```




    {'Social Networking': 3.29,
     'Photo & Video': 4.97,
     'Games': 58.16,
     'Music': 2.05,
     'Reference': 0.56,
     'Health & Fitness': 2.02,
     'Weather': 0.87,
     'Utilities': 2.51,
     'Travel': 1.24,
     'Shopping': 2.61,
     'News': 1.33,
     'Navigation': 0.19,
     'Lifestyle': 1.58,
     'Entertainment': 7.88,
     'Food & Drink': 0.81,
     'Sports': 2.14,
     'Book': 0.43,
     'Finance': 1.12,
     'Education': 3.66,
     'Productivity': 1.74,
     'Business': 0.53,
     'Catalogs': 0.12,
     'Medical': 0.19}




```python
for genre in freq_table_apple:
    total = 0
    len_genre = 0
    for row in apple_free:
        genre_app = row[11]
        if genre_app == genre:
            total += float(row[5])
            len_genre += 1
            
    avg_user_rating = total/len_genre
    print(genre, ": ", avg_user_rating)

```

    Social Networking :  71548.34905660378
    Photo & Video :  28441.54375
    Games :  22788.6696905016
    Music :  57326.530303030304
    Reference :  74942.11111111111
    Health & Fitness :  23298.015384615384
    Weather :  52279.892857142855
    Utilities :  18684.456790123455
    Travel :  28243.8
    Shopping :  26919.690476190477
    News :  21248.023255813954
    Navigation :  86090.33333333333
    Lifestyle :  16485.764705882353
    Entertainment :  14029.830708661417
    Food & Drink :  33333.92307692308
    Sports :  23008.898550724636
    Book :  39758.5
    Finance :  31467.944444444445
    Education :  7003.983050847458
    Productivity :  21028.410714285714
    Business :  7491.117647058823
    Catalogs :  4004.0
    Medical :  612.0


Most popular genre base on number of reatings is Navigation app which is only 0.19% of frequecy usage.


```python
for app in apple_free:
    if app[11] == 'Navigation':
        print(app[1], app[5])
```

    Waze - GPS Navigation, Maps & Real-time Traffic 345046
    Google Maps - Navigation & Transit 154911
    Geocaching® 12811
    CoPilot GPS – Car Navigation & Offline Maps 3582
    ImmobilienScout24: Real Estate Search in Germany 187
    Railway Route Search 5



```python
display_table(android_free, 5)
```

    1,000,000+ : 15.75
    100,000+ : 11.56
    10,000,000+ : 10.5
    10,000+ : 10.21
    1,000+ : 8.39
    100+ : 6.92
    5,000,000+ : 6.83
    500,000+ : 5.56
    50,000+ : 4.77
    5,000+ : 4.51
    10+ : 3.54
    500+ : 3.25
    50,000,000+ : 2.3
    100,000,000+ : 2.13
    50+ : 1.92
    5+ : 0.79
    1+ : 0.51
    500,000,000+ : 0.27
    1,000,000,000+ : 0.23
    0+ : 0.05



```python
freq_table_android = freq_table(android_free, 1)
```


```python
freq_table_android
```




    {'ART_AND_DESIGN': 0.64,
     'AUTO_AND_VEHICLES': 0.93,
     'BEAUTY': 0.6,
     'BOOKS_AND_REFERENCE': 2.14,
     'BUSINESS': 4.58,
     'COMICS': 0.62,
     'COMMUNICATION': 3.25,
     'DATING': 1.86,
     'EDUCATION': 1.13,
     'ENTERTAINMENT': 0.88,
     'EVENTS': 0.71,
     'FINANCE': 3.7,
     'FOOD_AND_DRINK': 1.24,
     'HEALTH_AND_FITNESS': 3.07,
     'HOUSE_AND_HOME': 0.82,
     'LIBRARIES_AND_DEMO': 0.94,
     'LIFESTYLE': 3.9,
     'GAME': 9.51,
     'FAMILY': 19.21,
     'MEDICAL': 3.54,
     'SOCIAL': 2.66,
     'SHOPPING': 2.25,
     'PHOTOGRAPHY': 2.94,
     'SPORTS': 3.42,
     'TRAVEL_AND_LOCAL': 2.34,
     'TOOLS': 8.46,
     'PERSONALIZATION': 3.32,
     'PRODUCTIVITY': 3.89,
     'PARENTING': 0.65,
     'WEATHER': 0.8,
     'VIDEO_PLAYERS': 1.78,
     'NEWS_AND_MAGAZINES': 2.8,
     'MAPS_AND_NAVIGATION': 1.4}




```python
for category in freq_table_android:
    total = 0
    len_category = 0
    for row in android_free:
        category_app = row[1]
        if category_app == category:
            number_of_installs = row[5]
            n_installs = number_of_installs.replace('+', '')
            n_installs = n_installs.replace(',', '')
            total += float(n_installs)
            len_category += 1
    avg_n_installs = total / len_category
    print(category, ": ", round(avg_n_installs, 1))
```

    ART_AND_DESIGN :  1986335.1
    AUTO_AND_VEHICLES :  647317.8
    BEAUTY :  513151.9
    BOOKS_AND_REFERENCE :  8767811.9
    BUSINESS :  1704192.3
    COMICS :  817657.3
    COMMUNICATION :  38326063.2
    DATING :  854028.8
    EDUCATION :  1768500.0
    ENTERTAINMENT :  9146923.1
    EVENTS :  253542.2
    FINANCE :  1387692.5
    FOOD_AND_DRINK :  1924897.7
    HEALTH_AND_FITNESS :  4167457.4
    HOUSE_AND_HOME :  1331540.6
    LIBRARIES_AND_DEMO :  638503.7
    LIFESTYLE :  1437816.3
    GAME :  12914435.9
    FAMILY :  5183203.6
    MEDICAL :  123064.8
    SOCIAL :  23253652.1
    SHOPPING :  7036877.3
    PHOTOGRAPHY :  17840110.4
    SPORTS :  4274688.7
    TRAVEL_AND_LOCAL :  13984077.7
    TOOLS :  10801391.3
    PERSONALIZATION :  5201482.6
    PRODUCTIVITY :  16772838.6
    PARENTING :  542603.6
    WEATHER :  5074486.2
    VIDEO_PLAYERS :  24790074.2
    NEWS_AND_MAGAZINES :  9549178.5
    MAPS_AND_NAVIGATION :  4056941.8


Most recomended app genre based on number of install is Communication


```python
    
```
