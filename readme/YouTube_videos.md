

```python
#
```

możemy sprawdzić czy ilość filmików dla każdego kanału daje rozkład normalny
to samo z wyświetleniami dla każdego kanału
i osobno dla filmików


Dodać analize zależności wyśwwietleń i lajków od kategorii

policzyć wskaźniki normalizacji, testy chi^2
może jakiś model przewidujący

# Youtube video data analysis

Importing libraries


```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn import linear_model
from pandas import read_csv
import scipy.stats as scp
```


```python
from scipy.stats import shapiro
import seaborn as sns
from scipy import stats

```

Uploading dataset


```python
from google.colab import files
uploaded = files.upload()
```



     <input type="file" id="files-e98fdbcd-4589-4c0e-8a15-740d29d7205d" name="files[]" multiple disabled />
     <output id="result-e98fdbcd-4589-4c0e-8a15-740d29d7205d">
      Upload widget is only available when the cell has been executed in the
      current browser session. Please rerun this cell to enable.
      </output>
      <script src="/nbextensions/google.colab/files.js"></script> 


    Saving USvideos.csv to USvideos.csv



```python
videos = read_csv("USvideos.csv")
```

Podstawowe informacje o zbiorze danych


```python
videos.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 40949 entries, 0 to 40948
    Data columns (total 16 columns):
    video_id                  40949 non-null object
    trending_date             40949 non-null object
    title                     40949 non-null object
    channel_title             40949 non-null object
    category_id               40949 non-null int64
    publish_time              40949 non-null object
    tags                      40949 non-null object
    views                     40949 non-null int64
    likes                     40949 non-null int64
    dislikes                  40949 non-null int64
    comment_count             40949 non-null int64
    thumbnail_link            40949 non-null object
    comments_disabled         40949 non-null bool
    ratings_disabled          40949 non-null bool
    video_error_or_removed    40949 non-null bool
    description               40379 non-null object
    dtypes: bool(3), int64(5), object(8)
    memory usage: 4.2+ MB



```python
videos.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category_id</th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>40949.000000</td>
      <td>4.094900e+04</td>
      <td>4.094900e+04</td>
      <td>4.094900e+04</td>
      <td>4.094900e+04</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>19.972429</td>
      <td>2.360785e+06</td>
      <td>7.426670e+04</td>
      <td>3.711401e+03</td>
      <td>8.446804e+03</td>
    </tr>
    <tr>
      <th>std</th>
      <td>7.568327</td>
      <td>7.394114e+06</td>
      <td>2.288853e+05</td>
      <td>2.902971e+04</td>
      <td>3.743049e+04</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>5.490000e+02</td>
      <td>0.000000e+00</td>
      <td>0.000000e+00</td>
      <td>0.000000e+00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>17.000000</td>
      <td>2.423290e+05</td>
      <td>5.424000e+03</td>
      <td>2.020000e+02</td>
      <td>6.140000e+02</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>24.000000</td>
      <td>6.818610e+05</td>
      <td>1.809100e+04</td>
      <td>6.310000e+02</td>
      <td>1.856000e+03</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>25.000000</td>
      <td>1.823157e+06</td>
      <td>5.541700e+04</td>
      <td>1.938000e+03</td>
      <td>5.755000e+03</td>
    </tr>
    <tr>
      <th>max</th>
      <td>43.000000</td>
      <td>2.252119e+08</td>
      <td>5.613827e+06</td>
      <td>1.674420e+06</td>
      <td>1.361580e+06</td>
    </tr>
  </tbody>
</table>
</div>






```python
videos.title
```




    0                       WE WANT TO TALK ABOUT OUR MARRIAGE
    1        The Trump Presidency: Last Week Tonight with J...
    2        Racist Superman | Rudy Mancuso, King Bach & Le...
    3                         Nickelback Lyrics: Real or Fake?
    4                                 I Dare You: GOING BALD!?
    5                                    2 Weeks with iPhone X
    6                Roy Moore & Jeff Sessions Cold Open - SNL
    7                      5 Ice Cream Gadgets put to the Test
    8        The Greatest Showman | Official Trailer 2 [HD]...
    9        Why the rise of the robots won’t mean the end ...
    10       Dion Lewis' 103-Yd Kick Return TD vs. Denver! ...
    11       (SPOILERS) 'Shiva Saves the Day' Talked About ...
    12              Marshmello - Blocks (Official Music Video)
    13                  Which Countries Are About To Collapse?
    14                                SHOPPING FOR NEW FISH!!!
    15                                        The New SpotMini
    16        One Change That Would Make Pacific Rim a Classic
    17       How does your body know you're full? - Hilary ...
    18                              HomeMade Electric Airplane
    19                Founding An Inbreeding-Free Space Colony
    20                        How Can You Control Your Dreams?
    21       The Making of Hela's Headdress from Thor: Ragn...
    22       Is It Dangerous To Talk To A Camera While Driv...
    23       What $4,800 Will Get You In NYC | Sweet Digs H...
    24                            Using Other People's Showers
    25                  SPAGHETTI BURRITO VS SPAGHETTI BURRITO
    26                    78557 and Proth Primes - Numberphile
    27         A Smart... MUG?! - Take apart a Heated Thermos!
    28       LeBron James admits he was ripping Phil Jackso...
    29                                 Nick Andopolis: Drummer
                                   ...                        
    40919    The History of Fortnite Battle Royale - Did Yo...
    40920    Christina Aguilera - Fall In Line (Official Vi...
    40921    James Bay - Delicate (Taylor Swift cover) in t...
    40922    Mindy Kaling Is Mad She Wasn't Invited to the ...
    40923                           Camels vs. Cactus!!!   جمل
    40924    GIANT Bowl of Lucky Charms CHALLENGE (5,000+ C...
    40925    Diplo, French Montana & Lil Pump ft. Zhavia - ...
    40926                                                  435
    40927            That Time It Rained for Two Million Years
    40928    Watch a Hercules Beetle Metamorphose Before Yo...
    40929                       Mustard, Nick Jonas - Anywhere
    40930    Maddie Poppe Wins American Idol 2018 - Finale ...
    40931    Terrible Magicians | Rudy Mancuso & Juanpa Zurita
    40932      The Voice 2018 Brynn Cartelli - Finale: Skyfall
    40933    8 Survival Myths That Will Definitely Make Thi...
    40934                                  Royal Wedding - SNL
    40935           Nicki Minaj - Chun-Li (Live on SNL / 2018)
    40936                LIE DETECTOR TEST WITH MY GIRLFRIEND!
    40937                      Lucas the Spider - Giant Spider
    40938                 Daddy Yankee - Hielo (Video Oficial)
    40939      SZA - Garden (Say It Like Dat) (Official Video)
    40940    Brad Pitt Bid $120k For A Night With Emilia Cl...
    40941              Dan + Shay - Speechless (Wedding Video)
    40942                Fifth Harmony - Don't Say You Love Me
    40943    BTS Plays With Puppies While Answering Fan Que...
    40944                         The Cat Who Caught the Laser
    40945                           True Facts : Ant Mutualism
    40946    I GAVE SAFIYA NYGAARD A PERFECT HAIR MAKEOVER ...
    40947                  How Black Panther Should Have Ended
    40948    Official Call of Duty®: Black Ops 4 — Multipla...
    Name: title, Length: 40949, dtype: object




```python
videos.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>video_id</th>
      <th>trending_date</th>
      <th>title</th>
      <th>channel_title</th>
      <th>category_id</th>
      <th>publish_time</th>
      <th>tags</th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>thumbnail_link</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2kyS6SvSYSE</td>
      <td>17.14.11</td>
      <td>WE WANT TO TALK ABOUT OUR MARRIAGE</td>
      <td>CaseyNeistat</td>
      <td>22</td>
      <td>2017-11-13T17:13:01.000Z</td>
      <td>SHANtell martin</td>
      <td>748374</td>
      <td>57527</td>
      <td>2966</td>
      <td>15954</td>
      <td>https://i.ytimg.com/vi/2kyS6SvSYSE/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>SHANTELL'S CHANNEL - https://www.youtube.com/s...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1ZAPwfrtAFY</td>
      <td>17.14.11</td>
      <td>The Trump Presidency: Last Week Tonight with J...</td>
      <td>LastWeekTonight</td>
      <td>24</td>
      <td>2017-11-13T07:30:00.000Z</td>
      <td>last week tonight trump presidency|"last week ...</td>
      <td>2418783</td>
      <td>97185</td>
      <td>6146</td>
      <td>12703</td>
      <td>https://i.ytimg.com/vi/1ZAPwfrtAFY/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>One year after the presidential election, John...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5qpjK5DgCt4</td>
      <td>17.14.11</td>
      <td>Racist Superman | Rudy Mancuso, King Bach &amp; Le...</td>
      <td>Rudy Mancuso</td>
      <td>23</td>
      <td>2017-11-12T19:05:24.000Z</td>
      <td>racist superman|"rudy"|"mancuso"|"king"|"bach"...</td>
      <td>3191434</td>
      <td>146033</td>
      <td>5339</td>
      <td>8181</td>
      <td>https://i.ytimg.com/vi/5qpjK5DgCt4/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>WATCH MY PREVIOUS VIDEO ▶ \n\nSUBSCRIBE ► http...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>puqaWrEC7tY</td>
      <td>17.14.11</td>
      <td>Nickelback Lyrics: Real or Fake?</td>
      <td>Good Mythical Morning</td>
      <td>24</td>
      <td>2017-11-13T11:00:04.000Z</td>
      <td>rhett and link|"gmm"|"good mythical morning"|"...</td>
      <td>343168</td>
      <td>10172</td>
      <td>666</td>
      <td>2146</td>
      <td>https://i.ytimg.com/vi/puqaWrEC7tY/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Today we find out if Link is a Nickelback amat...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>d380meD0W0M</td>
      <td>17.14.11</td>
      <td>I Dare You: GOING BALD!?</td>
      <td>nigahiga</td>
      <td>24</td>
      <td>2017-11-12T18:01:41.000Z</td>
      <td>ryan|"higa"|"higatv"|"nigahiga"|"i dare you"|"...</td>
      <td>2095731</td>
      <td>132235</td>
      <td>1989</td>
      <td>17518</td>
      <td>https://i.ytimg.com/vi/d380meD0W0M/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>I know it's been a while since we did this sho...</td>
    </tr>
  </tbody>
</table>
</div>




```python
first_5000 = videos[1:5000]
first_5000.shape
```




    (4999, 16)




```python
stat, p = shapiro(videos.views)
stat2, p2 = stats.normaltest(videos.views)
```

    /usr/local/lib/python3.6/dist-packages/scipy/stats/morestats.py:1660: UserWarning: p-value may not be accurate for N > 5000.
      warnings.warn("p-value may not be accurate for N > 5000.")



```python

```


```python
print("shapiro test p: " + str(p))
print("normal test p: " + str(p2))
```

    shapiro test p: 0.0
    normal test p: 0.0


Testy wskazują na to, że dane dotyczące wyświetleń nie pochodzą z rozkładu normalnego. Jest to spoodziewany wynik biorąc pod uwagę nasz zbiór danych.


```python
videos.shape
```




    (40949, 16)



Removing duplicate video occurances


```python
cleared = videos.drop_duplicates("title") # each title occurs only once
```


```python
cleared.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category_id</th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>6455.000000</td>
      <td>6.455000e+03</td>
      <td>6.455000e+03</td>
      <td>6455.000000</td>
      <td>6455.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>20.435167</td>
      <td>7.725127e+05</td>
      <td>3.446944e+04</td>
      <td>1436.518823</td>
      <td>4488.452672</td>
    </tr>
    <tr>
      <th>std</th>
      <td>7.216801</td>
      <td>1.968870e+06</td>
      <td>1.156808e+05</td>
      <td>11993.978470</td>
      <td>21310.200158</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>5.490000e+02</td>
      <td>0.000000e+00</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>17.000000</td>
      <td>8.401450e+04</td>
      <td>1.932000e+03</td>
      <td>73.000000</td>
      <td>261.500000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>24.000000</td>
      <td>2.740600e+05</td>
      <td>8.011000e+03</td>
      <td>244.000000</td>
      <td>922.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>25.000000</td>
      <td>7.591420e+05</td>
      <td>2.519050e+04</td>
      <td>775.500000</td>
      <td>2854.500000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>43.000000</td>
      <td>4.843165e+07</td>
      <td>3.880071e+06</td>
      <td>629120.000000</td>
      <td>733373.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
cleared.boxplot(column='views')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc8b2cb38>




![png](output_26_1.png)



```python
cleared.boxplot(column='likes')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc8b352e8>




![png](output_27_1.png)


Widzimy, że średnie odtworzeń, lajków, dislajków i komentarzy są w każdym przypadku większe niż ich mediana.
Co więcej, średnie odtworzeń, lajków, dislajków i komentarzy są większe od kwantyla górnego.
Wynika z tego że istnieje niewielki podzbiór filmików o znacznie większej popularności niż reszta


```python
cleared.boxplot(column='dislikes')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc8fd7320>




![png](output_29_1.png)



```python
cleared.boxplot(column='comment_count')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc90929e8>




![png](output_30_1.png)



```python
cleared.shape
```




    (6455, 16)




```python
cleared.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>video_id</th>
      <th>trending_date</th>
      <th>title</th>
      <th>channel_title</th>
      <th>category_id</th>
      <th>publish_time</th>
      <th>tags</th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>thumbnail_link</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2kyS6SvSYSE</td>
      <td>17.14.11</td>
      <td>WE WANT TO TALK ABOUT OUR MARRIAGE</td>
      <td>CaseyNeistat</td>
      <td>22</td>
      <td>2017-11-13T17:13:01.000Z</td>
      <td>SHANtell martin</td>
      <td>748374</td>
      <td>57527</td>
      <td>2966</td>
      <td>15954</td>
      <td>https://i.ytimg.com/vi/2kyS6SvSYSE/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>SHANTELL'S CHANNEL - https://www.youtube.com/s...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1ZAPwfrtAFY</td>
      <td>17.14.11</td>
      <td>The Trump Presidency: Last Week Tonight with J...</td>
      <td>LastWeekTonight</td>
      <td>24</td>
      <td>2017-11-13T07:30:00.000Z</td>
      <td>last week tonight trump presidency|"last week ...</td>
      <td>2418783</td>
      <td>97185</td>
      <td>6146</td>
      <td>12703</td>
      <td>https://i.ytimg.com/vi/1ZAPwfrtAFY/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>One year after the presidential election, John...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5qpjK5DgCt4</td>
      <td>17.14.11</td>
      <td>Racist Superman | Rudy Mancuso, King Bach &amp; Le...</td>
      <td>Rudy Mancuso</td>
      <td>23</td>
      <td>2017-11-12T19:05:24.000Z</td>
      <td>racist superman|"rudy"|"mancuso"|"king"|"bach"...</td>
      <td>3191434</td>
      <td>146033</td>
      <td>5339</td>
      <td>8181</td>
      <td>https://i.ytimg.com/vi/5qpjK5DgCt4/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>WATCH MY PREVIOUS VIDEO ▶ \n\nSUBSCRIBE ► http...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>puqaWrEC7tY</td>
      <td>17.14.11</td>
      <td>Nickelback Lyrics: Real or Fake?</td>
      <td>Good Mythical Morning</td>
      <td>24</td>
      <td>2017-11-13T11:00:04.000Z</td>
      <td>rhett and link|"gmm"|"good mythical morning"|"...</td>
      <td>343168</td>
      <td>10172</td>
      <td>666</td>
      <td>2146</td>
      <td>https://i.ytimg.com/vi/puqaWrEC7tY/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Today we find out if Link is a Nickelback amat...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>d380meD0W0M</td>
      <td>17.14.11</td>
      <td>I Dare You: GOING BALD!?</td>
      <td>nigahiga</td>
      <td>24</td>
      <td>2017-11-12T18:01:41.000Z</td>
      <td>ryan|"higa"|"higatv"|"nigahiga"|"i dare you"|"...</td>
      <td>2095731</td>
      <td>132235</td>
      <td>1989</td>
      <td>17518</td>
      <td>https://i.ytimg.com/vi/d380meD0W0M/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>I know it's been a while since we did this sho...</td>
    </tr>
  </tbody>
</table>
</div>




```python
stat2, p2 = shapiro(cleared.views)
print(p2)
```

    0.0


    /usr/local/lib/python3.6/dist-packages/scipy/stats/morestats.py:1660: UserWarning: p-value may not be accurate for N > 5000.
      warnings.warn("p-value may not be accurate for N > 5000.")



```python
'''Average views'''
videos['views'].mean()
```




    2360784.6382573447




```python
'''Average likes'''
videos['likes'].mean()
```




    74266.7024347359




```python
'''Average dislikes'''
videos['dislikes'].mean()
```




    3711.400888910596




```python
'''Average comment count'''
videos['comment_count'].mean()
```




    8446.803682629612



Histogram ilości wyświetleń


```python
fig, ax = plt.subplots()
_ = sns.distplot(cleared["views"], ax = ax, kde = False, color = "red")
```


![png](output_39_0.png)


Funkcja mapuje dane funkcją transform_func i zwraca dataFrame z przetransformowanymi danymi. Przykład:
  Dla kolumny : [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10], i transfor_func zwwracającej True jeśli liczba jest większa od 5, kolumna po wywołaniu będzie wyglądać następująco : [ False, False, False, False, False, True, True, True, True, True]


```python
def get_transformed_data(data, key, transform_func = lambda elem: elem) :
    '''Mapowanie danych w kolumnie przez transform_func'''
    if ( not type(key) == str):
        raise(Exception('Bad argument'))

    res = data.copy(deep = True)

    collumn = res[key]

    for i in range(0, len(collumn)):
        collumn[i] = transform_func(collumn[i])

    return res
```

Funkcja zwraca dataFrame z ununiętymi danymi które nie spełniają keep_if_predicate.


```python
def get_filtered_data(data, key, keep_if_predicate = lambda elem : True):
    '''Usuwanie danych nie spełniających keep_if_predicate'''
    if ( not type(key) == str):
        raise(Exception('Bad argument'))

    res = data.copy(deep=True)
    collumn = res[key]

    for i in range(0, len(collumn)):
        if not keep_if_predicate(collumn[i]):
            res.drop([i], axis=0, inplace=True)

    return res
```


```python
cleared.groupby('channel_title').sum()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category_id</th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
    </tr>
    <tr>
      <th>channel_title</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12 News</th>
      <td>22</td>
      <td>85643</td>
      <td>170</td>
      <td>45</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1MILLION Dance Studio</th>
      <td>96</td>
      <td>1733477</td>
      <td>122066</td>
      <td>1276</td>
      <td>8527</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1theK (원더케이)</th>
      <td>50</td>
      <td>7035666</td>
      <td>702920</td>
      <td>8435</td>
      <td>49777</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>20th Century Fox</th>
      <td>17</td>
      <td>48572239</td>
      <td>1204019</td>
      <td>21395</td>
      <td>78337</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2CELLOS</th>
      <td>10</td>
      <td>205869</td>
      <td>11198</td>
      <td>120</td>
      <td>446</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3Blue1Brown</th>
      <td>81</td>
      <td>764897</td>
      <td>43188</td>
      <td>262</td>
      <td>3680</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3D Printing Nerd</th>
      <td>56</td>
      <td>88340</td>
      <td>3662</td>
      <td>103</td>
      <td>699</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>42Fab - Metalworking and Multi-Medium Fabrication</th>
      <td>26</td>
      <td>17812</td>
      <td>230</td>
      <td>26</td>
      <td>54</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>494ta</th>
      <td>1</td>
      <td>637942</td>
      <td>793</td>
      <td>38</td>
      <td>137</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4given4ever1</th>
      <td>25</td>
      <td>191953</td>
      <td>6434</td>
      <td>453</td>
      <td>0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5-Minute Crafts</th>
      <td>78</td>
      <td>11723195</td>
      <td>114979</td>
      <td>17247</td>
      <td>12377</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>52 Skillz</th>
      <td>26</td>
      <td>29469</td>
      <td>418</td>
      <td>18</td>
      <td>50</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5FDPVEVO</th>
      <td>20</td>
      <td>811599</td>
      <td>57099</td>
      <td>1055</td>
      <td>4737</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5MadMovieMakers</th>
      <td>48</td>
      <td>661443</td>
      <td>21439</td>
      <td>327</td>
      <td>1295</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5SOSVEVO</th>
      <td>30</td>
      <td>1813858</td>
      <td>359749</td>
      <td>2092</td>
      <td>44012</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>60 Minutes</th>
      <td>50</td>
      <td>198237</td>
      <td>1319</td>
      <td>139</td>
      <td>628</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>730 WVFN</th>
      <td>17</td>
      <td>6064</td>
      <td>7</td>
      <td>23</td>
      <td>19</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>9-1-1 on FOX</th>
      <td>24</td>
      <td>1172</td>
      <td>12</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>90s Commercials</th>
      <td>27</td>
      <td>773</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>A Chick Called Albert</th>
      <td>15</td>
      <td>1481728</td>
      <td>64756</td>
      <td>1015</td>
      <td>10121</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>A&amp;E</th>
      <td>24</td>
      <td>238953</td>
      <td>2488</td>
      <td>191</td>
      <td>905</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>A24</th>
      <td>5</td>
      <td>1944524</td>
      <td>35277</td>
      <td>1659</td>
      <td>3811</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ABC Action News</th>
      <td>74</td>
      <td>1899127</td>
      <td>10695</td>
      <td>1161</td>
      <td>9772</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ABC News</th>
      <td>550</td>
      <td>3951004</td>
      <td>29570</td>
      <td>13841</td>
      <td>30766</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ABC News (Australia)</th>
      <td>50</td>
      <td>3662245</td>
      <td>23527</td>
      <td>1763</td>
      <td>0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ABC Television Network</th>
      <td>96</td>
      <td>4301253</td>
      <td>93232</td>
      <td>2918</td>
      <td>6414</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ABC7</th>
      <td>25</td>
      <td>299090</td>
      <td>10126</td>
      <td>456</td>
      <td>2807</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>AFP news agency</th>
      <td>25</td>
      <td>3634</td>
      <td>19</td>
      <td>2</td>
      <td>2</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>AIA awards</th>
      <td>22</td>
      <td>66888</td>
      <td>1098</td>
      <td>427</td>
      <td>313</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ALL URBAN CENTRAL</th>
      <td>24</td>
      <td>481171</td>
      <td>3989</td>
      <td>228</td>
      <td>495</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>taalk com</th>
      <td>26</td>
      <td>1107</td>
      <td>19</td>
      <td>2</td>
      <td>4</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>thataylaa</th>
      <td>26</td>
      <td>149709</td>
      <td>9401</td>
      <td>105</td>
      <td>868</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>the Hacksmith</th>
      <td>56</td>
      <td>1686731</td>
      <td>43101</td>
      <td>1261</td>
      <td>5807</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>theCHIVE</th>
      <td>24</td>
      <td>158013</td>
      <td>2232</td>
      <td>72</td>
      <td>197</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>theSkimm</th>
      <td>22</td>
      <td>11216</td>
      <td>2</td>
      <td>14</td>
      <td>13</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>thegameawards</th>
      <td>20</td>
      <td>1287342</td>
      <td>26557</td>
      <td>1467</td>
      <td>1905</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>theneedledrop</th>
      <td>60</td>
      <td>910139</td>
      <td>36595</td>
      <td>1494</td>
      <td>8816</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>thevirts</th>
      <td>24</td>
      <td>91085</td>
      <td>4085</td>
      <td>75</td>
      <td>586</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>todrickhall</th>
      <td>96</td>
      <td>693281</td>
      <td>57820</td>
      <td>982</td>
      <td>4228</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>tonyvera1902</th>
      <td>22</td>
      <td>103423</td>
      <td>1248</td>
      <td>81</td>
      <td>272</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>toofacedcosmetics</th>
      <td>26</td>
      <td>13747</td>
      <td>877</td>
      <td>39</td>
      <td>77</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>tovestyrkeVEVO</th>
      <td>10</td>
      <td>8720</td>
      <td>1155</td>
      <td>7</td>
      <td>96</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>truTV</th>
      <td>120</td>
      <td>923273</td>
      <td>16800</td>
      <td>1891</td>
      <td>2541</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>videogamedunkey</th>
      <td>60</td>
      <td>8128634</td>
      <td>479810</td>
      <td>11749</td>
      <td>34342</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>vlogbrothers</th>
      <td>242</td>
      <td>1751991</td>
      <td>129582</td>
      <td>1601</td>
      <td>16286</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>vnbreyes</th>
      <td>17</td>
      <td>2863</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>voordeel</th>
      <td>24</td>
      <td>50741</td>
      <td>5963</td>
      <td>27</td>
      <td>448</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>wdwmagic</th>
      <td>19</td>
      <td>188213</td>
      <td>4174</td>
      <td>2743</td>
      <td>2525</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>weezer</th>
      <td>10</td>
      <td>1191202</td>
      <td>37446</td>
      <td>1281</td>
      <td>3226</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>yeokm1</th>
      <td>28</td>
      <td>24311</td>
      <td>176</td>
      <td>2</td>
      <td>42</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>yovo68</th>
      <td>2</td>
      <td>304017</td>
      <td>3446</td>
      <td>158</td>
      <td>1925</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>zefrank1</th>
      <td>66</td>
      <td>2178525</td>
      <td>185964</td>
      <td>1336</td>
      <td>20571</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Алексей Навальный</th>
      <td>29</td>
      <td>5579079</td>
      <td>365439</td>
      <td>63579</td>
      <td>77462</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Никита Ордынский</th>
      <td>1</td>
      <td>282380</td>
      <td>35093</td>
      <td>303</td>
      <td>1944</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ТСН</th>
      <td>25</td>
      <td>180711</td>
      <td>1667</td>
      <td>418</td>
      <td>1082</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ワーナー ブラザース 公式チャンネル</th>
      <td>1</td>
      <td>755014</td>
      <td>15686</td>
      <td>558</td>
      <td>1768</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>圧倒的不審者の極み!</th>
      <td>28</td>
      <td>294419</td>
      <td>7310</td>
      <td>378</td>
      <td>2780</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>杰威爾音樂 JVR Music</th>
      <td>20</td>
      <td>22873768</td>
      <td>180699</td>
      <td>11758</td>
      <td>20164</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>郭韋辰</th>
      <td>28</td>
      <td>12594</td>
      <td>48</td>
      <td>1</td>
      <td>4</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>영국남자 Korean Englishman</th>
      <td>23</td>
      <td>588820</td>
      <td>18317</td>
      <td>254</td>
      <td>2214</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>2198 rows × 8 columns</p>
</div>




```python
cleared.groupby('channel_title').count()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>video_id</th>
      <th>trending_date</th>
      <th>title</th>
      <th>category_id</th>
      <th>publish_time</th>
      <th>tags</th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>thumbnail_link</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
      <th>description</th>
    </tr>
    <tr>
      <th>channel_title</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12 News</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1MILLION Dance Studio</th>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1theK (원더케이)</th>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
    </tr>
    <tr>
      <th>20th Century Fox</th>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
    </tr>
    <tr>
      <th>2CELLOS</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3Blue1Brown</th>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3D Printing Nerd</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>42Fab - Metalworking and Multi-Medium Fabrication</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>494ta</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4given4ever1</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5-Minute Crafts</th>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>52 Skillz</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5FDPVEVO</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>5MadMovieMakers</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>5SOSVEVO</th>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>60 Minutes</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>730 WVFN</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9-1-1 on FOX</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>90s Commercials</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>A Chick Called Albert</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>A&amp;E</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>A24</th>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
    </tr>
    <tr>
      <th>ABC Action News</th>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>ABC News</th>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
    </tr>
    <tr>
      <th>ABC News (Australia)</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>ABC Television Network</th>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>ABC7</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>AFP news agency</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>AIA awards</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>ALL URBAN CENTRAL</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>taalk com</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>thataylaa</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>the Hacksmith</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>theCHIVE</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>theSkimm</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>thegameawards</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>theneedledrop</th>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
      <td>6</td>
    </tr>
    <tr>
      <th>thevirts</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>todrickhall</th>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>tonyvera1902</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>toofacedcosmetics</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>tovestyrkeVEVO</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>truTV</th>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
    </tr>
    <tr>
      <th>videogamedunkey</th>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>vlogbrothers</th>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
    </tr>
    <tr>
      <th>vnbreyes</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>voordeel</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>wdwmagic</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>weezer</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>yeokm1</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>yovo68</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>zefrank1</th>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Алексей Навальный</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Никита Ордынский</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>ТСН</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>ワーナー ブラザース 公式チャンネル</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>圧倒的不審者の極み!</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>杰威爾音樂 JVR Music</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>郭韋辰</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>영국남자 Korean Englishman</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>2198 rows × 15 columns</p>
</div>




```python
stat3, p3 = shapiro(cleared.groupby('channel_title').count())
stat4, p4 = stats.normaltest(cleared.groupby('channel_title').count())
```

    /usr/local/lib/python3.6/dist-packages/scipy/stats/morestats.py:1660: UserWarning: p-value may not be accurate for N > 5000.
      warnings.warn("p-value may not be accurate for N > 5000.")



```python
print(str(p3) + " " + str(p4))
```

    0.0 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]


Rozkład ilości filmików w zależności od kanałów też nie jest normalny

Funkcja mapuje indeks kategorii w nazwę kategorii.


```python
def get_category_name( category_id ):

    category_map = {
    2 : 'Autos & Vehicles',
    1 :'Film & Animation',
    10 : 'Music',
    15 : 'Pets & Animals',
    17 : 'Sports',
    18 : 'Short Movies',
    19 : 'Travel & Events',
    20 : 'Gaming',
    21 : 'Videoblogging',
    22 : 'People & Blogs',
    23 : 'Comedy',
    24 : 'Entertainment',
    25 : 'News & Politics',
    26 : 'Howto & Style',
    27 : 'Education',
    28 : 'Science & Technology',
    29 : 'Nonprofits & Activism',
    30 : 'Movies',
    31 : 'Anime / Animation',
    32 : 'Action / Adventure',
    33 : 'Classics',
    34 : 'Comedy',
    35 : 'Documentary',
    36 : 'Drama',
    37 : 'Family',
    38 : 'Foreign',
    39 : 'Horror',
    40 : 'Sci - Fi / Fantasy',
    41 : 'Thriller',
    42 : 'Shorts',
    43 : 'Shows',
    44 : 'Trailers',
    }
    return category_map[category_id]
```

Zmapowanie numerów kategorii na ich nazwy 


```python
cleared['category_id'] = cleared['category_id'].map(get_category_name)
```

    /usr/local/lib/python3.6/dist-packages/ipykernel_launcher.py:1: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      """Entry point for launching an IPython kernel.


Funkcja formatująca opisy poszczególnych słupków w histogramie


```python
def slice_collumn_description(collumn_description, slice_length):
    if ( type(collumn_description) != str ):
        raise(Exception('Cannot format non string object'))
    result = ""
    for i in range(0,len(collumn_description)):
        if ( i % slice_length == 0 ):
            result = result + "\n"
        result = result + collumn_description[i]

    return result

```

Inicjalizacja słownika przechowującego zliczenia do rysowania wykresów


```python
counts = {}
```

Funkcja wstawiająca do słownika powtórzeń danych


```python
def insert_to_counts(key,val):
    global counts
    if ( type(key) != str):
        return
    if ( key not in counts.keys() ):
        counts.update({key : val})
    else:
        counts[key] += val
```

Funkcja tworząca medianę danego pogrupowanego DataFrame według danej kolumny


```python
def print_histogramme_median(groups, collumn):
    global counts
    counts = {}
    if ( type(collumn) != str ):
        raise(Exception("Collumn name must be string"))
    for key, group in groups:
        #print('{:>20}:{:>16}'.format(key, (group['dislikes']).median()) )
        insert_to_counts(slice_collumn_description(key, 8), int(round((group[collumn]).median())) )
    serie = pd.Series(counts)
    serie.plot.bar()
    plt.title('median')
    plt.show()
```

Funkcja tworząca dolny kwartyl danego pogrupowanego DataFrame według danej kolumny


```python
def print_histogramme_down_quantile(groups, collumn):
    global counts
    counts = {}
    if ( type(collumn) != str ):
        raise(Exception("Collumn name must be string"))
    histogram_data = []
    for key, group in groups:
        #print('{:>20}:{:>16}'.format(key, (group['dislikes']).median()) )
        insert_to_counts(slice_collumn_description(key, 8), int(round((group[collumn]).quantile(0.25))) )
    serie = pd.Series(counts)
    serie.plot.bar()
    plt.title('down quantile')
    plt.show()
```

Funkcja tworząca górny kwartyl danego pogrupowanego DataFrame według danej kolumny


```python
def print_histogramme_up_quantile(groups, collumn):
    global counts
    counts = {}
    if ( type(collumn) != str ):
        raise(Exception("Collumn name must be string"))
    histogram_data = []
    for key, group in groups:
        #print('{:>20}:{:>16}'.format(key, (group['dislikes']).median()) )
        insert_to_counts(slice_collumn_description(key, 8), int(round((group[collumn]).quantile(0.75))) )
        #histogram_data += int(round((group[collumn]).quantile(0.75))) * [slice_collumn_description(key, 6)]
    serie = pd.Series(counts)
    serie.plot.bar()
    plt.title('up quantile')
    plt.show()
```

Funkcja tworząca rozstęp międzykwartylowy danego pogrupowanego DataFrame według danej kolumny


```python
def print_histogramme_interquartile_range(groups, collumn):
    global counts
    counts = {}
    if ( type(collumn) != str ):
        raise(Exception("Collumn name must be string"))
    histogram_data = []
    for key, group in groups:
        #print('{:>20}:{:>16}'.format(key, (group['dislikes']).median()) )
        #histogram_data += int(round((group[collumn]).quantile(0.75) - (group[collumn]).quantile(0.25)) ) * [slice_collumn_description(key,6)]
        insert_to_counts(slice_collumn_description(key, 8), int(round((group[collumn]).quantile(0.75) - (group[collumn]).quantile(0.25)) ))
    serie = pd.Series(counts)
    serie.plot.bar()
    plt.title('interquartile range')
    plt.show()
```

Funkcja tworząca rozstęp maksymalną wartość danego pogrupowanego DataFrame według danej kolumny


```python
def print_histogramme_max(groups, collumn):
    global counts
    counts = {}
    if ( type(collumn) != str ):
        raise(Exception("Collumn name must be string"))
    histogram_data = []
    for key, group in groups:
        #print('{:>20}:{:>16}'.format(key, (group['dislikes']).median()) )
        #histogram_data += int(round((group[collumn]).max() ) ) * [slice_collumn_description(key, 6 )]
        insert_to_counts(slice_collumn_description(key, 8), int(round((group[collumn]).max())))
    serie = pd.Series(counts)
    serie.plot.bar()
    plt.title('max')
    plt.show()
```

Funkcja tworząca rozstęp minimalną wartość danego pogrupowanego DataFrame według danej kolumny


```python
def print_histogramme_min(groups, collumn):
    global counts
    counts = {}
    if ( type(collumn) != str ):
        raise(Exception("Collumn name must be string"))
    histogram_data = []
    for key, group in groups:
        #print('{:>20}:{:>16}'.format(key, (group['dislikes']).median()) )
        #histogram_data += int(round((group[collumn]).min() ) ) * [slice_collumn_description(key, 6 )]
        insert_to_counts(slice_collumn_description(key, 8), int(round((group[collumn]).min())))
    serie = pd.Series(counts)
    serie.plot.bar()
    plt.title('min')
    plt.show()
```

Funkcja tworząca różnicę między wartością maksymalną, a minimalną dla danego pogrupowanego DataFrame według danej kolumny


```python
def print_histogramme_difference(groups, collumn):
    global counts
    counts = {}
    if ( type(collumn) != str ):
        raise(Exception("Collumn name must be string"))
    histogram_data = []
    for key, group in groups:
        #print('{:>20}:{:>16}'.format(key, (group['dislikes']).median()) )
        #histogram_data += int(round((group[collumn]).max() - (group[collumn]).min() ) ) * [slice_collumn_description(key, 6)]
        insert_to_counts(slice_collumn_description(key, 8), int(round((group[collumn]).max() - (group[collumn]).min() ) ) )
    serie = pd.Series(counts)
    serie.plot.bar()
    plt.title('difference')
    plt.show()
```

Funkcja tworząca wartiancję dla danego pogrupowanego DataFrame według danej kolumny


```python
def print_histogramme_var(groups, collumn):
    global counts
    counts = {}
    if ( type(collumn) != str ):
        raise(Exception("Collumn name must be string"))
    histogram_data = []
    for key, group in groups:
        #print('{:>20}:{:>16}'.format(key, (group['dislikes']).median()) )
        #histogram_data += int(round(np.sqrt((group[collumn]).var())) ) * [slice_collumn_description(key, 6)]
        insert_to_counts(slice_collumn_description(key, 8), int(round(np.sqrt((group[collumn]).var())) ))
    serie = pd.Series(counts)
    serie.plot.bar()
    plt.title('var')
    plt.show()
```

Funkcja wypisująca wszystkie powyższe histogramy dla danej kolumny


```python
def print_all_histogramms(groups, collumn):
  print_histogramme_median(groups, collumn)
  print_histogramme_down_quantile(groups, collumn)
  print_histogramme_up_quantile(groups, collumn)
  print_histogramme_interquartile_range(groups, collumn)
  print_histogramme_max(groups, collumn)
  print_histogramme_min(groups, collumn)
  print_histogramme_difference(groups, collumn)
  print_histogramme_var(groups, collumn)
```


```python

```

**WYŚWIETLENIA:**

Średnia liczba wyświetlań dla poszczególnych kategorii:


```python
groups = cleared.groupby('category_id')
means = groups.mean()['views']
means.sort_values(ascending = False)
```




    category_id
    Music                    1.476113e+06
    Gaming                   1.207646e+06
    Nonprofits & Activism    9.574350e+05
    Film & Animation         9.261853e+05
    Entertainment            8.278752e+05
    Sports                   8.226218e+05
    Comedy                   7.745786e+05
    People & Blogs           7.093150e+05
    Science & Technology     5.813676e+05
    Howto & Style            4.724811e+05
    Autos & Vehicles         3.969387e+05
    Shows                    3.923588e+05
    Education                3.610156e+05
    Pets & Animals           2.947162e+05
    Travel & Events          2.686901e+05
    News & Politics          2.501705e+05
    Name: views, dtype: float64



Mediana dla liczby wyświetlań dla poszczególnych kategorii:


```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['views']).median()) )
```

        Autos & Vehicles:        150609.0
                  Comedy:        488580.0
               Education:        233448.0
           Entertainment:        325804.0
        Film & Animation:        349383.0
                  Gaming:        656404.5
           Howto & Style:        240659.0
                   Music:        380480.0
         News & Politics:         84968.0
    Nonprofits & Activism:         23866.0
          People & Blogs:        224742.0
          Pets & Animals:        157979.5
    Science & Technology:        235349.0
                   Shows:        383647.5
                  Sports:        300075.0
         Travel & Events:        200068.0


Kwantyl dolny:


```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['views']).quantile(0.25)) )
```

        Autos & Vehicles:         33272.5
                  Comedy:        149097.0
               Education:        145226.5
           Entertainment:       103180.25
        Film & Animation:         56072.0
                  Gaming:        161524.5
           Howto & Style:         84715.5
                   Music:        110822.0
         News & Politics:         21759.5
    Nonprofits & Activism:          7772.5
          People & Blogs:         84490.0
          Pets & Animals:        64873.75
    Science & Technology:        72806.75
                   Shows:        91736.25
                  Sports:        98725.25
         Travel & Events:         59758.0


Kwantyl górny:


```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['views']).quantile(0.75)) )
```

        Autos & Vehicles:       546863.25
                  Comedy:       1030313.0
               Education:        396935.0
           Entertainment:       839683.25
        Film & Animation:       1267983.0
                  Gaming:       1663793.0
           Howto & Style:       575034.25
                   Music:       1063265.5
         News & Politics:        316235.5
    Nonprofits & Activism:        166376.5
          People & Blogs:        739895.0
          Pets & Animals:        355153.5
    Science & Technology:        622540.5
                   Shows:        684270.0
                  Sports:       753776.25
         Travel & Events:        365603.0


Rozstęp międzykwartlowy:


```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['views']).quantile(0.75) - (group['views']).quantile(0.25)) )
```

        Autos & Vehicles:       513590.75
                  Comedy:        881216.0
               Education:        251708.5
           Entertainment:        736503.0
        Film & Animation:       1211911.0
                  Gaming:       1502268.5
           Howto & Style:       490318.75
                   Music:        952443.5
         News & Politics:        294476.0
    Nonprofits & Activism:        158604.0
          People & Blogs:        655405.0
          Pets & Animals:       290279.75
    Science & Technology:       549733.75
                   Shows:       592533.75
                  Sports:        655051.0
         Travel & Events:        305845.0


Maksymalna liczba wyświetleń dla poszczególnych kategorii:


```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['views']).max()) )
```

        Autos & Vehicles:         4361881
                  Comedy:         7177410
               Education:         2769297
           Entertainment:        37736281
        Film & Animation:        10385748
                  Gaming:         6886948
           Howto & Style:         7566372
                   Music:        48431654
         News & Politics:         4857665
    Nonprofits & Activism:         8041970
          People & Blogs:        20921796
          Pets & Animals:         2184856
    Science & Technology:        15006579
                   Shows:          765531
                  Sports:        14647590
         Travel & Events:         1851299


Minimalna liczba wyświetleń dla poszczególnych kategorii:


```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['views']).min()) )
```

        Autos & Vehicles:            2860
                  Comedy:            1807
               Education:             773
           Entertainment:             798
        Film & Animation:             943
                  Gaming:            1237
           Howto & Style:            1107
                   Music:            1591
         News & Politics:             549
    Nonprofits & Activism:            1456
          People & Blogs:             884
          Pets & Animals:            3393
    Science & Technology:             983
                   Shows:           36609
                  Sports:             658
         Travel & Events:             789


Rozstęp liczby wyświetleń dla poszczególnych kategorii:


```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['views'].max() - group['views'].min() ) ) )

```

        Autos & Vehicles:         4359021
                  Comedy:         7175603
               Education:         2768524
           Entertainment:        37735483
        Film & Animation:        10384805
                  Gaming:         6885711
           Howto & Style:         7565265
                   Music:        48430063
         News & Politics:         4857116
    Nonprofits & Activism:         8040514
          People & Blogs:        20920912
          Pets & Animals:         2181463
    Science & Technology:        15005596
                   Shows:          728922
                  Sports:        14646932
         Travel & Events:         1850510


Odchylenie standardowe dla liczby wyświetleń:


```python
for key, group in groups:
    print('{:>20}:{:>20}'.format(key, np.sqrt((group['views']).var())) )
```

        Autos & Vehicles:   638376.9285051583
                  Comedy:   937083.9681337501
               Education:   424058.5573772929
           Entertainment:  2002553.2523835364
        Film & Animation:  1493236.5348523648
                  Gaming:  1504321.2455531708
           Howto & Style:   729933.3770968313
                   Music:   3898330.169350798
         News & Politics:   434371.8189158471
    Nonprofits & Activism:   2422783.559440905
          People & Blogs:  1513615.4434677702
          Pets & Animals:   394434.2836900752
    Science & Technology:  1277857.3677477553
                   Shows:   372205.3752382547
                  Sports:  1774740.9436058681
         Travel & Events:  307891.82383146504


Histogramy:


```python
print_all_histogramms(groups, 'views')
```


![png](output_98_0.png)



![png](output_98_1.png)



![png](output_98_2.png)



![png](output_98_3.png)



![png](output_98_4.png)



![png](output_98_5.png)



![png](output_98_6.png)



![png](output_98_7.png)


**KLIKNIĘCIA 'LIKE':**

Średnia liczba kliknięć dla poszczególnych kategorii:


```python
#videos['category_id'] = videos['category_id'].map(get_category_name)
groups = cleared.groupby('category_id')
means = groups.mean()['likes']
means.sort_values(ascending = False)
```




    category_id
    Music                    103725.312576
    Nonprofits & Activism    103604.666667
    Gaming                    49470.538462
    Comedy                    39093.529946
    People & Blogs            32012.178218
    Film & Animation          30317.915888
    Entertainment             27172.359927
    Howto & Style             23814.669435
    Sports                    19690.260965
    Science & Technology      17831.912821
    Education                 15891.746094
    Shows                     11786.250000
    Pets & Animals            11277.063380
    Autos & Vehicles           6260.708333
    Travel & Events            5681.000000
    News & Politics            3890.405088
    Name: likes, dtype: float64






```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['likes']).median()) )

```

        Autos & Vehicles:          1798.5
                  Comedy:         17037.0
               Education:          9798.0
           Entertainment:          7639.0
        Film & Animation:          6563.0
                  Gaming:         22783.5
           Howto & Style:         11295.0
                   Music:         23709.0
         News & Politics:           736.0
    Nonprofits & Activism:           686.0
          People & Blogs:          9195.0
          Pets & Animals:          6711.0
    Science & Technology:          8135.0
                   Shows:         11114.0
                  Sports:          3561.0
         Travel & Events:          3300.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['likes']).quantile(0.25)))
```

        Autos & Vehicles:          374.25
                  Comedy:          5160.5
               Education:          5658.0
           Entertainment:         2153.75
        Film & Animation:          1291.0
                  Gaming:          6797.0
           Howto & Style:         3525.25
                   Music:          5074.5
         News & Politics:           167.0
    Nonprofits & Activism:            70.5
          People & Blogs:          2724.0
          Pets & Animals:         2382.75
    Science & Technology:         1524.25
                   Shows:         2862.75
                  Sports:          1129.0
         Travel & Events:           766.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['likes']).quantile(0.75)) )
```

        Autos & Vehicles:          9339.0
                  Comedy:         45842.0
               Education:         14892.0
           Entertainment:        22349.25
        Film & Animation:         35093.0
                  Gaming:        75677.75
           Howto & Style:         28167.0
                   Music:         85955.5
         News & Politics:          3399.0
    Nonprofits & Activism:          4273.5
          People & Blogs:         29162.0
          Pets & Animals:        15621.25
    Science & Technology:         19732.5
                   Shows:         20037.5
                  Sports:         8289.75
         Travel & Events:          9156.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['likes']).quantile(0.75) - (group['likes']).quantile(0.25)) )
```

        Autos & Vehicles:         8964.75
                  Comedy:         40681.5
               Education:          9234.0
           Entertainment:         20195.5
        Film & Animation:         33802.0
                  Gaming:        68880.75
           Howto & Style:        24641.75
                   Music:         80881.0
         News & Politics:          3232.0
    Nonprofits & Activism:          4203.0
          People & Blogs:         26438.0
          Pets & Animals:         13238.5
    Science & Technology:        18208.25
                   Shows:        17174.75
                  Sports:         7160.75
         Travel & Events:          8390.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['likes']).max()) )
```

        Autos & Vehicles:           28361
                  Comedy:          442915
               Education:          163576
           Entertainment:         1735895
        Film & Animation:          409750
                  Gaming:          340240
           Howto & Style:          293145
                   Music:         3880071
         News & Politics:          125700
    Nonprofits & Activism:         1167488
          People & Blogs:         1366736
          Pets & Animals:          108555
    Science & Technology:          278743
                   Shows:           24107
                  Sports:          889008
         Travel & Events:           21626



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['likes']).min()) )
```

        Autos & Vehicles:               4
                  Comedy:               6
               Education:               0
           Entertainment:               0
        Film & Animation:               0
                  Gaming:               2
           Howto & Style:               0
                   Music:               0
         News & Politics:               0
    Nonprofits & Activism:               0
          People & Blogs:               0
          Pets & Animals:               6
    Science & Technology:               0
                   Shows:             810
                  Sports:               0
         Travel & Events:               3



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['likes'].max() - group['likes'].min() ) ) )
```

        Autos & Vehicles:           28357
                  Comedy:          442909
               Education:          163576
           Entertainment:         1735895
        Film & Animation:          409750
                  Gaming:          340238
           Howto & Style:          293145
                   Music:         3880071
         News & Politics:          125700
    Nonprofits & Activism:         1167488
          People & Blogs:         1366736
          Pets & Animals:          108549
    Science & Technology:          278743
                   Shows:           23297
                  Sports:          889008
         Travel & Events:           21623



```python
for key, group in groups:
    print('{:>20}:{:>20}'.format(key, np.sqrt((group['likes']).var())) )
```

        Autos & Vehicles:    8465.91883391666
                  Comedy:  59377.191300781145
               Education:  26177.752235363172
           Entertainment:   86769.16839172764
        Film & Animation:  54903.126181163665
                  Gaming:  62347.209757939025
           Howto & Style:   35767.03616877136
                   Music:  263412.29769357806
         News & Politics:   8719.703707993163
    Nonprofits & Activism:   308878.2113158302
          People & Blogs:     82397.329063204
          Pets & Animals:  14073.230921519855
    Science & Technology:   29633.20517083412
                   Shows:  11368.109821631153
                  Sports:   76620.37977922041
         Travel & Events:   5733.788739786285


Histogramy:


```python
print_all_histogramms(groups, 'likes')
```


![png](output_112_0.png)



![png](output_112_1.png)



![png](output_112_2.png)



![png](output_112_3.png)



![png](output_112_4.png)



![png](output_112_5.png)



![png](output_112_6.png)



![png](output_112_7.png)


**KLIKNIĘCIA 'DISLIKE':**


```python
# videos['category_id'] = videos['category_id'].map(get_category_name)
groups = cleared.groupby('category_id')
means = groups.mean()['dislikes']
print(means.sort_values(ascending = False))
```

    category_id
    Nonprofits & Activism    14161.666667
    Gaming                    3652.423077
    Music                     2550.748474
    Entertainment             1852.656516
    People & Blogs            1686.172277
    Comedy                    1044.036298
    Sports                    1042.618421
    News & Politics            952.500978
    Film & Animation           928.015576
    Science & Technology       779.515385
    Howto & Style              608.189369
    Education                  444.527344
    Autos & Vehicles           366.916667
    Travel & Events            314.030769
    Shows                      254.250000
    Pets & Animals             216.225352
    Name: dislikes, dtype: float64



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['dislikes']).median()) )
```

        Autos & Vehicles:           108.5
                  Comedy:           448.0
               Education:           215.0
           Entertainment:           268.0
        Film & Animation:           266.0
                  Gaming:           703.5
           Howto & Style:           229.5
                   Music:           367.0
         News & Politics:           167.0
    Nonprofits & Activism:            33.0
          People & Blogs:           209.0
          Pets & Animals:           108.0
    Science & Technology:           214.5
                   Shows:           245.0
                  Sports:           168.5
         Travel & Events:            94.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['dislikes']).quantile(0.25)))
```

        Autos & Vehicles:           32.25
                  Comedy:           132.0
               Education:           100.0
           Entertainment:            92.0
        Film & Animation:            42.0
                  Gaming:          156.75
           Howto & Style:            84.0
                   Music:           105.0
         News & Politics:            38.5
    Nonprofits & Activism:             3.0
          People & Blogs:            60.0
          Pets & Animals:            31.5
    Science & Technology:           44.75
                   Shows:           151.5
                  Sports:            44.0
         Travel & Events:            31.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['dislikes']).quantile(0.75)) )
```

        Autos & Vehicles:          367.25
                  Comedy:          1181.5
               Education:          508.25
           Entertainment:           767.5
        Film & Animation:          1028.0
                  Gaming:         2476.75
           Howto & Style:           579.5
                   Music:          1566.0
         News & Politics:           508.0
    Nonprofits & Activism:           159.0
          People & Blogs:           835.0
          Pets & Animals:           283.0
    Science & Technology:           690.0
                   Shows:          347.75
                  Sports:          509.75
         Travel & Events:           300.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['dislikes']).quantile(0.75) - (group['dislikes']).quantile(0.25)) )
```

        Autos & Vehicles:           335.0
                  Comedy:          1049.5
               Education:          408.25
           Entertainment:           675.5
        Film & Animation:           986.0
                  Gaming:          2320.0
           Howto & Style:           495.5
                   Music:          1461.0
         News & Politics:           469.5
    Nonprofits & Activism:           156.0
          People & Blogs:           775.0
          Pets & Animals:           251.5
    Science & Technology:          645.25
                   Shows:          196.25
                  Sports:          465.75
         Travel & Events:           269.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['dislikes']).max()) )
```

        Autos & Vehicles:            9681
                  Comedy:           28023
               Education:            5684
           Entertainment:          629120
        Film & Animation:           10274
                  Gaming:          164004
           Howto & Style:           21877
                   Music:           96407
         News & Politics:          110707
    Nonprofits & Activism:          147643
          People & Blogs:          218841
          Pets & Animals:            2499
    Science & Technology:           26234
                   Shows:             461
                  Sports:          117128
         Travel & Events:            3068



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['dislikes']).min()) )
```

        Autos & Vehicles:               0
                  Comedy:               0
               Education:               0
           Entertainment:               0
        Film & Animation:               0
                  Gaming:               0
           Howto & Style:               0
                   Music:               0
         News & Politics:               0
    Nonprofits & Activism:               0
          People & Blogs:               0
          Pets & Animals:               0
    Science & Technology:               0
                   Shows:              66
                  Sports:               0
         Travel & Events:               0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['dislikes'].max() - group['dislikes'].min() ) ) )
```

        Autos & Vehicles:            9681
                  Comedy:           28023
               Education:            5684
           Entertainment:          629120
        Film & Animation:           10274
                  Gaming:          164004
           Howto & Style:           21877
                   Music:           96407
         News & Politics:          110707
    Nonprofits & Activism:          147643
          People & Blogs:          218841
          Pets & Animals:            2499
    Science & Technology:           26234
                   Shows:             395
                  Sports:          117128
         Travel & Events:            3068



```python
for key, group in groups:
    print('{:>20}:{:>20}'.format(key, np.sqrt((group['dislikes']).var())) )
```

        Autos & Vehicles:   1154.524007155586
                  Comedy:   2002.033820109416
               Education:   688.8118152996841
           Entertainment:  21017.513717562568
        Film & Animation:  1577.0395692187371
                  Gaming:  16367.199997587119
           Howto & Style:   1438.509269749303
                   Music:   7574.826554308369
         News & Politics:   5243.146479677251
    Nonprofits & Activism:  40384.762511314075
          People & Blogs:  11236.618327438715
          Pets & Animals:   321.5863029960975
    Science & Technology:   2166.869735567766
                   Shows:  170.10266507808356
                  Sports:   5998.286848760367
         Travel & Events:   585.0540832166386


Histogramy:


```python
print_all_histogramms(groups, 'dislikes')
```


![png](output_124_0.png)



![png](output_124_1.png)



![png](output_124_2.png)



![png](output_124_3.png)



![png](output_124_4.png)



![png](output_124_5.png)



![png](output_124_6.png)



![png](output_124_7.png)


**KOMENTARZE**




```python
groups = cleared.groupby('category_id')
means = groups.mean()['comment_count']
print(means.sort_values(ascending = False))
```

    category_id
    Nonprofits & Activism    29612.466667
    Music                    10340.279609
    Gaming                   10193.298077
    People & Blogs            4670.429703
    Comedy                    4206.023593
    Entertainment             4189.474421
    Film & Animation          4076.713396
    Howto & Style             3885.458472
    Sports                    2564.848684
    Science & Technology      2469.853846
    Education                 1952.835938
    Pets & Animals            1669.823944
    News & Politics           1498.356164
    Autos & Vehicles          1250.694444
    Shows                     1228.750000
    Travel & Events           1025.061538
    Name: comment_count, dtype: float64



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['comment_count']).median()) )
```

        Autos & Vehicles:           288.5
                  Comedy:          1399.0
               Education:          1014.0
           Entertainment:           825.5
        Film & Animation:           782.0
                  Gaming:          3694.5
           Howto & Style:          1052.5
                   Music:          1667.0
         News & Politics:           445.0
    Nonprofits & Activism:            37.0
          People & Blogs:           914.0
          Pets & Animals:           590.5
    Science & Technology:           873.0
                   Shows:          1435.0
                  Sports:           670.0
         Travel & Events:           451.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['comment_count']).quantile(0.25)))
```

        Autos & Vehicles:           47.75
                  Comedy:           421.5
               Education:          548.75
           Entertainment:          268.25
        Film & Animation:           139.0
                  Gaming:          605.75
           Howto & Style:           371.0
                   Music:           413.5
         News & Politics:            81.0
    Nonprofits & Activism:            13.0
          People & Blogs:           289.0
          Pets & Animals:          220.75
    Science & Technology:          178.25
                   Shows:          1124.5
                  Sports:          203.25
         Travel & Events:           113.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['comment_count']).quantile(0.75)) )
```

        Autos & Vehicles:          1676.0
                  Comedy:          4126.5
               Education:          2335.5
           Entertainment:          2446.5
        Film & Animation:          4170.0
                  Gaming:        12057.25
           Howto & Style:          2740.5
                   Music:          6859.5
         News & Politics:          1410.5
    Nonprofits & Activism:           665.5
          People & Blogs:          2685.0
          Pets & Animals:         1632.75
    Science & Technology:         2334.25
                   Shows:         1539.25
                  Sports:         1826.25
         Travel & Events:          1586.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['comment_count']).quantile(0.75) - (group['comment_count']).quantile(0.25)) )
```

        Autos & Vehicles:         1628.25
                  Comedy:          3705.0
               Education:         1786.75
           Entertainment:         2178.25
        Film & Animation:          4031.0
                  Gaming:         11451.5
           Howto & Style:          2369.5
                   Music:          6446.0
         News & Politics:          1329.5
    Nonprofits & Activism:           652.5
          People & Blogs:          2396.0
          Pets & Animals:          1412.0
    Science & Technology:          2156.0
                   Shows:          414.75
                  Sports:          1623.0
         Travel & Events:          1473.0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['comment_count']).max()) )
```

        Autos & Vehicles:            8316
                  Comedy:           75753
               Education:           17991
           Entertainment:          733373
        Film & Animation:           87691
                  Gaming:          115501
           Howto & Style:          171128
                   Music:          692305
         News & Politics:           35908
    Nonprofits & Activism:          363133
          People & Blogs:          321455
          Pets & Animals:           32598
    Science & Technology:           89194
                   Shows:            1825
                  Sports:           71486
         Travel & Events:            4533



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['comment_count']).min()) )
```

        Autos & Vehicles:               0
                  Comedy:               0
               Education:               0
           Entertainment:               0
        Film & Animation:               0
                  Gaming:               0
           Howto & Style:               0
                   Music:               0
         News & Politics:               0
    Nonprofits & Activism:               0
          People & Blogs:               0
          Pets & Animals:               0
    Science & Technology:               0
                   Shows:             220
                  Sports:               0
         Travel & Events:               0



```python
for key, group in groups:
    print('{:>20}:{:>16}'.format(key, (group['comment_count'].max() - group['comment_count'].min() ) ) )
```

        Autos & Vehicles:            8316
                  Comedy:           75753
               Education:           17991
           Entertainment:          733373
        Film & Animation:           87691
                  Gaming:          115501
           Howto & Style:          171128
                   Music:          692305
         News & Politics:           35908
    Nonprofits & Activism:          363133
          People & Blogs:          321455
          Pets & Animals:           32598
    Science & Technology:           89194
                   Shows:            1605
                  Sports:           71486
         Travel & Events:            4533



```python
for key, group in groups:
    print('{:>20}:{:>20}'.format(key, np.sqrt((group['comment_count']).var())) )
```

        Autos & Vehicles:  1999.9421445074734
                  Comedy:   8016.253583891952
               Education:   2767.958625660813
           Entertainment:  26034.449466774633
        Film & Animation:   8637.559275417521
                  Gaming:  16513.852384594895
           Howto & Style:  12830.104251612982
                   Music:   37931.56205729364
         News & Politics:   3303.816541450943
    Nonprofits & Activism:   94383.62106308991
          People & Blogs:     21433.755590697
          Pets & Animals:  3395.1729424596138
    Science & Technology:    6174.82289134754
                   Shows:   697.2160712433413
                  Sports:   6840.882045910763
         Travel & Events:  1221.9699627666166


Histogramy:


```python
print_all_histogramms(groups, 'comment_count')
```


![png](output_137_0.png)



![png](output_137_1.png)



![png](output_137_2.png)



![png](output_137_3.png)



![png](output_137_4.png)



![png](output_137_5.png)



![png](output_137_6.png)



![png](output_137_7.png)


# Korelacje


```python
cleared.corr()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>views</th>
      <td>1.000000</td>
      <td>0.750826</td>
      <td>0.392653</td>
      <td>0.589413</td>
      <td>0.011443</td>
      <td>0.009686</td>
      <td>0.002591</td>
    </tr>
    <tr>
      <th>likes</th>
      <td>0.750826</td>
      <td>1.000000</td>
      <td>0.361905</td>
      <td>0.806204</td>
      <td>-0.025570</td>
      <td>-0.020362</td>
      <td>-0.002543</td>
    </tr>
    <tr>
      <th>dislikes</th>
      <td>0.392653</td>
      <td>0.361905</td>
      <td>1.000000</td>
      <td>0.680250</td>
      <td>-0.003154</td>
      <td>-0.008185</td>
      <td>0.000418</td>
    </tr>
    <tr>
      <th>comment_count</th>
      <td>0.589413</td>
      <td>0.806204</td>
      <td>0.680250</td>
      <td>1.000000</td>
      <td>-0.027217</td>
      <td>-0.013956</td>
      <td>-0.001755</td>
    </tr>
    <tr>
      <th>comments_disabled</th>
      <td>0.011443</td>
      <td>-0.025570</td>
      <td>-0.003154</td>
      <td>-0.027217</td>
      <td>1.000000</td>
      <td>0.295850</td>
      <td>-0.003217</td>
    </tr>
    <tr>
      <th>ratings_disabled</th>
      <td>0.009686</td>
      <td>-0.020362</td>
      <td>-0.008185</td>
      <td>-0.013956</td>
      <td>0.295850</td>
      <td>1.000000</td>
      <td>-0.001702</td>
    </tr>
    <tr>
      <th>video_error_or_removed</th>
      <td>0.002591</td>
      <td>-0.002543</td>
      <td>0.000418</td>
      <td>-0.001755</td>
      <td>-0.003217</td>
      <td>-0.001702</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



Dzięki powyszej tabeli widzimy korelacje ilości wyświetleń, polubień i komentarzy, czego można było się spodziewać - częściej oglądany filmik powinien być częściej komentowany i lubiany. Z "dislikami" nie ma tak mocnej korelacji prawdopodobnie dlatego, że większość ludzi którzy nie lubią filmiku rzadko go wyświetla więcej niż raz, a osoby które go polubiły, mogą go wyświetlać więcej razy. Zbiór danych opiera się na filmikach z listy polecanych, więc raczej są to filmiki mające dużo więcej "lajków" niż "dislików". 

Co ciekawe istnieje też korelacja między ilością komentarzy i "dislajków". Prawdopodobnie wynika to z tego, że osoby którym filmik się nie podobał często uargumentowują w komentarzu czemu, niekiedy doprowadzając do dyskusji

Sprawdźmy jak by to wyglądało dla filmików z wyłączonymi komentarzami


```python
comments_disabled = cleared[cleared.comments_disabled == False]
```


```python
comments_disabled.corr()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>views</th>
      <td>1.000000</td>
      <td>0.759012</td>
      <td>0.395660</td>
      <td>0.596935</td>
      <td>NaN</td>
      <td>-0.005786</td>
      <td>0.002659</td>
    </tr>
    <tr>
      <th>likes</th>
      <td>0.759012</td>
      <td>1.000000</td>
      <td>0.361891</td>
      <td>0.806301</td>
      <td>NaN</td>
      <td>-0.013543</td>
      <td>-0.002627</td>
    </tr>
    <tr>
      <th>dislikes</th>
      <td>0.395660</td>
      <td>0.361891</td>
      <td>1.000000</td>
      <td>0.680762</td>
      <td>NaN</td>
      <td>-0.005402</td>
      <td>0.000408</td>
    </tr>
    <tr>
      <th>comment_count</th>
      <td>0.596935</td>
      <td>0.806301</td>
      <td>0.680762</td>
      <td>1.000000</td>
      <td>NaN</td>
      <td>-0.008961</td>
      <td>-0.001843</td>
    </tr>
    <tr>
      <th>comments_disabled</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>ratings_disabled</th>
      <td>-0.005786</td>
      <td>-0.013543</td>
      <td>-0.005402</td>
      <td>-0.008961</td>
      <td>NaN</td>
      <td>1.000000</td>
      <td>-0.001137</td>
    </tr>
    <tr>
      <th>video_error_or_removed</th>
      <td>0.002659</td>
      <td>-0.002627</td>
      <td>0.000408</td>
      <td>-0.001843</td>
      <td>NaN</td>
      <td>-0.001137</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



# Linear regression of likes and views


```python
views_and_likes = cleared[['views', 'likes']]
views_and_likes.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>views</th>
      <th>likes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>748374</td>
      <td>57527</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2418783</td>
      <td>97185</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3191434</td>
      <td>146033</td>
    </tr>
    <tr>
      <th>3</th>
      <td>343168</td>
      <td>10172</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2095731</td>
      <td>132235</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.scatter(views_and_likes[['views']], views_and_likes[['likes']])
plt.xlabel("views")
plt.ylabel("likes")
plt.show()
```


![png](output_146_0.png)



```python
lm = linear_model.LinearRegression()
model = lm.fit(views_and_likes[['views']], views_and_likes[['likes']])
lm.score(views_and_likes[['views']], views_and_likes[['likes']]) # współczynnik determinacji
```




    0.5637401826719866




```python
likes_predict = model.predict # linear regression
likes_predict(views_and_likes[['views']])
```




    array([[ 33404.56522102],
           [107094.21504431],
           [141179.50890086],
           ...,
           [ 38513.71321668],
           [ 24218.77459462],
           [ 13461.2200867 ]])




```python
plt.scatter(views_and_likes[['views']], views_and_likes[['likes']], color = "red")
plt.plot(views_and_likes[['views']], likes_predict(views_and_likes[['views']]))
plt.xlabel("views")
plt.ylabel("likes") 
plt.title('likes[views]')
```




    Text(0.5, 1.0, 'likes[views]')




![png](output_149_1.png)


# Analiza i Regresja tylko dla odtworzeń mniejszych niż średnia


```python
means = []
for i in range (cleared.shape[0]):
  means.append(int((cleared["views"].mean())))
#mean_vals = pd.DataFrame({"mean": means})
```




```python
lower_vals = cleared[cleared.views < cleared["views"].mean()]
```


```python
lower_vals.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>4862.000000</td>
      <td>4862.000000</td>
      <td>4862.000000</td>
      <td>4862.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>227565.126697</td>
      <td>9427.861785</td>
      <td>362.295969</td>
      <td>1191.065611</td>
    </tr>
    <tr>
      <th>std</th>
      <td>206097.533025</td>
      <td>13904.876430</td>
      <td>1353.824158</td>
      <td>2380.529183</td>
    </tr>
    <tr>
      <th>min</th>
      <td>549.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>52612.250000</td>
      <td>1124.750000</td>
      <td>47.250000</td>
      <td>169.250000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>165197.500000</td>
      <td>4603.000000</td>
      <td>143.000000</td>
      <td>563.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>352645.250000</td>
      <td>11981.750000</td>
      <td>356.000000</td>
      <td>1383.750000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>771035.000000</td>
      <td>159356.000000</td>
      <td>71617.000000</td>
      <td>83363.000000</td>
    </tr>
  </tbody>
</table>
</div>



w przypadku filmików o ilości odtworzeń mniejszej niż średnia zbiór jest bardziej "zbity" - średnie odtworzeń, lajków i komentarzy są mniejsz niż kwantyl górny, ale, co ciekawe, dislajków nie



```python
lower_vals.boxplot(column="views")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc8fdd198>




![png](output_156_1.png)



```python
lower_vals.boxplot(column="likes")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc8bdc4e0>




![png](output_157_1.png)



```python
lower_vals.boxplot(column="dislikes")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc910bb70>




![png](output_158_1.png)



```python
lower_vals.boxplot(column="comment_count")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc86e29b0>




![png](output_159_1.png)



```python
cleared.shape[0]
```




    6455




```python
lower_vals.shape
```




    (4862, 16)




```python
fig, ax = plt.subplots()
_ = sns.distplot(lower_vals["views"], ax = ax, kde = False, color = "red")
```


![png](output_162_0.png)



```python
m = cleared["views"].mean()
print(m)
```

    772512.7439194423



```python
cleared["views"][2]
```




    3191434




```python
cleared[2:3]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>video_id</th>
      <th>trending_date</th>
      <th>title</th>
      <th>channel_title</th>
      <th>category_id</th>
      <th>publish_time</th>
      <th>tags</th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>thumbnail_link</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>5qpjK5DgCt4</td>
      <td>17.14.11</td>
      <td>Racist Superman | Rudy Mancuso, King Bach &amp; Le...</td>
      <td>Rudy Mancuso</td>
      <td>Comedy</td>
      <td>2017-11-12T19:05:24.000Z</td>
      <td>racist superman|"rudy"|"mancuso"|"king"|"bach"...</td>
      <td>3191434</td>
      <td>146033</td>
      <td>5339</td>
      <td>8181</td>
      <td>https://i.ytimg.com/vi/5qpjK5DgCt4/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>WATCH MY PREVIOUS VIDEO ▶ \n\nSUBSCRIBE ► http...</td>
    </tr>
  </tbody>
</table>
</div>




```python
lower_vals
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>video_id</th>
      <th>trending_date</th>
      <th>title</th>
      <th>channel_title</th>
      <th>category_id</th>
      <th>publish_time</th>
      <th>tags</th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>thumbnail_link</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2kyS6SvSYSE</td>
      <td>17.14.11</td>
      <td>WE WANT TO TALK ABOUT OUR MARRIAGE</td>
      <td>CaseyNeistat</td>
      <td>People &amp; Blogs</td>
      <td>2017-11-13T17:13:01.000Z</td>
      <td>SHANtell martin</td>
      <td>748374</td>
      <td>57527</td>
      <td>2966</td>
      <td>15954</td>
      <td>https://i.ytimg.com/vi/2kyS6SvSYSE/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>SHANTELL'S CHANNEL - https://www.youtube.com/s...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>puqaWrEC7tY</td>
      <td>17.14.11</td>
      <td>Nickelback Lyrics: Real or Fake?</td>
      <td>Good Mythical Morning</td>
      <td>Entertainment</td>
      <td>2017-11-13T11:00:04.000Z</td>
      <td>rhett and link|"gmm"|"good mythical morning"|"...</td>
      <td>343168</td>
      <td>10172</td>
      <td>666</td>
      <td>2146</td>
      <td>https://i.ytimg.com/vi/puqaWrEC7tY/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Today we find out if Link is a Nickelback amat...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>gHZ1Qz0KiKM</td>
      <td>17.14.11</td>
      <td>2 Weeks with iPhone X</td>
      <td>iJustine</td>
      <td>Science &amp; Technology</td>
      <td>2017-11-13T19:07:23.000Z</td>
      <td>ijustine|"week with iPhone X"|"iphone x"|"appl...</td>
      <td>119180</td>
      <td>9763</td>
      <td>511</td>
      <td>1434</td>
      <td>https://i.ytimg.com/vi/gHZ1Qz0KiKM/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Using the iPhone for the past two weeks -- her...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>TUmyygCMMGA</td>
      <td>17.14.11</td>
      <td>Why the rise of the robots won’t mean the end ...</td>
      <td>Vox</td>
      <td>News &amp; Politics</td>
      <td>2017-11-13T13:45:16.000Z</td>
      <td>vox.com|"vox"|"explain"|"shift change"|"future...</td>
      <td>256426</td>
      <td>12654</td>
      <td>1363</td>
      <td>2368</td>
      <td>https://i.ytimg.com/vi/TUmyygCMMGA/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>For now, at least, we have better things to wo...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>9wRQljFNDW8</td>
      <td>17.14.11</td>
      <td>Dion Lewis' 103-Yd Kick Return TD vs. Denver! ...</td>
      <td>NFL</td>
      <td>Sports</td>
      <td>2017-11-13T02:05:26.000Z</td>
      <td>NFL|"Football"|"offense"|"defense"|"afc"|"nfc"...</td>
      <td>81377</td>
      <td>655</td>
      <td>25</td>
      <td>177</td>
      <td>https://i.ytimg.com/vi/9wRQljFNDW8/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>New England Patriots returner Dion Lewis blast...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>VifQlJit6A0</td>
      <td>17.14.11</td>
      <td>(SPOILERS) 'Shiva Saves the Day' Talked About ...</td>
      <td>amc</td>
      <td>Entertainment</td>
      <td>2017-11-13T03:00:00.000Z</td>
      <td>The Walking Dead|"shiva"|"tiger"|"king ezekiel...</td>
      <td>104578</td>
      <td>1576</td>
      <td>303</td>
      <td>1279</td>
      <td>https://i.ytimg.com/vi/VifQlJit6A0/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Shiva arrives just in time as King Ezekiel att...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>5E4ZBSInqUU</td>
      <td>17.14.11</td>
      <td>Marshmello - Blocks (Official Music Video)</td>
      <td>marshmello</td>
      <td>Music</td>
      <td>2017-11-13T17:00:00.000Z</td>
      <td>marshmello|"blocks"|"marshmello blocks"|"block...</td>
      <td>687582</td>
      <td>114188</td>
      <td>1333</td>
      <td>8371</td>
      <td>https://i.ytimg.com/vi/5E4ZBSInqUU/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>WATCH SILENCE MUSIC VIDEO ▶ https://youtu.be/T...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>GgVmn66oK_A</td>
      <td>17.14.11</td>
      <td>Which Countries Are About To Collapse?</td>
      <td>NowThis World</td>
      <td>News &amp; Politics</td>
      <td>2017-11-12T14:00:00.000Z</td>
      <td>nowthis|"nowthis world"|"world news"|"nowthis ...</td>
      <td>544770</td>
      <td>7848</td>
      <td>1171</td>
      <td>3981</td>
      <td>https://i.ytimg.com/vi/GgVmn66oK_A/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>The world at large is improving, but some coun...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>TaTleo4cOs8</td>
      <td>17.14.11</td>
      <td>SHOPPING FOR NEW FISH!!!</td>
      <td>The king of DIY</td>
      <td>Pets &amp; Animals</td>
      <td>2017-11-12T18:30:01.000Z</td>
      <td>shopping for new fish|"new fish"|"aquarium fis...</td>
      <td>207532</td>
      <td>7473</td>
      <td>246</td>
      <td>2120</td>
      <td>https://i.ytimg.com/vi/TaTleo4cOs8/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Today we go shopping for new fish for some of ...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>kgaO45SyaO4</td>
      <td>17.14.11</td>
      <td>The New SpotMini</td>
      <td>BostonDynamics</td>
      <td>Science &amp; Technology</td>
      <td>2017-11-13T20:09:58.000Z</td>
      <td>Robots|"Boston Dynamics"|"SpotMini"|"Legged Lo...</td>
      <td>75752</td>
      <td>9419</td>
      <td>52</td>
      <td>1230</td>
      <td>https://i.ytimg.com/vi/kgaO45SyaO4/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>For more information . . . stay tuned.</td>
    </tr>
    <tr>
      <th>16</th>
      <td>ZAQs-ctOqXQ</td>
      <td>17.14.11</td>
      <td>One Change That Would Make Pacific Rim a Classic</td>
      <td>Cracked</td>
      <td>Comedy</td>
      <td>2017-11-12T17:00:05.000Z</td>
      <td>pacific rim|"pacific rim 2"|"pacific rim seque...</td>
      <td>295639</td>
      <td>8011</td>
      <td>638</td>
      <td>1256</td>
      <td>https://i.ytimg.com/vi/ZAQs-ctOqXQ/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Pacific Rim was so good, we can’t believe they...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>YVfyYrEmzgM</td>
      <td>17.14.11</td>
      <td>How does your body know you're full? - Hilary ...</td>
      <td>TED-Ed</td>
      <td>Education</td>
      <td>2017-11-13T16:00:07.000Z</td>
      <td>TED|"TED-Ed"|"TED Education"|"TED Ed"|"Hilary ...</td>
      <td>78044</td>
      <td>5398</td>
      <td>53</td>
      <td>385</td>
      <td>https://i.ytimg.com/vi/YVfyYrEmzgM/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Check out our Patreon page: https://www.patreo...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>eNSN6qet1kE</td>
      <td>17.14.11</td>
      <td>HomeMade Electric Airplane</td>
      <td>PeterSripol</td>
      <td>Science &amp; Technology</td>
      <td>2017-11-13T15:30:17.000Z</td>
      <td>ultralight|"airplane"|"homemade"|"DIY"|"hoverb...</td>
      <td>97007</td>
      <td>11963</td>
      <td>36</td>
      <td>2211</td>
      <td>https://i.ytimg.com/vi/eNSN6qet1kE/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>aaaannnd now to fly out of ground effect! The ...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>B5HORANmzHw</td>
      <td>17.14.11</td>
      <td>Founding An Inbreeding-Free Space Colony</td>
      <td>SciShow</td>
      <td>Education</td>
      <td>2017-11-12T22:00:01.000Z</td>
      <td>SciShow|"science"|"Hank"|"Green"|"education"|"...</td>
      <td>223871</td>
      <td>8421</td>
      <td>191</td>
      <td>1214</td>
      <td>https://i.ytimg.com/vi/B5HORANmzHw/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Thanks to 23AndMe for supporting SciShow. Thes...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>vU14JY3x81A</td>
      <td>17.14.11</td>
      <td>How Can You Control Your Dreams?</td>
      <td>Life Noggin</td>
      <td>Education</td>
      <td>2017-11-13T14:00:03.000Z</td>
      <td>life noggin|"life noggin youtube"|"youtube lif...</td>
      <td>115791</td>
      <td>9586</td>
      <td>75</td>
      <td>2800</td>
      <td>https://i.ytimg.com/vi/vU14JY3x81A/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>What if there was a way to control your dreams...</td>
    </tr>
    <tr>
      <th>21</th>
      <td>6VhU_T463sU</td>
      <td>17.14.11</td>
      <td>The Making of Hela's Headdress from Thor: Ragn...</td>
      <td>Tested</td>
      <td>Science &amp; Technology</td>
      <td>2017-11-12T15:00:01.000Z</td>
      <td>tested|"testedcom"|"designercon 2017"|"preview...</td>
      <td>224019</td>
      <td>3585</td>
      <td>138</td>
      <td>208</td>
      <td>https://i.ytimg.com/vi/6VhU_T463sU/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>At this year's DesignerCon, we meet up with Ir...</td>
    </tr>
    <tr>
      <th>22</th>
      <td>_-aDHxoblr4</td>
      <td>17.14.11</td>
      <td>Is It Dangerous To Talk To A Camera While Driv...</td>
      <td>Tom Scott</td>
      <td>Education</td>
      <td>2017-11-13T16:00:03.000Z</td>
      <td>tom scott|"tomscott"|"built for science"|"nati...</td>
      <td>144418</td>
      <td>11758</td>
      <td>89</td>
      <td>1014</td>
      <td>https://i.ytimg.com/vi/_-aDHxoblr4/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>I'm visiting the University of Iowa's National...</td>
    </tr>
    <tr>
      <th>23</th>
      <td>JBZTZZAcFTw</td>
      <td>17.14.11</td>
      <td>What $4,800 Will Get You In NYC | Sweet Digs H...</td>
      <td>Refinery29</td>
      <td>Howto &amp; Style</td>
      <td>2017-11-12T16:00:01.000Z</td>
      <td>refinery29|"refinery 29"|"r29"|"r29 video"|"vi...</td>
      <td>145921</td>
      <td>1707</td>
      <td>578</td>
      <td>673</td>
      <td>https://i.ytimg.com/vi/JBZTZZAcFTw/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>On this episode of Sweet Digs, we tour Social ...</td>
    </tr>
    <tr>
      <th>24</th>
      <td>lZ68j2J_GOM</td>
      <td>17.14.11</td>
      <td>Using Other People's Showers</td>
      <td>Gus Johnson</td>
      <td>Comedy</td>
      <td>2017-11-13T14:44:24.000Z</td>
      <td>using other peoples showers|"gus"|"gus shower"...</td>
      <td>33980</td>
      <td>4884</td>
      <td>52</td>
      <td>234</td>
      <td>https://i.ytimg.com/vi/lZ68j2J_GOM/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Why is it so hard to figure out other people's...</td>
    </tr>
    <tr>
      <th>25</th>
      <td>dRpNZV18N_g</td>
      <td>17.14.11</td>
      <td>SPAGHETTI BURRITO VS SPAGHETTI BURRITO</td>
      <td>HellthyJunkFood</td>
      <td>Entertainment</td>
      <td>2017-11-12T14:00:04.000Z</td>
      <td>spaghetti burrito|"diy burrito"|"spaghetti"|"b...</td>
      <td>223077</td>
      <td>8676</td>
      <td>193</td>
      <td>1392</td>
      <td>https://i.ytimg.com/vi/dRpNZV18N_g/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Visit http://www.Bongiovibrand.com\nand get 20...</td>
    </tr>
    <tr>
      <th>26</th>
      <td>fcVjitaM3LY</td>
      <td>17.14.11</td>
      <td>78557 and Proth Primes - Numberphile</td>
      <td>Numberphile</td>
      <td>Science &amp; Technology</td>
      <td>2017-11-13T12:13:36.000Z</td>
      <td>numberphile|"prime numbers"|"proth prime"</td>
      <td>80705</td>
      <td>4687</td>
      <td>41</td>
      <td>437</td>
      <td>https://i.ytimg.com/vi/fcVjitaM3LY/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>James Grime is back and talking prime numbers....</td>
    </tr>
    <tr>
      <th>27</th>
      <td>qeWvgZLz9yU</td>
      <td>17.14.11</td>
      <td>A Smart... MUG?! - Take apart a Heated Thermos!</td>
      <td>JerryRigEverything</td>
      <td>Howto &amp; Style</td>
      <td>2017-11-13T16:00:03.000Z</td>
      <td>Smart mug|"Heated thermos"|"tech"|"gift idea"|...</td>
      <td>120727</td>
      <td>9033</td>
      <td>224</td>
      <td>1346</td>
      <td>https://i.ytimg.com/vi/qeWvgZLz9yU/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>YouTubes new channel!: https://www.youtube.com...</td>
    </tr>
    <tr>
      <th>28</th>
      <td>iIxy3JN3-jc</td>
      <td>17.14.11</td>
      <td>LeBron James admits he was ripping Phil Jackso...</td>
      <td>Cleveland Cavaliers on cleveland.com</td>
      <td>News &amp; Politics</td>
      <td>2017-11-13T15:42:28.000Z</td>
      <td>auth-jvardon-auth</td>
      <td>27943</td>
      <td>156</td>
      <td>36</td>
      <td>83</td>
      <td>https://i.ytimg.com/vi/iIxy3JN3-jc/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>LeBron James gave another all-time press confe...</td>
    </tr>
    <tr>
      <th>29</th>
      <td>n30k5CwLhS4</td>
      <td>17.14.11</td>
      <td>Nick Andopolis: Drummer</td>
      <td>FaeryInLoveInc</td>
      <td>Film &amp; Animation</td>
      <td>2011-05-29T17:03:12.000Z</td>
      <td>freaks and geeks|"jason segel"|"judd apatow"|"...</td>
      <td>50867</td>
      <td>715</td>
      <td>238</td>
      <td>246</td>
      <td>https://i.ytimg.com/vi/n30k5CwLhS4/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>The opening of Freaks and Geeks Episode 6, I'm...</td>
    </tr>
    <tr>
      <th>30</th>
      <td>U0hAC8O7RoI</td>
      <td>17.14.11</td>
      <td>I TOOK THE $3,000,000 LAMBO TO CARMAX! They of...</td>
      <td>hp_overload</td>
      <td>Autos &amp; Vehicles</td>
      <td>2017-11-13T01:43:12.000Z</td>
      <td>carmax|"lamborghini miura"|"miura carmax"|"lam...</td>
      <td>98378</td>
      <td>4035</td>
      <td>495</td>
      <td>486</td>
      <td>https://i.ytimg.com/vi/U0hAC8O7RoI/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Finally took the Miura to Carmax! Hope you enj...</td>
    </tr>
    <tr>
      <th>31</th>
      <td>CBVGjS_EJok</td>
      <td>17.14.11</td>
      <td>Amazon Christmas Advert 2017 - Toys &amp; Games</td>
      <td>Amazon.co.uk</td>
      <td>Entertainment</td>
      <td>2017-11-06T17:52:50.000Z</td>
      <td>Amazon|"Amazon Christmas"|"Amazon Xmas"|"Chris...</td>
      <td>26000</td>
      <td>119</td>
      <td>69</td>
      <td>0</td>
      <td>https://i.ytimg.com/vi/CBVGjS_EJok/default.jpg</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Shhhh. See how Amazon helps Dad create a magic...</td>
    </tr>
    <tr>
      <th>33</th>
      <td>hz7ukDjuq4w</td>
      <td>17.14.11</td>
      <td>What's Inside a Detectives Car?</td>
      <td>officer401</td>
      <td>Entertainment</td>
      <td>2017-11-12T23:41:37.000Z</td>
      <td>detective|"officer"|"401"|"officer401"|"police...</td>
      <td>67661</td>
      <td>3781</td>
      <td>84</td>
      <td>626</td>
      <td>https://i.ytimg.com/vi/hz7ukDjuq4w/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Intro Song: Dion Timmer - Lost\nEnding Song: S...</td>
    </tr>
    <tr>
      <th>34</th>
      <td>p2hJxyF7mok</td>
      <td>17.14.11</td>
      <td>New Emirates First Class Suite | Boeing 777 | ...</td>
      <td>Emirates</td>
      <td>Travel &amp; Events</td>
      <td>2017-11-12T05:55:42.000Z</td>
      <td>Emirates|"First Class"</td>
      <td>141148</td>
      <td>1661</td>
      <td>70</td>
      <td>236</td>
      <td>https://i.ytimg.com/vi/p2hJxyF7mok/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>We are the changing the game in First Class tr...</td>
    </tr>
    <tr>
      <th>35</th>
      <td>0mlNzVSJrT0</td>
      <td>17.14.11</td>
      <td>Me-O Cats Commercial</td>
      <td>Nobrand</td>
      <td>People &amp; Blogs</td>
      <td>2017-04-21T06:47:32.000Z</td>
      <td>cute|"cats"|"thai"|"eggs"</td>
      <td>98966</td>
      <td>2486</td>
      <td>184</td>
      <td>532</td>
      <td>https://i.ytimg.com/vi/0mlNzVSJrT0/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Kittens come out of the eggs in a Thai commerc...</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Om_zGhJLZ5U</td>
      <td>17.14.11</td>
      <td>TL;DW - Every DCEU Movie Before Justice League</td>
      <td>Screen Junkies</td>
      <td>Film &amp; Animation</td>
      <td>2017-11-12T18:00:03.000Z</td>
      <td>screenjunkies|"screen junkies"|"sj news"|"hone...</td>
      <td>288922</td>
      <td>7515</td>
      <td>792</td>
      <td>2111</td>
      <td>https://i.ytimg.com/vi/Om_zGhJLZ5U/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>With Justice League approaching fast we rewatc...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>39757</th>
      <td>ObDgFUTP0PA</td>
      <td>18.09.06</td>
      <td>Ghosts (Official Video) - Mike Shinoda</td>
      <td>Mike Shinoda</td>
      <td>Music</td>
      <td>2018-06-08T04:00:22.000Z</td>
      <td>Mike Shinoda|"Ghosts"|"Other"|"Post Traumatic"...</td>
      <td>281755</td>
      <td>33802</td>
      <td>266</td>
      <td>3370</td>
      <td>https://i.ytimg.com/vi/ObDgFUTP0PA/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Pre-Order Post Traumatic: http://mshnd.co/PTPr...</td>
    </tr>
    <tr>
      <th>39758</th>
      <td>K5lmXgKi6V0</td>
      <td>18.09.06</td>
      <td>Sandra Bullock, Sarah Paulson &amp; Awkwafina's Se...</td>
      <td>The Late Late Show with James Corden</td>
      <td>Entertainment</td>
      <td>2018-06-08T08:35:00.000Z</td>
      <td>The Late Late Show|"Late Late Show"|"James Cor...</td>
      <td>216239</td>
      <td>4542</td>
      <td>123</td>
      <td>171</td>
      <td>https://i.ytimg.com/vi/K5lmXgKi6V0/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>After asking Awkwafina about the rap song that...</td>
    </tr>
    <tr>
      <th>39762</th>
      <td>Nd3zqXro_P0</td>
      <td>18.09.06</td>
      <td>Why 350°F is the magic number for baking</td>
      <td>Vox</td>
      <td>News &amp; Politics</td>
      <td>2018-06-07T12:00:02.000Z</td>
      <td>baking|"maillard reaction"|"s pen"|"Vox.com"|"...</td>
      <td>429174</td>
      <td>10910</td>
      <td>374</td>
      <td>1111</td>
      <td>https://i.ytimg.com/vi/Nd3zqXro_P0/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Turns out there’s a lot of chemistry in cookin...</td>
    </tr>
    <tr>
      <th>39763</th>
      <td>4wIBCoxuOJ0</td>
      <td>18.09.06</td>
      <td>How one scientist averted a national health cr...</td>
      <td>TED-Ed</td>
      <td>Education</td>
      <td>2018-06-07T15:02:33.000Z</td>
      <td>TED|"TED-Ed"|"Teded"|"Ted Education"|"TED Ed"|...</td>
      <td>227695</td>
      <td>13435</td>
      <td>444</td>
      <td>866</td>
      <td>https://i.ytimg.com/vi/4wIBCoxuOJ0/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>TED wants to promote student ideas! Learn more...</td>
    </tr>
    <tr>
      <th>39765</th>
      <td>UXNnAB3ugPU</td>
      <td>18.09.06</td>
      <td>How My Makeup Looks Under a Microscope</td>
      <td>Tina Yong</td>
      <td>Howto &amp; Style</td>
      <td>2018-06-07T23:48:17.000Z</td>
      <td>Tina yong|"Tina Tries It"|"Microscope Makeup"|...</td>
      <td>676111</td>
      <td>23613</td>
      <td>525</td>
      <td>1773</td>
      <td>https://i.ytimg.com/vi/UXNnAB3ugPU/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>In this video, I look at my makeup under a mic...</td>
    </tr>
    <tr>
      <th>39770</th>
      <td>97Gh0LRdKu4</td>
      <td>18.09.06</td>
      <td>Queer Eye: Season 2 | Trailer [HD] | Netflix</td>
      <td>Netflix</td>
      <td>Entertainment</td>
      <td>2018-06-07T14:59:35.000Z</td>
      <td>Netflix|"Trailer"|"Netflix Original Series"|"N...</td>
      <td>249342</td>
      <td>4827</td>
      <td>316</td>
      <td>418</td>
      <td>https://i.ytimg.com/vi/97Gh0LRdKu4/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>I'M NOT CRYING, YOU'RE CRYING. Your best frien...</td>
    </tr>
    <tr>
      <th>39787</th>
      <td>sxS9h8mU2Yk</td>
      <td>18.09.06</td>
      <td>Sabrina Carpenter - Almost Love (Official Lyri...</td>
      <td>SabrinaCarpenterVEVO</td>
      <td>Music</td>
      <td>2018-06-06T04:00:01.000Z</td>
      <td>sabrina carpenter|"almost love"|"wango tango"|...</td>
      <td>489002</td>
      <td>49225</td>
      <td>480</td>
      <td>4041</td>
      <td>https://i.ytimg.com/vi/sxS9h8mU2Yk/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Almost Love is available here:Download: http:/...</td>
    </tr>
    <tr>
      <th>39949</th>
      <td>L4pkD78oKSo</td>
      <td>18.10.06</td>
      <td>Belmont Stakes 2018 I FULL RACE I Justify's Pu...</td>
      <td>NBC Sports</td>
      <td>Sports</td>
      <td>2018-06-09T23:10:07.000Z</td>
      <td>Horse racing|"horses"|"horse"|"racing"|"Triple...</td>
      <td>594004</td>
      <td>4470</td>
      <td>520</td>
      <td>1195</td>
      <td>https://i.ytimg.com/vi/L4pkD78oKSo/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Watch as Justify attempts to become only the 1...</td>
    </tr>
    <tr>
      <th>39954</th>
      <td>Js04hvdVUA8</td>
      <td>18.10.06</td>
      <td>Madden NFL 19 – Official Reveal Trailer</td>
      <td>EA SPORTS</td>
      <td>Gaming</td>
      <td>2018-06-09T18:42:53.000Z</td>
      <td>madden|"madden 19"|"madden nfl 19"|"nfl madden...</td>
      <td>454950</td>
      <td>7769</td>
      <td>1109</td>
      <td>2080</td>
      <td>https://i.ytimg.com/vi/Js04hvdVUA8/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Available August 10th. Madden 19 challenges yo...</td>
    </tr>
    <tr>
      <th>39958</th>
      <td>KRc50VFUsTk</td>
      <td>18.10.06</td>
      <td>FASHION PHOTO RUVIEW: Evil Twin with Raja and ...</td>
      <td>WOWPresents</td>
      <td>Entertainment</td>
      <td>2018-06-09T13:00:02.000Z</td>
      <td>world of wonder|"world of wonder productions"|...</td>
      <td>274294</td>
      <td>7285</td>
      <td>1365</td>
      <td>2268</td>
      <td>https://i.ytimg.com/vi/KRc50VFUsTk/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Is Drag Race available in your country? Watch ...</td>
    </tr>
    <tr>
      <th>39965</th>
      <td>L8RxxVYWTIQ</td>
      <td>18.10.06</td>
      <td>Shootout with Ronaldinho at Nicky Jam World Cu...</td>
      <td>Will Smith</td>
      <td>Entertainment</td>
      <td>2018-06-09T01:01:19.000Z</td>
      <td>comedy|"entertainment"|"will smith"|"will"|"sm...</td>
      <td>724963</td>
      <td>35342</td>
      <td>359</td>
      <td>1943</td>
      <td>https://i.ytimg.com/vi/L8RxxVYWTIQ/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Go behind the scenes of the music video shoot ...</td>
    </tr>
    <tr>
      <th>39975</th>
      <td>L9FA9U4s3Tg</td>
      <td>18.10.06</td>
      <td>Josh Groban - Granted (Official Lyric Video)</td>
      <td>Josh Groban</td>
      <td>Music</td>
      <td>2018-06-08T04:00:20.000Z</td>
      <td>josh groban|"granted"|"new song"|"new single"|...</td>
      <td>116841</td>
      <td>6767</td>
      <td>75</td>
      <td>403</td>
      <td>https://i.ytimg.com/vi/L9FA9U4s3Tg/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>The Official Lyric Video for Josh Groban's new...</td>
    </tr>
    <tr>
      <th>40152</th>
      <td>NBupCHcJ914</td>
      <td>18.11.06</td>
      <td>DIY MASTER EP 3: Boho Shibori Tie Dye</td>
      <td>LaurDIY</td>
      <td>Howto &amp; Style</td>
      <td>2018-06-10T16:00:01.000Z</td>
      <td>DIY|"do it yourself"|"how to"|"laurDIY"|"laure...</td>
      <td>669641</td>
      <td>52938</td>
      <td>596</td>
      <td>7608</td>
      <td>https://i.ytimg.com/vi/NBupCHcJ914/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>✂ click to join the #prettylittlelaurs fam!! h...</td>
    </tr>
    <tr>
      <th>40352</th>
      <td>Gv1xjf8BUrY</td>
      <td>18.12.06</td>
      <td>CHRIS PRATT is on our Game Show!</td>
      <td>Smosh</td>
      <td>Comedy</td>
      <td>2018-06-11T16:04:02.000Z</td>
      <td>chris pratt|"chris pratt game show"|"chriss pr...</td>
      <td>670902</td>
      <td>52024</td>
      <td>466</td>
      <td>7176</td>
      <td>https://i.ytimg.com/vi/Gv1xjf8BUrY/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Thanks to Jurassic World for sponsoring this e...</td>
    </tr>
    <tr>
      <th>40353</th>
      <td>srkj63VBSHM</td>
      <td>18.12.06</td>
      <td>Marjory Stoneman Douglas High School Students ...</td>
      <td>CBS</td>
      <td>Entertainment</td>
      <td>2018-06-11T06:00:04.000Z</td>
      <td>tony|"awards"|"broadway"|"musical"</td>
      <td>509062</td>
      <td>8667</td>
      <td>1047</td>
      <td>672</td>
      <td>https://i.ytimg.com/vi/srkj63VBSHM/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Students from Marjory Stoneman Douglas High Sc...</td>
    </tr>
    <tr>
      <th>40355</th>
      <td>4V3Y8Sbz9HI</td>
      <td>18.12.06</td>
      <td>TEENS GUESS THAT SONG CHALLENGE #8 (REACT)</td>
      <td>REACT</td>
      <td>Entertainment</td>
      <td>2018-06-11T19:00:00.000Z</td>
      <td>Guess that song|"Usher"|"Halsey"|"TEENS GUESS ...</td>
      <td>380532</td>
      <td>14644</td>
      <td>239</td>
      <td>2200</td>
      <td>https://i.ytimg.com/vi/4V3Y8Sbz9HI/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>SUBSCRIBE THEN HIT THE 🔔! New Videos 12pm PT o...</td>
    </tr>
    <tr>
      <th>40357</th>
      <td>XQSvN2Wd5MQ</td>
      <td>18.12.06</td>
      <td>International Dunkin' Donuts Taste Test</td>
      <td>Good Mythical Morning</td>
      <td>Entertainment</td>
      <td>2018-06-11T10:00:02.000Z</td>
      <td>gmm|"good mythical morning"|"rhettandlink"|"rh...</td>
      <td>716521</td>
      <td>23381</td>
      <td>375</td>
      <td>2871</td>
      <td>https://i.ytimg.com/vi/XQSvN2Wd5MQ/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Can we guess where in the world these Dunkin' ...</td>
    </tr>
    <tr>
      <th>40363</th>
      <td>3tYpfAPVEs0</td>
      <td>18.12.06</td>
      <td>Stephen A. and Max react to LeBron James' brok...</td>
      <td>ESPN</td>
      <td>Sports</td>
      <td>2018-06-11T14:43:53.000Z</td>
      <td>espn|"espn live"|"lebron broken hand"|"lebron ...</td>
      <td>746980</td>
      <td>6918</td>
      <td>950</td>
      <td>4662</td>
      <td>https://i.ytimg.com/vi/3tYpfAPVEs0/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>First Take's Stephen A. Smith and Max Kellerma...</td>
    </tr>
    <tr>
      <th>40364</th>
      <td>hKNRieaN0ZI</td>
      <td>18.12.06</td>
      <td>Shaq's Babysitting Gig Led to His Google Riches</td>
      <td>TheEllenShow</td>
      <td>Entertainment</td>
      <td>2018-06-11T13:00:10.000Z</td>
      <td>ellen|"ellen degeneres"|"the ellen show"|"seas...</td>
      <td>206975</td>
      <td>4233</td>
      <td>59</td>
      <td>293</td>
      <td>https://i.ytimg.com/vi/hKNRieaN0ZI/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Shaquille O'Neal explained to Ellen how a brie...</td>
    </tr>
    <tr>
      <th>40488</th>
      <td>X9fHoWLf1w0</td>
      <td>18.12.06</td>
      <td>The Best BBQ Meat | Great Taste</td>
      <td>All Def Digital</td>
      <td>Entertainment</td>
      <td>2018-05-26T16:00:04.000Z</td>
      <td>The Best BBQ Meat|"barbecue"|"barbeque"|"doboy...</td>
      <td>596062</td>
      <td>21629</td>
      <td>174</td>
      <td>3512</td>
      <td>https://i.ytimg.com/vi/X9fHoWLf1w0/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>The SquADD discusses their all-time favorite B...</td>
    </tr>
    <tr>
      <th>40550</th>
      <td>uHRwMmwbFnA</td>
      <td>18.13.06</td>
      <td>Super Smash Bros. Ultimate - Everyone is Here ...</td>
      <td>GameXplain</td>
      <td>Gaming</td>
      <td>2018-06-12T16:24:41.000Z</td>
      <td>Smash|"Super Smash Bros."|"Smash Bros."|"Super...</td>
      <td>470844</td>
      <td>13922</td>
      <td>402</td>
      <td>4843</td>
      <td>https://i.ytimg.com/vi/uHRwMmwbFnA/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Every fighter in the Super Smash Bros. series'...</td>
    </tr>
    <tr>
      <th>40552</th>
      <td>nBpxVdpMBZk</td>
      <td>18.13.06</td>
      <td>Loaded Baked Potato - You Suck at Cooking (epi...</td>
      <td>You Suck At Cooking</td>
      <td>Howto &amp; Style</td>
      <td>2018-06-12T12:41:58.000Z</td>
      <td>potatoes|"baked potatoes"|"loaded potato"|"loa...</td>
      <td>335348</td>
      <td>22487</td>
      <td>286</td>
      <td>1634</td>
      <td>https://i.ytimg.com/vi/nBpxVdpMBZk/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>For a 30 day trial and free book go to http://...</td>
    </tr>
    <tr>
      <th>40554</th>
      <td>s_SJZSAtLBA</td>
      <td>18.13.06</td>
      <td>Assassin's Creed Odyssey: E3 2018 Official Wor...</td>
      <td>Ubisoft North America</td>
      <td>Gaming</td>
      <td>2018-06-11T21:16:16.000Z</td>
      <td>Assassin's Creed Odyssey|"Assassin's Creed Ody...</td>
      <td>669475</td>
      <td>14059</td>
      <td>1133</td>
      <td>3615</td>
      <td>https://i.ytimg.com/vi/s_SJZSAtLBA/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Watch the world premiere of Assassin’s Creed O...</td>
    </tr>
    <tr>
      <th>40563</th>
      <td>6fi1RZNWCDI</td>
      <td>18.13.06</td>
      <td>It’s Just Water Weight | Kevin Hart: What The ...</td>
      <td>LOL Network</td>
      <td>Comedy</td>
      <td>2018-06-11T17:00:00.000Z</td>
      <td>What the Fit|"Kevin Hart What the Fit"|"Kevin ...</td>
      <td>226849</td>
      <td>5788</td>
      <td>130</td>
      <td>242</td>
      <td>https://i.ytimg.com/vi/6fi1RZNWCDI/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Fight your way to fitness with a firefighter’s...</td>
    </tr>
    <tr>
      <th>40567</th>
      <td>DjLr06pne6Q</td>
      <td>18.13.06</td>
      <td>Jorja Smith Gets Ready for Bed | Beauty Secret...</td>
      <td>Vogue</td>
      <td>Howto &amp; Style</td>
      <td>2018-06-12T13:48:29.000Z</td>
      <td>beauty|"beauty secrets"|"celebrity"|"celebrity...</td>
      <td>279728</td>
      <td>19691</td>
      <td>135</td>
      <td>579</td>
      <td>https://i.ytimg.com/vi/DjLr06pne6Q/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>“It’s not about looking cute,” says the rising...</td>
    </tr>
    <tr>
      <th>40755</th>
      <td>-9rdDeWzvsU</td>
      <td>18.14.06</td>
      <td>Stampede - Alexander Jean Ft. Lindsey Stirling</td>
      <td>Lindsey Stirling</td>
      <td>Music</td>
      <td>2018-06-13T16:00:24.000Z</td>
      <td>lindsey|"lindsay"|"violin"|"dubstep"|"electron...</td>
      <td>296615</td>
      <td>38671</td>
      <td>463</td>
      <td>2348</td>
      <td>https://i.ytimg.com/vi/-9rdDeWzvsU/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Lindsey Stirling &amp; Evanescence Co-Headline Sum...</td>
    </tr>
    <tr>
      <th>40759</th>
      <td>Gi56dSh8Fq8</td>
      <td>18.14.06</td>
      <td>Gourmet Chef Makes A Big Mac Super Fancy</td>
      <td>BuzzFeedVideo</td>
      <td>People &amp; Blogs</td>
      <td>2018-06-13T18:00:32.000Z</td>
      <td>mcdonalds|"big mac"|"fancy"|"fast food"|"jacqu...</td>
      <td>402418</td>
      <td>10070</td>
      <td>3303</td>
      <td>2142</td>
      <td>https://i.ytimg.com/vi/Gi56dSh8Fq8/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>It's your good ol' McDonald's Big Mac, but lik...</td>
    </tr>
    <tr>
      <th>40760</th>
      <td>dS5Thrl-4Kc</td>
      <td>18.14.06</td>
      <td>CRAYOLA MAKEUP | HIT OR MISS?</td>
      <td>Laura Lee</td>
      <td>Howto &amp; Style</td>
      <td>2018-06-12T18:55:26.000Z</td>
      <td>Laura88Lee|"crayola"|"crayon makeup"|"crayola ...</td>
      <td>607422</td>
      <td>26166</td>
      <td>895</td>
      <td>3517</td>
      <td>https://i.ytimg.com/vi/dS5Thrl-4Kc/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Hey Larlees, todays video is me testing Crayol...</td>
    </tr>
    <tr>
      <th>40764</th>
      <td>mpnshdmtE2Y</td>
      <td>18.14.06</td>
      <td>Carla Makes BA Smashburgers | From the Test Ki...</td>
      <td>Bon Appétit</td>
      <td>Howto &amp; Style</td>
      <td>2018-06-12T16:03:58.000Z</td>
      <td>bon appetit|"burgers"|"cheeseburgers"|"how to ...</td>
      <td>540149</td>
      <td>14206</td>
      <td>693</td>
      <td>1211</td>
      <td>https://i.ytimg.com/vi/mpnshdmtE2Y/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Ground chuck is a great all-purpose, buy-it-an...</td>
    </tr>
    <tr>
      <th>40766</th>
      <td>yz7Xq3T0YPs</td>
      <td>18.14.06</td>
      <td>Katherine Langford on 13 Reasons Why, Australi...</td>
      <td>Jimmy Kimmel Live</td>
      <td>Entertainment</td>
      <td>2018-06-13T09:00:06.000Z</td>
      <td>jimmy|"kimmel"|"live"|"late"|"night"|"talk"|"s...</td>
      <td>296295</td>
      <td>8157</td>
      <td>294</td>
      <td>764</td>
      <td>https://i.ytimg.com/vi/yz7Xq3T0YPs/default.jpg</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Katherine talks about learning accents, growin...</td>
    </tr>
  </tbody>
</table>
<p>4862 rows × 16 columns</p>
</div>




```python
lower_vals.shape
```




    (4862, 16)




```python
plt.scatter(lower_vals[['views']], lower_vals[['likes']])
plt.xlabel("views")
plt.ylabel("likes")
plt.show()
```


![png](output_168_0.png)



```python
lm_lower = linear_model.LinearRegression()
model_lower = lm_lower.fit(lower_vals[['views']], lower_vals[['likes']])
lm_lower.score(lower_vals[['views']], lower_vals[['likes']]) # współczynnik determinacji
```




    0.3925046974725276




```python
likes_predict_lower = model_lower.predict # linear regression
likes_predict_lower(lower_vals[['views']])
```




    array([[31441.6682837 ],
           [14314.22108906],
           [ 4846.58575732],
           ...,
           [25483.8395818 ],
           [22640.31121568],
           [12332.96999121]])




```python
plt.scatter(lower_vals[['views']], lower_vals[['likes']], color = "red")
plt.plot(lower_vals[['views']], likes_predict_lower(lower_vals[['views']]))
plt.xlabel("views")
plt.ylabel("likes") 
plt.title('likes[views] for lower vals')
```




    Text(0.5, 1.0, 'likes[views] for lower vals')




![png](output_171_1.png)


# Analiza i  Regresja tylko dla odtworzeń >= średnia


```python
higher_vals = cleared[cleared.views >= cleared["views"].mean()]
```


```python
higher_vals.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1.593000e+03</td>
      <td>1.593000e+03</td>
      <td>1593.000000</td>
      <td>1593.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>2.435749e+06</td>
      <td>1.108989e+05</td>
      <td>4715.157564</td>
      <td>14552.417451</td>
    </tr>
    <tr>
      <th>std</th>
      <td>3.451154e+06</td>
      <td>2.142439e+05</td>
      <td>23734.286679</td>
      <td>41099.567108</td>
    </tr>
    <tr>
      <th>min</th>
      <td>7.733340e+05</td>
      <td>0.000000e+00</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1.001371e+06</td>
      <td>2.462500e+04</td>
      <td>730.000000</td>
      <td>2520.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1.469703e+06</td>
      <td>5.203200e+04</td>
      <td>1416.000000</td>
      <td>5746.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.457390e+06</td>
      <td>1.146610e+05</td>
      <td>3064.000000</td>
      <td>12368.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>4.843165e+07</td>
      <td>3.880071e+06</td>
      <td>629120.000000</td>
      <td>733373.000000</td>
    </tr>
  </tbody>
</table>
</div>



Z kolei dla filmików bardziej popularnych niż średnia, średnia ilość odtworzeń jest minimalnie mniejsz niż kwantyl górny, podobnie jak i średnia ilość lajków.
Średnia ilość dislajków i komentarzy jest większa niż ich kwantyl górny


```python
higher_vals.boxplot(column="views")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc8bb4780>




![png](output_176_1.png)



```python
higher_vals.boxplot(column="likes")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc86ee320>




![png](output_177_1.png)



```python
higher_vals.boxplot(column="dislikes")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc8a537b8>




![png](output_178_1.png)



```python
higher_vals.boxplot(column="comment_count")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f4fc89dc4a8>




![png](output_179_1.png)



```python
higher_vals.shape
```




    (1593, 16)




```python
fig, ax = plt.subplots()
_ = sns.distplot(higher_vals["views"], ax = ax, kde = False, color = "red")
```


![png](output_181_0.png)



```python
plt.scatter(higher_vals[['views']], higher_vals[['likes']])
plt.xlabel("views")
plt.ylabel("likes")
plt.show()
```


![png](output_182_0.png)



```python
lm_higher = linear_model.LinearRegression()
model_higher = lm_higher.fit(higher_vals[['views']], higher_vals[['likes']])
lm_higher.score(higher_vals[['views']], higher_vals[['likes']]) # współczynnik determinacji
```




    0.4925430250666701




```python
lm_higher.get_params()
```




    {'copy_X': True, 'fit_intercept': True, 'n_jobs': None, 'normalize': False}




```python
likes_predict_higher = model_higher.predict # linear regression
likes_predict_higher(higher_vals[['views']])
```




    array([[110159.74157257],
           [143822.49122476],
           [ 96085.05765882],
           ...,
           [ 98139.71787876],
           [ 40191.8952012 ],
           [ 42429.40972443]])




```python
plt.scatter(higher_vals[['views']], higher_vals[['likes']], color = "red")
plt.plot(higher_vals[['views']], likes_predict_higher(higher_vals[['views']]))
plt.xlabel("views")
plt.ylabel("likes") 
plt.title('likes[views] for higher vals')
```




    Text(0.5, 1.0, 'likes[views] for higher vals')




![png](output_186_1.png)


# Probabilities and chi analyzis

Funkcja zliczająca średnią ilość kliknięć Like dla wybranego DataFame


```python
def get_like_propability(dataframe):
    views = dataframe['views']
    likes = dataframe['likes']

    zipped = zip(views,likes)

    like_propabilities = []
    for i in zipped:
        like_propabilities.append(i[1] / i[0] )

    average_like_propability = sum(like_propabilities) / len(like_propabilities)

    return average_like_propability
```

Funkcja zliczająca średnią ilość kliknięć Dislike dla wybranego DataFame


```python
def get_dislike_propability(dataframe):
    views = dataframe['views']
    dislikes = dataframe['dislikes']

    zipped = zip(views, dislikes)

    dislike_propabilities = []
    for i in zipped:
        dislike_propabilities.append(i[1] / i[0] )


    average_dislike_propability = sum(dislike_propabilities) / len(dislike_propabilities)
    return average_dislike_propability

```

Funkcja zliczająca średnią ilość komentarzy dla wybranego DataFame


```python
def get_comment_propability(dataframe):
    views = dataframe['views']
    comments = dataframe['comment_count']

    zipped = zip(views, comments)

    comment_propabilities = []
    for i in zipped:
        comment_propabilities.append(i[1] / i[0] )

    average_comment_propability = sum(comment_propabilities) / len(comment_propabilities)
    return average_comment_propability
```

Funkcja wypisująca wszystkie możliwe prawodpodobieństwa dla danej kategorii


```python
def print_all_propabilities(dataframe, category):
    if (type(category) != str ):
        raise(Exception("Given category name is not string"))
    print(category + ":")
    print("Like probability:", end = "\t")
    print(get_like_propability(dataframe))
    print("Dislike probability:", end = "\t")
    print(get_dislike_propability(dataframe))
    print("Comment probability:", end = "\t")
    print(get_comment_propability(dataframe))
    print("\n")
```

Wypisanie wszystkich prawdopodobieństw dla wszystkich filmików


```python
print_all_propabilities(cleared, "All")

grouped_by_category = cleared.groupby('category_id')
```

    All:
    Like probability:	0.04008573498768022
    Dislike probability:	0.0017176409051281802
    Comment probability:	0.005610980414914208
    
    


Wypisanie wszystkich prawdopodobieństw dla każdej kategorii osobno


```python
like_propabilities = []
dislike_propabilities = []
comment_propabilities = []
```


```python

def print_all_propabilities_all_categories(dataframe):
    global like_propabilities
    global dislike_propabilities
    global comment_propabilities

    grouped_by_category = dataframe.groupby('category_id')

    keys = []
    for key, _ in grouped_by_category:
        keys.append(key)

    like_propabilities = []
    dislike_propabilities = []
    comment_propabilities = []


    for key in keys:
        bool_category_table = dataframe['category_id'] == key
        category_dataframe = dataframe[bool_category_table]

        like_propabilities.append(get_like_propability(category_dataframe))
        dislike_propabilities.append(get_dislike_propability(category_dataframe))
        comment_propabilities.append(get_comment_propability(category_dataframe))

        print_all_propabilities(category_dataframe, key)
```

Wypisanie wszystkich prawdopodobieństw dla każdej kategorii


```python
print_all_propabilities_all_categories(cleared)
```

    Autos & Vehicles:
    Like probability:	0.017855856411111804
    Dislike probability:	0.0009764972165759346
    Comment probability:	0.003564466976982662
    
    
    Comedy:
    Like probability:	0.04965295503376354
    Dislike probability:	0.0014130706545664353
    Comment probability:	0.005765394608319075
    
    
    Education:
    Like probability:	0.04162769677809599
    Dislike probability:	0.0012995913366435634
    Comment probability:	0.0053735472774983275
    
    
    Entertainment:
    Like probability:	0.03198048080664675
    Dislike probability:	0.0016619458344495667
    Comment probability:	0.004698136999201604
    
    
    Film & Animation:
    Like probability:	0.0339219768795319
    Dislike probability:	0.0015084511724707582
    Comment probability:	0.004805151346573241
    
    
    Gaming:
    Like probability:	0.043621415957708604
    Dislike probability:	0.0019291661049829281
    Comment probability:	0.009005803040502447
    
    
    Howto & Style:
    Like probability:	0.05131032897859301
    Dislike probability:	0.0013101399776570453
    Comment probability:	0.006968298621107184
    
    
    Music:
    Like probability:	0.07284680903217203
    Dislike probability:	0.0016230929178255912
    Comment probability:	0.0065932912738347
    
    
    News & Politics:
    Like probability:	0.014472774216015248
    Dislike probability:	0.004003808042894789
    Comment probability:	0.007829460928763993
    
    
    Nonprofits & Activism:
    Like probability:	0.03519231610614695
    Dislike probability:	0.0029461822271774993
    Comment probability:	0.007222233693876197
    
    
    People & Blogs:
    Like probability:	0.04699793849488535
    Dislike probability:	0.0020979223630114116
    Comment probability:	0.005932149594877898
    
    
    Pets & Animals:
    Like probability:	0.044170532200649336
    Dislike probability:	0.0007736312299223069
    Comment probability:	0.005387188726440099
    
    
    Science & Technology:
    Like probability:	0.03570733281376095
    Dislike probability:	0.0013441441904512923
    Comment probability:	0.0051534962431342346
    
    
    Shows:
    Like probability:	0.02885583923085668
    Dislike probability:	0.0011278603206828922
    Comment probability:	0.005905794589244518
    
    
    Sports:
    Like probability:	0.016162238159680927
    Dislike probability:	0.0010903222795354667
    Comment probability:	0.0031695770484283185
    
    
    Travel & Events:
    Like probability:	0.024977422872039096
    Dislike probability:	0.001363518402189086
    Comment probability:	0.0041990587313784685
    
    



```python

```

Analiza Chi-square


```python
print("Chi analysis:")

all_videos_like_propability = get_like_propability(cleared)
all_videos_dislike_propability = get_dislike_propability(cleared)
all_videos_comment_propability = get_comment_propability(cleared)

all_videos_like_propability = [all_videos_like_propability] * len(like_propabilities)
all_videos_dislike_propability = [all_videos_dislike_propability] * len(dislike_propabilities)
all_videos_comment_propability = [all_videos_comment_propability] * len(comment_propabilities)

phi_likes = scp.chisquare(like_propabilities,f_exp = all_videos_like_propability)
phi_dislikes = scp.chisquare(dislike_propabilities,f_exp = all_videos_dislike_propability)
phi_comments = scp.chisquare(comment_propabilities,f_exp = all_videos_comment_propability)

print(phi_likes)
print(phi_dislikes)
print(phi_comments)
```

    Chi analysis:
    Power_divergenceResult(statistic=0.0896541780635618, pvalue=0.9999999999999947)
    Power_divergenceResult(statistic=0.005741210805259146, pvalue=1.0)
    Power_divergenceResult(statistic=0.006416766785149404, pvalue=1.0)


Analiza chi wykazuje brak zależności prawdopodobieństwa kliknięcia lajków, dislajków i dodawania komentarzy od kategorii

Wypisanie wszystkich prawdopodobieństw dla wyłączonych komentarzach i porównanie z przypadkiem ogólnym( wszystkie filmiki )


```python
print_all_propabilities(cleared,'All')
bool_comments_disabled_table = cleared['comments_disabled']
comments_disabled = cleared[bool_comments_disabled_table]
print_all_propabilities(comments_disabled,'comments_disabled')
```

    All:
    Like probability:	0.04008573498768022
    Dislike probability:	0.0017176409051281802
    Comment probability:	0.005610980414914208
    
    
    comments_disabled:
    Like probability:	0.012805255167537099
    Dislike probability:	0.003113634317776765
    Comment probability:	0.0
    
    


Filmiki z wyłączonymi komentarzami cechują się o wiele większym prawdopodobieństwem tego że odwiedzająca osoba kliknie dislajk i dużo mniejszym że kliknie lajk

Wypisanie wszystkich prawdopodobieństw dla wszystkich kategorii przy wyłączonych komentarzach


```python
print_all_propabilities(comments_disabled, 'All')
```

    All:
    Like probability:	0.012805255167537099
    Dislike probability:	0.003113634317776765
    Comment probability:	0.0
    
    


ogólne prawdopodobieństwo dislika rośnie przy wyłączonych komentarzach z ok 0.0017 do 0.0031
prawdopodobieństwo lajka maleje z 0.040 do 0.018


```python
print_all_propabilities_all_categories(comments_disabled)
```

    Autos & Vehicles:
    Like probability:	0.0033406509496993414
    Dislike probability:	0.0013362603798797365
    Comment probability:	0.0
    
    
    Comedy:
    Like probability:	0.0020168067226890756
    Dislike probability:	0.01310924369747899
    Comment probability:	0.0
    
    
    Education:
    Like probability:	0.0004344048653344917
    Dislike probability:	0.0
    Comment probability:	0.0
    
    
    Entertainment:
    Like probability:	0.020365586366536514
    Dislike probability:	0.001088578430700642
    Comment probability:	0.0
    
    
    Film & Animation:
    Like probability:	0.0028140732881021236
    Dislike probability:	0.0015994634727253885
    Comment probability:	0.0
    
    
    Gaming:
    Like probability:	0.037175265196353224
    Dislike probability:	0.0055422504282380885
    Comment probability:	0.0
    
    
    Howto & Style:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.0
    
    
    Music:
    Like probability:	0.007057737921692212
    Dislike probability:	0.0004628758938679144
    Comment probability:	0.0
    
    
    News & Politics:
    Like probability:	0.008849676627749497
    Dislike probability:	0.004029912217189677
    Comment probability:	0.0
    
    
    Nonprofits & Activism:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.0
    
    
    People & Blogs:
    Like probability:	0.00861471987818528
    Dislike probability:	0.012492171306026012
    Comment probability:	0.0
    
    
    Pets & Animals:
    Like probability:	0.010730507980164188
    Dislike probability:	0.0008021836062841189
    Comment probability:	0.0
    
    
    Science & Technology:
    Like probability:	0.01537746919102425
    Dislike probability:	0.0018630396723694168
    Comment probability:	0.0
    
    
    Sports:
    Like probability:	0.009168066827220574
    Dislike probability:	0.0005554417021284034
    Comment probability:	0.0
    
    


Najczęściej lubiana kategoria: Music
Najmniej lubiana: News and Politics
Najbardziej nielubiana: News and Politics
Najmniej nielubiana: Pets & Animals

Wypisanie wszystkich prawdopodobieństw dla wyłączonym ocenianiu i porównanie z przypadkiem ogólnym( wszystkie filmiki )


```python
print_all_propabilities(cleared, 'All')
bool_rating_disabled_table = cleared['ratings_disabled']
rating_disabled = cleared[bool_rating_disabled_table]
print_all_propabilities(rating_disabled, 'rating_disabled')

```

    All:
    Like propability:	0.04008573498768022
    Dislike propability:	0.0017176409051281802
    Comment propability:	0.005610980414914208
    
    
    rating_disabled:
    Like propability:	0.0
    Dislike propability:	0.0
    Comment propability:	0.002004482437898499
    
    


Wypisanie wszystkich prawdopodobieństw dla każdej kategorii przy wyłączonym ocenianiu


```python
print_all_propabilities_all_categories(rating_disabled)
```

    Education:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.000415401929232818
    
    
    Entertainment:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.0002555652913761794
    
    
    Film & Animation:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.0004195755311060733
    
    
    Howto & Style:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.0
    
    
    Music:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.001230938672761469
    
    
    News & Politics:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.0
    
    
    Nonprofits & Activism:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.0
    
    
    People & Blogs:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.011757454107100825
    
    
    Science & Technology:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.0010286198342106855
    
    
    Sports:
    Like probability:	0.0
    Dislike probability:	0.0
    Comment probability:	0.00022965468744925383
    
    


# Najpopularniejsze kanały





najpopularniejsze pod względem wyświetleń


```python
channels = cleared.groupby("channel_title").sum()
```


```python
channels.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
    </tr>
    <tr>
      <th>channel_title</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12 News</th>
      <td>85643</td>
      <td>170</td>
      <td>45</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1MILLION Dance Studio</th>
      <td>1733477</td>
      <td>122066</td>
      <td>1276</td>
      <td>8527</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1theK (원더케이)</th>
      <td>7035666</td>
      <td>702920</td>
      <td>8435</td>
      <td>49777</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>20th Century Fox</th>
      <td>48572239</td>
      <td>1204019</td>
      <td>21395</td>
      <td>78337</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2CELLOS</th>
      <td>205869</td>
      <td>11198</td>
      <td>120</td>
      <td>446</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
channels_sorted = channels.sort_values("views", ascending = False)
```


```python

```


```python
channels_sorted.head(50)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
    </tr>
    <tr>
      <th>channel_title</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Dude Perfect</th>
      <td>108024253</td>
      <td>4447335</td>
      <td>86018</td>
      <td>319353</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ibighit</th>
      <td>103235984</td>
      <td>16434967</td>
      <td>153241</td>
      <td>2420258</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>jypentertainment</th>
      <td>87356902</td>
      <td>3657159</td>
      <td>195893</td>
      <td>587408</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Marvel Entertainment</th>
      <td>80171420</td>
      <td>3731846</td>
      <td>41194</td>
      <td>455700</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>TheEllenShow</th>
      <td>71542914</td>
      <td>1821187</td>
      <td>41753</td>
      <td>106142</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Jimmy Kimmel Live</th>
      <td>63270493</td>
      <td>1215868</td>
      <td>68477</td>
      <td>118705</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>WWE</th>
      <td>60802364</td>
      <td>1103822</td>
      <td>60036</td>
      <td>143783</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>MalumaVEVO</th>
      <td>60445923</td>
      <td>1106425</td>
      <td>85784</td>
      <td>59955</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Ed Sheeran</th>
      <td>54453856</td>
      <td>3468220</td>
      <td>46087</td>
      <td>183023</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>The Tonight Show Starring Jimmy Fallon</th>
      <td>53807761</td>
      <td>1527726</td>
      <td>33199</td>
      <td>93361</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Sony Pictures Entertainment</th>
      <td>52862493</td>
      <td>1399335</td>
      <td>68433</td>
      <td>185928</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>The Late Late Show with James Corden</th>
      <td>50906286</td>
      <td>2067552</td>
      <td>48389</td>
      <td>127793</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Safiya Nygaard</th>
      <td>49031717</td>
      <td>2644275</td>
      <td>30820</td>
      <td>508544</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>BuzzFeedVideo</th>
      <td>48751283</td>
      <td>1284069</td>
      <td>64256</td>
      <td>128539</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>20th Century Fox</th>
      <td>48572239</td>
      <td>1204019</td>
      <td>21395</td>
      <td>78337</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Saturday Night Live</th>
      <td>45433555</td>
      <td>642215</td>
      <td>64272</td>
      <td>76789</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Warner Bros. Pictures</th>
      <td>43601508</td>
      <td>681135</td>
      <td>60824</td>
      <td>107384</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Logan Paul Vlogs</th>
      <td>42794469</td>
      <td>3972175</td>
      <td>1143913</td>
      <td>1664199</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>FoxStarHindi</th>
      <td>42397445</td>
      <td>1099079</td>
      <td>34405</td>
      <td>76163</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Universal Pictures</th>
      <td>42061134</td>
      <td>881298</td>
      <td>37799</td>
      <td>117668</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ESPN</th>
      <td>41744126</td>
      <td>372908</td>
      <td>62162</td>
      <td>159221</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Lele Pons</th>
      <td>39703115</td>
      <td>1942757</td>
      <td>98718</td>
      <td>117102</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>The Late Show with Stephen Colbert</th>
      <td>34705708</td>
      <td>487566</td>
      <td>55890</td>
      <td>82314</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>FBE</th>
      <td>33025152</td>
      <td>1047726</td>
      <td>59519</td>
      <td>219077</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>SMTOWN</th>
      <td>32764003</td>
      <td>4303591</td>
      <td>53763</td>
      <td>482029</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>TaylorSwiftVEVO</th>
      <td>32408000</td>
      <td>2392627</td>
      <td>101030</td>
      <td>215413</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ChildishGambinoVEVO</th>
      <td>31648454</td>
      <td>1405355</td>
      <td>51547</td>
      <td>149473</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>EminemVEVO</th>
      <td>31226491</td>
      <td>2051215</td>
      <td>87211</td>
      <td>258229</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Screen Junkies</th>
      <td>27784564</td>
      <td>908575</td>
      <td>29649</td>
      <td>122830</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>NFL</th>
      <td>27563523</td>
      <td>304395</td>
      <td>30664</td>
      <td>116242</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>YouTube Spotlight</th>
      <td>26192343</td>
      <td>1205883</td>
      <td>557520</td>
      <td>522211</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Marques Brownlee</th>
      <td>26141827</td>
      <td>1437578</td>
      <td>29492</td>
      <td>179795</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>MigosVEVO</th>
      <td>25138267</td>
      <td>782197</td>
      <td>37398</td>
      <td>95732</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>MLG Highlights</th>
      <td>25005083</td>
      <td>84409</td>
      <td>120580</td>
      <td>57173</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Netflix</th>
      <td>24458611</td>
      <td>685639</td>
      <td>28778</td>
      <td>66783</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>DrakeVEVO</th>
      <td>24421448</td>
      <td>641546</td>
      <td>16517</td>
      <td>42949</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>The Slow Mo Guys</th>
      <td>24142511</td>
      <td>1077530</td>
      <td>18887</td>
      <td>67033</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>JenniferLopezVEVO</th>
      <td>24039551</td>
      <td>646748</td>
      <td>67239</td>
      <td>48774</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>The Film Theorists</th>
      <td>23931467</td>
      <td>991965</td>
      <td>44129</td>
      <td>176915</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>jacksfilms</th>
      <td>23547923</td>
      <td>1757253</td>
      <td>34412</td>
      <td>552916</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Simply Nailogical</th>
      <td>23543404</td>
      <td>1325960</td>
      <td>11638</td>
      <td>166770</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Vox</th>
      <td>23483348</td>
      <td>804882</td>
      <td>101831</td>
      <td>135483</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>CamilaCabelloVEVO</th>
      <td>23033935</td>
      <td>1779420</td>
      <td>26387</td>
      <td>124974</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>杰威爾音樂 JVR Music</th>
      <td>22873768</td>
      <td>180699</td>
      <td>11758</td>
      <td>20164</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Tasty</th>
      <td>22747749</td>
      <td>790262</td>
      <td>14954</td>
      <td>44839</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>CNN</th>
      <td>22366886</td>
      <td>252184</td>
      <td>93013</td>
      <td>274638</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>James Charles</th>
      <td>21742072</td>
      <td>1611807</td>
      <td>31952</td>
      <td>252489</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>IISuperwomanII</th>
      <td>21705840</td>
      <td>1886396</td>
      <td>20655</td>
      <td>158459</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Star Wars</th>
      <td>21440411</td>
      <td>503332</td>
      <td>46321</td>
      <td>94174</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Global Road Entertainment</th>
      <td>21197227</td>
      <td>7846</td>
      <td>700</td>
      <td>838</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



najpopularniejsze pod względem ilości filmików w "trending"


```python
channels_videos_amount = cleared.groupby("channel_title").size()
```


```python
channels_videos_amount.head()
```




    channel_title
    12 News                   1
    1MILLION Dance Studio     4
    1theK (원더케이)              5
    20th Century Fox         17
    2CELLOS                   1
    dtype: int64




```python
channels_videos_amount.sort_values(ascending = False).head(50)
```




    channel_title
    ESPN                                       84
    TheEllenShow                               74
    The Tonight Show Starring Jimmy Fallon     72
    Jimmy Kimmel Live                          70
    The Late Show with Stephen Colbert         58
    Netflix                                    58
    NBA                                        54
    CNN                                        52
    Vox                                        48
    The Late Late Show with James Corden       46
    Refinery29                                 41
    BuzzFeedVideo                              40
    INSIDER                                    39
    Late Night with Seth Meyers                37
    NFL                                        36
    Saturday Night Live                        35
    WWE                                        34
    First We Feast                             31
    WIRED                                      29
    Tasty                                      28
    Washington Post                            27
    Great Big Story                            26
    SciShow                                    26
    CollegeHumor                               26
    Screen Junkies                             25
    Vogue                                      25
    FBE                                        25
    The King of Random                         25
    Life Noggin                                25
    Good Mythical Morning                      24
    Warner Bros. Pictures                      24
    Tom Scott                                  24
    jacksfilms                                 23
    Bon Appétit                                23
    HellthyJunkFood                            23
    NBC News                                   23
    TED-Ed                                     23
    ABC News                                   22
    Marques Brownlee                           22
    Vanity Fair                                22
    Watch What Happens Live with Andy Cohen    21
    BBC Radio 1                                20
    TODAY                                      20
    NBC Sports                                 19
    E! Entertainment                           19
    BBC News                                   19
    The Voice                                  19
    The Slow Mo Guys                           19
    IISuperwomanII                             18
    The Dodo                                   18
    dtype: int64



Który kanał ma najwięcej lajków?


```python
channels_sorted_likes = channels.sort_values("likes", ascending = False)
channels_sorted_likes.head(50)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>views</th>
      <th>likes</th>
      <th>dislikes</th>
      <th>comment_count</th>
      <th>comments_disabled</th>
      <th>ratings_disabled</th>
      <th>video_error_or_removed</th>
    </tr>
    <tr>
      <th>channel_title</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>ibighit</th>
      <td>103235984</td>
      <td>16434967</td>
      <td>153241</td>
      <td>2420258</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Dude Perfect</th>
      <td>108024253</td>
      <td>4447335</td>
      <td>86018</td>
      <td>319353</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>SMTOWN</th>
      <td>32764003</td>
      <td>4303591</td>
      <td>53763</td>
      <td>482029</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Logan Paul Vlogs</th>
      <td>42794469</td>
      <td>3972175</td>
      <td>1143913</td>
      <td>1664199</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Marvel Entertainment</th>
      <td>80171420</td>
      <td>3731846</td>
      <td>41194</td>
      <td>455700</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>jypentertainment</th>
      <td>87356902</td>
      <td>3657159</td>
      <td>195893</td>
      <td>587408</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Ed Sheeran</th>
      <td>54453856</td>
      <td>3468220</td>
      <td>46087</td>
      <td>183023</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Safiya Nygaard</th>
      <td>49031717</td>
      <td>2644275</td>
      <td>30820</td>
      <td>508544</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>TaylorSwiftVEVO</th>
      <td>32408000</td>
      <td>2392627</td>
      <td>101030</td>
      <td>215413</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>The Late Late Show with James Corden</th>
      <td>50906286</td>
      <td>2067552</td>
      <td>48389</td>
      <td>127793</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>EminemVEVO</th>
      <td>31226491</td>
      <td>2051215</td>
      <td>87211</td>
      <td>258229</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>BANGTANTV</th>
      <td>10981855</td>
      <td>2017520</td>
      <td>5096</td>
      <td>137562</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Lele Pons</th>
      <td>39703115</td>
      <td>1942757</td>
      <td>98718</td>
      <td>117102</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>IISuperwomanII</th>
      <td>21705840</td>
      <td>1886396</td>
      <td>20655</td>
      <td>158459</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>TheEllenShow</th>
      <td>71542914</td>
      <td>1821187</td>
      <td>41753</td>
      <td>106142</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>CamilaCabelloVEVO</th>
      <td>23033935</td>
      <td>1779420</td>
      <td>26387</td>
      <td>124974</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>jacksfilms</th>
      <td>23547923</td>
      <td>1757253</td>
      <td>34412</td>
      <td>552916</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Liza Koshy</th>
      <td>16410327</td>
      <td>1632413</td>
      <td>13899</td>
      <td>81214</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>James Charles</th>
      <td>21742072</td>
      <td>1611807</td>
      <td>31952</td>
      <td>252489</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>NikkieTutorials</th>
      <td>19752858</td>
      <td>1552782</td>
      <td>17940</td>
      <td>407965</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ArianaGrandeVevo</th>
      <td>17600956</td>
      <td>1530896</td>
      <td>46003</td>
      <td>150996</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>The Tonight Show Starring Jimmy Fallon</th>
      <td>53807761</td>
      <td>1527726</td>
      <td>33199</td>
      <td>93361</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ShawnMendesVEVO</th>
      <td>7997332</td>
      <td>1522953</td>
      <td>8078</td>
      <td>118426</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Marques Brownlee</th>
      <td>26141827</td>
      <td>1437578</td>
      <td>29492</td>
      <td>179795</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ChildishGambinoVEVO</th>
      <td>31648454</td>
      <td>1405355</td>
      <td>51547</td>
      <td>149473</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Sony Pictures Entertainment</th>
      <td>52862493</td>
      <td>1399335</td>
      <td>68433</td>
      <td>185928</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>nigahiga</th>
      <td>19641995</td>
      <td>1374131</td>
      <td>22046</td>
      <td>137070</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>David Dobrik</th>
      <td>16884972</td>
      <td>1366736</td>
      <td>59930</td>
      <td>237907</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Simply Nailogical</th>
      <td>23543404</td>
      <td>1325960</td>
      <td>11638</td>
      <td>166770</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>BuzzFeedVideo</th>
      <td>48751283</td>
      <td>1284069</td>
      <td>64256</td>
      <td>128539</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Jimmy Kimmel Live</th>
      <td>63270493</td>
      <td>1215868</td>
      <td>68477</td>
      <td>118705</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>YouTube Spotlight</th>
      <td>26192343</td>
      <td>1205883</td>
      <td>557520</td>
      <td>522211</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>20th Century Fox</th>
      <td>48572239</td>
      <td>1204019</td>
      <td>21395</td>
      <td>78337</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Vogue</th>
      <td>17429848</td>
      <td>1195882</td>
      <td>15326</td>
      <td>64425</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>CaseyNeistat</th>
      <td>19231304</td>
      <td>1138499</td>
      <td>44483</td>
      <td>115861</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Kurzgesagt – In a Nutshell</th>
      <td>16158632</td>
      <td>1111936</td>
      <td>22676</td>
      <td>109469</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Shawn Mendes</th>
      <td>8217435</td>
      <td>1106630</td>
      <td>6113</td>
      <td>84032</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>MalumaVEVO</th>
      <td>60445923</td>
      <td>1106425</td>
      <td>85784</td>
      <td>59955</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>WWE</th>
      <td>60802364</td>
      <td>1103822</td>
      <td>60036</td>
      <td>143783</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>FoxStarHindi</th>
      <td>42397445</td>
      <td>1099079</td>
      <td>34405</td>
      <td>76163</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>The Slow Mo Guys</th>
      <td>24142511</td>
      <td>1077530</td>
      <td>18887</td>
      <td>67033</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>ZaynVEVO</th>
      <td>13520090</td>
      <td>1070589</td>
      <td>20855</td>
      <td>88444</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>SelenaGomezVEVO</th>
      <td>9969546</td>
      <td>1064539</td>
      <td>23464</td>
      <td>74141</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>FBE</th>
      <td>33025152</td>
      <td>1047726</td>
      <td>59519</td>
      <td>219077</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Jaiden Animations</th>
      <td>11752672</td>
      <td>1019467</td>
      <td>12679</td>
      <td>257516</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>The Film Theorists</th>
      <td>23931467</td>
      <td>991965</td>
      <td>44129</td>
      <td>176915</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>KendrickLamarVEVO</th>
      <td>12960419</td>
      <td>955675</td>
      <td>12499</td>
      <td>70633</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>HarryStylesVEVO</th>
      <td>10228276</td>
      <td>946967</td>
      <td>16472</td>
      <td>65765</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Charlie Puth</th>
      <td>10762133</td>
      <td>932041</td>
      <td>9562</td>
      <td>61139</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Unbox Therapy</th>
      <td>19947708</td>
      <td>927262</td>
      <td>40927</td>
      <td>145728</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python

```

# Najczęściej używane słowa w kategoriach

Inicjalizacja słownika przechowującego wystąpienia słów


```python
word_occurances = {}
```

Funkcja wstawiająca słowo do słownika, jeśli dane słowo występuje w słowniku, to inkrementuje licznik wystąpień o 1. W przeciwnym przypadku wstawia do słownika, za bazową wartością licznika 1. 


```python
def insert_to_word_occurances(word):
    global word_occurances
    if ( type(word) != str):
        return
    if ( word not in word_occurances.keys() ):
        word_occurances.update({word : 1})
    else:
        word_occurances[word] += 1
```

Funkcja wyliczająca wystąpienia słów w kolumnie description w podanym dataset


```python
def print_word_occurances_in_dataset(dataset):
    global word_occurances
    word_occurances = {}

    for not_splitted_yet in dataset['description']:

        try:
            words = not_splitted_yet.split()
        except(AttributeError):
            words = []
        for word in words:
            insert_to_word_occurances(word)

    word_occurances = sorted(word_occurances.items(), key = lambda kv : kv[1] )

    print("\n50 least commonly used words")
    for i in range(0,50):
        print(word_occurances[i])
    word_occurances = word_occurances[-1::-1]
    print("\n50 most commonly used words")
    for i in range(0,50):
        print(word_occurances[i])
    print()
```

Funkcja wypisująca wszystkie wystąpienia słów w podanym datasecie , grupując po kluczach kolumny description


```python
def print_word_occurances_by_collumn_in_dataset(dataframe):
    grouped_by_category = dataframe.groupby('category_id')

    keys = []
    for key, _ in grouped_by_category:
        keys.append(key)


    for key in keys:
        bool_category_table = dataframe['category_id'] == key
        category_dataframe = dataframe[bool_category_table]
        print()
        print(key)
        print_word_occurances_in_dataset(category_dataframe)
        print()
```

Wypisanie wszystkich wystąpień słów we wszystkich opisach


```python
print_word_occurances_in_dataset(cleared)
```

    
    50 least commonly used words
    ("SHANTELL'S", 1)
    ('https://www.youtube.com/shantellmartin\\nCANDICE', 1)
    ('https://www.lovebilly.com\\n\\nfilmed', 1)
    ('https://twitter.com/CaseyNeistat\\n\\namazing', 1)
    ('https://soundcloud.com/discoteeth\\n\\nad', 1)
    ('disclosure.', 1)
    ('AD.', 1)
    ("'GALAXY", 1)
    ("PROJECT'", 1)
    ('clear.', 1)
    ('specifics.', 1)
    ('election,', 1)
    ('catheter', 1)
    ("hasn't.\\n\\nConnect", 1)
    ('http://youtube.com/c/rudymancuso\\nLele', 1)
    ('http://youtube.com/c/lelepons\\nKing', 1)
    ('https://youtube.com/user/BachelorsPadTv\\n\\nVideo', 1)
    ('Effects:', 1)
    ('\\nCaleb', 1)
    ('Natale', 1)
    ('https://instagram.com/calebnatale\\n\\nPA:\\nPaulina', 1)
    ('Gregory\\n\\n\\nShots', 1)
    ('http://youtube.com/c/shots\\n\\n#Rudy\\n#RudyMancuso', 1)
    ('devotee.', 1)
    ("#1218\\nDon't", 1)
    ('https://goo.gl/xeZNQt\\nWatch', 1)
    ('https://youtu.be/MhCdiiB8CQg', 1)
    ('https://youtu.be/7qiOrNao9fg\\nWatch', 1)
    ('http://bit.ly/GMM1218\\n\\nPick', 1)
    ('yet!\\nLeave', 1)
    ('\\n\\nOrder', 1)
    ('thoughts!\\nAll', 1)
    ('https://www.youtube.com/watch?v=vqztGUwhRlQ&list=PLoYRB6C09WUDbCndtEDELX-Fpk_pgATvF\\n►', 1)
    ('http://www.youtube.com/subscription_center?add_user=ijustine\\n►', 1)
    ('http://ijustinebook.com\\n►', 1)
    ('STICKERS!', 1)
    ('http://ijustinestickers.com\\n\\n▼', 1)
    ('SOCIAL\\nhttp://instagram.com/ijustine\\nhttp://facebook.com/ijustine\\nhttp://twitter.com/ijustine\\nSnapchat:', 1)
    ('iJustine\\n\\n————————————\\n\\n▼', 1)
    ('VIDEOS\\n\\nSony', 1)
    ('http://amzn.to/2jesbxA\\nG7X', 1)
    ('http://amzn.to/2f6n2Bs\\nCanon', 1)
    ('http://amzn.to/2eRKhQo\\nSony', 1)
    ('http://amzn.to/2okeG2a\\nGoPro', 1)
    ('http://amzn.to/2e1KyhM\\nGoPro', 1)
    ('http://amzn.to/2oksMQT\\nEpidemic', 1)
    ('\\n\\nFavorite', 1)
    ('lenses:', 1)
    ('http://amzn.to/2dT7mFr\\nCanon', 1)
    ('http://amzn.to/2dT62SU\\nSony', 1)
    
    50 most commonly used words
    ('the', 20922)
    ('and', 15161)
    ('to', 13811)
    ('on', 11577)
    ('of', 10083)
    ('-', 9703)
    ('a', 9003)
    ('in', 6405)
    ('for', 6309)
    ('with', 5057)
    ('you', 4600)
    ('is', 4574)
    ('The', 4288)
    ('I', 3802)
    ('by', 3459)
    ('_', 3403)
    ('from', 3013)
    ('at', 2747)
    ('this', 2661)
    ('that', 2429)
    ('more', 2347)
    ('my', 2323)
    ('our', 2125)
    ('out', 2122)
    ('as', 2118)
    ('|', 2108)
    ('&', 2058)
    ('it', 1991)
    ('your', 1961)
    ('us', 1832)
    ('all', 1708)
    ('are', 1683)
    ('video', 1632)
    ('we', 1373)
    ('about', 1351)
    ('new', 1350)
    ('me', 1346)
    ('be', 1337)
    ('his', 1333)
    ('or', 1331)
    ('videos', 1254)
    ('an', 1232)
    ('was', 1217)
    ('Jimmy', 1190)
    ('have', 1165)
    ('can', 1158)
    ('up', 1134)
    ('Show', 1126)
    ('Late', 1080)
    ('get', 1054)
    




Wypisanie wystąpień wszystkich słów po kolumnach


```python
print_word_occurances_by_collumn_in_dataset(cleared)
```

    
    Autos & Vehicles
    
    50 least commonly used words
    ('Finally', 1)
    ('Miura', 1)
    ('Carmax!', 1)
    ('Hope', 1)
    ('enjoyed!\\nMake', 1)
    ('@Hp_overload', 1)
    ('10,', 1)
    ('Ha', 1)
    ('Noi,', 1)
    ('Vietnam\\n\\nWhen', 1)
    ('walking', 1)
    ('dog', 1)
    ('street,', 1)
    ('train', 1)
    ('parked', 1)
    ('trail.', 1)
    ('However,', 1)
    ('pavement', 1)
    ('opposite', 1)
    ('trail', 1)
    ('hearing', 1)
    ('horn', 1)
    ('train.', 1)
    ('result,', 1)
    ('destroyed', 1)
    ('train.\\n\\n\\nTO', 1)
    ('Olympic', 1)
    ('Hoy', 1)
    ('stranger', 1)
    ('record-breaking', 1)
    ('achievements', 1)
    ('wheels,', 1)
    ('now', 1)
    ('Scot', 1)
    ('lay', 1)
    ('claim', 1)
    ('record', 1)
    ('four', 1)
    ('wheels.', 1)
    ('Joining', 1)
    ('celebrate', 1)
    ('60', 1)
    ('Seven,', 1)
    ('‘60', 1)
    ('Challenge’', 1)
    ('Donington', 1)
    ('Park.', 1)
    ('completed.\\n\\nTo', 1)
    ('visit:', 1)
    ('https://tinyurl.com/ybvgpbfq', 1)
    
    50 most commonly used words
    ('the', 284)
    ('and', 165)
    ('to', 153)
    ('a', 119)
    ('of', 107)
    ('on', 97)
    ('in', 81)
    ('is', 73)
    ('for', 63)
    ('with', 47)
    ('-', 45)
    ('The', 45)
    ('you', 39)
    ('us', 36)
    ('more', 35)
    ('that', 32)
    ('car', 32)
    ('at', 31)
    ('our', 30)
    ('this', 30)
    ('your', 29)
    ('be', 28)
    ('are', 28)
    ('by', 27)
    ('will', 25)
    ('new', 25)
    ('it', 24)
    ('was', 20)
    ('from', 20)
    ('all', 19)
    ('or', 17)
    ('video', 17)
    ('I', 17)
    ('as', 16)
    ('my', 15)
    ('has', 14)
    ('about', 14)
    ('out', 14)
    ('their', 13)
    ('we', 13)
    ('but', 13)
    ('2018', 12)
    ('not', 12)
    ('so', 12)
    ('an', 12)
    ('can', 12)
    ('any', 12)
    ('one', 11)
    ('Ford', 11)
    ('This', 11)
    
    
    
    Comedy
    
    50 least commonly used words
    ('http://youtube.com/c/rudymancuso\\nLele', 1)
    ('http://youtube.com/c/lelepons\\nKing', 1)
    ('https://youtube.com/user/BachelorsPadTv\\n\\nVideo', 1)
    ('Effects:', 1)
    ('\\nCaleb', 1)
    ('Natale', 1)
    ('https://instagram.com/calebnatale\\n\\nPA:\\nPaulina', 1)
    ('Gregory\\n\\n\\nShots', 1)
    ('http://youtube.com/c/shots\\n\\n#Rudy\\n#RudyMancuso', 1)
    ('Pacific', 1)
    ('Rim', 1)
    ('good,', 1)
    ('freaking', 1)
    ('perfect.', 1)
    ('no.', 1)
    ('“Add', 1)
    ('swords.”\\n\\nCHECK', 1)
    ('showers?\\n-', 1)
    ('http://gustoonz.cottoncart.com/\\n\\nIt', 1)
    ('difficult', 1)
    ('showers.', 1)
    ('Celebrities', 1)
    ('2017!\\nWhat', 1)
    ('Swift', 1)
    ('invited', 1)
    ('enemies', 1)
    ('toThanksgiving?', 1)
    ('Kanye,', 1)
    ('Harris,', 1)
    ('Demi', 1)
    ('Lovato,', 1)
    ('Kylie', 1)
    ('Jenner.', 1)
    ('games', 1)
    ('begin!', 1)
    ('➜', 1)
    ('http://bit.ly/2vxi9ch\\n\\nSpecial', 1)
    ('to:\\nJoe', 1)
    ('Jonas-', 1)
    ('http://youtube.com/dahberger\\nKaty', 1)
    ('Perry-', 1)
    ('Alessandra', 1)
    ('DeMartino', 1)
    ('@alessandralorenn\\nhttps://www.youtube.com/xxYourPalAlxx\\nDemi', 1)
    ('Lovato-', 1)
    ('@therealjessemarie\\nKanye', 1)
    ('West-', 1)
    ('Juvany', 1)
    ('Georges\\n\\nIf', 1)
    ('Parody', 1)
    
    50 most commonly used words
    ('and', 1033)
    ('the', 889)
    ('-', 883)
    ('|', 874)
    ('to', 865)
    ('on', 758)
    ('Jimmy', 646)
    ('of', 584)
    ('The', 550)
    ('with', 538)
    ('Tonight', 513)
    ('a', 460)
    ('for', 454)
    ('Show', 392)
    ('more', 347)
    ('Night', 340)
    ('Late', 299)
    ('NBC:', 292)
    ('Starring', 291)
    ('in', 273)
    ('Kimmel', 247)
    ('Tumblr:', 238)
    ('is', 221)
    ('Fallon:', 216)
    ('I', 212)
    ('by', 209)
    ('Seth', 208)
    ('you', 208)
    ('my', 190)
    ('►', 184)
    ('\\nFollow', 180)
    ('Google+:', 166)
    ('from', 163)
    ('&', 161)
    ('Fallon', 150)
    ('this', 150)
    ('Show:', 144)
    ('Jimmy:', 144)
    ('us', 140)
    ('Live', 127)
    ('videos', 126)
    ('celebrity', 120)
    ('comedy', 117)
    ('YouTube', 115)
    ('about', 113)
    ('his', 112)
    ('features', 110)
    ('Weeknights', 109)
    ('our', 108)
    ('out', 107)
    
    
    
    Education
    
    50 least commonly used words
    ('https://ed.ted.com/lessons/how-does-your-body-know-you-re-full-hilary-coller\\n\\nHunger', 1)
    ('claws', 1)
    ('belly.', 1)
    ('tugs', 1)
    ('intestines,', 1)
    ('writhe,', 1)
    ('aching', 1)
    ('fed.', 1)
    ('hungry', 1)
    ('unpleasant', 1)
    ('physical', 1)
    ('sensation', 1)
    ('that’s', 1)
    ('impossible', 1)
    ('ignore.', 1)
    ('reacted', 1)
    ('gorging', 1)
    ('morning', 1)
    ('pancakes,', 1)
    ('force:', 1)
    ('fullness.', 1)
    ('you’re', 1)
    ('full?', 1)
    ('Coller', 1)
    ('explains.\\n\\nLesson', 1)
    ('Coller,', 1)
    ('Sashko', 1)
    ('Danylenko.\\n\\nThank', 1)
    ('possible.\\nNoa', 1)
    ('Bijan', 1)
    ('Bayat', 1)
    ('Mokhtari,', 1)
    ('Wewel,', 1)
    ('Ayaan', 1)
    ('Heban,', 1)
    ('Khory,', 1)
    ('Monica', 1)
    ('Ward,', 1)
    ('Scheelings.', 1)
    ('23AndMe', 1)
    ('kits', 1)
    ('gifts,', 1)
    ('https://23AndMe.com/SciShow\\n\\nHow', 1)
    ('genetic', 1)
    ('diversity', 1)
    ('colony', 1)
    ('planet?\\n\\nHosted', 1)
    ('http://instagram.com/thescishow\\n----------\\nSources:\\nhttps://www.newscientist.com/article/dn1936-magic-number-for-space-pioneers-calculated/', 1)
    ('\\nhttp://www.popularmechanics.com/space/deep-space/a10369/how-many-people-does-it-take-to-colonize-another-star-system-16654747/', 1)
    ('\\nhttp://www.sciencedirect.com/science/article/pii/S0094576513004669', 1)
    
    50 most commonly used words
    ('the', 890)
    ('to', 668)
    ('and', 597)
    ('a', 471)
    ('of', 457)
    ('you', 344)
    ('on', 338)
    ('for', 335)
    ('by', 302)
    ('in', 254)
    ('is', 237)
    ('our', 210)
    ('at', 207)
    ('this', 199)
    ('from', 165)
    ('that', 151)
    ('your', 151)
    ('video', 150)
    ('-', 143)
    ('can', 132)
    ('out', 121)
    ('with', 118)
    ('more', 116)
    ('about', 109)
    ('\\n', 105)
    ('Life', 103)
    ('as', 102)
    ('are', 101)
    ('we', 86)
    ('it', 85)
    ('or', 84)
    ('The', 84)
    ('be', 84)
    ('us', 79)
    ('Kevin', 79)
    ('but', 78)
    ('how', 77)
    ('every', 75)
    ('Noggin', 75)
    ('all', 73)
    ('my', 69)
    ('videos', 68)
    ('Patreon', 68)
    ('other', 63)
    ('Patreon:', 63)
    ('get', 61)
    ('What', 61)
    ('so', 61)
    ('have', 59)
    ('&', 58)
    
    
    
    Entertainment
    
    50 least commonly used words
    ('election,', 1)
    ('catheter', 1)
    ('cowboy', 1)
    ("hasn't.\\n\\nConnect", 1)
    ('amateur', 1)
    ('devotee.', 1)
    ("#1218\\nDon't", 1)
    ('https://goo.gl/xeZNQt\\nWatch', 1)
    ('https://youtu.be/MhCdiiB8CQg', 1)
    ('https://youtu.be/7qiOrNao9fg\\nWatch', 1)
    ('http://bit.ly/GMM1218\\n\\nPick', 1)
    ('yet!\\nLeave', 1)
    ('dares', 1)
    ('section!', 1)
    ('\\n\\nOrder', 1)
    ('Embattled', 1)
    ('candidate', 1)
    ('Vice', 1)
    ('Shiva', 1)
    ('Ezekiel', 1)
    ('walkers.\\n\\n#TheWalkingDead', 1)
    ('4\\n\\nSubscribe', 1)
    ('http://www.Bongiovibrand.com\\nand', 1)
    ('CODE:', 1)
    ('BURRITO\\n\\nI', 1)
    ('scenario,', 1)
    ('decide!\\n\\nJP', 1)
    ('NEED\\nBongiovi', 1)
    ('https://www.bongiovibrand.com/\\nDeep', 1)
    ('http://amzn.to/2nHDBe6\\nLarge', 1)
    ('Loaf', 1)
    ('http://amzn.to/2yLu4Ij\\n\\nMerchandise:\\nhttp://bit.ly/HJFMerch\\n\\nFULL', 1)
    ('DETAILS:\\nhttp://www.hellthyjunkfood.com/spaghetti-burrito/\\n\\n🍔Social', 1)
    ('Shhhh.', 1)
    ('Roger', 1)
    ('Hodgson’s', 1)
    ('“Give', 1)
    ('Bit”#givealittlebit', 1)
    ('http://www.rogerhodgson.com', 1)
    ('Intro', 1)
    ('Dion', 1)
    ('Timmer', 1)
    ('Lost\\nEnding', 1)
    ('Slips', 1)
    ('Slurs', 1)
    ('Divided', 1)
    ('VIP\\nNEW', 1)
    ('STORE:', 1)
    ('https://store.officer401.com/\\n-------------------------------------------------------------------------------\\nPATREON:', 1)
    ('https://www.patreon.com/officer401\\nDISCORD', 1)
    
    50 most commonly used words
    ('the', 6230)
    ('and', 4877)
    ('on', 3668)
    ('to', 3370)
    ('of', 3177)
    ('_', 3040)
    ('a', 2447)
    ('The', 1875)
    ('with', 1867)
    ('-', 1848)
    ('in', 1745)
    ('for', 1739)
    ('is', 1303)
    ('from', 1047)
    ('by', 1019)
    ('as', 947)
    ('at', 875)
    ('you', 837)
    ('&', 724)
    ('Late', 714)
    ('I', 694)
    ('this', 690)
    ('Show', 655)
    ('that', 640)
    ('more', 621)
    ('|', 595)
    ('all', 580)
    ('his', 559)
    ('Kimmel', 544)
    ('out', 541)
    ('HERE:', 532)
    ('Jimmy', 532)
    ('it', 525)
    ('new', 498)
    ('my', 449)
    ('Netflix', 446)
    ('her', 429)
    ('about', 397)
    ('videos', 389)
    ('your', 372)
    ('Live', 358)
    ('have', 353)
    ('an', 349)
    ('are', 344)
    ('their', 334)
    ('CBS', 325)
    ('or', 325)
    ('was', 321)
    ('can', 317)
    ('us', 311)
    
    
    
    Film & Animation
    
    50 least commonly used words
    ('Christmas\\n\\nDirected', 1)
    ('http://bit.ly/FOXSubscribe\\n\\n\\nVisit', 1)
    ('Freaks', 1)
    ('Geeks', 1)
    ('6,', 1)
    ('Band', 1)
    ('fast', 1)
    ('rewatched', 1)
    ('DCEU', 1)
    ('too.', 1)
    ('Gilbert', 1)
    ('important', 1)
    ('forward!\\n\\nGot', 1)
    ('apps', 1)
    ('creativity', 1)
    ('productive!\\nSponsored', 1)
    ('http://kingston.gg/BoltSaraDietschy', 1)
    ('\\nKingston', 1)
    ('https://www.youtube.com/user/KingstonTechMemory', 1)
    ('\\nMy', 1)
    ('https://www.youtube.com/watch?v=Q-GgCnQr2Nk\\n\\n\\n-', 1)
    ('\\nAndrewApplePie', 1)
    ('https://soundcloud.com/andrewapplepie\\nHanvai', 1)
    ('https://soundcloud.com/hanvai', 1)
    ('\\n\\n\\n-', 1)
    ('APP', 1)
    ('AFFILIATE', 1)
    ('\\nFree', 1)
    ('Bitcoin', 1)
    ('coinbase:', 1)
    ('https://www.coinbase.com/join/59a64e671c4383008ff909fc\\nLYFT', 1)
    ('(rideshare', 1)
    ('uber)', 1)
    ('SARA177146\\nPostmates', 1)
    ('(dope', 1)
    ('food', 1)
    ('delivery)', 1)
    ('QU5Q\\n\\n\\n-', 1)
    ('RECENTS', 1)
    ('VIDS', 1)
    ('\\nWatching', 1)
    ("Subscribers'", 1)
    ('Videos!!', 1)
    ('https://www.youtube.com/watch?v=Xpjb8Wvtu9Y&t=1s\\nMoving', 1)
    ('NYC', 1)
    ('Office', 1)
    ('Studio!', 1)
    ('https://www.youtube.com/watch?v=VMj9Es0iISI&t=26s\\nMispronouncing', 1)
    ('X!?!', 1)
    ('VID', 1)
    
    50 most commonly used words
    ('the', 1188)
    ('and', 886)
    ('to', 689)
    ('of', 617)
    ('a', 562)
    ('on', 539)
    ('-', 369)
    ('in', 324)
    ('is', 307)
    ('for', 301)
    ('by', 291)
    ('with', 240)
    ('The', 205)
    ('you', 151)
    ('us', 147)
    ('his', 144)
    ('►', 143)
    ('I', 129)
    ('as', 128)
    ('that', 126)
    ('this', 124)
    ('from', 123)
    ('at', 101)
    ('be', 87)
    ('it', 86)
    ('an', 85)
    ('more', 83)
    ('are', 81)
    ('Century', 79)
    ('20th', 79)
    ('our', 78)
    ('my', 78)
    ('we', 75)
    ('me', 74)
    ('your', 73)
    ('●', 69)
    ('out', 67)
    ('&', 67)
    ('he', 66)
    ('all', 66)
    ('movie', 65)
    ('or', 63)
    ('about', 63)
    ('►►', 63)
    ('will', 62)
    ('In', 61)
    ('was', 59)
    ('new', 59)
    ('have', 59)
    ('up', 57)
    
    
    
    Gaming
    
    50 least commonly used words
    ('Amid', 1)
    ('post-war', 1)
    ('boom', 1)
    ("Hollywood's", 1)
    ('Age,', 1)
    ('Cole', 1)
    ('Phelps,', 1)
    ('LAPD', 1)
    ('detective', 1)
    ('thrown', 1)
    ('headfirst', 1)
    ('city', 1)
    ('drowning', 1)
    ('success.', 1)
    ('Search', 1)
    ('chase', 1)
    ('suspects', 1)
    ('interrogate', 1)
    ('witnesses', 1)
    ('struggle', 1)
    ('truth.', 1)
    ('14th', 1)
    ('Switch!\\n\\nLearn', 1)
    ('https://goo.gl/2W69Rs\\n\\n#NintendoSwitch', 1)
    ('#LANoire\\n\\nSubscribe', 1)
    ('Date!', 1)
    ('watching!\\n\\nAnimated', 1)
    ('After', 1)
    ('appearance', 1)
    ('GTLive,', 1)
    ('MatPat', 1)
    ('(Game', 1)
    ('Theory)', 1)
    ("Cuphead!\\nCuphead's", 1)
    ('ODDEST', 1)
    ('Adventure', 1)
    ('(GTLive)', 1)
    ('https://youtu.be/T4alTnUUxbk\\n\\nYes,', 1)
    ('guy.', 1)
    ('childhood', 1)
    ('AKA', 1)
    ('Fairly', 1)
    ('Odd', 1)
    ('Parents,', 1)
    ('Danny', 1)
    ('Phantom,', 1)
    ('TUFF', 1)
    ('Puppy,', 1)
    ('now...the', 1)
    ('NOOG', 1)
    
    50 most commonly used words
    ('the', 262)
    ('and', 197)
    ('to', 176)
    ('on', 166)
    ('of', 142)
    ('-', 126)
    ('for', 116)
    ('a', 110)
    ('►', 93)
    ('us', 85)
    ('in', 81)
    ('you', 58)
    ('Nintendo', 57)
    ('with', 55)
    ('your', 51)
    ('is', 51)
    ('more', 43)
    ('I', 41)
    ('by', 41)
    ('The', 37)
    ('as', 36)
    ('new', 34)
    ('at', 31)
    ('out', 29)
    ('►►', 28)
    ('this', 28)
    ('my', 28)
    ('all', 26)
    ('our', 25)
    ('from', 24)
    ('me', 23)
    ('Instagram:', 22)
    ('Twitter:', 22)
    ('Facebook:', 20)
    ('or', 19)
    ('are', 19)
    ('Clash', 18)
    ('game', 18)
    ('it', 18)
    ('about', 16)
    ('be', 16)
    ('some', 15)
    ('Pokémon', 15)
    ('battle', 14)
    ('world', 14)
    ('that', 14)
    ('http://google.com/+Nintendo', 14)
    ('Google+:', 14)
    ('Pinterest:', 14)
    ('latest!', 14)
    
    
    
    Howto & Style
    
    50 least commonly used words
    ('Media', 1)
    ("Hickson's", 1)
    ('Upper', 1)
    ('Side.', 1)
    ('walks', 1)
    ('R29er', 1)
    ('$2,600', 1)
    ('NYC\\nhttps://www.youtube.com/watch?v=jihpJRqUTn0&t=21s\\n\\nSEE', 1)
    ('Hickson', 1)
    ('https://instagram.com/aphickson/', 1)
    ('channel!:', 1)
    ('https://www.youtube.com/unboxed', 1)
    ('creators!', 1)
    ('list.', 1)
    ('coming,', 1)
    ('go,', 1)
    ('desk!', 1)
    ('Ember', 1)
    ('http://amzn.to/2zC9VFE\\n\\nThe', 1)
    ('curious', 1)
    ("Apple's", 1)
    ('Recognization', 1)
    ('spoilers', 1)
    ('Brows,', 1)
    ('Makeup,', 1)
    ('Lips,', 1)
    ('contacts.', 1)
    ('Anyways', 1)
    ('notifications.\\n\\nProducts', 1)
    ('\\nWashable', 1)
    ('Elmers', 1)
    ('glue\\nMac', 1)
    ('foundation\\nColourpop', 1)
    ('Pencil\\nUrban', 1)
    ('Concealer\\nClinique', 1)
    ('\\nLily', 1)
    ('\\nGerard', 1)
    ('Highlight\\nTrestique', 1)
    ('stick\\nKat', 1)
    ('Palette\\nBen', 1)
    ('Scar', 1)
    ('Watching', 1)
    ('!!!', 1)
    ('monster:\\nhttp://bit.ly/2mboXgj\\n\\nJoe', 1)
    ('befriends', 1)
    ('noisy', 1)
    ('Monster', 1)
    ('tired', 1)
    ('Joe', 1)
    ('receives', 1)
    
    50 most commonly used words
    ('the', 2075)
    ('-', 1909)
    ('to', 1735)
    ('and', 1680)
    ('a', 1216)
    ('of', 1153)
    ('I', 1001)
    ('on', 951)
    ('for', 914)
    ('in', 843)
    ('you', 825)
    ('is', 650)
    ('my', 638)
    ('this', 511)
    ('with', 498)
    ('_', 353)
    ('&', 351)
    ('are', 331)
    ('or', 323)
    ('out', 320)
    ('//', 297)
    ('me', 295)
    ('your', 292)
    ('A', 281)
    ('from', 272)
    ('video', 267)
    ('that', 256)
    ('it', 254)
    ('by', 223)
    ('E', 211)
    ('make', 208)
    ('not', 197)
    ('T', 194)
    ('at', 193)
    ('O', 189)
    ('This', 187)
    ('how', 185)
    ('The', 184)
    ('be', 178)
    ('some', 174)
    ('more', 174)
    ('N', 173)
    ('all', 169)
    ('so', 167)
    ('like', 160)
    ('Refinery29', 159)
    ('videos', 152)
    ('an', 148)
    ('S', 147)
    ('code', 146)
    
    
    
    Music
    
    50 least commonly used words
    ('http://www.youtube.com/marshmellomusic?sub_confirmation=1\\n\\nMARSHMELLO:', 1)
    ('http://spoti.fi/2lxqzzm\\nSoundCloud', 1)
    ('https://shop.marshmellomusic.com/\\n\\nCREDITS:\\nAgency:', 1)
    ('Creative\\nDirector:', 1)
    ('Burke\\nCreative', 1)
    ('Malikyar,', 1)
    ('Gill\\nExecutive', 1)
    ('Huffman\\nProducer:', 1)
    ('Chennisi\\nDP:', 1)
    ('Quill\\nProduction', 1)
    ('Richoux\\nEditor:', 1)
    ('Jungquist\\nColorist:', 1)
    ('Roth', 1)
    ('http://shady.sr/WOWEminem', 1)
    ('Eminem:', 1)
    ('https://goo.gl/AquNpo\\nSubscribe', 1)
    ('https://goo.gl/DxCrDV\\n\\nFor', 1)
    ('\\nhttp://eminem.com\\nhttp://facebook.com/eminem\\nhttp://twitter.com/eminem\\nhttp://instagram.com/eminem\\nhttp://eminem.tumblr.com\\nhttp://shadyrecords.com\\nhttp://facebook.com/shadyrecords\\nhttp://twitter.com/shadyrecords\\nhttp://instagram.com/shadyrecords\\nhttp://trustshady.tumblr.com\\n\\nMusic', 1)
    ('Records\\nhttp://vevo.ly/gA7xKt', 1)
    ('Should', 1)
    ('Loved', 1)
    ('Shadowboxers)', 1)
    ('https://HunterHayes.lnk.to/ysbl\\n\\nLike', 1)
    ('Album,', 1)
    ('‘Feed', 1)
    ('Machine’,', 1)
    ('http://smarturl.it/FeedTheMachine\\n\\nProduced', 1)
    ('Films\\n\\nFor', 1)
    ('Nickelback,', 1)
    ('visit:\\nhttp://www.nickelback.com\\nhttp://www.facebook.com/nickelback\\nhttp://www.twitter.com/nickelback\\nhttp://www.instagram.com/nickelback', 1)
    ('Blackout’', 1)
    ('now.\\n', 1)
    ('https://U2.lnk.to/ISID\\n', 1)
    ('\\n#U2TheBlackout', 1)
    ('\\n#U2SongsofExperience', 1)
    ('see\\n\\nMusic', 1)
    ('Blackout.', 1)
    ('Limited\\n\\nhttp://vevo.ly/TETtx3', 1)
    ('HEADPHONES!', 1)
    ('🎧.', 1)
    ('4K!', 1)
    ('👀\\nProduced', 1)
    ('Ellevan.', 1)
    ('Strø.', 1)
    ('Shot', 1)
    ('RED.\\nSpecial', 1)
    ('EllevanMusic', 1)
    ('HumbleThePoet', 1)
    ('tireless', 1)
    ('produce,', 1)
    
    50 most commonly used words
    ('the', 1675)
    ('-', 1640)
    ('to', 1082)
    ('I', 825)
    ('you', 760)
    ('and', 759)
    ('on', 721)
    ('a', 676)
    ('of', 639)
    ('by', 631)
    ('in', 520)
    ('for', 513)
    ('my', 424)
    ('with', 368)
    ('Music', 355)
    ('me', 352)
    ('is', 351)
    ('it', 343)
    ('&', 332)
    ('The', 313)
    ('video', 303)
    ('just', 287)
    ('we', 284)
    ('that', 257)
    ('this', 237)
    ('from', 237)
    ('–', 234)
    ('|', 216)
    ('at', 211)
    ('out', 209)
    ('your', 196)
    ('all', 196)
    ('performing', 195)
    ('know', 187)
    ('2018', 180)
    ('album', 177)
    ('new', 169)
    ('get', 168)
    ('be', 167)
    ('up', 163)
    ('like', 162)
    ('our', 149)
    ('available', 148)
    ('▶', 142)
    ('here:', 138)
    ('MUSIC', 138)
    ('Music:', 131)
    ('@', 128)
    ('(C)', 128)
    (':', 120)
    
    
    
    News & Politics
    
    50 least commonly used words
    ('least,', 1)
    ('worry', 1)
    ('about.\\n\\n\\nSubscribe', 1)
    ('http://goo.gl/0bsAjO\\n\\nSources:', 1)
    ('\\nhttps://economics.mit.edu/files/11563\\nhttps://www.aeaweb.org/full_issue.php?doi=10.1257/jep.29.3#page=33\\nhttp://voxeu.org/article/how-computer-automation-affects-occupations\\nhttps://www.opensocietyfoundations.org/sites/default/files/future-work-lit-review-20150428.pdf\\nhttps://obamawhitehouse.archives.gov/sites/whitehouse.gov/files/documents/Artificial-Intelligence-Automation-Economy.PDF\\nhttps://www.vox.com/2015/7/27/9038829/automation-myth\\nhttps://www.amazon.com/dp/B00PWX7RPG/ref=dp-kindle-redirect\\nhttps://www.amazon.com/Second-Machine-Age-Prosperity-Technologies-ebook/dp/B00D97HPQI/ref=sr_1_1\\nhttps://www.amazon.com/New-Division-Labor-Computers-Creating/dp/0691124027/ref=sr_1_1?\\nhttps://www.oxfordmartin.ox.ac.uk/downloads/academic/The_Future_of_Employment.pdf\\n\\nClips:\\nhttps://www.youtube.com/watch?v=VTlV0Y5yAww\\nhttps://www.youtube.com/watch?v=_luhn7TLfWU\\nhttps://www.youtube.com/watch?v=rVlhMGQgDkY\\nhttps://www.youtube.com/watch?v=rCoFKUJ_8Yo\\nhttps://www.youtube.com/watch?v=yeyn9zzrC84\\nhttps://www.youtube.com/watch?v=7Pq-S557XQU\\nhttps://www.youtube.com/watch?v=WSKi8HfcxEk\\n\\n///\\n\\nRecent', 1)
    ('advancements', 1)
    ('robotics', 1)
    ('commentators', 1)
    ('worrying', 1)
    ('obsolescence', 1)
    ('worker.', 1)
    ('Silicon', 1)
    ('Valley', 1)
    ('assumption', 1)
    ('scarce.', 1)
    ('economists', 1)
    ('skeptical', 1)
    ('claims,', 1)
    ('notion', 1)
    ('fixed', 1)
    ('debunked', 1)
    ('productivity', 1)
    ('boom.', 1)
    ('Fortunately,', 1)
    ('divine', 1)
    ('labor', 1)
    ('\\n\\n///\\n\\nVox.com', 1)
    ('improving,', 1)
    ('governments', 1)
    ('function.', 1)
    ('Although', 1)
    ('disputed,', 1)
    ('collapse,', 1)
    ('video.\\n»', 1)
    ('World:', 1)
    ('http://go.nowth.is/World_Subscribe\\n»', 1)
    ('http://go.nowth.is/NowThisWorld_Twitter\\n\\nConnect', 1)
    ('NowThis\\n»', 1)
    ('http://go.nowth.is/News_Subscribe\\n»', 1)
    ('http://go.nowth.is/News_Facebook\\n»', 1)
    ('Tweet', 1)
    ('http://go.nowth.is/News_Twitter\\n»', 1)
    ('http://go.nowth.is/News_Instagram\\n»', 1)
    ('http://go.nowth.is/News_Snapchat\\n\\nNowThis', 1)
    ('topical', 1)
    ('9am', 1)
    ('ET.\\n\\nhttp://www.youtube.com/nowthisworld', 1)
    ('all-time', 1)
    ('7.2-magnitude', 1)
    ('rattled', 1)
    
    50 most commonly used words
    ('the', 2213)
    ('to', 1460)
    ('and', 1446)
    ('on', 1116)
    ('of', 1050)
    ('a', 786)
    ('in', 709)
    ('News', 459)
    ('for', 381)
    ('news', 355)
    ('is', 346)
    ('you', 333)
    ('CBS', 330)
    ('from', 325)
    ('with', 291)
    ('The', 278)
    ('that', 271)
    ('our', 263)
    ('NBC', 245)
    ('HERE:', 245)
    ('at', 211)
    ('more', 194)
    ('TODAY', 193)
    ('as', 187)
    ('Subscribe', 182)
    ('are', 175)
    ('it', 165)
    ('This', 164)
    ('has', 162)
    ('Morning', 161)
    ('latest', 156)
    ('out', 156)
    ('Twitter:', 155)
    ('was', 153)
    ('Facebook:', 148)
    ('about', 146)
    ('by', 143)
    ('your', 141)
    ('his', 129)
    ('all', 123)
    ('video', 122)
    ('their', 119)
    ('have', 119)
    ('an', 115)
    ('up', 107)
    ('full', 105)
    ('how', 100)
    ('people', 94)
    ('►', 92)
    ('will', 92)
    
    
    
    Nonprofits & Activism
    
    50 least commonly used words
    ('Undocumented', 1)
    ('Hollywood?', 1)
    ('Yes,', 1)
    ('#HereToStay!', 1)
    ('Actor', 1)
    ('Bambadjan', 1)
    ('Bamba,', 1)
    ('starred', 1)
    ('Good', 1)
    ('Place', 1)
    ('Black', 1)
    ('Panther', 1)
    ('2018,', 1)
    ('revealed', 1)
    ('he', 1)
    ('DACA', 1)
    ('today', 1)
    ('LA', 1)
    ('Times.', 1)
    ('Please', 1)
    ('join', 1)
    ('us', 1)
    ('encouraging', 1)
    ('Hollywood', 1)
    ('stand', 1)
    ('sign', 1)
    ('petition', 1)
    ('at:', 1)
    ('https://defineamerican.com/bamba/\\n\\nLearn', 1)
    ("Bamba's", 1)
    ('http://www.latimes.com/entertainment/movies/la-et-mn-bambadjan-bamba-undocumented-20171128-htmlstory.html\\n\\nDefine', 1)
    ('American', 1)
    ('non-profit', 1)
    ('culture', 1)
    ('uses', 1)
    ('transcend', 1)
    ('politics', 1)
    ('shift', 1)
    ('conversation', 1)
    ('identity,', 1)
    ('America.\\n\\nHow', 1)
    ('do', 1)
    ('define', 1)
    ('American?\\n\\nSee', 1)
    ('http://defineamerican.com/stories\\n\\nSubscribe:', 1)
    ('\\nEmail:', 1)
    ('http://defineamerican.com/subscribe', 1)
    ('\\nYoutube:', 1)
    ('http://www.youtube.com/defineamerican\\nTwitter:', 1)
    ('https://twitter.com/defineamerican\\nFacebook:', 1)
    
    50 most commonly used words
    ('and', 59)
    ('the', 50)
    ('to', 37)
    ('in', 29)
    ('a', 28)
    ('of', 27)
    ('for', 17)
    ('is', 15)
    ('by', 14)
    ('that', 13)
    ('on', 11)
    ('with', 11)
    ('her', 9)
    ('I', 9)
    ('from', 9)
    ('at', 9)
    ('about', 9)
    ('—', 8)
    ('shark', 8)
    ('more', 8)
    ('The', 8)
    ('(The', 7)
    ('homeless', 7)
    ('are', 7)
    ('his', 6)
    ('–', 6)
    ('can', 6)
    ('out', 6)
    ('she', 6)
    ('people', 6)
    ('you', 6)
    ('Guild', 5)
    ('Writers', 5)
    ('в', 5)
    ('на', 5)
    ('New', 5)
    ('hard', 5)
    ('be', 5)
    ('presented', 4)
    ('fins', 4)
    ('U.S.', 4)
    ('finning', 4)
    ('their', 4)
    ('social', 4)
    ('works', 4)
    ('She', 4)
    ('or', 4)
    ('Manda', 4)
    ('was', 4)
    ('21', 4)
    
    
    
    People & Blogs
    
    50 least commonly used words
    ("SHANTELL'S", 1)
    ('https://www.youtube.com/shantellmartin\\nCANDICE', 1)
    ('https://www.lovebilly.com\\n\\nfilmed', 1)
    ('https://twitter.com/CaseyNeistat\\n\\namazing', 1)
    ('https://soundcloud.com/discoteeth\\n\\nad', 1)
    ('disclosure.', 1)
    ('AN', 1)
    ('AD.', 1)
    ('promoting', 1)
    ('anything.', 1)
    ('samsung', 1)
    ('produce', 1)
    ("'GALAXY", 1)
    ("PROJECT'", 1)
    ('initiative', 1)
    ('enables', 1)
    ('otherwise', 1)
    ('opportunity', 1)
    ('make.', 1)
    ('clear.', 1)
    ("i'll", 1)
    ('specifics.', 1)
    ('Kittens', 1)
    ('commercial\\n\\nReuploaded', 1)
    ('Youku', 1)
    ('deleted.', 1)
    ('Molly', 1)
    ('god', 1)
    ('damn', 1)
    ('challenged', 1)
    ('blind', 1)
    ('cake', 1)
    ('decorating', 1)
    ('contest!', 1)
    ('won?', 1)
    ('(YES,', 1)
    ('AGREE', 1)
    ('THAT', 1)
    ('MOLLY', 1)
    ('DEFINITELY', 1)
    ('WON)\\nWatch', 1)
    ('subscribe!\\nhttps://www.youtube.com/user/MollyBurkeOfficial\\n\\nSnapChat:', 1)
    ('ShopMissA', 1)
    ('sells', 1)
    ('primer,', 1)
    ('eyebrow', 1)
    ('pencil,', 1)
    ('bronzer,', 1)
    ('this?\\n\\nThis', 1)
    ("just...I'm", 1)
    
    50 most commonly used words
    ('-', 1557)
    ('the', 1148)
    ('to', 874)
    ('and', 845)
    ('a', 660)
    ('on', 632)
    ('of', 593)
    ('I', 388)
    ('in', 384)
    ('for', 359)
    ('you', 339)
    ('with', 282)
    ('via', 263)
    ('is', 238)
    ('Music', 235)
    ('at', 234)
    ('The', 234)
    ('my', 216)
    ('Production', 209)
    ('Chappell', 209)
    ('Warner', 209)
    ('out', 185)
    ('videos', 179)
    ('us', 171)
    ('from', 162)
    ('our', 160)
    ('this', 146)
    ('more', 139)
    ('by', 136)
    ('as', 133)
    ('that', 132)
    ('was', 108)
    ('&', 104)
    ('it', 102)
    ('all', 99)
    ('me', 98)
    ('we', 97)
    ('video', 95)
    ('an', 94)
    ('your', 90)
    ('MORE', 89)
    ('have', 87)
    ('so', 85)
    ('about', 84)
    ('New', 75)
    ('are', 75)
    ('Audio', 74)
    ('his', 71)
    ('be', 71)
    ('up', 71)
    
    
    
    Pets & Animals
    
    50 least commonly used words
    ('shopping', 1)
    ('aquariums.', 1)
    ('based', 1)
    ('aquarium...', 1)
    ('luck.', 1)
    ('did', 1)
    ('have...', 1)
    ('course.', 1)
    ('lol\\n\\nDetails', 1)
    ('buying', 1)
    ('tickets:\\n\\nBuy', 1)
    ('tickets', 1)
    ('store:\\n75', 1)
    ('Macdonald', 1)
    ('Ave\\nDartmouth,', 1)
    ('Nova', 1)
    ('Scotia\\nOR\\nCall', 1)
    ('buy:', 1)
    ('\\n1', 1)
    ('(902)', 1)
    ('446-3474\\nOR', 1)
    ('https://goo.gl/4BaMqb\\n\\nTickets', 1)
    ('$10', 1)
    ('each\\nDraw', 1)
    ('Dec', 1)
    ('3rd', 1)
    ('store(store', 1)
    ('\\n\\n3rd', 1)
    ('$200', 1)
    ('\\n2nd', 1)
    ('$300', 1)
    ('\\n1st', 1)
    ('1/2', 1)
    ('trip', 1)
    ('King', 1)
    ('Gallery', 1)
    ('dinner.', 1)
    ('\\nWinner', 1)
    ('store.', 1)
    ('Date', 1)
    ('Saturday.', 1)
    ('http://customaquariums.com', 1)
    ('Hours\\n\\nEnjoy', 1)
    ('Week!\\nWith', 1)
    ('daylight', 1)
    ('shrinking,', 1)
    ('can.', 1)
    ('Set', 1)
    ('Hours', 1)
    ('pm;', 1)
    
    50 most commonly used words
    ('the', 686)
    ('to', 542)
    ('and', 534)
    ('-', 391)
    ('on', 336)
    ('a', 319)
    ('of', 298)
    ('in', 164)
    ('is', 157)
    ('our', 156)
    ('for', 153)
    ('us', 143)
    ('Cat', 122)
    ('you', 120)
    ('BBC', 116)
    ('this', 112)
    ('more', 103)
    ('out', 100)
    ("Simon's", 98)
    ('from', 96)
    ('with', 94)
    ('are', 92)
    ('Earth', 89)
    ('your', 89)
    ('Peterson', 79)
    ('can', 79)
    ('Instagram:', 73)
    ('his', 73)
    ('we', 71)
    ('as', 69)
    ('at', 69)
    ('new', 67)
    ('he', 65)
    ('it', 65)
    ('they', 64)
    ('their', 62)
    ('animal', 62)
    ('about', 59)
    ('I', 59)
    ('The', 58)
    ('that', 57)
    ('amazing', 55)
    ('Facebook:', 54)
    ('was', 54)
    ('an', 54)
    ('Coyote', 51)
    ('by', 51)
    ('videos', 49)
    ('be', 49)
    ('Wilderness', 47)
    
    
    
    Science & Technology
    
    50 least commonly used words
    ('thoughts!\\nAll', 1)
    ('Videos:', 1)
    ('https://www.youtube.com/watch?v=vqztGUwhRlQ&list=PLoYRB6C09WUDbCndtEDELX-Fpk_pgATvF\\n►', 1)
    ('http://www.youtube.com/subscription_center?add_user=ijustine\\n►', 1)
    ('BOOK!', 1)
    ('http://ijustinebook.com\\n►', 1)
    ('STICKERS!', 1)
    ('http://ijustinestickers.com\\n\\n▼', 1)
    ('SOCIAL\\nhttp://instagram.com/ijustine\\nhttp://facebook.com/ijustine\\nhttp://twitter.com/ijustine\\nSnapchat:', 1)
    ('iJustine\\n\\n————————————\\n\\n▼', 1)
    ('STUFF', 1)
    ('VIDEOS\\n\\nSony', 1)
    ('http://amzn.to/2jesbxA\\nG7X', 1)
    ('http://amzn.to/2f6n2Bs\\nCanon', 1)
    ('80D', 1)
    ('http://amzn.to/2eRKhQo\\nSony', 1)
    ('http://amzn.to/2okeG2a\\nGoPro', 1)
    ('http://amzn.to/2e1KyhM\\nGoPro', 1)
    ('http://amzn.to/2oksMQT\\nEpidemic', 1)
    ('\\n\\nFavorite', 1)
    ('lenses:', 1)
    ('\\nCanon', 1)
    ('EF', 1)
    ('24-70mm', 1)
    ('http://amzn.to/2dT7mFr\\nCanon', 1)
    ('EF-S', 1)
    ('http://amzn.to/2dT62SU\\nSony', 1)
    ('16-35mm', 1)
    ('http://amzn.to/2ftPaTf\\nSony', 1)
    ('Distagon', 1)
    ('35mm', 1)
    ('http://amzn.to/2oB0XQj\\nSony', 1)
    ('angle', 1)
    ('http://amzn.to/2e1Myqz\\n\\nRode', 1)
    ('Small', 1)
    ('http://amzn.to/2fkiVGJ\\nRode', 1)
    ('Larger', 1)
    ('(battery', 1)
    ('required)', 1)
    ('http://amzn.to/2ftNkl8\\nSony', 1)
    ('XLR', 1)
    ('adapter', 1)
    ('http://amzn.to/2kCcIDH\\nSmall', 1)
    ('http://amzn.to/2oX7Eih\\n\\nFavorite', 1)
    ('Card', 1)
    ('http://amzn.to/2oWRGoD\\n\\nDJI', 1)
    ('http://amzn.to/2f6nL5E\\nPhantom', 1)
    ('http://amzn.to/2pbDrN1\\nPhantom', 1)
    ('http://amzn.to/2oX63Jz', 1)
    ('Pint', 1)
    
    50 most commonly used words
    ('the', 1337)
    ('to', 881)
    ('and', 858)
    ('a', 651)
    ('of', 619)
    ('on', 551)
    ('-', 467)
    ('in', 394)
    ('for', 390)
    ('is', 362)
    ('you', 294)
    ('by', 251)
    ('The', 251)
    ('with', 232)
    ('this', 229)
    ('that', 228)
    ('at', 220)
    ('I', 217)
    ('our', 201)
    ('more', 169)
    ('from', 165)
    ('it', 159)
    ('are', 146)
    ('video', 142)
    ('your', 141)
    ('us', 126)
    ('be', 124)
    ('we', 115)
    ('out', 109)
    ('will', 106)
    ('my', 105)
    ('an', 103)
    ('or', 103)
    ('iPhone', 98)
    ('can', 93)
    ('all', 90)
    ('not', 87)
    ('as', 86)
    ('make', 83)
    ('about', 82)
    ('was', 80)
    ('do', 79)
    ('have', 78)
    ('up', 78)
    ('new', 75)
    ('if', 75)
    ('how', 71)
    ('one', 71)
    ('first', 70)
    ('like', 70)
    
    
    
    Shows
    
    50 least commonly used words
    ('comes', 1)
    ('clean', 1)
    ('after', 1)
    ('overwhelming', 1)
    ('evidence', 1)
    ('shows', 1)
    ("it's", 1)
    ('throttling', 1)
    ('speed', 1)
    ('older', 1)
    ('iPhones.', 1)
    ('But', 1)
    ('why?', 1)
    ("Here's", 1)
    ('reaction', 1)
    ('slowing', 1)
    ('your', 1)
    ('SE', 1)
    ('Plus.\\n\\nSubscribe', 1)
    ("We'll", 1)
    ('break', 1)
    ('all', 1)
    ('HomePod', 1)
    ('details', 1)
    ('you', 1)
    ('need', 1)
    ('know.', 1)
    ('announced', 1)
    ("HomePod's", 1)
    ('Jan.', 1)
    ('26', 1)
    ('preorder', 1)
    ('open', 1)
    ('before', 1)
    ('its', 1)
    ('Feb.', 1)
    ('9', 1)
    ('release', 1)
    ('date.\\n\\nSubscribe', 1)
    ('In', 1)
    ('season', 1)
    ('5', 1)
    ('premiere', 1)
    ('Zones,', 1)
    ('Warriors', 1)
    ('get', 1)
    ('news', 1)
    ('big', 1)
    ('offseason', 1)
    ('trades', 1)
    
    50 most commonly used words
    ('on', 10)
    ('us', 10)
    ('the', 8)
    ('of', 6)
    ('Twitter:', 4)
    ('Facebook:', 4)
    ('and', 4)
    ('iPhone', 4)
    ('to', 4)
    ('Apple', 4)
    ('new', 3)
    ('our', 3)
    ('down', 3)
    ('http://www.facebook.com/bleacherreport', 2)
    ('coverage:', 2)
    ('sports', 2)
    ('exclusive', 2)
    ('more', 2)
    ('Game', 2)
    ('http://bit.ly/2icCYYm', 2)
    ('Instagram:', 2)
    ('https://www.twitter.com/cnet\\nFollow', 2)
    ('https://www.facebook.com/cnet\\nFollow', 2)
    ('https://cnet.app.link/GWuXq8ExzG\\nLike', 2)
    ('app:', 2)
    ('CNET', 2)
    ('http://cnet.co/2g8kcf4\\nDownload', 2)
    ('playlists:', 2)
    ('out', 2)
    ('http://cnet.co/2heRhep\\nCheck', 2)
    ('CNET:', 2)
    ('7', 2)
    ('6S', 2)
    ('Plus,', 2)
    ('6', 2)
    ('http://www.twitter.com/bleacherreportLike', 1)
    ('https://www.youtube.com/user/BleacherReport?sub_confirmation=1Follow', 1)
    ('http://bleacherreport.com/Subscribe:', 1)
    ('Find', 1)
    ('https://www.gameofzonesstore.com/', 1)
    ('MERCH!:', 1)
    ('ZONES', 1)
    ('OF', 1)
    ('GAME', 1)
    ('GET', 1)
    ('🏝🗣', 1)
    ('Blake', 1)
    ('for', 1)
    ('trading', 1)
    ('into', 1)
    
    
    
    Sports
    
    50 least commonly used words
    ('returner', 1)
    ('Dion', 1)
    ('blasts', 1)
    ('kickoff', 1)
    ('Broncos', 1)
    ('Stoyle', 1)
    ('Demetrius', 1)
    ('Magee', 1)
    ('gain,', 1)
    ('laterals', 1)
    ('tackle,', 1)
    ('Riggs,', 1)
    ('comes', 1)
    ('Heidelberg.', 1)
    ('Student', 1)
    ('Princes', 1)
    ('49-20', 1)
    ('Capital', 1)
    ('Nov.', 1)
    ('11,', 1)
    ('timely', 1)
    ('distraction', 1)
    ('trouble', 1)
    ('steps', 1)
    ('ring.', 1)
    ('wildest', 1)
    ('distractions', 1)
    ('targeted', 1)
    ('defeated.\\nGet', 1)
    ('Parking', 1)
    ('Lot', 1)
    ('Chronicles', 1)
    ('return,', 1)
    ('rainy', 1)
    ('11/8', 1)
    ('Timberwolves.', 1)
    ('Area', 1)
    ('legend', 1)
    ('E-40', 1)
    ('stop', 1)
    ('by,', 1)
    ('guest', 1)
    ('Arthur', 1)
    ('Renowitzky', 1)
    ('Life', 1)
    ('Goes', 1)
    ('Foundation', 1)
    ('(http://www.lgof.org).\\n\\nWho', 1)
    ('episode?', 1)
    ('Leave', 1)
    
    50 most commonly used words
    ('the', 1662)
    ('on', 1549)
    ('to', 1080)
    ('and', 947)
    ('of', 474)
    ('for', 458)
    ('in', 453)
    ('with', 358)
    ('ESPN', 347)
    ('a', 324)
    ('NFL', 324)
    ('our', 286)
    ('YouTube', 262)
    ('us', 249)
    ('Watch', 216)
    ('you', 216)
    ('your', 216)
    ('at', 207)
    ('-', 201)
    ('is', 192)
    ('Twitter:', 186)
    ('highlights', 185)
    ('more', 178)
    ('Facebook:', 176)
    ('Follow', 175)
    ('from', 162)
    ('YouTube:', 159)
    ('Instagram:', 155)
    ('out', 143)
    ('The', 138)
    ('sports', 137)
    ('get', 136)
    ('all', 133)
    ('NFL,', 132)
    ('NBA,', 126)
    ('NBA', 124)
    ('First', 118)
    ('official', 118)
    ('WWE', 116)
    ('&', 115)
    ('Subscribe', 113)
    ('that', 107)
    ('news', 104)
    ('NCAA', 99)
    ('Social', 99)
    ('videos', 97)
    ('College', 96)
    ('more.', 94)
    ('TV:', 94)
    ('favorite', 94)
    
    
    
    Travel & Events
    
    50 least commonly used words
    ('Inspired', 1)
    ('@MercedesBenz,', 1)
    ('fully', 1)
    ('enclosed', 1)
    ('@Boeing', 1)
    ('777', 1)
    ('Suites', 1)
    ('floor-to-ceiling', 1)
    ('sliding', 1)
    ('doors,', 1)
    ('combine', 1)
    ('technology', 1)
    ('intelligent', 1)
    ('deliver', 1)
    ('function,', 1)
    ('luxury', 1)
    ('comfort.', 1)
    ('http://bit.ly/emiratesgamechanger', 1)
    ('(For', 1)
    ('licensing', 1)
    ('usage,', 1)
    ('licensing@viralhog.com)\\nLightning', 1)
    ('strike', 1)
    ('KL743', 1)
    (',heading', 1)
    ('Lima.', 1)
    ('footages', 1)
    ('perfectly', 1)
    ('aircrafts', 1)
    ('responds', 1)
    ('lightning.', 1)
    ('Early', 1)
    ('drone', 1)
    ('beach', 1)
    ('Haifa,', 1)
    ('Israel.', 1)
    ('Bicycle', 1)
    ('equipped', 1)
    ('Vostok', 1)
    ('Titanium', 1)
    ('extremities', 1)
    ('rack.', 1)
    ("World's", 1)
    ('BBJ', 1)
    ('Configuration', 1)
    ('operated', 1)
    ('Deer', 1)
    ('Jet.\\n\\nThis', 1)
    ('opened', 1)
    ('display', 1)
    
    50 most commonly used words
    ('the', 325)
    ('and', 274)
    ('to', 195)
    ('a', 174)
    ('in', 151)
    ('of', 140)
    ('on', 134)
    ('for', 115)
    ('food', 76)
    ('is', 71)
    ('I', 69)
    ('you', 69)
    ('street', 64)
    ('with', 58)
    ('our', 57)
    ('as', 50)
    ('was', 48)
    ('we', 47)
    ('this', 47)
    ('Disney', 46)
    ('that', 46)
    ('from', 43)
    ('at', 42)
    ('-', 40)
    ('by', 40)
    ('us', 37)
    ('more', 33)
    ('Walt', 32)
    ('can', 31)
    ('so', 30)
    ('an', 30)
    ('delicious', 29)
    ('Instagram:', 28)
    ('up', 28)
    ('The', 28)
    ('best', 27)
    ('videos', 27)
    ('my', 27)
    ('all', 24)
    ('amazing', 24)
    ('Indian', 24)
    ('around', 23)
    ('it', 23)
    ('much', 22)
    ('World', 21)
    ('their', 21)
    ('most', 21)
    ('Subscribe', 20)
    ('INSIDER', 20)
    ('world', 20)
    
    


Ogólnie dominują najczęściej występujące słowa w języku angielskim, szczególnie słowo "The"
Dodatkowo dla poszczególnych kategorii często pojawiają się np. tytuły programmów i nazwika np. "Jimmy" w "Comedy" od słynnego late night show Jimmiego Kimmela

# Tagi

Inicjalizacja słownika przechowującego informacje dla poszczególnych tagów


```python
tag_views = {}
```

Funkcja wstawiająca nazwę taga oraz ilość pewnej cechy taga do powyższego słownika


```python
def insert_to_tag_view(key,val):
    global tag_views
    if ( type(key) != str):
        return
    if ( key not in tag_views.keys() ):
        tag_views.update({key : val})
    else:
        tag_views[key] += val
```


```python
tag_views = {}
all_tags = cleared['tags']
first = cleared['views']
for j in range(0,len(all_tags)):
    try:
      tags = all_tags[j].split("|\"")
    except(Exception):
      continue
    for i in range(1,len(tags)):
        tags[i] = tags[i][:-1]
    for i in range(0,len(tags)):
        try:
          insert_to_tag_view(tags[i], first[j])
        except(Exception):
          print(j)
```


```python
print(tag_views)
```

    {'SHANtell martin': 748374, 'last week tonight trump presidency': 2418783, 'last week tonight donald trump': 2418783, 'john oliver trump': 2418783, 'donald trump': 4745942, 'racist superman': 6204934, 'rudy': 10959539, 'mancuso': 10959539, 'king': 3611322, 'bach': 3191434, 'racist': 3191434, 'superman': 4248999, 'love': 7501305, 'rudy mancuso poo bear black white official music video': 3962469, 'iphone x by pineapple': 3191434, 'lelepons': 38943570, 'hannahstocking': 27840245, 'rudymancuso': 27840245, 'inanna': 25719204, 'anwar': 27840245, 'sarkis': 25719204, 'shots': 27840245, 'shotsstudios': 27840245, 'alesso': 27840245, 'anitta': 24629388, 'brazil': 24372329, "Getting My Driver's License | Lele Pons": 3191434, 'rhett and link': 1456525, 'gmm': 1456525, 'good mythical morning': 1456525, 'rhett and link good mythical morning': 1456525, 'good mythical morning rhett and link': 1456525, 'mythical morning': 1456525, 'Season 12': 1456525, 'nickelback lyrics': 343168, 'nickelback lyrics real or fake': 343168, 'nickelback': 343168, 'nickelback songs': 343168, 'nickelback song': 343168, 'rhett link nickelback': 343168, 'gmm nickelback': 343168, 'lyrics (website category)': 343168, 'nickelback (musical group)': 343168, 'rock': 3287674, 'music': 31953531, 'lyrics': 51156609, 'chad kroeger': 343168, 'canada': 1693544, 'music (industry)': 343168, 'mythical': 959440, 'gmm challenge': 343168, 'comedy': 94141557, 'funny': 115302736, 'challenge': 5358579, 'ryan': 4268286, 'higa': 3332407, 'higatv': 3332407, 'nigahiga': 9415948, 'i dare you': 2095731, 'idy': 2095731, 'rhpc': 2095731, 'dares': 2095731, 'no truth': 2095731, 'comments': 2095731, 'stupid': 4202071, 'fail': 4404655, 'ijustine': 795232, 'week with iPhone X': 119180, 'iphone x': 9399559, 'apple': 8757524, 'iphone': 17143972, 'iphone x review': 4048316, 'iphone x unboxing': 4048316, 'SNL': 12188347, 'Saturday Night Live': 13281233, 'SNL Season 43': 11456580, 'Episode 1730': 2103417, 'Tiffany Haddish': 2229840, 'Roy Moore': 6406246, 'Mikey Day': 2103417, 'Mike Pence': 2103417, 'Beck Bennett': 2219479, 'Jeff Sessions': 2240420, 'Kate McKinnon': 9105226, 's43': 11616722, 's43e5': 2103417, 'episode 5': 2103417, 'live': 74014159, 'new york': 14049446, 'sketch': 21390662, 'hilarious': 19292826, 'late night': 51094185, 'host': 23987692, 'guest': 12002456, 'laugh': 14115896, 'impersonation': 11616722, 'actor': 13744002, 'improv': 15364017, 'musician': 11719974, 'comedian': 50133838, 'actress': 6687257, 'If Loving You Is Wrong': 2103417, 'Oprah Winfrey': 2103417, 'OWN': 2103417, 'Girls Trip': 2103417, 'The Carmichael Show': 2103417, 'Keanu': 2103417, 'Taylor Swift': 7768104, 'Reputation': 4469472, 'Look What You Made Me Do': 2696683, 'ready for it?': 2103417, 'cold open': 2103417, '5 Ice Cream Gadgets': 817732, 'Ice Cream': 817732, 'Cream Sandwich Maker': 817732, 'gadgets': 3140277, 'gadget review': 817732, 'review': 26548420, 'unboxing': 8584576, 'kitchen gadgets': 817732, 'Gadgets put to the Test': 817732, 'testing': 3623651, '10 Kitchen Gadgets': 817732, '7 Camping Coffee Gadgets': 817732, '10 Kitchen Gadgets put to the Test': 817732, 'Trailer': 16428936, 'Hugh Jackman': 980101, 'Michelle Williams': 826059, 'Zac Efron': 826059, 'Zendaya': 2084925, 'Rebecca Ferguson': 826059, 'pasek and paul': 826059, 'la la land': 826059, 'moulin rouge': 826059, 'high school musical': 1657540, 'hugh jackman musical': 826059, 'zac efron musical': 826059, 'musical': 2912215, 'the greatest showman': 980101, 'greatest showman': 826059, 'Michael Gracey': 826059, 'P.T. Barnum': 826059, 'Barnum and Bailey': 826059, 'Barnum Circus': 826059, 'Barnum and Bailey Circus': 826059, '20th century fox': 826059, 'greatest showman trailer': 826059, 'trailer': 18667644, 'official trailer': 4607098, 'the greatest showman trailer': 826059, 'logan': 9379167, 'Benj Pasek': 826059, 'Justin Paul': 826059, 'vox.com': 8074721, 'vox': 8074721, 'explain': 8077958, 'shift change': 711715, 'future of work': 809687, 'automation': 1264976, 'robots': 809687, 'jobs': 809687, 'technological unemployment': 256426, 'joss fong': 256426, 'heidi shierholz': 256426, 'martin ford': 256426, 'rise of the robots': 256426, 'humans': 2174633, 'workers': 1709943, 'employment': 1264976, 'economics': 946257, 'macroeconomics': 256426, 'silicon valley': 256426, 'basic income': 1262945, 'NFL': 9638812, 'Football': 9603660, 'offense': 9364377, 'defense': 9898167, 'afc': 9364377, 'nfc': 9364377, 'American Football': 9364377, 'highlight': 19234449, 'highlights': 10832600, 'game': 14505457, 'games': 13987130, 'sport': 9533904, 'sports': 20025777, 'action': 11724506, 'play': 12606592, 'plays': 10441134, 'season': 10201142, '2017': 32882915, 'rookie': 3087523, 'rookies': 1802193, 'recap': 10773352, 'run': 9284702, 'sprint': 9284702, 'catch': 10169306, 'huge': 9620604, 'amazing': 10820921, 'touchdown': 9470880, 'td': 9284702, 'week 9': 1802193, 'wk 9': 1802193, 'new england': 1372941, 'patriots': 1372941, 'pats': 1372941, 'denver': 81377, 'broncos': 81377, 'lewis': 200879, 'kickoff': 81377, 'kick return': 81377, 'return td': 81377, 'special teams': 81377, 'sp:dt=2017-11-12T20:30:00-05:00': 81377, 'sp:vl=en-US': 9745846, 'sp:st=football': 9745846, 'sp:li=nfl': 9284702, 'sp:ti:home=Den': 81377, 'sp:ti:away=NE': 1372941, 'sp:scp=athlete_in_match': 3047829, 'sp:ty=high': 12412518, 'nfl-lewdio': 81377, 'The Walking Dead': 472776, 'shiva': 104578, 'tiger': 104578, 'king ezekiel': 104578, 'episode 804': 104578, 'episode 4': 104578, 'some guy': 104578, 'sad': 2151879, 'zombies': 468044, 'all out war': 468044, 'war': 468044, 'walkers': 1214136, 'the kingdom': 468044, 'the saviors': 468044, 'Rick': 468044, 'Rick Grimes': 468044, 'Negan': 468044, 'Ezekiel': 468044, 'Daryl': 472776, 'Michonne': 468044, 'Morgan': 468044, 'Carl': 554788, 'Dwight': 468044, 'Jeffrey Dean Morgan': 3053237, 'Andrew Lincoln': 468044, 'Norman Reedus': 472776, 'Danai Gurira': 468044, 'Lennie James': 468044, 'robert kirkman': 468044, 'scott m. gimple': 468044, 'apocalypse': 689841, 'season 8': 468044, 'alexandria': 468044, 'AMC': 472776, 'tv': 2639757, 'tv show': 4896624, 'television': 13448947, '11/12_TWD': 104578, 'Featured': 1158550, 'marshmello': 687582, 'blocks': 687582, 'marshmello blocks': 687582, 'blocks music video': 687582, 'marshmello music video': 687582, 'roblox bully story': 687582, 'marshmello alone': 687582, 'roblox': 687582, 'cuphead': 743042, 'marshmello nightcore': 687582, 'marshmello musically': 687582, 'music for kids': 687582, 'happy music': 687582, 'videos that make you happy': 687582, 'sad songs': 687582, 'childrens music': 687582, 'joytime': 687582, 'marshmello joytime': 687582, 'frat bro': 687582, 'band': 820794, 'high school': 709019, 'crush': 4418371, 'mellogang': 687582, 'nowthis': 544770, 'nowthis world': 544770, 'world news': 914803, 'nowthis news': 544770, 'international news': 544770, 'countries': 1093527, 'collapse': 544770, 'failing countries': 544770, 'war in the world': 544770, 'fragile states': 544770, 'the fragile states index': 544770, 'fund for peace': 544770, 'social': 550583, 'economic': 551515, 'political': 661988, 'which countries are failing': 544770, 'Central African Republic': 544770, 'CAR': 544770, 'countries that are sinking': 544770, 'countries that are dying': 544770, 'countries that are going to disappear': 544770, 'Somalia': 544770, 'colonization': 544770, 'dictatorship': 941116, 'south sudan': 544770, 'shopping for new fish': 207532, 'new fish': 207532, 'aquarium fish': 207532, 'aquarium': 207532, 'uarujoey': 207532, 'king of diy': 207532, 'aquarium gallery': 207532, 'the king of diy': 207532, 'fish tank': 207532, 'fish (animal)': 207532, 'diy king': 207532, 'arowana': 207532, 'flower horn': 207532, 'fish': 368062, 'Robots': 75752, 'Boston Dynamics': 1679167, 'SpotMini': 75752, 'Legged Locomotion': 1679167, 'Dynamic robot': 75752, 'pacific rim': 295639, 'pacific rim 2': 295639, 'pacific rim sequel': 295639, 'Guillermo del Toro': 295639, 'Cracked': 1398307, 'cracked.com': 1398307, 'spoof': 2634983, 'satire': 11764959, 'parody': 19472603, 'spoofs': 1398307, 'josh sargent': 295639, 'fix a movie': 295639, 'how to fix': 295639, 'charlie day': 295639, 'idris elba': 295639, 'fighting robots': 295639, 'fight scenes': 295639, 'pacific rim problems': 295639, 'pacific rim epic': 295639, 'pacific rim comic con': 295639, 'pacific rim characters': 295639, 'pacific rim robots': 295639, 'pacific rim machines': 295639, 'mako mori': 295639, 'fan theory': 295639, 'headcanon': 295639, 'how it should have ended': 295639, 'TED': 366861, 'TED-Ed': 366861, 'TED Education': 366861, 'TED Ed': 366861, 'Hilary Coller': 78044, 'Sashko Danylenko': 78044, 'hunger': 2552327, 'fullness': 78044, 'appetite': 78044, 'food': 13769822, 'stomach': 78044, 'digestion': 78044, 'vagus nerve': 78044, 'hypothalamus': 78044, 'hormones': 78044, 'endocrine cells': 78044, 'digestive system': 78044, 'intestine': 78044, 'ghrelin': 78044, 'insulin': 78044, 'leptin': 78044, 'water': 1432669, 'fiber': 78044, 'protein': 78044, 'ultralight': 97007, 'airplane': 1286191, 'homemade': 220566, 'DIY': 9637283, 'hoverbike': 97007, 'part 103': 97007, 'ultra light': 97007, 'aircraft': 827835, 'aviation': 272859, 'do it yourself': 3142335, 'basement': 97007, 'petersripol': 97007, 'peter sripol': 248746, 'biplane': 97007, 'flitetest': 97007, 'flite test': 97007, 'electric vehicle': 454059, 'electric': 713702, 'SciShow': 1036414, 'science': 6760587, 'Hank': 1036414, 'Green': 1036414, 'education': 2982219, 'learn': 2553537, 'stefan chin': 223871, 'mars': 2129358, 'colony': 223871, 'genetic diversity': 223871, 'founder effect': 223871, 'alleles': 223871, 'effective population size': 223871, 'mutations': 223871, 'interstellar mission': 223871, 'acta astronautica': 223871, 'Founding An Inbreeding-Free Space Colony': 223871, 'life noggin': 741317, 'life noggin youtube': 741317, 'youtube life noggin': 741317, 'life noggin channel': 741317, 'education channel': 741317, 'life noggin face reveal': 741317, 'edutainment': 741317, 'edutainment videos': 741317, 'blocko': 741317, 'blocko life noggin': 741317, 'technology': 4459873, 'educational': 2935839, 'school': 3977742, 'lucid dream': 115791, 'lucid dreaming': 115791, 'control your dreams': 115791, 'how to control your dreams': 115791, 'how to lucid dream': 115791, 'sleep': 115791, 'dreaming': 407898, 'nightmare': 115791, 'dream': 115791, 'REM sleep': 115791, 'what is lucid dreaming': 115791, 'sleep study': 115791, 'brain activity': 115791, 'consciousness': 115791, 'PTSD': 115791, 'lucid dreaming experience': 115791, 'tested': 310096, 'testedcom': 224019, 'designercon 2017': 224019, 'preview': 688275, 'toys': 1379133, 'props': 224019, 'costumes': 224019, 'ironhead studio': 224019, 'marvel': 40668991, 'thor': 1230658, 'thor 3': 224019, 'thor ragnorok': 224019, 'thor ragnarok': 423374, 'hela': 224019, 'helmet': 224019, 'cosplay': 906338, 'headdress': 224019, 'costume': 799023, 'tom scott': 1168907, 'tomscott': 1168907, 'built for science': 336777, 'national advanced driving simulator': 144418, 'university of iowa': 144418, 'driving simulation': 144418, 'driver safety': 144418, 'driver distraction': 144418, 'refinery29': 533309, 'refinery 29': 533309, 'r29': 533309, 'r29 video': 533309, 'video': 27897638, 'refinery29 video': 533309, 'female': 533309, 'empowerment': 741536, 'house tour': 1318893, 'sweet digs': 188145, 'new york city': 6288215, 'apartment decor': 188145, 'interior design': 475256, 'home tour': 1059535, 'big apple': 188145, 'real estate': 188145, 'apartment': 188145, 'new apartment tour': 145921, 'bedroom tour': 1026696, 'new apartment': 480342, 'house tour 2017': 145921, 'living room': 248068, 'living room decor': 145921, 'walk through': 145921, 'nyc apartment': 188145, 'new house': 462689, 'kitchen tour': 188145, 'modern home': 188145, 'decorating on a budget': 188145, 'my new house': 145921, 'moving out': 438118, 'video blog': 174652, 'manhattan': 1070908, 'NY': 1172749, 'central park': 145921, 'using other peoples showers': 33980, 'gus': 1190993, 'gus shower': 33980, 'other people shower': 33980, 'ketchup shower': 33980, 'funny bathroom': 33980, 'gustoonz': 1190993, 'gus johnson': 1190993, 'gus other peoples showers': 33980, 'funny video': 40759360, 'trending': 2722080, 'youtube trending': 1305400, 'trending video': 991474, 'viral video': 3369879, 'advertiser friendly': 991474, 'family friendly': 15706485, 'clean': 16307795, 'shower joke': 33980, 'sketch comedy': 5256229, 'youtube haiku': 233499, 'shower meme': 33980, 'meme': 211104, 'new meme': 1190993, 'meme 2017': 1190993, 'meme 2018': 1190993, 'spaghetti burrito': 223077, 'diy burrito': 223077, 'spaghetti': 970619, 'burrito': 223077, 'burrito recipe': 223077, 'spaghetti deep fried': 223077, 'deep fried spaghetti': 223077, 'spgahetti sandwich': 223077, 'giant burrito': 223077, 'numberphile': 203346, 'prime numbers': 80705, 'proth prime': 80705, 'Smart mug': 120727, 'Heated thermos': 120727, 'tech': 5322308, 'gift idea': 744530, 'unboxed': 171429, 'Ember mug': 120727, 'ember': 120727, 'Ember review': 120727, 'Heated mug': 120727, 'teardown': 120727, 'Thermous': 120727, 'winter': 1286468, 'holiday': 6324904, 'christmas': 30508263, 'gift': 4151239, 'mug': 120727, 'auth-jvardon-auth': 27943, 'freaks and geeks': 50867, 'jason segel': 50867, 'judd apatow': 50867, 'drums': 50867, 'rush': 1422373, 'paul feig': 50867, 'drummer': 50867, 'nick andopolis': 50867, 'carmax': 98378, 'lamborghini miura': 98378, 'miura carmax': 98378, 'lamborghini carmax': 98378, 'took lamborghini to carmax': 98378, 'supercar carmax': 98378, 'selling my car at acrmax': 98378, 'lambo carmax': 98378, 'stradman': 98378, 'vehicle virgins': 98378, 'doug demuro': 803547, 'Amazon': 383649, 'Amazon Christmas': 26000, 'Amazon Xmas': 26000, 'Christmas': 3692651, 'Xmas': 26000, 'ad': 163904, 'christmas ad': 7250515, 'commercial': 214412, 'Holidays': 248091, 'advert': 26000, 'christmas advert': 26000, 'Amazon ad': 26000, 'UK': 296078, 'Amazon Christmas Advert': 26000, 'Christmas (Holiday)': 26000, 'Amazon Christmas advert 2017': 26000, 'Amazon xmas ad 2017': 26000, 'Amazon Holiday Advert': 26000, 'Delivery': 26000, 'Gift': 64219, 'Amazon Delivery': 26000, 'boxes': 26000, 'singing boxes': 26000, 'smile': 26000, 'give a littlle bit': 26000, 'Roger Hodgson - Give a Little Bit': 26000, 'Roger Hodgson': 26000, 'Eminem': 25194267, 'Walk': 19064018, 'On': 19121836, 'Water': 19288669, 'Aftermath/Shady/Interscope': 21127713, 'Rap': 27064903, 'detective': 222533, 'officer': 67661, '401': 67661, 'officer401': 67661, 'police': 1049011, 'cop': 67661, 'cops': 109132, 'law': 67661, 'enforcement': 67661, 'investigator': 67661, 'ford': 937990, 'fusion': 67661, 'crown vic': 67661, 'charger': 67661, 'dodge': 67661, 'patrol': 165703, 'cid': 67661, 'criminal': 67661, 'investigations': 67661, 'division': 319344, 'mre': 67661, 'survival food': 67661, 'trunk': 67661, '2011': 195182, '2012': 67661, '2013': 67661, '2014': 67661, '2015': 128782, '2016': 87629, 'fusion se': 67661, 'ford fusion': 67661, 'ford fusion se': 67661, 'vest': 67661, 'outter': 67661, 'carrier': 67661, 'bullet-proof': 67661, 'bullet-proof vest': 67661, 'level 2 armor': 67661, 'level 3 armor': 67661, 'Emirates': 141148, 'First Class': 141148, 'cute': 5963055, 'cats': 2543607, 'thai': 98966, 'eggs': 252612, 'screenjunkies': 5184992, 'screen junkies': 5184992, 'sj news': 288922, 'honest trailers': 5030079, 'honest trailer': 5030079, 'wonder woman': 3476353, 'batman': 2908805, 'the joker jared leto': 288922, 'man of steel': 288922, 'justice league': 2550456, 'justice league review': 952839, 'batman vs superman': 288922, 'bvs': 288922, 'dceu': 1780959, 'dc movie plot': 288922, 'justice league plot': 288922, 'wonder woman plot': 288922, 'batman plot': 288922, 'motherbox': 288922, 'justice league trailer': 428877, 'suicide squad': 288922, 'will smith': 1212790, 'deadshot': 288922, 'joker harley quinn': 288922, 'margot robbie': 1654921, 'suicide squad review': 288922, 'dc marvel': 288922, 'Hunter': 160068, 'Hayes': 32547, 'you should be loved': 32547, 'the shadowboxers': 13917, 'pictures': 32547, 'part one': 13917, 'official': 64627415, 'music video': 6141149, 'trilogy': 763662, 'hunter hayes': 32547, 'wanted': 754850, 'i want crazy': 13917, 'storm warning': 13917, 'niki and gabi': 1177520, 'nikiandgabibeauty': 605932, 'celebrities on thanksgiving': 605932, 'celebrities': 40874910, 'thanksgiving': 8193204, 'celebrity thanksgiving': 605932, 'thanksgiving 2017': 609278, 'celebrities in high school': 605932, 'celebrities at prom': 605932, 'celebrities have a sleepover': 605932, 'celebrity talent show': 605932, 'celebrities at the beach': 605932, 'taylor swift': 1620445, 'Nickelback': 57169, 'Feed The Machine': 57169, 'The Betrayal Act III': 57169, 'The Betrayal': 57169, 'Rock': 289996, 'Alternative': 4308658, 'Anthem Films': 57169, 'Kevin Slack': 57169, 'Chad Kroeger': 57169, 'Ryan Peake': 57169, 'Mike Kroeger': 57169, 'Daniel Adair': 57169, 'How You Remind Me': 57169, 'Rockstar': 57169, 'U2': 181020, 'The': 1877369, 'Blackout': 60506, 'Island': 8487787, 'Records': 15289744, 'bbc': 2648907, 'bbc news': 781322, 'news': 11565859, 'iran': 133411, 'iran news': 133411, 'iraq': 133411, 'iraq news': 40089, 'earthquake': 409834, 'breaking news': 2109019, 'Iraq-Iran earthquake': 34785, '[none]': 11528800, 'matthew santoro facts': 328330, 'matthew santoro': 328330, 'Ellevan': 328330, 'EllevanMusic': 328330, 'HumbleThePoet': 328330, 'Humble The Poet': 328330, 'FACTS': 328330, 'whats that facts': 328330, 'shawn johnson': 667977, 'andrew east': 667977, 'shawn east': 667977, 'shawn and andrew': 667977, 'olympian': 667977, 'nfl player': 667977, 'athletes': 1830820, 'vlog': 14443332, 'couples': 1718326, 'google irl': 667977, 'google feud': 667977, 'google markiplier': 667977, 'google myself': 667977, 'reactions': 5126155, 'reacts': 6690627, 'internet': 5089680, 'memes': 725940, 'birthday': 1738113, 'cringe': 667977, 'laughing': 837216, 'daily': 11282437, 'vlogger': 5842165, 'boy': 1046106, 'diy': 8577638, 'google': 1547261, 'myself': 667977, 'googled': 667977, 'googling': 667977, 'liza': 7181809, 'lizakoshy': 4246479, 'wednesdays': 4246479, 'liza koshy': 8546765, 'googling myself': 667977, 'liza koshy net worth': 667977, 'liza koshy google': 667977, 'liza facts': 667977, 'liza panties': 667977, 'net worth': 667977, 'youtuber net worth': 667977, 'youtuber facts': 667977, 'iphonex makeup': 1456472, 'iphonex test': 1456472, 'new iphone': 7211313, 'iphone vs makeup': 1456472, 'face id': 4878863, 'face id test': 1456472, 'iphone x challenge': 1456472, 'iphone hack': 1456472, 'iphone x makeup tutorial': 1456472, 'makup tutorial': 1456472, '2017 makeup': 1456472, 'promise phan': 1456472, 'twins face id': 1456472, 'twins iphonex': 1456472, 'iphonex face': 1456472, 'iphonex review': 1456472, 'people are awesome': 1710043, 'people are awesome 2017': 911688, 'youtube': 4955974, 'hd': 1109588, 'compilation': 2870732, 'amazing people': 911688, 'incredible': 2870732, 'gopro': 4095790, 'gopro hero': 911688, 'extreme sports': 1841241, 'adventure travel': 911688, 'people are awesome videos': 911688, 'PAA': 911688, 'the pet collective': 225129, 'pet collective TV': 225129, 'I love my pets': 225129, 'awesome pets': 225129, 'pets are awesome': 225129, 'Adventure Pup': 225129, 'Adventure Dogs': 225129, 'Good Doggo': 225129, 'Adventure Cats': 225129, 'Surfing': 225129, 'snowboarding': 225129, 'skiing': 225129, 'wakeboarding': 225129, 'NBA': 1143887, 'Basketball': 967007, 'Sports': 5994114, 'D3sports': 4569, 'NCAA Division III': 4569, 'D3sports.com': 4569, 'D3football.com': 4569, 'Division III football': 4569, '#d3fb': 4569, 'college football': 530445, 'Division III (Sports Association)': 4569, 'iPhone X': 4271091, 'animojis': 2045386, 'facial recognition': 3871091, 'ELDERS REACT TO iPHONE X Facial Recognition Animojis': 2045386, 'elders react': 6503564, 'react': 7180754, 'reaction': 10066077, 'thefinebros': 8383867, 'fine brothers': 8383867, 'fine brothers entertainment': 8383867, 'finebros': 3062573, 'fine bros': 4666123, 'FBE': 7520751, 'watch': 6503564, 'for the first time': 6812872, 'reviews': 6739379, 'responds': 6503564, 'respond': 6503564, 'youtubers react': 6503564, 'teens react': 6503564, 'kids react': 6503564, 'adults react': 6503564, 'parents react': 6503564, 'teenagers react': 2045386, 'iphone 8': 5748867, 'nails': 13549199, 'nail art': 13549199, 'nail tutorial': 13549199, 'beauty tutorial': 13694136, 'nail art tutorial': 13549199, 'diy nails': 13549199, 'easy nail art': 13549199, 'diy nail art': 13549199, 'cute nail art': 11164216, 'simply nailogical': 14925720, 'simplynailogical sister': 1842393, 'simplynailogical jen': 1842393, 'jenny': 1842393, "what's on my iphone": 1842393, 'phone games': 1842393, 'best fiends': 1842393, 'sister fun': 1842393, 'watermarble': 1842393, 'will it watermarble': 1842393, 'simplynailogical watermarble': 1842393, 'watermarble nails': 1842393, 'watermarble objects': 1842393, 'hydrodipping': 1842393, 'marble': 1842393, 'nail polish marble': 1842393, 'CBS Sunday Morning': 6473, 'CBS News': 261309, 'On The Road': 6473, 'lin manuel miranda': 6473, 'puerto rico': 6473, 'hamilton': 192180, 'hurricane maria': 6473, 'wwe': 8774696, 'world wrestling entertainment': 7645994, 'wrestling': 8774696, 'wrestler': 8241814, 'wrestle': 7645994, 'superstars': 7645994, 'कुश्ती': 7645994, 'पहलवान': 7645994, 'डब्लू डब्लू ई': 7645994, 'मैच': 7645994, 'सुपरस्टार': 7645994, 'व्यावसायिक कुश्ती': 7645994, 'مصارعه': 7645994, 'WWE Top 10': 2913708, 'Stone Cold Steve Austin': 1044813, 'Undertaker': 1044813, 'Kurt Angle': 1044813, 'Kofi Kingston': 2233139, 'Xavier Woods': 1044813, 'Big E': 1044813, 'Christian': 1044813, 'Dean Ambrose': 1725382, 'John Cena': 1640633, 'Boogeyman': 1044813, 'top 10': 4234625, 'wwe distraction': 1044813, 'wwe best distractions': 1044813, 'wwe clips': 1044813, 'wwe youtube': 1044813, 'best of wwe': 1044813, 'best wrestling distractions': 1044813, 'Jennifer Lopez ft. Wisin': 9548677, 'Jennifer Lopez ft. Wisin Music': 9548677, 'Jennifer Lopez ft. Wisin Official Video': 9548677, 'Jennifer Lopez ft. Wisin Video': 9548677, 'Jennifer Lopez ft. Wisin Video Oficial': 9548677, 'Jennifer Lopez ft. Wisin Nuevo Video': 9548677, 'Jennifer Lopez ft. Wisin New Video': 9548677, 'Amor': 9548677, 'Amor Official Video': 9548677, 'Official Video': 14068207, 'Amor Single': 9548677, 'Single': 9669950, 'Jennifer Lopez ft. Wisin New Single': 9548677, 'Jennifer Lopez ft. Wisin Single': 9548677, 'Jennifer Lopez ft. Wisin Amor': 9548677, 'Jennifer': 9548677, 'Lopez': 9548677, 'itsgrace': 887212, 'grace': 887212, 'helbig': 887212, 'gracehelbig': 887212, 'dailygrace': 887212, 'tutorial': 6217675, 'lifestyle': 4281073, 'Graham Norton': 1887274, 'Graham Norton Show Official': 1887274, 'Entertainment': 2075440, 'Chat Show': 1887274, 'Graham Norton full episodes': 1887274, 'the graham Norton show full eps': 1887274, 'graham Norton full eps': 1887274, 'watch graham Norton online': 1887274, 'jason momoa': 1887274, 'hugh grant': 1887274, 'sarah millican': 1887274, 'kelly clarkson': 1941276, 'graham norton red chair': 1887274, 'game of thrones': 1929652, 'javale mcgee': 162597, 'golden state warriors': 162597, 'kevin durant': 162597, 'oakland': 162597, 'bay area': 184034, 'san francisco': 947534, 'oracle arena': 162597, 'e40': 162597, 'e-40': 162597, 'hip hop': 2748885, 'rap': 8661317, 'beats': 162597, 'beats by dre': 162597, 'life goes on': 162597, 'arthur': 459903, 'renowitzky': 162597, 'klay thompson': 162597, 'minnesota timberwolves': 162597, 'nick young': 162597, 'swaggy p': 162597, 'KELLYANNE CONWAY ROY MOORE': 9132, 'KELLYANNE CONWAY ROY MOORE THIS WEEK ABC': 9132, 'KELLYANNE CONWAY ROY MOORE THIS WEEK': 9132, 'KELLYANNE CONWAY THIS WEEK ABC 11/12/17': 9132, 'KELLYANNE CONWAY THIS WEEK ABC': 9132, 'KELLYANNE CONWAY': 9132, 'ROY MOORE': 9132, 'THIS WEEK ABC': 9132, 'POLITICS': 9132, 'viralhog': 732965, 'Crash': 21336, 'Fail': 1319864, 'train': 1648093, 'swipe': 7265, 'crashes': 7265, 'destroy': 7265, 'destroys': 7265, 'vehicle': 7265, 'parked': 7265, 'car': 2286116, 'truck': 19477, 'Vietnam': 7265, 'accident': 29355, 'collision': 7265, "daddy's home": 291597, "daddy's home 2": 291597, 'mel gibson': 291597, 'john lithgow': 291597, 'family': 18899611, 'movie': 21460867, 'Awesometacular': 1319280, 'jeremy jahns': 1319280, 'Google': 1157004, 'Pixel 2': 290801, 'Pixel 2 XL': 290801, 'Pixel 2 Camera': 290801, 'Moment Lens': 290801, 'Pixel 2 Moment case': 290801, 'Moment Case Lens': 290801, 'Smartphone': 290801, 'Apple': 504964, 'google pixel': 290801, 'apple iphone x': 4219937, 'pixel 2 xl review': 290801, 'google pixel 2': 290801, 'camera': 4321495, 'google pixel xl': 290801, 'pixel 2 unboxing': 290801, 'pixel': 290801, 'android': 4092267, 'jonathan morrison': 290801, 'tld': 290801, '7.3 magnitude earthquake': 5304, 'earthquake iraq iran': 5304, 'iraq iran earthquake': 5304, 'iraq-iran earthquake': 5304, 'pakistan': 539094, 'iran earthquake': 5304, 'iran-iraq earthquake': 5304, 'iraq earthquake': 5304, 'magnitude': 255497, '7.3 magnitude': 5304, 'iraq-iran': 5304, 'iran iraq earthquake': 5304, 'iran iraq border earthquake': 5304, '400 dead iran iraq earthquake': 5304, 'Time': 85925, 'time magazine': 227530, 'magazine': 252420, 'time (magazine)': 7121, 'time.com': 85925, 'news today': 85925, 'interview': 25316617, 'health': 2667863, 'politics': 2496221, 'entertainment': 3668754, 'business': 146796, 'vets': 812516, 'veterans day': 812516, 'veterans': 812516, 'veteransday': 812516, 'veterans day 2017': 812516, 'BPG/RVG/RCA Records': 5203557, 'G-Eazy': 2642930, 'The Plan': 2642930, 'navy': 105058, 'united states navy': 105058, 'us navy': 105058, 'military': 523144, 'sailors': 105058, 'united states': 1730675, 'america': 2596054, 'usa': 1895840, 'usn': 105058, 'service members': 105058, 'IGN': 1303803, 'Batman': 809991, 'Elseworlds': 537715, 'DC Comics': 614648, 'DC Universe Movie': 537715, 'Warner Bros. Home Entertainment': 537715, 'batman gotham by gaslight': 537715, 'batman animated movie': 537715, 'dc animated movie': 537715, 'wearing online dollar store makeup for a week': 2744430, 'online dollar store makeup': 2744430, 'dollar store makeup': 2744430, 'daiso': 2744430, 'shopmissa makeup': 2744430, 'shopmissa haul': 2744430, 'dollar store makeup haul': 2744430, 'dollar store': 2968941, 'shopmissa': 2744430, 'foundation': 2952657, 'concealer': 2794968, 'eye primer': 2744430, 'eyebrow pencil': 2744430, 'eyeliner': 2744430, 'bronzer': 2744430, 'contour': 3291230, 'face powder': 2744430, 'lipstick': 6244484, '$1': 2744430, '$1 makeup': 2744430, 'safiya makeup': 2744430, 'safiya dollar store': 2744430, 'safiya nygaard': 8024228, 'safiya': 6158586, 'safiya and tyler': 4610072, 'getting my drivers license': 10586956, 'lele': 25880725, 'pons': 25880725, 'getting': 3358068, 'my': 3963790, 'drivers': 3358068, 'license': 3358068, 'terrible soccer players': 10586956, 'halloween costume contest': 8112673, 'first time flying': 3358068, 'how to not get bullied': 3358068, 'Craziest Wisdom Teeth Removal Story | Hannah Stocking': 3358068, '| Hannah Stocking': 3358068, 'Hannah Stocking': 3358068, 'Ride With Norman Reedus': 4732, 'behind the scenes': 2606850, 'INSIDER': 708385, 'pop culture': 69054, 'john lewis christmas': 7224515, 'john lewis': 7224515, 'mozthemonster': 7224515, 'christmas 2017': 7224515, 'christmas ad 2017': 7224515, 'john lewis christmas advert': 7224515, 'moz': 7224515, 'edsheeran': 47640195, 'ed sheeran': 49085780, 'acoustic': 48506136, 'cover': 51434547, 'remix': 51217842, 'official video': 52871744, 'session': 47778688, 'best productivity apps 2017': 52591, 'best iphone apps for creativity 2016': 52591, '10 Essential Productivity Apps for iPhone and Android thomas frank': 52591, '6 iPhone Apps You NEED in Your Life Right Now! | Best iOS Apps for Productivity (2017)': 52591, 'iphone x unboxing apple mkbhd ijustine jonathan morrison': 52591, 'apple iphone x review first impressions sara dietschy': 52591, 'sara dietschy tech': 52591, 'kingston bolt thunderbolt review': 52591, 'best apps for editing video on phone': 52591, 'best app for video editing on iphone': 52591, 'best photo editing app': 52591, 'megan batoon': 75822, 'cooking': 11193856, 'how': 1617746, 'to': 10283228, 'freestyle': 369580, 'recipe': 8313536, 'weight': 75822, 'loss': 318020, 'youtuber': 4705723, 'exercise': 1131631, 'health and fitness': 75822, 'reacting': 1640258, 'marathon': 75822, 'running': 163482, 'hard': 194983, 'half marathon': 75822, 'training': 1053107, 'los angeles': 2774548, 'Justice LEague': 43715, 'Movie': 2967728, 'Reactions': 137329, 'Reviews': 43715, 'Film': 3001992, 'Disney': 4924782, 'Netflix': 4912205, 'DC': 2083818, 'John Campea': 597739, 'Movie News': 214192, 'Show': 348265, 'Foster': 303956, 'People': 470451, 'Sit': 303956, 'Next': 303956, 'Me': 7608933, '(Vertical': 303956, 'Video)': 303956, 'Columbia': 13145349, 'Alternative/Indie': 303956, 'What What Happens live': 729815, 'reality': 1094770, 'fun': 6249359, 'celebrity': 39189767, 'Andy Cohen': 729815, 'talk': 8569471, 'show': 18717366, 'program': 1088589, 'Bravo': 958210, 'Watch What Happens Live': 599436, 'WWHL': 599436, 'bravo andy': 634903, 'Watch': 714015, 'What': 599436, 'Happens': 599436, 'Trump': 3927609, "Rosie O'Donnell": 238643, 'President of the United States': 238643, 'public distaste': 238643, "Rosie O'Donnel wwhl": 238643, "Rosie O'Donnel and Donald Trump": 238643, 'Donald Trump': 814903, 'President Trump': 403244, "Donald Trump and Rosie O'Donnel": 238643, "Rosie O'Donnell Watch What Happens Live": 238643, 'hillary clinton': 1066352, 'rosie wwhl': 238643, "rosie o'donnell andy cohen": 238643, 'plead the fifth': 274110, 'talk show': 25639938, 'Mayo Clinic': 237307, 'Health Care (Issue)': 237307, 'Healthcare Science (Field Of Study)': 237307, 'organ transplant': 237307, 'Essam and Dalal Obaid Center for Reconstructive Transplant Surgery': 237307, 'face transplant': 237307, 'plastic surgery': 2025427, 'reconstructive surgery': 237307, 'Jason Derulo': 1282413, 'French Montana': 3197601, 'Pop': 46645525, 'Other': 1448111, 'Tip Toe': 1282413, 'Official Music Video': 1489285, 'Jason Desrouleaux': 1282413, 'James Corden': 29880177, 'The Late Late Show': 29453021, 'Colbert': 29453021, 'late night show': 22174978, 'Stephen Colbert': 58824077, 'Comedy': 39142170, 'monologue': 30416883, 'impressions': 29453021, 'carpool': 22506620, 'karaoke': 23168941, 'CBS': 32576448, 'Late Late Show': 29453021, 'Corden': 29453021, 'joke': 29662862, 'jokes': 39808873, 'funny videos': 29561639, 'humor': 59776188, 'celeb': 29872757, 'hollywood': 40886890, 'famous': 29623169, 'Harvey Weinstein': 148226, 'Howard Stern': 2913347, 'Howard Stern Show': 148226, 'Hollywood': 1981084, 'casting couch': 148226, 'angie everhart': 148226, 'robin quivers': 148226, 'george takei': 148226, 'Star Terk': 148226, 'Star Trek': 189229, 'Gay': 148226, 'Homo': 148226, 'LBGT': 148226, 'ebay': 1484136, 'haul': 4102572, 'cheap': 8136797, 'extra': 484185, 'amber scholl': 1622743, 'online': 492385, 'shopping': 2467648, 'try on': 1523496, 'CLOTHES': 484185, 'shoes': 484185, 'Extreme Tan': 300617, 'Tanning': 300617, 'Tanning Bed': 300617, 'How to get best Tan': 300617, 'Black to White': 300617, 'White To Tan': 300617, 'How to apply fake tan': 300617, 'Tanning Routine': 300617, 'Best fake tan': 300617, 'How to tan faster in the sun': 300617, 'How to make tan last': 300617, 'tanned skin': 300617, 'tan makeup': 300617, 'cave': 92523, 'cheese': 1651443, 'goat': 92523, 'rind': 92523, "sheep's milk": 92523, 'crown finish caves': 92523, 'brooklyn': 194819, 'crown heights': 92523, 'aging cheese': 92523, 'affinage': 92523, 'artisanal food': 92523, 'benton brown': 92523, 'how to age cheese': 92523, 'cheese aging': 92523, 'cheese caves': 92523, 'how cheese is made': 92523, 'how to make cheese': 1205075, 'how cheese is aged': 92523, 'cheese recipe': 92523, 'cheese making': 92523, 'best cheese': 92523, 'cheeses': 1205075, 'how cheese aging works': 92523, 'cheese is aged': 92523, 'i got a guy': 92523, 'bon appetit': 428905, 'bon appétit': 669071, 'seattle': 7524, 'seahawks': 1278001, 'richard': 7524, 'sherman': 7524, 'nfl': 299861, 'thursday': 7524, 'night': 6451481, 'football': 12143140, 'injuries': 7524, 'gma': 213659, 'ice primer': 103927, 'beauty hack': 157429, 'weird beauty hack': 103927, 'ice under foundation': 103927, 'pore minimizer': 103927, 'BuzzFeed': 794552, 'buzzfeed celeb': 177707, 'Mark Ruffalo': 177707, 'Thor': 177707, 'Ragnarok': 177707, 'thirst': 177707, 'thirsty': 177707, 'tweet': 931034, 'surprise': 177707, 'flattered': 177707, 'embarrassing': 1139053, 'embarrassed': 177707, 'LOL': 177707, 'awkward': 1728252, 'daddy': 177707, 'Game Night': 1751064, '2018': 1976702, 'Jason Bateman': 1751064, 'Rachel McAdams': 1751064, 'Official': 9993862, 'Clip': 2377564, 'TV Spot': 1751064, 'International': 1796019, 'Teaser': 3635575, 'Little': 46746, 'Big': 108723, 'Town': 46746, 'with': 8260133, 'Jimmy': 1082550, 'Webb': 46746, 'Wichita': 46746, 'Lineman': 46746, '(Live': 46746, 'from': 46746, 'the': 13168747, 'CMA': 46746, 'Awards)': 46746, 'Country': 702160, 'Music': 1586500, 'Association': 46746, 'Classic': 46746, 'Alan Walker': 1285400, 'DJ Walkzz': 1285400, 'K-391': 1285400, 'House': 2703742, 'Techno': 2703742, 'Behind the senes': 746092, 'All Falls Down': 1285400, 'Noah Cyrus': 746092, 'World Of Walker': 746092, 'WoW': 746092, 'Alan': 746092, 'Walker': 746092, 'EDM': 2169372, 'Alone': 1285400, 'Faded': 1285400, 'Tired': 746092, 'The making of': 746092, 'unmasked': 746092, 'production': 764889, 'whats the trick': 746092, 'hello hello': 746092, 'Norway': 746092, 'Buzludzha': 746092, 'Bulgaria': 746092, 'Digital Farm Animals': 746092, 'electro': 746092, 'cryptogram': 746092, 'walkers join': 746092, 'drone': 899002, 'ill be fine': 746092, 'kristian berg': 746092, 'juliander': 746092, 'allan': 746092, 'valker': 746092, 'croatia': 746092, 'Robin Bøe': 746092, 'superfruit': 1063977, 'super': 9781668, 'fruit': 2139436, 'scott': 1063977, 'hoying': 1063977, 'mitch': 1079359, 'grassi': 1063977, 'fcute': 1063977, 'weekly': 1063977, 'obsessions': 1105448, 'everyone': 1063977, 'heres': 1063977, 'good': 1129677, 'song': 5582285, 'dont': 1063977, 'listen': 1364500, 'anything': 1063977, 'else': 1063977, 'today': 1075722, 'future': 2657665, 'friends': 6595285, 'Chess': 289986, 'Saint Louis': 289986, 'Club': 67429, 'American Broadcasting Company': 2237404, 'ABC': 2274423, 'ABC Network': 2237404, 'Television': 12319408, 'TV': 2554748, 'what to watch': 2237404, 'Television Program': 2324509, "New Year's Day": 2237404, 'Performance': 2237404, 'Giraffe': 45455, 'Zoo': 45455, 'New York': 854564, 'giraffe cam': 45455, 'Animal': 45455, 'Adventure': 45455, 'Animal Adventure': 45455, 'Harpursville': 45455, 'Park': 74709, 'how to dry a shirt in 30 seconds': 2063667, 'how to dry a shirt': 2063667, 'life hack': 2063667, 'how to quickly dry clothing': 2063667, 'how to dry a shirt fast': 2063667, 'quickly dry clothes': 2063667, 'quickly dry clothing': 2063667, 'dry a shirt fast': 2063667, 'howtobasic': 2063667, 'baby driver': 2736733, 'edgar wright': 2736733, 'everything wrong with': 10098946, 'eww': 10098946, 'cinemasins': 10098946, 'cinema sins': 10098946, 'mistakes': 10098946, 'Kimbra': 165698, 'Top of the World': 165698, 'Primal Heart': 165698, 'Gotye': 165698, 'Somebody I Used To Know': 165698, 'Vows': 165698, 'Settle Down': 165698, 'Cameo Lover': 165698, 'capitalfmofficial': 1429810, 'capital': 1474765, 'capital fm': 1429810, 'capital radio': 1429810, 'radio': 3994643, 'capital tv': 1429810, 'camila cabello': 23965669, 'camila': 21386160, 'camila fifth harmony': 836544, '5th harmony': 956046, 'harmonizers': 19798454, 'Ally Brooke': 836544, 'Ally Brooke 5th harmony': 836544, 'Ally fifth harmony': 836544, 'Ally 5th harmony': 836544, 'Normani Kordei': 836544, 'Normani 5th harmony': 836544, 'Lauren Jauregui': 836544, 'Lauren fifth harmony': 836544, 'Dinah Jane': 836544, 'Dinah 5th harmony': 836544, 'shawn mendes': 11939869, 'justin bieber': 2108264, 'despacito': 836544, 'ed sheeran live': 836544, 'iisuperwomanii': 10727022, 'iisuperwomenii': 3371669, 'superwoman': 10727022, 'superwomen': 3371669, 'brown': 5537813, 'indian': 4247043, 'desi': 3371669, 'punjabi': 3371669, 'hindi': 3371669, 'when someone has a crush on you': 3371669, 'crush on you': 3371669, 'when a crush': 3371669, 'crush you': 3371669, 'on you': 3371669, 'someone on': 3371669, 'when someone': 3371669, 'llsuperwomanll': 4070095, 'lilly singh': 9067250, 'lily singh': 5667395, 'lily sing': 6217726, 'lilly sing': 3371669, 'IIsuperwomanII': 3371669, 'someone has': 3371669, 'you crush': 3371669, 'when you': 3371669, 'lilly': 10727022, 'singh': 10727022, 'when someone likes you': 3371669, 'when someone has a crush': 3371669, 'that feeling when': 3371669, 'someone likes you': 3371669, 'someone has a crush on you': 3371669, 'lilly singh crush': 3371669, 'Hopeless Records': 195685, 'neck deep': 295304, 'parachute': 746527, 'the peace and the panic': 195685, 'in bloom': 195685, 'tpatp': 195685, 'neckdeepuk': 195685, 'ben barlow': 195685, 'dani washington': 195685, 'fil thorpe evans': 195685, 'sam bowden': 195685, 'matt west': 195685, 'pop punk': 195685, 'Japan': 1323548, 'Japanese': 2569461, 'yt:cc=on': 2579481, "idiot's": 1098897, 'guide': 1855628, 'squat': 1098897, 'toilets': 1098897, 'washiki': 1098897, 'toilet': 1098897, 'japanese toilets': 1098897, 'how to': 21448493, 'use': 1098897, 'bathroom': 1937448, 'japanese bathroom': 1098897, "idiot's guide": 1098897, '和式': 1098897, '使い方': 1098897, 'rachel': 1951614, 'jun': 1098897, 'rachel & jun': 1098897, 'rachel and jun': 1098897, '日本': 2569461, 'ガイド': 1098897, '面白': 1098897, '海外': 1592176, 'おもしろ': 1098897, 'アメリカ': 1098897, '英会話': 1592176, '英語': 2569461, '外国': 1098897, 'レイチェル': 1592176, 'ジュン': 2569461, 'トイレ': 1098897, 'ネタ': 1098897, '様式': 1098897, '便所': 1098897, 'bbc music': 893462, 'Sam Smith': 1119724, 'Wedding': 1088848, 'Sam Smith At The BBC': 893462, 'At the BBC': 893462, 'Late Night': 882182, 'Seth Meyers': 6986402, 'Mark Wahlberg': 277888, 'Kids': 225286, 'Celeb Connections': 225286, 'NBC': 22642704, 'NBC TV': 20260014, 'stand-up': 12830627, 'snl seth meyers': 10366652, 'promo': 12683913, 'seth': 11209166, 'meyers': 10366652, 'weekend update': 10366652, 'news satire': 10366652, 'Boogie Nights': 225286, 'Ted': 225286, 'Lone Survivor': 225286, 'Transformers': 225286, "Daddy's Home": 277888, 'The Fighter': 225286, 'The Departed': 225286, 'H&M Holiday': 264793, 'H&M Holiday Collection': 264793, 'H&M': 264793, 'Fashion': 3913864, 'Nicki Minaj': 25597959, 'John Turturro': 264793, 'Jesse Williams': 6561186, 'Johan Renck': 264793, 'young thug': 15914831, 'havana': 20843219, 'fifth harmony': 20127982, '5h': 18961910, 'crying in the club': 18961910, 'omg': 19284728, 'i have questions': 18961910, 'know no better': 18961910, 'lauren jauregui': 5725530, 'dinah jane': 5596239, 'normandi kordei': 5476737, 'ally brooke': 5476737, 'LeLe Pons': 11103325, 'LuJuan James': 11103325, 'influencers': 11103325, 'instagram influencer': 11103325, 'lele instagram': 11103325, 'latina influencer': 11103325, 'lelepons dance videos': 11103325, 'lele pons dance battle': 11103325, 'Camila Cabello feat. Young Thug': 5476737, 'Havana': 11103325, 'Syco Music/Epic': 18961910, 'lizza': 3578502, 'lizzza': 6513832, 'lizzzavine': 3578502, 'lizzzak': 3578502, 'lizzzako': 3578502, 'koshy': 3578502, 'top picks topics': 3578502, 'conversations': 3578502, 'boob talk': 3578502, 'period talk': 3578502, 'girl talk': 3640563, 'talking to you': 3578502, 'liza q and a': 3578502, 'liza topics': 3578502, 'liza koshy too': 3578502, 'liza koshy characters': 3578502, 'tea with liza': 3578502, 'boobs': 3578502, 'boob size': 3578502, 'breast milk': 3578502, 'having kids': 3578502, 'when im having kids': 3578502, 'liza pregnant': 3578502, 'liza david pregnant': 3578502, 'liza david kids': 3578502, 'naming kids': 3578502, 'kids names': 3578502, 'series': 6070040, 'owen wilson': 704417, 'owen': 304926, 'wilson': 2525546, 'julia roberts': 308471, 'tandem biking': 304926, 'wonder': 322412, 'wonder the movie': 304926, 'Ellen': 9220805, 'degeneres': 8811391, 'ellen degeneres': 8811391, 'the ellen show': 8811391, 'ellen fans': 8811391, 'ellen tickets': 8811391, 'ellentube': 8811391, 'ellen audience': 8811391, 'jenn im': 804420, 'jenn im vlog': 580548, 'imjennim': 804420, 'october vlog': 247319, 'monthly vlog': 580548, 'ben jolliffe': 247319, 'birthday vlog': 247319, 'surprise vlog': 247319, 'Michael Shannon': 1688275, 'Richard Jenkins': 645817, 'Doug Jones': 1149488, 'sally hawkins': 645817, 'guillermo del toro': 645817, 'the shape of water': 645817, 'shape of water': 645817, 'fox searchlight': 645817, 'fox movie': 645817, 'fox movies': 645817, 'new trailer': 5550550, 'octavia spencer': 645817, 'hidden figures': 645817, 'Shape of water': 645817, 'Luke': 240870, 'Bryan': 232174, 'Holy': 268253, 'Night': 268253, 'Capitol': 2433566, 'Nashville': 422805, 'Holiday': 1550687, 'Niall Horan': 538838, 'Niall': 419341, 'Horan': 419341, 'Too Much To Ask': 419341, 'Too Much Too Ask': 419341, 'To Much To Ask': 419341, 'To Much Too Ask': 419341, 'TMTA': 419341, 'Too Much To Ask Acoustic': 419341, 'Too Much Too Ask Acoustic': 419341, 'To Much To Ask Acoustic': 419341, 'To Much Too Ask Acoustic': 419341, 'TMTA Acoustic': 419341, 'Acoustic': 582434, 'Acoustic Video': 419341, 'Empire': 350584, 'Of': 187382, 'Sun': 138988, 'Way': 811998, 'To': 2177567, 'Go': 1147572, 'EMI': 138988, 'eots': 138988, 'waay': 138988, 'too': 217792, 'goo': 138988, 'emipre of the sun': 138988, 'son': 138988, 'electronic': 1868183, 'edm': 2260029, 'australian': 167527, 'adm': 138988, 'aussie music. triplej': 138988, 'like a version': 138988, 'two leaves': 138988, 'flume': 138988, 'alison wonderland': 138988, 'cut copy': 138988, 'pnau': 138988, 'mgmt': 138988, 'hot chip': 138988, 'the presets': 138988, 'phoenix': 138988, 'hermitude': 138988, 'Nobumichi Asai': 138988, 'Ryo Kitabatake': 138988, 'NF': 1774018, 'Let You Down': 1774018, "I'm sorry that I let you down": 1774018, 'nfrealmusic': 1774018, 'perception': 1774018, 'nf official': 1774018, 'hip-hop': 2588418, 'singer': 6692266, 'Google Pixelbook': 1575525, 'Chromebook': 1575525, 'Pixel': 1575525, '$1000': 1575525, '1000 dollar': 1575525, '1000': 5500096, 'expensive chromebook': 1575525, 'pixelbook review': 1575525, 'pixelbook pen': 1575525, 'chromebook pixel': 1575525, 'chromebook': 1575525, 'best chromebook': 1575525, 'MKBHD': 10066975, 'Bright': 1379580, 'Bright: The Album': 1743704, 'Bright The Album': 1743704, 'Bastille': 626688, 'World Gone Mad': 626688, 'Atlantic Records': 1351872, 'orc': 626688, '11-8-17': 219030, 'danny sapko': 493662, 'bass': 550258, 'funk bass': 493662, 'bass solo': 493662, 'free mp3 download': 493662, 'best bass': 493662, 'davie504': 493662, 'bass boosted songs': 493662, 'MEME': 493662, 'FUNNY VIDEO': 493662, 'VIRAL VIDEO': 493662, 'COWS': 493662, 'FUNK BASS': 493662, 'funk bass solo': 493662, 'anna wintour': 1017803, 'meryl streep': 1017803, 'meryl streep interview': 1017803, 'meryl streep harvey weinstein': 1017803, 'harvey weinstein': 1323454, 'meryl streep interview vogue': 1017803, 'meryl streep vogue': 1017803, 'anna wintour interview': 1017803, 'anna wintour meryl streep': 1017803, 'miranda priestly': 1017803, 'meryl streep miranda priestly': 1017803, 'anna': 2393038, 'devil wears prada': 1017803, 'vogue': 4903079, 'the post': 1017803, 'the post meryl streep': 1017803, 'katharine graham': 1017803, 'katharine graham meryl streep': 1017803, 'the devil wears prada': 1017803, 'vogue.com': 4903079, 'dc comics': 1151986, 'michelle khare': 343535, 'buzzfeed michelle': 343535, 'michelle': 139955, 'khare': 139955, 'wonderwoman': 153896, 'I Trained Like Batman For A Month': 139955, 'I Trained Like Batman': 139955, 'extreme': 866649, 'weightloss': 139955, 'release': 139955, 'filmselect': 139955, 'the flash': 287957, 'aquaman': 713903, 'gal godot': 139955, 'ben affleck': 969087, 'batman v superman': 139955, 'superhero': 38252595, 'mk ultra': 139955, 'fitness': 448116, 'trends': 1888615, 'google trends': 313926, 'film': 5243009, 'justice league movie': 1083050, 'spoiler': 534664, 'leaked': 139955, 'tay zonday': 139955, 'fight': 156957, 'worldstar': 146290, 'stunt': 1186691, 'Full Frontal with Samantha Bee': 1358943, 'Full Frontal': 1358943, 'Samantha Bee': 1358943, 'Sam Bee': 1358943, 'TBS': 2190424, 'Russia': 1650740, 'nicole guerriero': 294387, 'arriba liqud lipstick': 294387, 'colourpop cosmetics': 294387, 'classic glam': 294387, 'makeup tutorial': 6190753, 'holiday glam': 294387, 'red lip makeup tutorial': 294387, 'how to wear a red lip': 294387, 'beauty vlogger': 294387, 'beauty guru': 326152, 'iluvsarahii': 294387, 'long lasting red lip': 294387, 'bulletproof makeup': 294387, 'long lasting makeup': 294387, 'The Late Show': 7278043, 'Late Show': 7278043, 'skits': 7386661, 'bit': 7278043, 'letterman': 7470652, 'david letterman': 7470652, 'jimmy': 11845520, 'jimmy kimmel': 7211924, 'jimmy kimmel live': 6451583, 'comedic': 21738882, 'clip': 26657529, 'mean tweets': 7211924, 'country music': 1329783, 'cma awards': 1315873, 'Zac Brown Band': 1315873, 'Cassadee Pope': 1315873, 'Blake Shelton': 1656221, 'Luke Combs': 1336829, 'Randy Houser': 1315873, 'Old Dominion': 1315873, 'Trace Adkins': 1315873, 'Darius Rucker': 1315873, 'Dan + Shay': 1315873, 'Jana Kramer': 1315873, 'Chris Young': 1315873, 'Florida Georgia Line': 1692429, 'Lady Antebellum': 1315873, 'Chris Stapleton': 1315873, 'Jake Owen': 1493221, 'Little Big Town': 1315873, 'justin moore': 1315873, 'twitter': 6451536, 'SuperCarlinBrothers': 188003, 'disney': 824287, 'fox': 1142586, 'disney princess': 188003, 'anastasia': 188003, 'is anastasia a disney princess': 188003, 'is anastasia disney': 188003, 'is disney buying fox': 188003, 'super carlin brothers': 188003, 'ben carlin': 188003, 'j carlin': 188003, 'jonathan carlin': 188003, 'moana': 188003, 'frozen': 342874, 'snubbed disney princesses': 188003, 'anna and elsa': 188003, 'could anastasia become a disney princess': 188003, 'don bluth': 188003, 'disney history': 188003, 'netflix': 2638018, 'hercules': 515939, 'disney renaissance': 188003, 'meg ryan': 188003, 'the little mermaid': 188003, 'anastasia december': 188003, 'once upon december anastasia': 188003, 'once upon a december': 188003, 'anastasia music': 188003, 'Sia Snowman Holiday': 1624771, 'Business Insider': 231575, 'Tesla': 2791567, 'Elon Musk': 2791567, 'Business': 125970, 'Sara Silverstein': 125970, 'Model 3': 125970, 'Battery': 125970, 'Gigafactory': 125970, '2CELLOS': 205869, 'Luka Sulic': 205869, 'Stjepan Hauser': 205869, 'cello': 286680, 'cellist': 205869, 'cellists': 205869, 'Glee': 1308472, 'The Piano Guys': 356810, 'crossover': 213985, '2 Cellos': 205869, 'two cellos': 205869, 'Hauser': 205869, 'Ennio Morricone': 205869, 'Cinema Paradiso': 205869, 'soundtrack': 205869, 'classical': 205869, 'passion': 205869, 'Oliver Dragojevic': 205869, 'LSO': 205869, 'London Symphony Orchestra': 205869, 'new album': 278190, 'score': 205869, 'album': 387375, 'indie': 290047, 'underground': 897044, 'new': 6995315, 'latest': 229048, 'full song': 220848, 'track': 759227, 'concert': 543147, 'performance': 1478482, 'update': 1088436, 'the needle drop': 220848, 'anthony fantano': 220848, 'discussion': 220848, 'music nerd': 220848, 'pop': 5819560, 'soul': 1397948, 'sam smith': 98422, 'the thrill of it all': 98422, 'in the lonely hour': 98422, 'too good at goodbyes': 98422, 'say it first': 98422, 'pray': 98422, 'london': 732978, 'cartoon': 6272255, 'simons cat': 955908, "simon's cat": 955908, 'simonscat': 1073319, 'simon tofield': 955908, 'simon the cat': 955908, 'funny cats': 1073319, 'cute cats': 955908, 'cat fails': 1088419, 'animated animals': 955908, 'animated cats': 955908, 'tofield': 955908, "simon's katze": 1073319, 'simon': 1180559, 'cat': 2749245, 'black and white': 955908, 'kitty': 605055, 'black and white cat': 1073319, 'Кот Саймона': 1073319, 'cat lovers': 1073319, 'animal (film character)': 955908, 'funny cat': 955908, 'kitten': 1411192, 'kittens': 1319742, 'pets': 3748559, 'simons cats': 955908, 'Cat': 1267807, 'Simon': 955908, 'Tofield': 955908, 'cartoons': 955908, 'Toons': 955908, 'Animated': 961166, 'Animation': 12790860, 'Kitten': 1073319, 'Funny': 11039159, 'Humour': 1073319, 'videos': 778324, 'Guide to Birthdays': 426078, "Birthdays Simon's Cat": 426078, 'Cat Birthday': 426078, 'andy': 297947, 'grammer': 33315, 'parts': 33315, 'smoke': 33315, 'clears': 33315, 'songwriter': 1708072, 'mu': 33315, 'Hip Hop': 2792916, "Remy Ma feat. Lil' Kim": 2035881, 'Wake Me Up': 2035881, 'nintendo': 3333231, 'play nintendo': 1736094, 'gameplay': 3078896, 'video game': 2522809, 'kids': 5122646, 'adventure': 1911946, 'rpg': 1736094, 'nintendo switch': 1736094, 'pro controller': 1627066, 'l.a. noire': 154872, 'la noire': 154872, 'noire': 154872, 'crime': 528392, 'cole phelps': 154872, 'lapd': 154872, 'clues': 154872, 'detective game': 154872, 'interrogate': 154872, 'thriller': 190339, 'l.a. noire for nintendo switch': 154872, 'real crimes': 154872, 'abilities': 154872, 'skills': 1779350, 'unlockables': 154872, 'Rockstar Games': 154872, 'Dina': 72626, 'Tokio': 72626, 'Dinatokio': 72626, 'Torkia': 72626, 'Hijab': 72626, 'Hijabi': 72626, 'outfit diary': 72626, 'diary': 72626, 'outfits i wore this week': 72626, 'lookbook': 2316366, 'modest': 72626, 'dressing': 72626, 'muslim': 72626, 'islam': 72626, 'outfits': 658803, 'fashion': 7020734, 'fashionista': 72626, 'blogging': 72626, 'blogger': 2643528, 'instagram': 1620853, 'logan paul': 7970218, 'zoella': 2915323, 'jake paul': 2038005, 'creative': 111722, 'looks': 125213, 'ideas': 626751, 'autumn': 2021058, 'spring': 265566, 'summer': 438149, 'fashion week': 72626, 'buzzfeed': 5858484, 'buzzfeed boldly': 1984820, 'what to wear': 72626, 'outfits of the week': 72626, 'outfit': 606651, 'outfit of the day': 72626, 'how to style': 72626, 'ootd': 104391, 'clothing': 1342873, 'outfit ideas': 72626, 'makeup': 10030261, 'style': 7468406, 'fall looks': 72626, 'how to wear': 72626, 'ootw': 72626, 'Jungle': 264511, 'Mr.305/Polo Grounds Music/RCA Records': 264511, 'Pitbull & Stereotypes feat. E-40 & Abraham Mateo': 264511, 'cbs': 807134, 'late show': 192609, 'dave letterman': 192609, 'letterman clips': 192609, 'celebrity guests': 431848, 'celebrity interviews': 496082, 'late night talk show': 192609, 'paul shaffer': 192609, 'Biff': 192609, 'late': 5586546, 'david': 1129821, '“top 10”': 192609, 'top ten': 192609, 'list': 1520839, 'paul': 8077815, 'shaffer': 192609, 'tricks': 9057636, 'Elbow': 154494, 'Golden': 154494, 'Slumbers': 154494, 'Polydor': 371291, 'madonna': 77371, 'skincare': 477243, 'beauty': 7257311, 'face masks': 53321, 'masks': 53321, 'chrome clay mask': 53321, 'clay masks': 53321, 'clay mask': 53321, 'eye mask': 198258, 'face wash': 53321, 'routine': 1628775, 'express yourself': 53321, 'serum': 53321, 'cash': 498377, "Wayne's": 95085, 'World': 828328, 'wayne': 95085, 'waynes': 95085, 'fender': 95085, 'strat': 95085, 'stratocaster': 95085, 'cassandra': 95085, 'Sigrid': 198717, 'Strangers': 198717, 'Chess Club/RCA Victor': 764716, 'MØ': 764716, 'When I Was Young': 764716, 'the script': 287070, 'the script new single': 135337, 'the script arms open': 287070, 'the script arms open acoustic': 176364, 'the script arms open acoustic version': 135337, 'rain': 135337, 'hall of fame': 135337, 'breakeven': 135337, 'superheroes': 135337, 'rain lyrics': 135337, "the man who can't be moved": 135337, 'if you could see me now': 135337, 'Arms Open (Acoustic)': 135337, 'The Script': 246043, 'brothers green eats': 77630, 'budget cooking': 77630, 'cooking for the price of coffee': 77630, 'banana pancakes': 77630, 'falafel': 77630, 'cabbage rolls': 77630, 'steamed cabbage rolls': 77630, 'homemade almond milk': 77630, 'oat flour': 77630, 'simple cooking': 77630, 'simple recipes': 77630, 'budget cooking recipes': 77630, 'budget recipes': 77630, 'apple compote': 77630, 'apple jam': 77630, 'pear compote': 77630, 'Baran Bo Odar': 378750, 'Jantje Friese': 378750, 'DARK': 378750, 'darkmain': 378750, 'Netflix Original Series': 3458341, 'Netflix Series': 2067028, 'movies streaming': 378750, 'movies online': 1436302, 'television online': 1827941, 'documentary': 2756628, 'drama': 4239341, '08282016NtflxUKIE': 378750, 'watch movies': 1827941, 'featured': 378750, 'Dark': 378750, 'Louis Hofmann': 378750, 'Oliver Masucci': 378750, 'Netflix Dark': 378750, 'Netflix Drama': 378750, 'Crime Series': 378750, 'PLvahqwMqN4M35d1XdbUEWZT_r36Z6tIz3': 378750, 'PLvahqwMqN4M2N01FfQy2wXkyVyucAL86b': 811863, 'PLvahqwMqN4M0MGkARAHH7sCVVEepIBVYe': 811863, 'tokyo': 410019, 'japan': 963800, 'skytree': 151709, 'metropolitan': 151709, 'tower': 151709, 'japanese': 3328452, 'observation': 151709, 'odaiba': 151709, 'paramotor': 175852, 'tucker': 175852, 'gott': 175852, 'tucker gott': 175852, 'paramotor crash': 175852, 'paramotor accident': 175852, 'paramotor training': 175852, 'paravlog': 175852, 'vlogging': 1644488, 'motovlog': 175852, 'hero': 175852, 'flying': 885934, 'paragliding': 175852, 'adrenaline': 175852, 'flight': 729088, 'fly': 885934, 'pilot': 175852, 'av geek': 175852, 'action sports': 929553, 'flying car': 175852, 'take off': 175852, 'landing': 747440, 'aviator ppg': 175852, 'team fly halo': 175852, 'scout': 175852, 'flying to mcdonalds': 175852, '15000': 175852, 'improvise classical music': 5149, 'improvisation': 113767, 'classical music improv': 5149, 'debussy improv': 5149, 'whole tone scale': 5149, 'bach improv': 5149, 'chopin': 5149, 'chopin improv': 5149, 'classical music': 5149, 'piano improv': 5149, 'nahre sol': 5149, 'nahre sol music': 5149, 'classical composers imitation': 5149, 'classical music funny': 5149, 'music funny': 5149, 'music satire': 5149, 'music imitation': 5149, 'piano improviser': 5149, 'Thirty': 1905487, 'Seconds': 1905487, 'Mars': 2206752, 'Mars/Interscope': 1905487, 'Thirty Seconds To Mars': 1905487, 'Thirty Seconds to Mars Ellen': 1905487, '30 seconds to mars ellen': 1905487, 'mars ellen': 1905487, 'ellen': 1935414, 'Walk On Water': 1905487, 'Thirty Seconds to Mars': 1905487, '30 seconds to mars walk on water': 1905487, 'thirty seconds to mars walk on water': 1905487, 'mars walk on water': 1905487, 'walk on water vid': 1905487, '30 sec to mars': 1905487, '30 secs to mars walk on water': 1905487, 'walk on water video': 1905487, 'vid': 1905487, 'Harry Styles': 10289104, 'Kiwi': 10289104, 'swiftie': 476389, 'reputation': 3443116, 'ready for it': 753510, 'gorgeous': 1358659, 'are you ready for it': 476389, 'speak now': 476389, 'sparks fly': 476389, 'call it what you want': 598815, 'look what you made me do': 753510, 'boyfriend': 3421317, 'relationship': 1557297, 'new taylor swift': 476389, 'joe alwyn': 476389, 'tom hiddleston': 476389, 'john mayer': 476389, 'our song': 476389, 'aclu': 476389, 'twsift': 476389, 'wildest dreams': 476389, 'break up': 476389, 'Keith Urban': 754558, 'Female': 810025, 'New Music': 754558, 'CMA Awards': 754558, 'Maroon 5 SZA What Lovers Do Red Pill Blues Ellen show Ellen DeGeneres': 762616, 'X Factor': 199429, 'X Factor Italia': 199429, 'X Factor Italy': 199429, 'XFactor': 199429, 'Talent Show': 199429, 'Talent (Tv Genre)': 199429, 'Talent Show (Tv Genre)': 199429, 'Talent': 199429, 'Sky Uno': 199429, 'Sky Uno HD': 199429, 'SkyUno': 199429, 'Tv': 199429, 'Tv Entertainment': 199429, 'Music (Tv Genre)': 199429, 'Entertainment (Tv genre)': 199429, 'Musica': 199429, 'xf11': 199429, '#xf11': 199429, 'mara maionchi': 199429, 'manuel agnelli': 199429, 'fedez': 199429, 'levante': 199429, 'alessandro cattelan': 199429, 'harry styles': 199429, 'sign of the times': 199429, 'ospite terzo live': 199429, 'ospite x factor': 199429, "i picked my girlfriend's outfit blindfolded": 691229, 'blindfolded outfit shopping': 691229, 'blindfolded outfit challenge': 691229, 'blindfolded outfit': 691229, 'picking my girlfriends outfit': 691229, 'i picked my girlfriends outfit': 691229, 'boyfriend picks my outfit': 691229, 'boyfriend and girlfriend': 691229, 'couples shopping': 691229, 'tyler and safiya': 691229, 'tyler williams': 691229, 'thrift store outfit': 691229, 'thrift store': 691229, 'thrifting': 691229, 'googlevideo': 6412, 'chris': 204105, 'jack': 196854, 'smith': 1210074, 'de sena': 108618, 'chris & jack': 108618, 'heist': 108618, 'robbery': 373520, 'funny videos 2017': 108618, 'james bond': 108618, 'jason bourne': 108618, 'mission impossible': 108618, 'the italian job': 108618, 'oceans eleven': 108618, 'oceans 11': 108618, "ocean's 11": 108618, 'George Clooney': 108618, 'brad pitt': 108618, 'Matt Damon': 108618, 'stake out': 108618, 'investigation': 614504, 'twizzlers': 108618, 'ride along': 108618, 'montage': 108618, 'gender roles': 108618, 'feminism': 375301, 'trope': 108618, 'driving': 1572297, 'coffee': 2055536, 'jordan riggs': 108618, 'the school of life': 324943, 'life': 4861498, 'relationships': 1165631, 'mood': 79306, 'alain de botton': 324943, 'sermon': 79306, 'philosophy': 324943, 'lecture': 83315, 'wisdom': 324943, 'London': 1815772, 'secular': 145089, 'self': 79306, 'improvement': 282162, 'curriculum': 145089, 'big questions': 145089, 'wellness': 384866, 'mindfullness': 324943, 'psychology': 601387, 'language': 109575, 'shipping': 573994, 'diminutives': 79306, 'origins': 79306, 'etymology': 79306, 'origin': 79306, 'understanding': 79306, 'learn languages': 79306, 'teaching': 316962, 'PL-SELF': 145089, 'Diminateurs': 79306, '爱称': 79306, 'Diminutivos': 79306, 'Diminutive': 79306, 'Sprache verstehen': 79306, 'lenguaje de comprensión': 79306, 'समझ भाषा': 79306, '理解语言': 79306, 'entendendo linguagem': 79306, 'comprendre la langue': 79306, 'vocabulary': 79306, 'school of life': 259160, 'Kardashians': 2121708, 'Real Time': 1884601, 'Kim Kardashian': 2358849, 'Kourtney Kardashian': 2127889, 'Scott Disick': 1941711, 'Khloe Kardashian': 1399650, 'New Season': 2127889, 'E! Entertainment Schedule': 1890782, 'Celebrity': 5690621, 'Celeb Gossip': 2127889, 'Celeb News': 2136337, 'E! News': 2143472, 'E! News Now': 2136337, 'Chelsea Handler': 2392864, 'The Soup': 2127889, 'Celebrity News': 2135024, 'Celebrity Pictures': 2127889, 'Gossip': 2130156, 'Giuliana Rancic': 2127889, 'Chelsea Lately': 2392864, 'Comedians': 2127889, 'Kanye West': 1969357, 'Keeping Up with the Kardashians': 1636757, 'Kardashian': 2041288, 'KUWTK': 1941711, 'Kendall Jenner': 1941711, 'Kylie Jenner': 2293748, 'Whitney Port': 55398, 'I Love My Baby But': 55398, 'Grief': 55398, 'Grieving': 55398, 'Death of a Parent': 55398, 'Parenting': 93103, '90s commercials': 773, 'Huffy': 773, 'the breakfast club': 2550747, 'power1051': 2550747, 'celebrity news': 2812543, 'angela yee': 2550747, 'charlamagne tha god': 2550747, 'dj envy': 2550747, 'enter': 99619, 'shikari': 99619, 'Enter Shikari': 99619, 'The Sights': 99619, 'The Spark': 99619, 'Ally Pally': 99619, 'Radio 1': 1371710, 'Lower Than Atlantis': 99619, 'Twenty one pilots': 99619, 'Pvris': 99619, 'royal blood': 99619, 'Pierce the veil': 99619, 'don broco': 99619, 'sleeping with sirens': 99619, "sorry you're not a winner": 99619, 'Caterham': 4850, 'Chris Hoy': 4850, 'Caterham Seven': 4850, 'Avon Tyres': 4850, 'Caterham 7': 4850, 'Seven 620R': 4850, 'Caterham 620R': 4850, 'last friday night': 226088, 'ella fitzgerald': 226088, 'jazz': 709914, 'swing': 226088, 'olivia kuper harris': 226088, 'katy perry': 317567, 'katie perry': 226088, 'pmj': 226088, 'katy perry cover': 226088, 'last friday night cover': 226088, 'katy perry last friday night': 226088, 'last friday night katy perry': 226088, 'katy perry last friday night cover': 226088, 'last friday night katy perry cover': 226088, 'katy perry cover last friday night': 226088, 'last friday night cover katy perry': 226088, 'cover katy perry': 226088, 'cover last friday night': 226088, 'cover last friday night katy perry': 226088, 'olivia kuper harris cover': 226088, 'olivia kuper harris live': 226088, 'Barbies': 651036, 'P!nk': 3254622, 'RCA Records Label': 3594874, 'tamar': 451356, 'tamar braxton': 468358, 'tamar braxton vince herbert': 451356, 'tamar and vince': 468358, 'vince herbert': 451356, 'tamar braxton talk show': 451356, 'tamar vince interview': 451356, 'tamar vince season 5': 451356, 'tamar braxton marriage': 451356, 'tamar vince divorce': 451356, 'tamar chronicles': 451356, 'the braxtons': 451356, 'braxton family values': 451356, 'tamar and vince trailer': 451356, 'tamar braxton interview': 451356, 'tamar and vince premiere': 451356, 'lanai': 46571, 'sanctuary': 77147, 'feral': 46571, 'hawaii': 78210, 'hawaiian': 46571, 'lenai': 46571, 'island': 129103, 'tropical': 61924, 'travel': 2465799, 'tourist': 46571, 'destination': 46571, 'visit': 411108, 'best': 5009729, 'GWR': 28539, 'Guinness World Records': 28539, 'Guinness Records': 28539, 'Guinness': 28539, 'World Record': 28539, 'Guinness Book': 28539, 'World Record Book': 28539, 'record book': 28539, 'record breakers': 28539, 'Record': 327283, 'Officially Amazing': 28539, 'guinness buch der rekorde': 28539, 'ギネス世界記録': 28539, 'ギネス': 28539, 'ギネスブック': 28539, 'fire': 1344458, 'fire breathing': 28539, 'flame': 28539, 'backflip': 28539, 'back flip': 28539, 'fire breathing backflip': 28539, 'twist backflips': 28539, 'full twist backflips': 28539, 'backflip tutorial': 28539, 'how to backflip': 28539, 'flip': 532412, 'fire flip': 28539, 'house fire flip': 28539, 'fire flips': 28539, 'steve-o': 28539, 'slomo': 3965578, 'the slow mo guys': 3953110, 'slowmoguys': 3965578, 'slow motion': 692029, 'australia': 1532199, 'Blockbuster': 12609, 'blockbuster.dk': 12609, 'blockbuster.se': 12609, 'blockbuster.no': 12609, 'blockbuster.fi': 12609, 'in': 1704778, 'tahoe': 30021, 'surgery': 471266, 'transformation': 3539162, 'surgery makeup': 471266, 'before and after': 2324257, 'christen dominique': 471266, 'christen': 471266, 'dominique': 471266, 'make up': 679818, 'plastic surgery makeup': 471266, 'power of makeup': 584079, 'glam': 555702, 'full coverage': 480223, 'the power of makeup': 690953, 'makeup transformation': 522998, 'glam makeup tutorial': 471266, 'glam makeup': 471266, 'nose contour': 471266, 'nose job': 471266, 'highlight and contour': 521804, 'sculpt': 471266, 'how to contour': 471266, 'james': 4686128, 'james charles': 2363551, 'charles': 2405929, 'mua': 2363551, 'makeup artist': 2671435, 'covergirl': 2363551, 'coverboy': 2363551, 'easy makeup': 2387678, 'beauty guru gets a makeover': 1073134, 'beauty guru gets a makeover at mac': 1073134, 'beauty guru gets a makeover at sephora': 1073134, 'beauty guru gets a makeover at ulta': 1073134, 'sephora': 1073134, 'ulta': 1073134, 'mac': 1073134, 'makeover': 3122787, 'jaclyn hill': 1763486, 'nba': 5209943, 'basketball': 5007350, 'dawkins': 139048, 'dawk': 139048, 'ins': 139048, 'dawkinsmta': 139048, 'Highlights': 139048, 'biggest earthquake': 250193, 'biggest earthquakes': 250193, 'earthquakes': 250193, 'earth': 297854, 'tsunami': 250193, 'richter scale': 250193, 'san andreas': 250193, 'quakes': 250193, 'quake': 250193, 'earthquake forecasting': 250193, 'earthquake forecast': 250193, 'tectonic': 250193, 'aftershock': 250193, 'indian ocean': 250193, 'san andreas fault': 250193, 'the infographics show': 935060, 'theinfographicsshow': 935060, 'biggest': 250193, 'Biggest Earthquakes Ever': 250193, 'Biggest Earthquakes': 250193, 'Biggest Earthquake': 250193, 'goprohero4': 2457408, 'lava': 2854737, 'volcano': 2539940, 'Viral': 555306, 'Video': 5069578, 'Epic': 777147, 'cupcakes': 503516, 'cupcake': 187482, 'cup cake': 122669, 'cake': 1546566, 'cakes': 297573, 'bake': 215098, 'bakes': 122669, 'baking': 1336390, 'baker': 333789, 'crumbs and doilies': 232100, 'C&D': 122669, 'cupcake jemma': 232100, 'jemma wilson': 232100, 'gemma': 187482, 'chocolate': 1292005, 'orange': 122669, 'chocorange': 122669, "terry's": 122669, 'autumns': 122669, 'autumnal': 122669, 'oranges': 1088492, 'baking with': 122669, 'for': 1756715, 'how to make': 3156266, 'making': 1425666, 'chocolate leaves': 122669, 'sugarcraft': 122669, 'cupcake decorating': 167287, 'treacle': 122669, 'british': 3775095, 'english': 820869, 'swish swish': 56012, 'making of': 62757, 'just dance': 56012, 'bts': 5515928, 'im with her': 6148, "i'm with her": 6148, 'folk music': 6148, 'indie folk': 6148, 'sara watkins': 6148, 'folk': 6148, 'indie folk music': 6148, 'new folk': 6148, 'indie folk songs': 6148, 'acoustic music': 6148, 'prairie home companion': 6148, "aoife o'donovan": 6148, 'indie folk playlist': 6148, 'acoustic indie': 6148, 'see you around': 6148, "i'm with her see you around": 6148, "see you around i'm with her": 6148, 'im with her see you around': 6148, 'see you around im with her': 6148, 'aoife odonovan': 6148, "i'm with her band": 6148, 'im with her band': 6148, 'Sarah Jarosz': 6148, 'Sara Jarosz': 6148, 'Rounder Records': 6148, 'indie folk music love songs': 6148, 'zoe sugg': 2694291, 'zoe': 2694291, 'sugg': 2160701, 'cosmetics': 6306921, 'collaboration': 2325888, 'chatty': 2231965, 'everyday': 1443027, 'berry': 1390440, 'gold': 1390440, 'viral': 815271, 'dramatic': 244532, 'selena gomez instagram full': 82087, 'instagram live selena gomez': 82087, 'teens': 1099274, 'guitar': 1427124, 'home': 1891431, 'selana at home': 82087, 'hannah': 3881346, 'hart': 1079082, 'hannah hart': 365171, 'harto': 359120, 'my drunk kitchen': 359120, 'girlfriend': 805198, 'buzzfeed ella': 359120, 'ella mielniczenko': 359120, 'buzzfeed video': 1782184, 'ella': 359120, 'lgbt': 2046564, 'buzzfeedvideo': 1944759, 'violet': 5066647, 'buzzfeed violet': 359120, 'couple': 826305, 'dating': 777777, 'buzzfeedviolet': 359120, 'ashly perez': 359120, 'date': 359120, 'lesbian': 359120, 'dating advice': 359120, 'relationship advice': 359120, 'coming out': 1998618, 'dating tips': 359120, 'dating coach': 359120, 'handcraft': 359120, 'Phillip': 38068, 'Phillips': 38068, 'Magnetic': 38068, 'Interscope': 2997122, 'YouTube': 3553568, 'Beauty': 5864982, 'Makeup': 2783345, 'Tutorial': 9046928, 'Review': 3576238, 'Tati': 2570902, 'Westbrook': 2570902, 'GlamLifeGuru': 2570902, 'Beauty expert': 2570902, 'drugstore': 5444445, 'luxury': 2579859, 'Haul': 2570902, 'favorites': 2761952, 'Best': 2989659, 'worst': 3706841, 'Natasha Denona': 1277364, 'Holiday collection': 1277364, 'Holiday Makeup 2017': 1277364, 'Holiday Gift Set': 1277364, 'Metallic Shadow': 1277364, 'Sephora': 1277364, 'Giveaway': 1277364, 'Regret': 1277364, 'Ozuna': 2860083, 'Reggaeton': 1498866, 'Odisea': 1498866, 'Odisea The Album': 1498866, 'Música Sin Fronteras': 1498866, 'Documentary': 1498866, 'YouTube Documentary': 1498866, 'Documental': 1498866, 'Musica Sin Fronteras': 1498866, 'madelaine': 812167, 'madelainepetsch': 812167, 'madelaine petsch': 855288, 'petsch': 812167, 'cheryl': 812167, 'blossom': 812167, 'cherylblossom': 812167, 'cheryl blossom': 815889, 'riverdale': 2872667, 'travis mills': 360947, 'espn': 2044776, 'espn live': 1805639, 'first take': 1327510, 'first take espn': 670426, 'espn first take': 670426, 'first take daily': 1327510, 'first take today': 1127275, 'nba first take': 470191, 'first take nba': 470191, 'first take stephen a smith': 470191, 'stephen a smith': 1094186, 'stephen a. smith': 1094186, 'stephen a smith rant': 670426, '76ers': 602761, 'joel embiid': 476004, 'trust the process': 470191, 'stephen a smith 76ers': 470191, 'stephen a smith nba rant': 470191, 'DubNation': 16305, 'Golden State Warriors': 16305, 'Golden State': 16305, 'Warriors': 16305, 'GSW': 16305, 'Stephen Curry': 16305, 'Steph Curry': 16305, 'Steph': 16305, 'Curry': 16305, 'Steve Kerr': 16305, 'Kerr': 16305, 'Coach Kerr': 16305, 'Kevin Durant': 16305, 'Durant': 64892, 'KD': 16305, 'Draymond Green': 16305, 'Draymond': 16305, 'Klay Thompson': 16305, 'Klay': 16305, 'Andre Iguodala': 16305, 'Andre': 16305, 'Iguodala': 16305, 'Zaza Pachulia': 16305, 'Zaza': 16305, 'Pachulia': 16305, 'Jordan Bell': 16305, 'Nick Young': 16305, 'Swaggy': 16305, 'Swaggy P': 16305, 'David West': 16305, 'D.West': 16305, 'Patrick McCaw': 16305, 'Pat McCaw': 16305, 'McCaw': 16305, 'McGawd': 16305, 'P-Nice': 16305, 'Minnesota Timberwolves': 16305, 'Minnesota': 16305, 'Timberwolves': 16305, 'Technology': 1137590, 'self-driving car': 169511, 'Las Vegas': 169511, 'News': 901987, 'Thanksgiving': 1860395, 'keanu reeves': 704363, 'keanu': 704363, 'the matrix': 704363, 'john wick': 704363, 'john wick 2': 704363, 'arch motorcycles': 704363, 'krgt-1': 704363, 'motorcycle': 2176557, 'harley': 704363, 'chopper': 704363, 'norton': 704363, 'triumph': 704363, 'virtual reality': 4151539, 'vr': 4142085, 'htc vive': 704363, 'augmented realty': 704363, 'design': 2763102, 'california cruiser': 704363, 'arch s1': 704363, 'arch 143': 704363, 'keanu reeves motorcycle': 704363, 'keanu reeves vr': 704363, 'vive': 704363, 'keanu motorcycle': 704363, 'keanu reeves cool': 704363, 'wired': 9354530, 'arch': 704363, 'gard hollinger': 704363, 'keanu reeves interview': 704363, 'keanu reeves nice': 704363, 'bike': 780548, 'custom bike': 704363, 'custom motorcycle': 704363, 'oculus': 704363, 'motorcycle building': 704363, 'wired.com': 9354530, 'illustrator': 130778, 'kids describe': 130778, 'hiho': 293612, 'hiho kids': 130778, 'kids shows': 130778, 'kids videos for kids': 130778, 'kids describe to an illustrator': 130778, 'kids try': 130778, 'pbs kids': 130778, 'Hiho': 293612, 'electrical': 972893, 'electroboom': 972893, 'electronics': 1275687, 'engineering': 1086552, 'equipment': 972893, 'mehdi': 972893, 'mehdi sadaghdar': 972893, 'arc': 1595553, 'mishap': 972893, 'physics': 3185188, 'Sadaghdar': 972893, 'test': 3631733, 'tools': 1282688, 'circuit': 1012008, 'shock': 2017726, 'spark': 978858, 'protection': 972893, 'short circuit': 972893, 'Muzo': 560569, 'Noise cancelling': 560569, 'active noise cancelling': 560569, 'kickstarter': 560569, 'indiegogo': 560569, 'crowd funding': 560569, 'fake': 560569, 'project': 2063337, 'amplifier': 972893, 'ambient': 560569, 'speaker': 560569, 'surface': 831428, 'actuator': 560569, 'spirit airlines': 462490, 'spirit': 462490, 'brent': 462490, 'pella': 462490, 'animation': 4059020, 'illustration': 996430, 'travel jokes': 462490, 'saturday night live': 622632, 'snl': 10515994, 'saturday': 462490, 'holiday travel': 462490, 'airline review': 462490, 'airplane review': 462490, 'slow': 3924571, 'mo': 4284572, 'motion': 3963667, 'Slow Motion': 4215371, '1000fps': 3924571, 'gav': 3924571, 'dan': 4505367, 'phantom': 3924571, 'guys': 3924571, 'HD': 9880721, 'flex': 3924571, 'gavin': 3924571, 'free': 4015656, 'gavin free': 3924571, 'high speed camera': 3924571, '2000': 3924571, '2000fps': 3924571, '5000': 3924571, '5000fps': 3924571, 'slicing': 1525400, 'samurai': 2352396, 'iphone 10': 5765555, 'walmart': 2549563, 'techsmartt': 1836419, 'keaton keller': 1836419, 'trained': 977285, 'how i trained my cats': 977285, 'cook': 6998223, 'Jun': 1470564, 'Rachel': 1470564, 'paw': 977285, 'trick': 1237897, 'キッチン': 1470564, '料理': 1470564, 'ねこ': 1470564, 'ぬこ': 1470564, 'お手': 977285, 'Juns kitchen': 977285, "Jun's kitchen": 977285, 'Face ID': 2216669, 'Face ID Fooled': 390964, 'Face ID Fail': 390964, 'Face ID Test': 390964, 'Face ID Review': 390964, 'iPhone X Review': 907420, 'Facial Recognition': 390964, "Son unlocks mom's iphone x": 390964, "Ten-Year-Old's Face Unlocks Face ID": 390964, 'Jimmy Fallon': 9893362, 'Tonight Show Starring Jimmy Fallon': 1611093, 'The Tonight Show': 9893362, 'Talk Show': 10132601, 'tonight': 9893362, 'comedy sketches': 9893362, 'talent': 11199734, 'Fallon Stand-up': 9893362, 'Fallon monologue': 9893362, 'variety': 9893362, 'I Love You': 1611093, 'hand squeeze': 1611093, 'tribute': 1633046, 'mother': 1996827, 'Gloria Fallon': 1611093, 'lie detector': 1602868, 'jibawi': 4940041, 'alphacat': 983365, 'lie': 983365, 'detector': 983365, 'aladdin gets a car': 1602868, 'alesso anitta is that for me anwar jibawi dance': 1602868, 'movie spoilers': 983365, 'Lie Detector | Anwar Jibawi & Alphacat': 983365, 'Ray Romano': 2765121, 'Michael Keaton': 2765121, 'Halle Berry': 2765121, 'Anthony Anderson': 2773099, 'David Spade': 2765121, 'Chris Hemsworth': 3807579, 'Kristen Bell': 2765121, 'Jon Stewart': 4588898, 'Tracy Morgan': 2765121, 'Amy Schumer': 2765121, 'Liam Neeson': 2765121, 'Larry David': 2765121, 'Mike Tyson': 2765121, 'Jeff Bridges': 2765121, 'Zach Galifianakis': 2765121, 'Kim Kardashian West': 2765121, 'Jennifer Lawrence': 2873907, 'David Letterman': 2765121, 'will ferrell': 2765121, 'batman forever': 829132, 'batman justice league': 829132, 'justice league release': 829132, 'jim carrey': 829132, 'jim carrey riddler': 829132, 'val kilmer': 829132, 'batman and robin': 829132, 'tommy lee jones': 829132, 'tim burton batman': 829132, 'batman movie': 829132, 'chritopher nolan batman': 829132, 'dc movie': 898068, 'batman wonder woman': 829132, 'breaking news video': 1278694, 'video updates': 1278694, 'live video': 1290688, 'live updates': 1278694, 'press conference': 1278694, 'live speeches': 1278694, 'real time coverage': 1278694, 'Attorney General Jeff Sessions': 137003, 'House Judiciary Committee': 137003, 'jeff sessions testimony': 137003, 'jeff sessions live': 137003, 'jeff sessions live testimony': 137003, 'Dolan Twins': 25674070, 'Spilling Tea': 891912, 'Spilling Tea On Each Other': 891912, 'DOlan TWins London': 891912, 'morphe': 175539, 'morphe 350': 175539, 'morphe 3502 review': 175539, 'morphe 3502 swatches': 175539, 'morphe 3502 review dark skin': 175539, 'morphe discount code': 175539, 'morphe code': 175539, 'Donatella Versace': 14565, "Antonio D' Amico": 14565, 'Penelope Cruz': 14565, 'American Crime Story: The Assassination of Gianni Versace': 14565, 'American Crime Story: The Assassination of Gianni Versace Trailer': 14565, 'American Crime Story: The Assassination of Gianni Versace Official Trailer': 14565, 'American Crime Story: The Assassination of Gianni Versace Promo': 14565, 'season 1': 1991179, 'FX': 14565, 'FX series': 14565, 'FX tv series': 14565, 'Max Greenfield': 14565, 'tvpromosdb': 14565, 'DIRECTV': 125645, 'DIRECTVNOW': 125645, 'ATT': 125645, 'AT&T': 2612580, 'The Making of a Song': 125645, 'Taylor Swift NOW': 125645, 'Swifties': 125645, 'Delicate': 125645, 'venting online': 394709, 'dark souls': 394709, 'dragon': 394709, 'reddit': 794200, 'youtube comments': 394709, 'beat the game': 394709, 'spoilers': 394709, 'ending': 394709, 'final': 394709, 'tweeting': 394709, 'easy life hacks': 7178665, 'life hacks that will change your life': 863116, 'life hack ideas': 863116, '10 AWESOME LIFE HACKS with TEENS REACT': 863116, 'staff react': 863116, 'fbe staff': 863116, 'fbe': 863116, 'employees': 863116, 'coworkers': 863116, 'co-workers': 863116, 'laugh challenge': 1880303, 'try not to laugh': 2749946, 'try to watch without laughing or grinning': 1880303, 'react gaming': 1880303, 'kids versus food': 1880303, 'do they know it': 1880303, 'lyric breakdown': 1880303, 'the 10s': 1880303, 'the 10': 1880303, 'simple life hacks': 7198524, 'hacks': 1372640, 'life hacks': 3836435, 'staff reacts': 1880303, 'jay cutler': 200235, 'first take nfl': 200235, 'nfl first take': 200235, 'stephen a smith jay cutler': 200235, 'jay cutler stephen a smith': 200235, 'first take jay cutler': 200235, 'miami dolphins': 200235, 'dolphins qb': 200235, 'miami qb': 200235, 'TAYLOR SWIFT': 122426, 'poprvwz': 122426, '1989': 123906, 'shake it off': 122426, 'delicate': 122426, 'trap': 142170, 'r&b': 901667, 'blank space': 122426, 'First we feast': 5442735, 'fwf': 5442735, 'firstwefeast': 5442735, 'food porn': 5670718, 'chef': 7354407, 'kitchen': 12717512, 'cocktail': 5602105, 'bartender': 5442735, 'craft beer': 5442735, 'complex': 6292120, 'complex media': 6292120, 'Cook (Profession)sean evans': 5442735, 'sean evans': 5294504, 'sean in the wild': 569201, 'frank pinello': 145100, 'the pizza show': 145100, 'japanese food': 145100, 'sakura yagi': 145100, 'little tokyo': 145100, 'east village': 145100, 'soba noodles': 145100, 'omurice': 145100, 'Brian Hull': 52743, 'Brian Hull Impressions': 52743, 'Mickey Mouse': 52743, 'Brian Hull Voices': 52743, 'Brian Hull Vlogs': 52743, 'Mickey Mouse Surprise': 52743, 'Walt Disney': 52743, 'Mickey Came to My House': 52743, 'Mickey Mouse in the House': 52743, 'Mickey Mouse in My House': 52743, 'Magic Moments': 52743, 'Disney Surprise': 52743, 'Disney Magic Moments': 52743, 'Dreams Come True': 52743, 'laser cutter': 1536072, 'william osman': 1640582, 'crappy science': 1640582, 'sandals': 121681, 'flip flops': 121681, 'laser sandals': 121681, 'zip ties': 121681, 'make sandals': 121681, 'tap dancing': 121681, 'zip tie': 121681, 'laser': 160796, 'access hollywood': 93527, 'blake shelton and gwen stefani': 11404, '“you make it feel like chris': 11404, 'gwen stefani': 122154, 'gwen stefani 2017': 11404, 'gwen stefani and blake shelton': 11404, 'interviews': 1467514, 'people magazine': 177899, 'access': 95301, 'gossip': 2634555, 'wednesday': 11404, 'november 15.': 11404, 'gwen stefani new album sexiest': 11404, 'entertainment news': 1402168, 'gwen stefani latest': 11404, 'gwen stefani christmas album': 11404, 'American Things Europeans Find Weird': 419965, 'americans': 645072, 'american behavior': 419965, 'things americans do': 419965, 'weird americans': 419965, 'united states of america': 431764, 'american people': 419965, 'american customs': 419965, 'europeans': 419965, 'european thinking': 419965, 'weird american things': 419965, 'things people do': 419965, 'things america does': 419965, 'Carey Mulligan': 137749, 'Tell People': 137749, 'Actress': 3429662, 'Drive': 137749, 'An Education': 137749, 'Never Let Me Go': 137749, 'Inside Llewyn Davis': 137749, 'The Great Gatsby': 137749, 'Shame': 137749, 'Wildlife': 195953, 'emma chamberlain': 126438, 'emma chambie': 126438, 'cooking with emma': 126438, 'funfetti': 126438, 'how to make cake': 126438, 'funfetti cake recipe': 126438, 'healthy': 219016, 'vegan': 544164, 'paleo': 126438, 'the room': 2464272, 'so bad its good': 206319, "movies that are so bad they're good": 206319, 'worst movies': 206319, 'The Room': 206319, 'sharknado': 206319, 'the disaster artist': 2534408, 'the disaster artist the room': 206319, 'the princess bride': 206319, 'so bad its good movies': 206319, 'best so bad its good movies': 206319, 'video essay': 700655, 'now you see it': 206319, 'abc': 1078970, 'dancing': 974877, 'stars': 621108, 'dwts': 617455, 'Lindsey Stirling': 271379, 'Maksim Chmerkovskiy': 60564, 'Meryl Davis': 60564, 'Tango': 60564, 'Feel\u200b \u200bSo\u200b \u200bClose': 60564, 'Calvin\u200b \u200bHarris': 60564, 'Season 25': 617455, 'Mark Ballas': 485684, 'Dancing with the Stars': 617455, 'Colin': 137860, 'Farrell': 137860, 'Colin Farrell': 137860, 'The killing of a sacred deer': 137860, 'colin farrell family': 137860, 'the lobster': 137860, 'the beguiled': 137860, 'summit': 4009, 'conference': 4009, 'talks': 4009, 'performances': 33263, 'gathering': 4009, 'community': 4009, 'bezos': 4009, 'jeff bezos': 4009, 'mark bezos': 4009, 'amazon.com': 4009, 'amazon': 3762305, 'la17': 4009, 'summit la17': 4009, 'festival': 92245, 'ideas conference': 4009, 'ideas festival': 4009, "world's richest man": 4009, 'Taylor swift Instagram Story': 3006, 'Target': 116489, 'Taylorswift': 3006, 'Taylor swift': 3006, 'taylurking': 3006, 'instagram Story': 3006, 'Tay': 3006, 'NBA Highlights': 145350, 'Joel Embiid': 46954, 'Willie Reed': 46954, 'breath': 190669, 'hold': 190669, 'holding breath': 190669, 'breathing': 190669, 'holding your breath': 190669, 'record': 296090, 'watchcut': 359908, 'cut': 359908, 'cut video': 190669, 'how to make vanilla cupcakes': 121530, 'over the top recipes': 121530, 'easy cupcake recipes': 121530, 'vanilla cupcakes': 121530, 'chocolate cupcakes': 121530, 'french macarons': 121530, 'how to make macarons': 121530, 'the scran line': 121530, 'the scranline': 121530, 'nick makrides': 121530, 'pastry design': 121530, 'how to pipe cupcakes': 121530, 'Ice Boats': 94694, 'Lake Geneva': 94694, '5D Mark II': 94694, 'Fontana': 94694, 'Wisconsin': 298987, 'Matt Mason Photography': 94694, 'Ice Boat': 94694, 'victoria beckham': 18438, 'victoria beckham advice': 18438, 'victoria beckham style': 18438, 'victoria beckham fashion': 18438, 'fashion advice': 18438, 'victoria beckham fashion advice': 18438, 'derek does stuff with a friend': 753032, 'ddswaf': 18438, 'derek does stuff with friends': 753032, 'derek blasberg': 1383394, 'man buns': 18438, 'maternity fashion': 18438, 'maternity clothes': 18438, 'white after labor day': 18438, 'victoria beckham 2017': 18438, 'victoria beckham interview': 18438, 'interview victoria beckham': 18438, 'vanity fair': 2634335, 'vanity fair magazine': 2634335, 'vf': 2634335, 'red bull': 1205674, 'redbull': 2645085, 'Matt Jones': 71661, 'Matt': 71661, 'Jones': 71661, 'MTB': 71661, 'masterclass': 71661, 'Frames of mind': 71661, 'mountain bike': 71661, 'mountain biking': 71661, 'downhill mountain biking': 71661, 'downhill': 71661, 'biking': 71661, 'trail': 71661, 'mountain': 71661, 'mountainbike': 71661, 'transworld': 71661, 'eurosport': 71661, 'trek': 71661, 'plus bike': 71661, 'rampage 2017': 71661, 'mx nation': 71661, 'specialized': 71661, 'cycling': 71661, 'cyclist': 71661, 'how to manual': 71661, 'cornering': 71661, 'down': 133638, 'dh': 71661, 'difficult': 622503, 'fancy': 186836, 'editing': 71661, 'rotoscoping': 71661, 'impossible': 368967, 'stunts': 710163, 'riding': 71661, 'ride': 71661, 'freeride': 71661, 'dirt': 71661, 'enduro': 71661, 'jump': 1104286, 'downhill mtb': 71661, 'uci': 71661, 'dallas': 2920766, 'cowboys': 2920766, 'atlanta': 938273, 'falcons': 935879, 'prescott': 1864630, 'dak': 1864630, 'td pass': 2920766, 'julio jones': 935879, 'sack': 935879, 'tackle': 1015554, 'clayborn': 935879, 'falcons win': 935879, 'sp:dt=2017-11-12T16:25:00-05:00': 1720816, 'sp:ti:home=Atl': 935879, 'sp:ti:away=Dal': 935879, 'Demi Lovato': 8482531, 'Sorry Not Sorry': 1840207, 'Live Lounge': 1403244, 'BBC Radio 1': 131153, 'unisex': 34138, 'clothing haul': 34138, 'giveaway': 2007535, 'men': 741441, 'women': 4250566, 'mark ferris': 61754, 'gender': 588506, 'velvet': 34138, 'suede': 34138, 'fei fei sun': 144937, 'beauty secrets': 415079, 'vogue beauty secrets': 144937, 'fei fei sun beauty secrets': 144937, 'estee lauder': 144937, 'fei fei': 144937, 'fei sun': 144937, 'fei fei sun estee lauder': 144937, 'complexion': 157727, 'shandong china': 144937, 'weifang': 144937, 'how to makeup': 213642, 'morning beauty routine': 144937, 'morning beauty': 144937, 'beauty how to': 144937, 'face massager': 144937, 'face serum': 144937, 'makeup powder': 144937, 'eye makeup': 195475, 'blush': 144937, 'Todrick Hall': 227446, 'Pentatonix': 1856820, 'GAY': 227446, 'cyborg': 113963, 'j. Fla': 113963, 'j fla': 113963, 'samantha harvey': 113963, 'clara marz': 113963, 'mario bautista': 113963, 'Gary Clark Jr': 113963, 'come together': 113963, 'come together song': 113963, 'come together cover': 113963, 'super heros': 113963, 'dc entertainment': 113963, 'warner brothers': 127904, 'wb pictures': 3714652, 'warner bros': 2699156, 'Beatles': 127313, 'Beatle cover': 113963, 'sam harvey': 113963, 'alter ego': 113963, 'beyonce': 9024323, 'irak': 93322, 'زلزال العراق': 93322, 'زلزال ايران': 93322, 'هزة ارضية': 93322, 'هزة ارضية ايران': 93322, 'زلزله': 93322, 'زلزله ايران': 93322, 'زمين لرزه ايران': 93322, 'iran quake': 93322, 'Iran-Iraq border earthquake': 93322, 'bbc world news': 93322, 'bbc news coverage': 93322, 'bbc news live': 93322, 'Jeep': 93829, 'jeep wrangler': 93829, 'jeep 4x4': 93829, 'jeep cj': 93829, 'cj-7': 93829, 'cj-5': 93829, 'jeep renagade': 93829, 'wrangler': 93829, 'off-roading': 93829, 'four wheel drive': 93829, '4x4': 93829, 'lifted truck': 93829, 'utility vehicle': 93829, 'military vehicle': 93829, 'rock crawling': 93829, 'desert racing': 93829, 'camping vehicle': 93829, 'jeep sahara': 93829, 'lifted jeep': 93829, 'mudding': 93829, 'sand dunes': 93829, 'hunting': 111421, 'fishing': 93829, 'camping': 456851, 'classic car': 93829, 'evolution': 1024941, 'ham': 31639, 'ice cream': 263363, 'candied': 31639, 'candy': 650897, 'pineapple': 31639, 'candied fruit': 31639, 'ice cream flavor': 31639, 'flavor': 31639, 'pinapple': 31639, 'oddfellows': 31639, 'sam mason': 31639, 'maple bacon': 31639, 'bacon ice cream': 31639, 'ice cream shop': 31639, 'hawaiian pizza': 31639, 'creation': 31639, 'invention': 31639, 'try it': 308083, 'dessert': 387842, 'steak': 1375171, 'sushi': 227983, 'eater': 227983, 'eater.com': 227983, 'foodie': 227983, 'dining': 227983, 'dish': 227983, 'restaurant': 1279331, 'restaurants': 227983, 'munchies': 192230, 'Cooking (Interest)': 227983, 'Eating': 227983, 'barbecue': 227983, 'the meat show': 31639, 'nick solares': 31639, 'Hanson': 45200, "Finally It's Christmas": 45200, 'Snowed In': 45200, 'ION Bottleless Water Cooler': 399491, 'Stonyrbook': 399491, 'stonybrook': 399491, 'stonybrook cooler review': 399491, 'stonybrook review': 399491, 'botleless review': 399491, 'hot': 1266282, 'cold': 399491, 'carbonated': 399491, 'wow': 676455, 'radiator': 399491, 'boot': 399491, 'nba video': 5813, 'nba interview': 5813, 'joel': 5813, 'embiid': 5813, 'win': 8207, 'clippers': 5813, 'los angeles clippers': 5813, 'scuffle': 5813, 'willie': 5813, 'reed': 5813, 'willie reed': 5813, 'media': 5813, 'social media': 1190533, 'philadelphia 76ers': 119739, 'Sirah': 18317, 'Skrillex': 1436659, 'female rapper': 54137, 'Owsla': 18317, 'Deadbeat': 18317, 'Dead Beat': 18317, 'Rick and Morty': 246134, 'Rick and Morty Season 3': 246134, 'Rick and Morty Full Episodes': 246134, 'Rick Sanchez': 246134, 'Morty Smith': 246134, 'MortyMatters': 246134, 'Rick and Morty Explained': 246134, 'Valk Aviation': 571588, 'Amsterdam Airport': 571588, 'Schiphol': 571588, 'Airport': 571588, 'planes': 571588, 'planespotting': 571588, 'takeoff': 571588, 'Boeing': 571588, 'KLM': 571588, 'lightning strike': 571588, 'thunderstrike': 571588, 'B777-300': 571588, 'PH-BVS': 571588, 'spray takeoff': 571588, 'wongcondensation': 571588, 'wingvortex': 571588, '12-11-2017': 571588, 'Bokito TV': 571588, 'KL743': 571588, 'galexy note 8': 42336, 'samsung creators': 42336, 'neistat': 42336, 'casey neistat': 1725638, 'shantell martin': 42336, 'drawing': 118553, 'HuskerOnline.com': 6684, 'Nebraska Football': 6684, 'Huskers': 6684, 'Riovals.com': 6684, 'essiebutton': 128523, 'Estée Lalonde': 128523, 'Estee Lalonde': 128523, 'Essie Button': 128523, 'Essie': 128523, 'No Makeup Makeup': 128523, 'Drugstore Makeup Tutorial': 128523, 'Cute Outfits': 128523, 'Makeup Tutorials': 128523, 'Healthy Foods': 128523, 'Eye Makeup': 128523, 'Outfit Ideas': 128523, 'Natural Makeup': 128523, 'Easy Healthy Recipes': 128523, 'Skin Care Products': 128523, 'Best Makeup': 128523, 'favourites': 128523, 'november': 128523, 'new in': 128523, 'glossier': 166399, 'you': 2550181, 'perfume': 128523, 'ouai': 128523, 'messy hair': 128523, 'charlotte tilbury': 166399, 'lancome': 128523, 'highlighter': 424994, 'glow': 141313, 'scandinavian': 188446, 'rupi kaur': 128523, 'the office': 200060, 'the office full episodes': 200060, 'rainn wilson': 200060, 'john krasinski': 2536588, 'steve carell': 200060, 'michael scott': 200060, 'the office fire drill': 200060, 'jim and dwight pranks': 200060, 'dwight schrute': 200060, 'jim halpert': 200060, 'jenna fischer': 363027, 'the office thug life': 200060, 'the office funniest moments': 200060, 'the office bloopers season 1': 200060, 'the office cpr': 200060, 'the office parkour': 200060, 'Best The Office Moments': 200060, 'The Office Best Pranks': 200060, 'Funny Pranks': 200060, 'Office Pranks': 200060, 'lord of the rings tv show': 11741, 'lord of the rings': 3653, 'the hobbit': 3653, 'lotr': 3653, 'tv series': 3653, 'peter jackson': 3653, 'jrr tolkien': 3653, 'fantasy': 3653, 'the two towers': 3653, 'the return of the king': 3653, 'the lord of the rings': 3653, 'book': 1800640, 'aragorn': 3653, 'rumors': 170148, 'the lord speaks': 3653, 'tv shows like lord of the rings': 3653, 'Entertainment Weekly': 3653, 'EW': 3653, 'movies': 5878127, 'celebs': 1300686, 'film (media genre)': 3653, 'daddy yankee': 5626588, 'Camila Cabello & Daddy Yankee': 5626588, 'Latin': 6126534, 'Arizona': 85643, 'Bill gates': 85643, 'land': 1983255, 'tech city': 85643, 'smart city': 85643, 'bkav': 1080230, 'iphonex': 1080230, 'faceid': 1080230, 'iphone10': 1080230, 'face id beaten': 1080230, 'bphone2017': 1080230, 'bphone': 1080230, 'Nguyen Tu Quang': 1080230, 'Ngo Tuan Anh': 1080230, 'Nguyễn Tử Quảng': 1080230, 'Ngô Tuấn Anh': 1080230, 'dapulse': 3931, 'monday.com': 3931, 'monday': 132064, 'project management': 3931, 'coats': 101233, 'poutine': 101233, 'stranger things': 11596307, 'stranger things 2': 5772085, '80s': 265751, 'audition': 1294139, 'stranger things 2017': 101233, 'stranger things audition': 101233, 'stranger things season 2': 5772085, 'gq magazine': 101233, 'stranger things characters': 101233, 'stranger things steve': 101233, 'steve stranger things': 101233, 'joe keery': 6175706, 'joe keery 2017': 101233, 'stranger things joe keery': 101233, 'joe': 101233, 'keery': 101233, 'stranger things joe': 101233, 'cast of stranger things': 101233, 'stranger things cast': 5772085, 'joe keery hair': 5772085, 'stranger': 101233, 'gq': 101233, 'TMZ': 3562732, 'TMZ Sports': 3505996, 'TMZ Sports Channel': 3505996, 'TMZ 2017': 3505996, 'TMZ Sports 2017': 3505996, 'Athletes': 3505996, 'TMZ News': 3505996, 'Famous': 3562732, 'Hall of Fame': 3505996, 'Sports News': 3505996, 'TMZ 2016': 3505996, 'TMZ2016FS11221': 3562732, 'Roy Halladay': 3505996, 'Roy halladay Plane Crash': 3505996, 'Roy Halladay Plane Crash Video': 3505996, 'Roy Halladay Video Footage': 3505996, 'Roy Halladay Plane footage': 3505996, 'Raw Video': 3505996, 'Trending': 3514444, 'MLB': 3505996, 'Roy Halladay MLB': 3505996, 'TMZ Roy Halladay': 3505996, 'box': 1532584, 'bulky': 90454, 'case': 90454, 'caters': 90454, 'clips': 90454, 'conceal': 90454, 'engagement': 1111021, 'happy': 538641, 'mobile': 90454, 'partner': 90454, 'phone': 1100317, 'position': 90454, 'proposal': 415267, 'propose': 90454, 'ring': 422106, 'slimline': 90454, 'subtle': 90454, 'traditional': 90454, 'user': 90454, "world's": 90454, 'york': 90454, 'Deadpool': 3979891, '20th Century Fox (Production Company)': 3979891, 'Deadpool Movie': 3979891, 'Ryan Reynolds (Celebrity)': 3979891, 'Ed Skrein (Musical Artist)': 3979891, 'T. J. Miller (TV Writer)': 3979891, 'Gina Carano (Martial Artist)': 3979891, 'Red band': 3979891, 'Red band deadpool': 3979891, 'Marvel': 10627326, 'Marvel Comics': 3984034, 'Comic Book (Comic Book Genre)': 3979891, 'Dead pool': 3979891, 'Deadpool green band': 3979891, 'Deadpool red band': 3979891, 'Action': 5595736, 'Action Comedy': 3979891, 'X-Men (Award-Winning Work)': 3979891, 'Forest': 387902, 'Cooking': 387902, 'cooking tutorial': 387902, 'you suck at cooking': 867943, 'ysac': 748782, 'pumpkin': 1056830, 'pumpkin pie': 810871, 'pie': 1796194, 'spices': 387902, 'tanner': 1803724, 'tanner braungardt': 1803724, 'trampoline tricks': 1803724, 'cliff jumping': 1803724, 'teaching a backflip': 1803724, 'flip over car': 1803724, 'flipping over supercar': 1803724, 'flip over car gone wrong': 1803724, 'car flip accident': 1803724, 'horrible accident': 1803724, 'near death experience': 1803724, 'near death fail': 1803724, 'car fail caught on camera': 1803724, 'tumbling fails': 1803724, 'gymnastic fails': 1803724, 'worst fails compilation': 1803724, 'epic fail compilation': 1803724, 'audi r8 crash': 1803724, 'supercar crash compilation': 1803724, 'supercar crash': 1803724, 'luxary car crash': 1803724, 'parkour fail': 1803724, 'parkour and freerunning': 1803724, 'first date': 224426, 'animated': 6549156, 'short': 1185772, 'shorts': 4722180, 'animation shorts': 224426, 'ihascupquake': 224426, 'redb15': 224426, 'cupquake': 224426, 'red': 274340, 'cupquake and red first date': 224426, 'ihascupquake first date': 224426, 'funny animation': 224426, 'funny cartoon': 224426, 'funny first date': 224426, 'jlo': 224426, 'anaconda': 315511, 'married couple': 224426, 'gamer couple': 224426, 'our first date': 224426, 'ihascupquake animated': 224426, 'cupquake animation': 224426, 'ihascupquake animation': 224426, 'Justice league': 593969, 'avengers': 40108967, 'wb': 4031691, 'comic book': 593969, 'heroes': 593969, 'pawpaw': 533940, 'paw paw': 533940, 'papaya': 533940, 'andrew moore': 533940, 'maeve turner': 533940, 'sustainable fruit': 533940, 'organic': 553799, 'plants': 533940, 'taste test': 2095799, 'eating': 1175915, 'eat': 612800, 'american history': 533940, 'american': 533940, 'all-american': 533940, 'apples': 533940, 'controlled atmosphere storage': 533940, 'farming': 533940, 'farms': 533940, 'industrial agriculture': 533940, 'paw-paw': 533940, 'weird fruit': 533940, 'food tasting': 533940, 'unusual fruit': 533940, 'exotic fruit': 533940, "world's strangest fruits": 533940, 'facts': 1265979, 'bizarre': 533940, 'odd': 533940, 'brooklyn botanic garden': 533940, 'top weirdest fruits in the world': 533940, 'kim': 1638866, 'kardashian': 1676313, 'kim kardashian west': 1676313, 'kim k': 1638866, 'kim k make up': 1559003, 'kim and kanye': 1638866, 'kanye west': 1559003, 'north west': 1559003, 'saint west': 1559003, 'kim kardashian baby': 1559003, 'kimmel': 5393937, 'larry': 760341, 'outtakes': 763985, 'curb': 760341, 'your': 2450223, 'enthusiasm': 760341, 'seinfeld': 760341, 'laughter': 760341, 'larry david': 760341, 'curb your enthusiasm': 760341, 'jimmy kimmel birthday': 760341, 'Fall Out Boy': 1258937, 'Hold Me Tight Or Don’t': 1258937, 'Hold Me Tight': 1258937, 'MANIA': 1258937, 'FOB': 1258937, 'Patrick Stump': 1258937, 'Pete Wentz': 1258937, 'Joe Trohman': 1258937, 'Andy Hurley': 1258937, 'Fall': 1601524, 'Out': 1649918, 'Boy': 1601524, 'HOLD': 1258937, 'ME': 1258937, 'TIGHT': 1258937, 'OR': 1258937, 'DON’T': 1258937, 'alt-j': 133212, 'alt j': 133212, 'altj': 133212, 'an awesome': 133212, 'wave': 155302, 'this is all yours': 133212, 'relaxer': 133212, '∆': 133212, 'Relaxer': 133212, 'Pleader': 133212, 'Deadcrush': 133212, 'Luis Fonsi': 24990275, 'Echame La Culpa': 208117, 'kickflip': 229515, 'ollie': 368148, 'skate': 711298, 'skateboarding': 229515, 'tech support': 229515, 'tony hawk': 229515, 'twitter support': 229515, 'twitter tech support': 229515, '900': 229515, 'how to skateboard': 229515, 'tony hawk skateboarding': 229515, 'tony hawk twitter': 229515, "tony hawk's pro skater": 229515, 'tony hawk questions': 229515, 'tony hawk interview': 229515, 'tony hawk skate': 229515, 'skateboarding tutorial': 229515, 'how to skate': 229515, 'how to do a kickflip': 229515, 'how to ollie': 229515, 'tony hawk interview 2017': 229515, 'handplant': 229515, 'how to handplant': 229515, 'invert': 229515, 'mctwist': 229515, 'how to put on griptape': 229515, 'skating': 229515, 'gal gadot': 1496471, 'Joe Keery': 241250, 'Talks About': 211596, 'Famous Hair': 211596, 'Stranger Things': 1743006, "Molly's Game": 211596, 'Slice': 211596, 'Chicago Fire': 211596, 'Steve Harrington': 211596, 'Ben Schwartz': 211596, 'pointless tech gadgets': 623803, 'smart product': 623803, 'cool gadgets': 1051941, 'new gadget': 623803, 'weird things online': 623803, 'tech under': 623803, 'tech invention': 623803, 'unbox': 5803299, 'on amazon': 1687131, 'reacting to': 2322545, 'inventions': 1710868, 'gadgets from': 623803, 'things on amazon': 623803, 'shopping gadget': 623803, 'latest tech': 623803, 'weird tech': 623803, 'gag gift': 1259217, 'mathias': 623803, 'matthias': 623803, 'matthiasiam': 623803, 'hi5 studios': 623803, '#M757': 623803, 'Zodiac': 42571, 'aquarius': 2180401, 'Kris Jenner': 444750, 'Khloe Kardashian Odom': 237107, 'Bruce Jenner': 237107, 'Robert Kardashian Jr.': 237107, 'E! Entertainment': 258871, 'Keeping Up With the Kardashian': 237107, 'iPhone 10': 2342161, 'iPhone ten': 1825705, 'notch': 1825705, 'iPhone notch': 1825705, 'fullscreen iphone': 1825705, 'iPhone X review': 1825705, 'iPhone 10 review': 1825705, 'super retina display': 1825705, 'super retina': 1825705, 'super retina hd': 1825705, 'FaceID': 1825705, 'iPhone X Face ID': 1825705, 'iPhone facial recognition': 1825705, 'ios 11': 1825705, 'iOS 11.1': 1825705, 'iPhone update': 1825705, 'iOS update': 1825705, 'iPhone X features': 1825705, 'iPhone X gestures': 1825705, 'keeping up with the kardashians': 2395050, 'kylie jenner': 1742484, 'khloe kardashians': 79863, 'Dashboard Confessional': 50934, 'Dashboard': 50934, 'Chris Carrabba': 50934, 'Chris Caraba': 50934, 'Chris Carraba': 50934, 'Dashboard Music': 50934, 'Dashbaord Band': 50934, 'New Dashboard Confessional Song': 50934, 'New Song': 50934, 'new single': 50934, 'We Fight': 50934, 'Crooked Shadows': 50934, 'We Fight Song': 50934, 'We fight our way in': 50934, 'we fight our way out': 50934, 'Fueled By Ramen': 874219, 'FBR': 874219, 'New FBR signing': 50934, 'fueled by': 50934, 'dashboard signs with FBR': 50934, 'New Dashboard Record': 50934, 'New Dashboard Confessional': 50934, 'New Dashboard Album': 50934, 'New Dashboard Confessional Album': 50934, 'New dashboard track': 50934, 'emo': 180161, 'alternative': 50934, 'john green': 375916, 'mental floss': 64118, 'koalas': 21740, 'marsupial': 21740, 'joey': 874457, 'pouch': 21740, 'chlamydia': 21740, 'animals': 2942769, 'history': 600366, 'zoo': 221095, 'buck': 21740, 'mammel': 21740, 'bear': 1214287, 'eons': 21740, 'hank green': 550459, 'eucalyptus': 21740, 'camels': 21740, 'lindsay': 1731191, 'imperial force': 21740, 'home range': 21740, 'trees': 66358, 'pap': 21740, 'Mundu': 21740, 'SmackDown LIVE': 2570663, 'AJ Styles': 1863499, 'Daniel Bryan': 845763, 'survivor series': 2034089, 'sdl': 845763, 'smackdown results': 845763, 'smackdown winners': 845763, 'What Does A Cochlear Implant Sound Like?': 177648, 'Inner ear': 177648, 'deaf': 177648, 'hard of hearing': 177648, 'hearing aid': 177648, 'electrical currents': 177648, 'hair cells': 177648, 'ear': 177648, 'auditory nerve': 177648, 'channels': 177648, 'frequency': 177648, 'pitch': 1005122, 'timbre': 177648, 'speech': 3914644, 'jillian vessey': 51466, 'pixielocks': 51466, 'pink': 593269, 'silver play button': 17901, 'youtube play button': 17901, 'playbutton': 17901, 'showtime': 15232, 'shosports': 15232, 'Los Angeles Lakers': 10838, 'Phoenix Suns': 10838, 'Devin Booker': 109234, 'Titanic': 17129, 'Titanic Movie': 17129, 'Leonardo DiCaprio': 17129, 'Kate Winslet': 17129, 'James Cameron': 17129, 'Jim Cameron': 17129, 'Atlanta Hawks': 2394, 'ATL Hawks': 2394, 'Hawks basketball': 2394, 'Atlanta basketball': 2394, 'Hawks NBA': 2394, 'Hawks videos': 2394, 'Hawks players': 2394, 'Hawks highlights': 2394, 'ATL sports': 2394, 'dunks': 2394, 'starters': 418074, 'Julius Randle': 98396, 'Devin Booker And Julius Randle Fight': 98396, 'tobeornottobethatisthequestion': 342855, 'Justice League': 386364, 'DCEU': 90641, 'Zack Snyder': 90641, 'Joss Whedon': 90641, 'Warner Bros.': 517797, 'WB': 1733849, 'Comic Book': 90641, 'Superhero': 167574, 'Superman': 152763, 'Flash': 173499, 'Aquaman': 100086, 'Cyborg': 100086, 'Wonder Woman': 1860438, 'Steppenwolf': 90641, 'Mother Box': 90641, 'Ben Afleck': 90641, 'Gal Gadot': 1788871, 'Ezra Miller': 90641, 'Jason Momoa': 90641, 'Henry Cavill': 90641, 'Amy Adams': 90641, 'J.K. Simmons': 90641, 'Robin Wright': 90641, 'Amber Heard': 90641, 'Diane Lane': 90641, 'Ciarán Hinds': 90641, 'Jeremy Irons': 90641, 'animal': 499880, 'animal videos': 30576, 'big cat rescue': 30576, 'big cat rescue florida': 30576, 'cat videos': 163087, 'cougars': 30576, 'cute animals': 30576, 'large': 30576, 'Lions': 30576, 'panthers': 30576, 'tigers': 30576, 'watch cats online': 30576, 'animal sanctuary': 30576, 'big cat videos': 30576, 'hybrid cats': 30576, 'hybrids': 30576, 'Savannah cat': 30576, 'conservation': 30576, 'non-profit organization': 30576, 'Tampa': 30576, 'Florida': 1413008, 'rescue': 52666, 'new cat': 30576, 'Rachel Platten': 33887, 'Whole Heart': 33887, 'maths': 100472, 'math': 100472, 'mathematics': 100472, 'matt parker': 100472, 'menace': 100472, 'machine learning': 106056, 'naughts and crosses': 100472, 'tic tac toe': 100472, 'matthew scroggs': 100472, 'manchester': 100472, 'mosi': 100472, 'simple': 621387, 'easy': 2612369, 'matchboxes': 100472, 'beads': 100472, 'remy ma': 414929, 'wendy williams': 866310, 'lil kim': 414929, 'vincent herbert': 414929, '#youtubeblack': 866310, 'documentaries': 689546, 'docs': 489121, 'culture': 1931646, 'world': 2741511, 'exclusive': 734737, 'independent': 745395, 'journalism': 676196, 'vice guide': 34221, 'vice.com': 34221, 'vice': 698286, 'vice magazine': 34221, 'vice mag': 34221, 'vice videos': 676196, 'short films': 34221, 'transgender': 34221, 'civil rights': 34221, 'trans rights': 34221, 'lgbtq': 733123, 'virginia': 34221, 'house of representatives': 34221, 'state legislature': 34221, 'danica roem': 34221, 'election': 34221, 'democrat': 125007, 'republican': 125007, 'progressive politics': 34221, 'district 13': 34221, 'northern virginia': 34221, 'local politics': 34221, 'Jordan Fisher': 346076, 'Lindsay Arnold': 346076, 'Paige VanZant': 214305, 'Jive': 214305, 'Proud\u200b \u200bMary': 214305, 'Tina\u200b \u200bTurner': 214305, 'BBC': 1414570, 'BBC Worldwide': 58204, 'Nature': 58204, 'Natural History': 58204, 'Animals': 1984314, 'Wild': 80899, 'blue planet': 162795, 'blue planet II': 162795, 'blue planet 2': 162795, 'bbc earth': 162795, 'ted griffiths': 31910, 'nature documentary': 162795, 'animal documentary': 136501, 'bbc documentary': 162795, 'wildlife cameraman': 31910, 'antarctic': 31910, 'alucia productions': 31910, 'What Does It Take To Be A Blue Planet II Cameraman?': 31910, 'clean bandit': 37575, 'i miss you': 37575, 'live lounge': 37575, 'julie michaels': 37575, 'sarasota': 1338, 'sarasota police': 1338, 'city of sarasota': 1338, 'srq': 1338, '941': 1338, 'florida': 507224, 'sarasota pd': 1338, 'sarasotapd': 1338, 'traffic': 1878811, 'sarasota police traffic unit': 1338, 'traffic unit': 1338, 'suncoast': 1338, 'florida police': 1338, 'law enforcement': 507224, 'police officer': 1338, 'Vostok.bike': 1118, 'Brompton Bicycle': 1118, 'Brompton Alfine': 1118, 'Vostok Titanium Components': 1118, 'Oregon': 687, 'Ducks': 687, 'college athletics': 687, 'college basketball': 687, 'Tracktown USA': 687, 'Track & Field': 687, 'Hayward Field': 687, 'Autzen Stadium': 687, 'NIKE': 687, 'lord of the rings tv series': 8088, 'lord of the rings amazon': 8088, 'lord of the rings amazon original': 8088, 'lord of the rings tv': 8088, 'lord of the rings tv series everything to know': 8088, 'Hybrid Network': 8088, 'lord of the rings tv announced': 8088, 'lord of the rings tv silmarillion': 8088, 'Apple iPhone X': 9036, 'iPhone X One Week Later': 9036, 'iPhone X Accessories': 9036, 'iPhone X Real Review': 9036, 'iPhone X Overview': 9036, 'iPhone X Space Gray': 9036, 'iPhone X Cases': 9036, 'FCPX': 9036, 'iPhone X vs iPhone 8': 9036, 'Best iPhone 2018': 9036, 'iPhone X vs Galaxy Note 8': 9036, 'dbrand': 1076177, 'Google Pixel XL 2 vs iPhone X': 9036, 'Animoji': 9036, 'Portrait Mode': 9036, 'MMA': 1249702, 'Mixed Martial Arts': 1249702, 'Bellator': 1249702, 'Bellator MMA': 1249702, 'Fighting': 1506083, 'Fight': 1776636, 'Fighter': 1249702, 'UFC': 1249702, 'Ultimate Fighting Championship': 1249702, 'Strikeforce': 1249702, 'WWE': 1845522, 'Punch': 1452399, 'Kick': 1249702, 'Grappling': 1249702, 'MMA highlights': 1249702, 'MMA KO': 1249702, 'MMA Knockouts': 1249702, 'MMA best moments': 1249702, 'MMA fights': 1249702, 'MMA moves': 1249702, 'MMA best fights': 1249702, 'Analysis': 1249702, 'Sports Analysis': 1249702, 'Breakdown': 1249702, 'Boxing': 1249702, 'Martial Arts': 1249702, 'Karate': 1249702, 'BJJ': 1249702, 'Brazilian Jiu-Jitsu': 1249702, 'Judo': 1249702, 'Taekwondo': 1249702, 'Wrestling': 1249702, 'Combat': 1249702, 'Submission': 1249702, 'Knock out': 1249702, 'KO': 1249702, 'Spike': 1249702, 'Spike TV': 1249702, 'Sports Highlights': 1249702, 'Conor McGregor': 1337959, 'crab': 465893, 'bird': 8262386, 'biology': 824185, 'ecology': 496027, 'nature': 813878, 'Robotics': 1629187, 'Humanoid Robots': 1603415, 'Dynamic robots': 1603415, 'Rampage': 2585193, 'Rampage the Movie': 2585193, 'The Rock': 3305155, 'Dwayne Johnson': 3305155, 'gurilla': 2585193, 'george': 2778497, 'lizzy': 2585193, 'crispr': 2615327, 'dwayne': 5410118, 'warner': 2585193, 'wb trailer': 2585193, 'niomi harris': 2585193, 'Malin Akerman': 2585193, 'Jake Lacy': 2585193, 'Joe Manganiello': 2585193, 'P.J. Byrne': 2585193, 'Marley Shelton': 2585193, 'Breanne Hill': 2585193, 'Jack Quaid': 2585193, 'Matt Gerald': 2585193, 'Brad Peyton': 2585193, 'New Line': 2585193, 'New Line Cinema': 2585193, 'Dany Garcia': 2585193, 'Rampage World Tour': 2585193, 'WB Pictures': 3627651, 'Warner Bros Entertainment': 2585193, 'warner bros pics': 2585193, 'Rampage movie': 2585193, 'horror movie': 2336528, 'thriller movie': 2336528, 'science fiction': 2336528, 'sci fi': 2336528, 'emily blunt': 2336528, 'new movie': 8237047, 'new movie trailers': 2336528, 'movie trailer': 2340416, '2018 movies': 2336528, 'upcoming movies': 2336528, 'bob saget': 645131, 'hot ones': 4980366, 'spicy wings': 3927363, 'full house': 501670, 'hot wing challenge': 3927363, 'danny tanner': 645131, 'food challenge': 2304282, '10 questions': 2829106, '10 wings': 1960038, 'hot sauce': 4984753, 'pepper x': 501670, 'the last dab': 2304282, 'hot ones hot sauce': 2304282, 'hiccups': 501670, 'roast': 8193572, 'standup': 501670, 'miranda sings': 2558626, 'tinder': 334497, 'tinder takeover': 334497, 'miranda sings tinder': 334497, 'hijacks dating account': 334497, 'hijacks tinder account': 334497, 'dating profile': 334497, 'hijacks tinder': 334497, 'miranda sings funny': 334497, 'miranda sings 2017': 334497, 'colleen miranda sings': 334497, 'miranda sings challenge': 334497, 'miranda sings songs': 334497, 'mirandasings08': 2309470, 'miranda': 2309470, 'sings': 2309470, 'tinder account': 334497, 'tinder takeover miranda sings': 334497, 'miranda sings vanity fair': 334497, 'Luis': 499946, 'Fonsi': 499946, 'Demi': 8505393, 'Lovato': 8505393, 'Échame': 499946, 'La': 499946, 'Culpa': 499946, 'UMLE': 499946, 'Latino': 499946, 'emergence': 1164713, 'ants': 1164713, 'intelligence': 1344567, 'ant': 1164713, 'sum of its part': 1164713, 'more is more': 1164713, 'conciousness': 1164713, 'fascinating': 1164713, 'human': 1788709, 'cell': 1575766, 'kurzgesagt': 1164713, 'in a nutshell': 1164713, '73 qs': 3087097, '73 questions': 3087097, 'celeb style': 2935330, 'homes': 3017229, 'liza koshy 73 questions': 2935330, '73 questions vogue': 2935330, 'jet packinski': 3565692, '73': 2935330, 'jet packinski 73 questions': 2935330, '73 questions with helga': 2935330, 'liza koshy youtube': 2935330, 'liza koshy funny': 4300286, 'liza koshy funny moments': 4300286, 'liza koshy 73': 2935330, '73qs': 2935330, 'david dobrik': 2935330, 'liza koshy apartment': 2935330, 'liza koshy home': 2935330, 'liza koshy boyfriend': 2935330, 'real 73 questions': 2935330, 'parody 73 questions': 2935330, 'liza koshy parody': 2935330, 'BIGHIT': 17429472, '빅히트': 19017178, '방탄소년단': 26723679, 'BTS': 26804432, 'BANGTAN': 17429472, '방탄': 19017178, 'race': 2123845, 'poverty': 1512405, 'tickets': 505886, 'propublica': 505886, 'pedestrian': 1337843, 'infrastructure': 505886, 'walking': 2341521, 'walking while black': 505886, 'profiling': 505886, 'racial profiling': 505886, 'policing': 505886, 'jacksonville': 505886, 'duval county': 505886, 'citation': 505886, 'urban planning': 1224788, 'topher sanders': 505886, 'ben conarck': 505886, 'jaywalking': 505886, 'devonte shipman': 505886, 'JSO': 505886, 'florida times-union': 505886, 'African American': 505886, 'city planning': 1224788, 'pedestrian deaths': 505886, 'pedestrian safety': 505886, "Jacksonville Sheriff's Office": 505886, 'data': 505886, 'violations': 505886, 'electric drill': 178977, 'short animation': 529830, 'shelf': 178977, 'ledge': 178977, 'drill': 178977, 'OnePlus 5T': 1117997, 'OnePlus 5T preview': 1117997, 'OnePlus 5T review': 1117997, 'OnePlus 5T features': 1117997, 'OnePlus': 1548454, '5T': 1548454, 'OnePlus 6': 1117997, 'OnePlus 5T vs': 1117997, 'OnePlus camera': 1117997, 'OnePlus 5T camera': 1117997, 'jacksfilms': 5350967, 'yiay': 4303239, 'emojis': 1117510, 'emoji': 1117510, 'The Talk': 239239, 'Julie Chen': 239239, 'Aisha Tyler': 239239, 'Sharon Osbourne': 239239, 'Sara Gilbert': 239239, 'Sheryl Underwood': 239239, 'daytime talk': 239239, 'The View': 532743, 'redo you': 239239, 'issues': 239239, 'hot topic': 239239, 'discussions': 239239, 'Daytime': 239239, 'i tried period yoga pants': 1276326, 'period yoga pants': 1276326, 'period': 1663439, 'menstruation': 1663439, 'period clothes': 1276326, 'period leggings': 1276326, 'period products': 1276326, 'tampons': 1663439, 'pads': 1276326, 'girl': 2059631, 'feminine care': 1276326, 'girls': 1754852, 'yoga pants': 1276326, 'activewear': 1276326, 'safiya period': 1276326, 'safiya period videos': 1276326, 'what the period': 1276326, 'Blake': 340348, 'Shelton': 340348, 'Country music': 340348, 'Sexiest Man Alive': 340348, 'peoples magazine': 340348, "people's Sexiest Man Alive": 340348, 'Gigi Hadid': 1035804, 'Gives': 1035804, 'Only': 1064787, "Men's Pair": 1035804, 'EyeLoveMore': 1035804, 'Mules': 1035804, 'IMG Models': 1035804, "Victoria's Secret": 1247207, 'Gigi By Tommy Hilfiger': 1035804, 'American Music Awards': 3600154, 'surface book': 270859, 'surface book 2': 270859, 'microsoft surface book 2': 270859, 'microsoft surface book': 270859, 'microsoft surface': 270859, 'macbook': 270859, 'macbook pro': 270859, 'surface book 2 review': 270859, 'new surface book': 270859, 'new surface': 270859, 'microsoft': 270859, 'windows 10': 270859, 'laptop': 270859, 'tablet': 270859, 'windows': 270859, 'austin evans': 580711, 'pixel buds': 218153, 'google pixel buds': 218153, 'google pixel buds review': 218153, 'wireless earbuds': 218153, 'google assistant': 218153, 'airpods': 905474, 'translate': 218153, 'translation': 218153, 'the verge': 293933, 'verge': 293933, "sean o'kane": 218153, 'Sony Pictures': 177348, 'holiday movie': 672660, 'Christmas movie': 177348, 'The Star OST': 177348, 'The Story of the First Christmas': 177348, 'Steven Yeun': 177348, 'Gina Rodriguez': 2715086, 'Keegan-Michael Key': 177348, 'Kelly Clarkson': 355522, 'Fifth Harmony': 1538565, 'Kelsea Ballerini': 177348, 'Kirk Franklin': 177348, 'Zara Larsson': 177348, 'A Great Big World': 177348, 'Yolanda Adams': 177348, 'Jessie James Decker': 177348, 'Saving Forever': 177348, 'Casting Crowns': 177348, 'Bo': 177348, 'Ruth': 177348, 'All I Want For Christmas': 177348, 'All I Want For Christmas Is You': 177348, 'Mariah Christmas Song': 177348, 'Mariah Xmas Song': 177348, 'Mariah': 177348, 'Collegehumor': 4174473, 'CH originals': 4174473, 'adam ruins lab rats': 655161, 'adam ruins lab mice': 655161, 'the truth about lab mice': 655161, 'animal welfare': 655161, 'animal testing': 655161, 'lab animals': 655161, 'adam ruins lab animals': 655161, 'holosexual': 3069532, 'holo': 3069532, 'holographic': 4077994, 'diamond': 1693011, 'glitter': 1989830, 'holo glitter': 1693011, 'latte': 1693011, 'cappuccino': 1693011, 'diy latte': 1693011, 'holo cappuccino': 1693011, 'diamond cappuccino': 1693011, 'glitter coffee': 1693011, 'gold coffee': 1693011, 'edible glitter': 1693011, 'food glitter': 1693011, 'drink glitter': 1693011, 'disco dust': 1693011, 'cake glitter': 1693011, 'fancy cappucino': 1693011, 'diy cappuccino': 1693011, 'simplybakelogical': 1693011, 'edible holo': 1693011, 'auth-jking413-auth': 5938, 'Jay-King': 5938, 'victoria secret fashion show': 459591, 'vs fashion show': 895486, 'vsfs': 597857, "victoria's secret": 1207723, 'vs': 4993903, 'karlie kloss': 2175086, 'klossy': 901730, 'karlie': 2001115, 'kloss': 2001115, 'shanghai': 727759, 'shanghai china': 206393, 'china': 1261549, 'moon juice': 76491, 'brad kale chip': 76491, 'nutritionist': 76491, 'dietitian': 76491, 'healthy snacks': 76491, 'healthy on the road': 76491, "what's in my bag": 76491, 'hillary': 769769, 'clinton': 778905, 'donald': 1158849, 'trump': 11070722, 'justice': 769769, 'department': 769769, 'jeff': 769769, 'sessions': 769769, 'impeachment': 769769, 'blvd': 769769, 'jeff sessions': 769769, 'department of justice': 769769, 'impeach hillary': 769769, 'Matt Reeves': 39504, 'Ben Affleck': 48949, 'Villains': 39504, 'Basmati Blues': 39504, 'Brie Larson': 39504, 'Movies': 4878474, 'blissing me': 81947, 'björk': 225642, 'Indie Pop': 225642, 'utopia': 225642, 'one little indian records': 81947, 'bjork': 315848, 'DJ Snake': 673010, 'Lauv': 673010, 'A Different Way': 673010, 'Arc De Triomphe': 673010, 'Paris': 673010, 'DJ': 673010, 'Snake': 673010, 'Different': 673010, 'Geffen': 673010, 'Dance': 673010, 'Total Divas': 326709, 'The Miz': 326709, 'Maryse': 326709, 'iPhone 10 Review': 516456, 'iphone 2017': 516456, 'iphone x hands on': 3929136, 'iphone x vs': 3929136, 'iphone x vs iphone 8': 3929136, 'iphone x specs': 516456, 'animoji': 516456, 'iphone 10 review': 516456, 'iphone x vs 8': 3929136, 'smartphone': 946913, 'iPhone X Camera': 516456, 'iphone x vs iphone 8 plus': 3929136, 'iphone ten': 516456, 'game theory': 131677, 'film theory': 1513734, "don't deal with the devil": 55460, 'matpat': 1513734, 'gtlive': 131677, 'butch hartman': 131677, 'fairly oddparents': 131677, 'danny phantom': 131677, 'nickelodeon': 408121, 'global warming': 558560, 'climate change': 2802149, 'global climate models': 116732, 'computer models': 116732, 'supercomputers': 116732, 'atmosphere': 116732, 'hindcasting': 116732, 'temperature increase': 116732, 'un reports': 116732, 'ice sheets': 116732, 'sea level rise': 482780, 'melting ice sheets': 116732, 'carbon dioxide': 482780, 'greenhouse gasses': 116732, 'greenhouse effect': 116732, 'us global change research program': 116732, 'melting ice caps': 1997553, 'growing season': 116732, 'hurricanes': 482780, 'hurricane season': 116732, 'hurricane landfalls': 116732, 'irma': 116732, 'harvey': 223182, 'ocean warming': 116732, 'collider': 65241, 'movie talk': 235838, 'deadpool': 93909, 'deadpool 2': 59190, 'bob ross': 59190, 'rotten tomatoes': 257090, 'dc': 93909, 'mcu': 73131, 'quentin tarantino': 100623, 'super mario bros': 59190, 'halloween': 501582, 'michael myers': 59190, 'danny mcbride': 59190, 'what women want': 59190, 'taraji p henson': 59190, 'nba countdown': 113926, 'ben simmons': 113926, 'ben simmons rookie': 113926, 'ben simmons highlights': 113926, 'ben simmons nba countdown': 113926, 'ben simmons espn': 113926, 'sixers': 113926, 'best rookies': 113926, 'nba rookies': 113926, 'rookie highlights': 113926, 'best nba highlights': 113926, 'nba espn': 113926, 'espn nba': 113926, 'Music video': 174757, 'mv': 174757, 'linkin park': 174757, 'isa': 174757, 'isatv': 174757, 'internation secret agents': 174757, 'dumbfoundead': 174757, 'korean': 345155, 'kpop': 2684310, 'escape from L.A.': 174757, 'LA': 238778, 'los Angeles': 174757, 'pbs digital studios': 76475, 'pbs': 76475, 'joe hanson': 76475, "it's okay to be smart": 76475, 'its okay to be smart': 76475, "it's ok to be smart": 76475, 'its ok to be smart': 76475, 'public broadcasting service': 76475, 'moon': 827853, 'moon cycles': 76475, 'moon phases': 76475, 'lunar cycles': 76475, 'lunar biological cycles': 76475, 'lunar biology': 76475, 'biological clock': 76475, 'biological rhythms': 76475, 'moonlight': 76475, 'tides': 76475, 'reproductive cycles': 76475, 'circadian rhythm': 76475, 'itsokaytobesmart': 76475, 'neuroscience': 106609, 'ocean': 124859, 'time': 1521187, 'clock': 76475, 'rhythms': 76475, 'circadian clock': 76475, 'Get It Right Diplo feat. Mø': 360001, 'Get it Right': 360001, 'Get It Right Song': 360001, 'Major Lazer': 360001, 'Give Me Future Soundtrack': 360001, 'Give Me Future Documentary': 360001, 'Get It Right Mø': 360001, 'Get it right momomoyouth': 360001, 'Get It Right Diplo': 360001, 'Diplo': 360001, 'Major Lazer Documentary Soundtrack': 360001, 'Momomoyouth': 360001, 'Mø new song': 360001, 'Diplo new song': 360001, 'Mayor lazor': 360001, 'Mayor lasor': 360001, 'mØ': 360001, 'moe': 360001, 'official lyric video diplo': 360001, 'mØ official lyric video': 360001, 'major laser': 360001, 'get it right lyric video': 360001, 'diplo get it right': 360001, 'diplo': 360001, 'major lazer': 360001, 'walshy fire': 360001, 'MTV': 286360, 'American Idol': 1742857, 'Beyonce': 4180037, 'Flash Mob': 113483, '90s Disney': 113483, 'Scooter Braun': 113483, 'Mashup': 274115, 'Parody': 1095326, 'Ratchet': 113483, 'Ghetto': 113483, 'Grown Woman': 113483, 'Evolution of Disney': 113483, 'Low': 133044, 'Wizard of Ahhhs': 113483, 'Mean Gurlz': 113483, 'Hungry Games': 113483, 'Reality Show': 113483, 'katherine fairfax wright': 69199, 'call me kuchu': 69199, 'pride': 127162, 'pulse': 69199, 'mass shooting': 69199, 'gun violence': 69199, 'racism': 507178, 'acceptance': 1743956, 'artist': 2037272, 'r&B': 69199, 'netlifx': 69199, 'itunes': 70808, 'hulu': 69199, 'dance': 3032302, 'dua lipa': 297668, 'golden slumbers': 276231, 'xmas songs': 276231, 'christmas music': 9571083, 'john lewis advert': 276231, 'festive': 1536449, 'spa': 6582, 'rose': 44176, 'nyc': 4112901, 'Philadelphia': 12831, 'Sixers': 12831, 'Ben': 12831, 'Simmons': 12831, 'Los Angeles': 43262, 'Lakers': 12831, 'story': 324298, 'storytime': 417658, 'stories': 194704, '90s': 405174, 'child': 2171389, 'childhood': 1240329, 'memories': 77486, 'mascara': 72921, 'lashes': 72921, 'long lashes': 22383, 'eyelashes': 22383, 'maybelline': 22383, 'waterproof mascara': 22383, 'eyelash hacks': 22383, 'eyelash': 22383, 'beauty review': 22383, 'drugstore makeup': 745819, 'industry': 22383, 'affordable makeup': 22383, 'everyday makeup': 881101, 'full face': 410998, 'fall makeup': 22383, 'morning routine': 942366, 'makeup haul': 39755, 'makeup routine': 124172, 'new drugstore makeup': 22383, 'makeup tips': 22383, 'production line': 34435, 'how stuff is made': 41180, 'maybelline mascara': 22383, 'drugstore mascara': 22383, 'Kesha': 28169, 'Learn To Let Go': 28169, 'Devel': 298744, 'Sixteen': 298744, 'Devel Sixteen': 298744, 'Hypercar': 298744, 'Drag Racer': 298744, '5000hp': 298744, 'Crazy': 298744, 'Fast': 298744, 'Ridiculous': 298744, 'Insane': 298744, 'Dubai': 298744, 'UAE': 298744, 'Motorshow': 298744, 'Launch': 342518, 'Premiere': 298744, 'Dubai Motorshow': 298744, 'DIMS': 298744, 'Exclusive': 298744, 'Introduction': 298744, 'Intro': 298744, 'V16': 298744, '5000bhp': 298744, 'Fastest': 298744, 'Top Speed': 298744, 'Shmee': 298744, 'Shmee150': 298744, 'The TODAY Show': 1874161, 'TODAY Show': 1874161, 'TODAY': 1874161, 'NBC News': 1874161, 'Celebrity Interviews': 1874161, 'TODAY Show Recipes': 1874161, 'Fitness': 1918233, 'Lifestyle': 1986777, 'TODAY Show Interview': 1874161, 'Ambush Makeover': 1874161, 'Kathie Lee and Hoda': 1874161, 'KLG and Hoda': 1874161, 'olympic gold medalist aly raisman': 83497, 'aly raisman': 83497, 'aly raisman interview': 83497, 'sexual abuse allegations': 83497, 'team us doctor': 83497, 'team usa doctor larry nassar': 83497, 'larry nassar sexual abuse allegation': 83497, 'usa gymnastics leaders': 83497, 'gymnastics': 277607, 'sexual assault': 1195512, 'gymnast': 83497, 'olympic': 83497, 'doctor': 434779, 'sexual abuse': 83497, 'maroney': 83497, 'abuse': 83497, 'usa gymnastics': 83497, 'industrial': 8043, 'time lapse': 8043, 'product': 8043, 'development': 8043, 'tutorial video': 8043, 'products': 29052, 'rendering': 8043, 'sketching': 8043, 'product design': 8043, 'product development': 8043, 'prototype': 167413, 'vacuum forming': 3591, 'laser cutting': 3591, 'solvent welding': 3591, 'solvent bonding': 3591, 'industrial design': 3591, 'low volume production': 3591, 'computer aided design': 3591, 'REN shape': 3591, 'auralnauts': 66800, 'auralnauts star wars': 66800, 'Darth Vader': 941967, 'Darth Vader Helmet': 66800, 'James Earl Jones': 66800, 'Anakin Skywalker': 66800, 'Darth Vader Mask': 66800, 'Darth Vader real voice': 66800, 'Hayden Christensen': 66800, '60 Minutes': 42422, 'Raisman': 42422, 'Aly Raisman': 42422, 'Larry Nassar': 42422, 'Nassar': 42422, 'US National Team': 42422, 'USA Gymnastics': 42422, 'Paramore': 823285, 'Parmore': 823285, 'Paramor': 823285, 'Para more': 823285, 'fake happy': 823285, 'fake happy too': 823285, "i'm sure everybody here is fake happy too": 823285, 'Hayley Williams': 823285, 'Taylor York': 823285, 'Zac Farro': 823285, 'New Paramore Song': 823285, 'new Paramore album': 823285, 'After Laughter': 823285, 'After Lafter': 823285, "paramore's new song": 823285, "paramore's new album": 823285, 'new music from paramore': 823285, 'Hayley williams new song': 823285, '5more': 823285, 'paramore5': 823285, 'new paramore video': 823285, 'paramore new video': 823285, 'upside down smiley face': 823285, 'upside down smile face': 823285, 'ucla': 366048, 'environmentalism': 663971, 'global trade': 366048, 'fossil fuels': 366048, 'climate labs': 366048, 'convervationist': 366048, 'renewable energy': 721821, 'online shopping': 366048, 'environment': 2627324, 'environmental impact': 366048, 'cost': 608246, 'sustainability': 824501, 'extreme weather': 366048, 'weather': 370684, 'emissions': 366048, 'un': 366048, 'temperature': 366048, 'climate': 1563231, 'climate lab': 663971, 'Chris': 190631, 'Stapleton': 190631, "Tryin'": 133092, 'Untangle': 133092, 'My': 133092, 'Mind': 133092, 'Mercury': 190631, 'Search Party': 178325, 'Tim McGraw': 178325, 'Faith Hill': 178325, 'country': 262596, 'The Rest Of Our Life': 178325, 'Sundown Heaven Town': 178325, 'Joy To The World': 178325, 'divorce': 195327, 'The Roots': 178325, 'trucks': 633614, 'drinking': 178325, 'Fireflies': 178325, 'taxes': 522864, 'government': 595323, 'learning': 641848, 'income tax': 81759, 'capital gains': 81759, 'congress': 124279, 'senate': 1493652, 'republicans': 3578645, 'vote': 81759, 'Red Velvet': 4340962, '레드벨벳': 4340962, '피카부': 4340962, 'PeekABoo': 4340962, 'Peek-A-Boo': 4340962, '아이린': 4340962, '웬디': 4340962, '슬기': 4340962, '조이': 4340962, '예리': 4340962, 'IRENE': 4340962, 'WENDY': 4340962, 'SEULGI': 4340962, 'JOY': 4340962, 'YERI': 4340962, 'Perfect Velvet': 4340962, 'smtown': 4340962, 'sm entertainment': 4340962, 'sment': 4340962, 'Green Day': 646615, 'Back in the USA': 646615, "god's favorite band": 646615, 'billie joe armstrong': 646615, 'tre cool': 646615, 'Mike Dirnt': 646615, 'Hailee': 1008584, 'Steinfeld': 1008584, 'Alesso': 1008584, 'Let': 1008584, 'Universal': 751525, 'Hailee Steinfeld new music': 751525, 'Let Me Go video': 751525, 'Alesso and Hailee Steinfeld': 751525, 'Hailee Steinfeld and Florida Georgia Line': 751525, 'Hailee and watt song': 751525, 'AMA performances': 751525, 'LETMEGOxAMAs': 751525, 'Most dangerous drug': 803168, 'Most lethal drug': 803168, 'Is alcohol dangeorus': 803168, 'science of drugs': 803168, 'war on drugs': 803168, 'your brain on drugs': 803168, 'your brain on heroin': 803168, 'is heroin the most dangerous drug': 803168, 'fentanyl': 803168, 'opioid crisis': 803168, 'is alcohol bad for you': 803168, 'alcohol poisoning': 803168, 'harm reduction': 803168, 'drug related crime': 803168, 'what drugs are legal': 803168, 'how to assess risk': 803168, 'why are drugs addictive': 803168, 'what are drugs': 803168, 'ld50': 803168, 'prescription drugs': 803168, 'lil peep': 803168, 'drug overdose': 803168, 'Cheerios': 185707, 'Lin-Manuel Miranda': 185707, 'Lin-Manuel': 185707, 'Miranda': 185707, 'Macey Hensley': 185707, 'kid expert': 185707, 'hamilton broadway': 185707, 'founding fathers': 185707, 'Yas Queen': 33260, 'Yass Queen': 33260, 'Yasss Queen': 33260, 'Transparent': 33260, 'Jeffrey Tambor': 33260, 'secret': 1444495, 'protocal': 544230, 'procedure': 544230, 'process': 616744, 'when': 1242656, 'queen': 682793, 'dies': 544230, 'Queen Elizabeth': 544230, 'Queen of England': 544230, 'death': 675660, 'hyde park corner': 544230, 'the monarch': 544230, 'monarchy': 544230, 'england': 1376187, 'uk': 1615300, 'united kingdom': 544230, 'commonwealth': 544230, 'unknown': 544230, 'obit light': 544230, 'tab for a cause': 544230, 'half as interesting': 1695299, 'hai': 1339526, 'wendover': 1695299, 'productions': 900003, 'wendover productions': 1695299, 'fast': 898254, 'interesting': 1616490, 'educative': 544230, 'coronation': 544230, 'economic impact': 544230, 'real life lore': 681870, 'real life lore maps': 681870, 'real life lore geography': 681870, 'real life maps': 681870, 'world map': 681870, 'world map is wrong': 681870, 'world map with countries': 681870, 'world map real size': 681870, 'map of the world': 681870, 'world geography': 681870, 'geography': 1260896, 'geography (field of study)': 681870, 'facts you didn’t know': 681870, 'computer': 226374, 'upload your brain': 187278, 'upload your mind': 187278, 'mind uploading': 187278, 'mind upload': 187278, 'upload': 605770, 'immortality': 187278, 'upload consciousness': 187278, 'upload memory': 187278, 'what happens to you if': 187278, 'computers': 187278, 'ultra records': 248793, 'ultra music': 248793, 'ultrarecords': 248793, 'ultramusic': 248793, 'house': 1285469, 'premiere': 1984547, 'ultra': 248793, 'steve': 276439, 'aoki': 248793, 'lauren': 540426, 'jauregui': 248793, 'steve aoki': 248793, '5 harmony': 248793, 'all night': 248793, 'steve aoki all night': 248793, 'steve aoki lauren jauregui all night': 248793, 'lauren jauregui all night': 248793, 'lauren jaregui': 248793, 'all night steve aoki lauren jauregui': 248793, 'lauren jauregui fifth harmony': 248793, 'Fifth Harmony - Work from Home ft. Ty Dolla $ign': 248793, 'Steve Aoki & Louis Tomlinson - Just Hold On (Official Video)': 248793, 'Shade45': 26393, 'SiriusXM': 26393, 'Walk on Water': 4066554, 'the real': 608194, 'daytime': 639400, 'tamera mowry': 608194, 'adrienne bailon': 608194, 'loni love': 608194, 'jeannie mai': 608194, 'dice': 123751, 'cheating': 123751, 'loaded dice': 123751, 'woodworking': 1110117, 'Will Smith': 1563047, 'Orc': 985571, 'Soundtrack': 1816944, 'Home': 1158565, 'Machine Gun Kelly': 985571, 'MGK': 985571, 'Bebe Rexha': 985571, 'X Ambassadors': 985571, 'Macklemore': 151313, 'Naked': 329122, 'Justin Bieber': 31816, 'Painting': 31816, 'Rapper': 31816, 'naked painting': 31816, 'Macklemore wwhl': 31816, 'T-Pain': 31816, 'TPain': 31816, 'Justin Bieber painting': 31816, "Macklemore's painting of Justin Bieber": 31816, 'bravo late night': 121443, 'wwhl after show': 121443, 'Tpain wwhl': 31816, 'wwhl caller': 31816, 'Macklemore and Justin Bieber': 31816, 'bieber': 31816, 'macklemore bieber painting': 31816, 'bieber syrup painting': 31816, 'macklemore bieber': 31816, 'lovelaurenelizabeth': 50702, 'livelaurenelizabeth': 50702, 'lolufullyloaded': 50702, 'elizabeth': 50702, 'violet benson': 50702, 'chloe parr': 50702, 'lauren elizabeth': 50702, 'daddyissues': 50702, 'awesomenesstv': 50702, "macy's": 50702, 'gift exchange': 50702, 'white elephant': 50702, 'gift guide': 287162, 'party': 1574170, 'intense': 50702, 'Macy’s': 50702, 'holidays': 5465461, 'gifting': 50702, 'presents': 50702, 'best gift': 50702, 'shopping guide': 50702, 'xmas': 4352740, 'hanukkah': 318630, 'jewelry': 125559, 'huge haul': 89852, 'fall haul': 89852, 'try on haul': 107224, 'fall fashion': 89852, 'casual fashion': 89852, 'jenn im haul': 89852, 'korean fashion': 89852, 'clothesencounters': 557101, 'clothes encounters': 557101, 'EMAS': 172877, 'FASHION REVIEW': 172877, 'kristen mcatee': 172877, 'Hooked': 57818, 'It': 491796, 'lucie fink': 53502, 'lucie r29': 113425, '5 day challenge': 113425, 'try living with lucie': 53502, 'viral challenge': 53502, 'DIY life hacks': 53502, 'DIY makeup': 73361, 'DIY Beauty': 53502, 'tests': 643329, 'makeup look': 348640, 'lip balm': 53502, 'get ready morning routine': 53502, 'projects': 53502, 'makeup collection': 53502, 'crafts': 6539146, 'makeup ideas': 53502, 'the rest of our life': 18346, 'soul to soul': 18346, 'soul 2 soul': 18346, 'break first': 18346, 'this kiss': 18346, 'humble and kind': 18346, 'Arista Nashville': 18346, 'Cowboy Lullaby (Audio)': 18346, 'Tim McGraw & Faith Hill': 18346, 'tracee': 64209, 'ellis': 64209, 'ross': 916926, 'cher': 21007, 'michael': 21007, 'jackson': 21007, 'diana': 106587, 'vacation': 21007, 'black-ish': 208994, 'tennis': 9621936, 'tracee ellis ross': 252196, 'diana ross': 64209, 'michael jackson': 21007, 'streaming': 1449191, '08282016NtflxUSCAN': 2127485, 'a christmas prince': 47358, 'rom com': 47358, 'romance': 1412199, 'Christmas Day': 47358, 'Christmas Eve': 47358, 'Rose McIver': 47358, 'Ben Lamb': 47358, 'Alice Krige': 47358, 'funnyordie': 36294, 'funny or die': 126348, 'lol': 2143272, 'The B Team': 9445, 'Barb Wire': 9445, 'Pamela Anderson': 9445, 'Howard the Duck': 9445, 'Shaq': 9445, "Shaquille O'Neal": 9445, 'Steel': 9445, 'Blankman': 9445, 'Damon Wayans': 9445, 'The Flash': 86378, 'Kardashian Holiday': 304954, 'Sneak Peek': 404520, 'Keeping Up With the Kardashians': 304954, 'A Very Kardashian Holiday': 484951, 'mayim bialik': 87795, 'bialik mayim': 60570, 'the big bang theory': 87795, 'big bang theory': 60570, 'bialik': 46197, 'mayim': 46197, 'turkey': 1387539, 'LAUV': 178887, 'PARIS IN THE RAIN': 178887, 'EASY LOVE': 178887, 'THE OTHER': 178887, 'MUSIC VIDEO': 178887, 'bakery': 109431, 'cookie': 64813, 'cookies': 444052, 'biscuits': 64813, 'pastry': 585728, 'shortcrust pastry': 64813, 'filling': 64813, 'nutella': 64813, 'nutela': 64813, 'hazelnut': 64813, 'hazelnut paste': 64813, 'choc chips': 64813, 'chips': 387631, 'dough': 64813, 'rolled': 64813, 'blind baking': 64813, 'all in one pie': 64813, 'tart': 64813, 'tart case': 64813, 'puff pastry': 64813, 'gemma wilson': 64813, 'bake shop': 64813, 'home baking': 64813, 'quick': 800394, 'UCLA': 25521, 'UCLA basketbal': 25521, 'UCLA basktebal players': 25521, 'UCLA Basketbal china': 25521, 'UCLA Basketball China': 25521, 'UCLA Basketball China Arrest': 25521, 'UCLA China Arrest': 25521, 'Basketball players china': 25521, 'basketball players arrest china': 25521, 'college basketball china': 25521, 'college basketbal players arrest china': 25521, 'ucla basketball players releasted': 25521, 'ucla basketball players return': 25521, 'us news': 2956576, 'ranz': 331642, 'ranz kyle': 331642, 'niana': 331642, 'niana guerrero': 331642, 'ranz niana': 331642, 'ranz and niana': 331642, 'ranz kyle niana': 331642, 'niana ranz': 331642, 'siblings': 331642, 'sibling goals': 331642, '#siblinggoals': 331642, 'goals': 1956120, 'vlogs': 7093914, 'dance choreography': 331642, 'la': 593148, 'la vlog': 331642, 'airport': 429684, 'kid': 402906, 'goes to la': 331642, 'season 2': 2732827, 'carpool karaoke': 331642, 'roadtrip': 331642, 'scary': 331642, 'prank': 732909, 'pranks': 1380575, 'hawkins': 331642, 'headquarters': 331642, 'facebook': 331642, 'pencils': 210266, "how it's made": 229063, 'science channel': 210266, 'factory': 210266, 'INSIDER design': 268372, 'business insider': 703653, 'tech insider': 703653, 'nbc news': 216695, 'current events': 1909817, 'top stories': 272637, 'trump live': 38528, 'president trump': 166627, 'white house': 52053, 'motor sport': 329578, 'cardi b': 293758, 'nicki minaj': 452100, 'migos': 329578, 'quavo': 329578, 'choreography': 293758, 'matt steffanina': 293758, 'hip hop dance': 293758, 'offset': 293758, 'wedding': 986443, 'cardi b offset': 293758, 'cardi b proposal': 293758, 'rapping': 293758, 'kenneth san jose': 293758, 'ken san jose': 293758, '80 fitz': 293758, 'beatbox': 293758, 'MisterWives': 7544, 'Oh': 7544, 'Love': 6390681, 'Photo': 7544, 'Finish': 7544, 'Republic': 7544, 'littleBits': 1387, 'starwars': 1663329, 'inventorswanted': 1387, 'droid': 365924, 'thelastjedi': 167882, 'r2d2': 1387, 'r2d2toy': 1387, 'giftguide': 1387, 'forceawakens': 1387, 'religion of sports': 11807, 'gotham chopra': 11807, 'tom brady': 11807, 'michael strahan': 11807, 'religion of sports strahan': 11807, 'religion of sports brady': 11807, 'words': 13676, 'playing the game': 13676, 'teamwork': 13676, 'team': 2946804, 'athletics': 13676, 'literacy': 13676, 'literature': 13676, 'great minds': 13676, 'typing': 13676, 'Pixar': 4707527, 'Disney Pixar': 4707527, 'Pixar Movie': 4707527, 'the incredibles': 4707527, 'incredibles 2': 4707527, 'jack jack': 4707527, 'elastigirl': 4707527, 'mr. incredible': 4707527, 'dash': 4707527, 'edna mode': 4707527, 'new look': 4707527, 'teaser': 5653413, 'first look': 4707527, 'sneak peek': 4707527, 'family movie': 4707527, 'Selena': 2959054, 'Gomez': 2959054, 'Marshmello': 3078551, 'Wolves': 2959054, 'zimbabwe': 417911, 'coup': 492246, 'military coup': 396346, 'mugabe': 396346, 'robert mugabe': 417911, 'mnangagwa': 396346, 'africa': 396346, 'rhodesia': 396346, 'bob mugabe': 396346, 'grace mugabe': 396346, "cou d'etat": 396346, 'conflict': 396346, 'sub saharan africa': 396346, 'protests': 396346, 'army': 396346, 'tanks': 396346, 'harare': 396346, 'dictator': 396346, 'corruption': 396346, 'zanu pf': 396346, 'politicians': 396346, "coup d'etat": 396346, 'south africa': 396346, 'african': 604573, 'gucci grace': 396346, 'Roman Reigns': 1781688, 'Randy Orton': 1188326, 'Aja Kong': 1188326, 'Beth Phoenix': 1188326, 'André the Giant': 1188326, 'Ultimate Warrior': 1188326, 'Ken Shamrock': 1188326, 'Dolph Ziggler': 1188326, 'Dwayne The Rock': 1188326, 'Johnson': 1908288, 'wwe top matches': 1188326, 'wwe top 10': 1781688, 'pumpkin spice': 322818, 'flavored': 632613, 'psl': 322818, 'spiced': 322818, 'crazy': 1150401, 'tasty': 2196113, 'delicious': 533938, 'opening': 958232, 'yummy': 322818, 'for kids': 1457809, 'justine': 322818, 'justine ezarik': 322818, 'blog': 322818, 'youtubers': 1572542, 'collab': 1048968, 'treats': 322818, 'knife': 536557, 'marshmallows': 322818, 'doritos': 322818, 'potato': 1026865, 'trying': 322818, 'idea': 632613, 'oreo': 322818, 'caramel': 322818, 'werthers': 322818, 'cereal': 487224, 'hersheys': 322818, 'pretzels': 322818, 'coated': 322818, 'dipped': 322818, 'trader joes': 322818, 'whole foods': 329153, 'fall': 525950, 'seasonal': 440229, 'Episode 1731': 4061207, 'Chance the Rapper': 4061207, 'Leslie Jones': 3728411, 'Kenan Thompson Pete Davidson': 21046, 'Cecily Strong': 3823427, 'Heidi Gardner': 21046, 'Luke Null': 21046, 'Kyle Mooney': 181188, 'Chance the Rapper Monologue': 21046, 'Melissa Villasenor': 3728411, 's43e6': 4061207, 'episode 6': 4061207, 'Chance': 4061207, 'Chicago': 4956559, 'Coloring Book': 4061207, 'I’m the One': 28843365, 'Justin Verlander': 195386, 'Kate Upton': 195386, 'Missed': 195386, 'Because': 195386, 'World Series': 195386, 'model': 1307592, 'Houston Astros': 195386, 'Detroit Tigers': 195386, 'pitcher': 195386, 'The Other Woman': 195386, 'Sports Illustrated': 195386, 'The Layover': 195386, 'The Disaster Artist': 2047630, 'Thomas Sanders': 224394, 'thomas sanders vine': 224394, 'thomas sanders vlog': 224394, 'thomas sanders channel': 224394, 'thatsthat24': 101553, 'fosterdawg': 101553, 'foster dawg': 101553, 'fanders': 101553, 'vine': 224394, 'thomas': 1322640, 'sanders': 224394, 'ultimate': 224394, 'no. 6': 101553, 'dragonball super': 101553, 'dragonball': 101553, 'thomas sanders vines': 224394, 'anime': 1611572, 'vine guy': 224394, 'storytime guy': 224394, 'narrator guy': 224394, 'real or fake anime': 101553, 'crunchyroll': 101553, 'gundam': 361471, 'bodacious space pirates': 101553, 'black butler': 101553, 'soul captor': 101553, 'hunter x hunter': 101553, 'erased': 101553, 'yu gi oh': 101553, 'yugioh': 101553, 'sonic the hedgehog': 101553, 'knuckles': 101553, 'knuckles the echidna': 101553, 'black clover': 101553, 'GT Series': 831968, 'Qualification Race': 831968, 'Macau Grand Prix': 831968, 'FIA GT World Cup': 831968, 'Pile Up': 831968, 'traffic jam': 831968, 'start': 831968, '1st lap': 831968, 'big crash': 831968, 'carnage': 831968, 'racing': 1085875, 'wtf': 2193450, 'Circuito da Guia': 831968, 'Sports car racing': 831968, 'Guia Circuit': 831968, 'Macau Peninsula': 831968, 'Macau (China)': 831968, '#MacauGP': 831968, 'red flag': 831968, 'Hiroki Yoshimoto': 831968, 'Chaz Mostert': 831968, 'Fabian Plentz': 831968, 'Robin Frijns': 831968, "Darryl O'Young": 831968, 'Romain Dumas': 831968, 'Mirko Bortolotti': 831968, 'Marco Wittmann': 831968, 'Renger Van der Zande': 831968, 'Felix Rosenqvist': 831968, 'Lucas di Grassi': 831968, '#GTWorldCup': 831968, 'Meanwhile at Macau': 831968, 'Macau street circuit': 831968, 'Tesla Roadster': 2280611, 'Tesla Roadster 2.0': 2280611, 'Roadster 2': 2280611, '2020 Tesla Roadster': 2280611, '2020 Roadster': 2280611, 'fastest productioncar': 2280611, '0-60': 2280611, 'Tesla Roadster launch': 2280611, 'Tesla roadster': 2280611, 'Tesla Roadster 2.5': 2280611, 'Tesla launch': 2280611, 'Tesla Roadster vs': 2280611, 'Roadster vs': 2280611, 'Roadster 0-60': 2280611, 'chicken parm pizza': 370302, 'quality italian': 370302, 'chicken crust pizza': 370302, 'chicken parmesan pizza': 370302, 'chicken base pizza': 370302, 'nyc chicken parm pizza': 370302, 'nile wilson': 194110, 'nile wilson gymnastics': 194110, 'nile wilson olympics': 194110, 'olympic gymnast': 194110, 'amazing gymnastics': 194110, 'strength training': 194110, 'strength': 194110, 'hard work': 194110, 'success': 471103, 'yoga challenge with tom daley': 194110, 'tom daley': 194110, 'gone sexual': 194110, 'tom daley vs nile wilson': 194110, 'tom daley sexual': 194110, 'yoga': 194110, 'trying yoga': 194110, 'ultimate gymnastics challenge': 194110, 'gymnastics meets yoga': 194110, 'anthony padilla': 551566, 'jack douglass': 240931, 'jon cozart': 240931, 'paint': 897998, 'joey graceffa': 439621, 'alex wassabi': 289735, 'laurdiy': 812519, 'merch': 1717098, 'shirts': 240931, 'dad hat': 240931, 'high quality merch': 390162, 'anthony padilla smosh': 390162, 'anthony padilla merch': 390162, 'merch anthony padilla': 240931, 'smosh anthony padilla': 402335, 'anthony padilla merchandise': 240931, 'merchandise anthony padilla': 240931, 'rclbeauty101': 467983, "don't hurt me": 467983, 'you are not alone': 481093, 'I am ugly': 467983, 'PSA': 467983, 'anti bullying': 1630826, 'any cyber cullying': 467983, 'cyber bully': 467983, 'confidence': 676210, 'maddie': 680389, 'ziegler': 680389, 'mckeen': 365523, 'dancer': 680389, 'chandelier': 680389, 'sia': 680389, 'dancemoms': 680389, 'good morning britain': 52602, 'breakfast show': 52602, 'morning news': 52602, 'gmb': 52602, 'good morning britain interview': 52602, 'itv': 1358974, 'piers morgan': 52602, 'susanna reid': 52602, 'Talk Shows - Topic': 52602, 'Will Ferrell': 52602, "Daddy's Home 2": 648422, 'Stream': 52602, 'Step Brothers': 52602, 'marky Mark': 52602, 'Sia Ho Ho Ho Holiday': 610459, 'fanny': 43202, 'pack': 623998, 'marshalls': 43202, 'memory': 43202, 'mom': 142053, 'daughter': 43202, 'fanny pack': 43202, 'Tove': 665727, 'Lo': 126419, 'cycles': 367988, 'Imagine': 433978, 'Dragons': 433978, 'Miss': 433978, 'Congeniality': 433978, 'Whatever': 433978, 'Takes': 433978, 'KIDinaKORNER/Interscope': 433978, 'Chloe x Halle': 7108, 'I Say So': 7108, 'Parkwood Entertainment/Columbia': 7108, 'R&B/Soul': 7108, 'Trucks': 12212, 'Weather': 14912, 'Croatia': 12212, 'windy': 12212, 'small': 759754, 'strong': 12212, 'blow': 12212, 'fsu': 920, 'florida state': 920, 'Seminoles': 920, 'Seminole': 920, 'noles': 920, 'nole': 920, 'florida state university': 920, 'f.s.u.': 920, 'ncaa': 20253, 'college': 308678, 'john thrasher': 920, 'jimbo': 920, 'jimbo fisher': 920, 'coach': 132350, 'fsu football': 920, 'florida state football': 920, 'seminoles football': 920, 'john': 460401, 'thrasher': 920, 'fisher': 229008, 'noles football': 920, 'president': 93417, 'fsu president': 920, 'forever': 581716, 'Textron': 3748, 'Scorpion': 3748, 'ScorpionJet': 3748, 'Textron Defense': 3748, 'Light Attack': 3748, 'ISRT': 3748, 'O-AX': 3748, 'Tegan and Sara': 5258, 'The Con X: Covers': 5258, 'The Con': 5258, 'Tegan': 5258, 'Sara': 5258, 'Sara Bareilles': 1634632, 'Floorplan': 5258, 'Music Video': 640482, 'commerical': 15382, 'y2k': 15382, 'sing': 289006, 'along': 15382, 'DNS': 4759, 'privacy': 4759, 'security': 4759, 'Gin Wigmore': 4843, 'Cabrona': 4843, 'Carbona': 4843, 'Girl Gang': 4843, 'Girlgang': 4843, 'Gin Ivory': 4843, 'Gin Cabrona': 4843, 'Ivory': 4843, 'bad girl': 4843, 'Kill of the night': 4843, 'black sheep': 4843, 'man like that': 4843, 'new rush': 4843, 'written in the water': 4843, 'oh my': 4843, 'i will love you': 4843, 'hey ho': 4843, 'devil in me': 4843, 'instrumental': 61439, 'audio': 213471, 'juice': 4843, 'tonic': 4843, 'chords': 44960, 'Heineken': 4843, 'air new Zealand': 4843, 'blood to bone': 4843, 'dirty love': 4843, 'logic': 1580622, '1-800': 4843, 'black spiderman': 4843, 'take': 312353, 'it': 4843, 'Kpop': 1357577, '1theK': 1357577, '원더케이': 1357577, 'loen': 1357577, '로엔': 1357577, '뮤비': 1357577, '티져': 1357577, 'MV': 1357577, '신곡': 1357577, '한류': 1357577, 'hallyu': 1357577, 'ロエン': 1357577, 'ミュージック': 1357577, 'ミュージックビデオ': 1357577, 'ケーポップ': 1357577, '韓国の歌': 1357577, 'アイドル': 1357577, '韓流': 1357577, '韓国': 1357577, '아이돌': 1357577, 'idol': 1357577, 'Samuel': 1357577, '사무엘': 1357577, 'Candy': 1357577, '캔디': 1357577, '프로듀스 101': 1357577, '용감한형제': 1357577, '무엘이': 1357577, '사무엘 신곡': 1357577, '사무엘 컴백': 1357577, 'corgi': 45834, 'welsh corgi': 45834, 'dog': 1644504, 'pet': 49245, 'husky': 45834, 'gatsby': 45834, 'pembroke': 45834, 'dogtor': 26473, 'appointment': 26473, 'loki': 45834, 'gohan': 45834, 'lääkäri': 26473, 'koira': 26473, 'koiranpentu': 26473, 'söpö': 26473, 'cutest': 45834, 'short movie': 26473, 'giants': 1011737, '49ers': 784937, 'beathard': 784937, 'goodwin': 784937, 'breida': 784937, 'manning': 1011737, 'shepard': 784937, 'post game highlights': 6996969, '49ers win': 784937, 'sp:ti:home=SF': 784937, 'sp:ti:away=NYG': 784937, 'a wrinkle in time': 165139, 'oprah winfrey': 165139, 'reese witherspoon': 165139, 'mindy kaling': 165139, 'storm reid': 165139, 'payoff': 165139, 'tesser': 165139, 'aunt beast': 165139, 'ava duvernay': 165139, 'epic': 662183, 'whatsit': 165139, 'who': 165139, 'which': 165139, 'mrs': 165139, 'meg murry': 165139, 'chris pine': 165139, 'Dwarf': 4575521, 'Squad': 4575521, 'Vlogs': 4575521, 'Evan': 4575521, 'Logan': 4575521, 'Paul': 4575521, 'Rest': 4575521, 'In': 4811978, 'Peace': 4575521, 'Arya': 4575521, 'Mamba': 4575521, 'Weekend Update': 1675419, 'Colin Jost': 1675419, 'Michael Che': 1675419, 'rapper': 5790602, 'Marshall Mathers': 4040161, 'i dressed like it was 1977': 1174413, '1977 fashions': 1174413, '1977 outfits': 1174413, '1970s fashion': 1174413, '1970s outfits': 1174413, 'funky': 1174413, 'disco': 1174413, 'boogie': 1174413, 'jive': 1174413, 'studio 54': 1174413, '1970s clothes': 1174413, '1977': 1174413, '1970s': 1174413, '1987 fashion': 1174413, '1997 fashion': 1174413, '2007 fashion': 1174413, 'punk fashion': 1174413, 'disco fashion': 1174413, 'polyester pantsuit': 1174413, 'safiya 1977': 1174413, 'safiya decades': 1174413, 'what we wore': 1174413, 'safiya fashion': 3312243, 'week 11': 1282936, 'wk 11': 1282936, 'kc': 226800, 'kansas city': 226800, 'chiefs': 226800, 'hunt': 226800, 'hill': 226800, 'upset': 1518364, 'giants win': 226800, 'giants upset': 226800, 'darkwa': 226800, 'sp:dt=2017-11-19T13:00:00-05:00': 226800, 'sp:ti:home=NYG': 226800, 'sp:ti:away=KC': 226800, 'Gordon': 1726301, 'Gordon Ramsay': 1726301, 'Ramsay': 3149447, 'Ramsey': 3149447, 'Chef Ramsay': 1726301, 'Kitchen Nightmares': 1726301, 'Hotel Hell': 1726301, 'gordon Ramsay it’s raw': 3149447, 'gordon Ramsay best insults': 3149447, 'kitchen nightmares full episode': 3149447, 'kitchen nightmares watch online': 3149447, 'gordon ramsay likes the food': 3149447, 'gordon ramsay donkey': 3149447, 'gordon Ramsay idiot sandwich': 3149447, 'gordon Ramsay steak': 3149447, 'gordon Ramsay caviar': 3149447, 'Gordon Ramsey': 3149447, 'soap stairs': 601839, 'Japanese game': 601839, "Don't laugh game": 601839, "Funny Japanese's Game": 601839, 'Funny Japanese Game Show': 601839, 'Slippery Stairs': 601839, 'Funny Japanese': 601839, 'try': 1321337, 'not': 601839, 'funny moments': 601839, 'Game Show': 601839, 'jeux': 601839, 'japonais': 601839, 'humour': 765100, 'rire': 601839, 'funniest video': 601839, 'funniest game': 601839, 'funny game': 601839, 'funny game show': 601839, 'funniest game show': 601839, 'Jokes': 601839, 'Japan Game Show': 601839, 'Weird Japanese game': 601839, 'strange competition': 601839, 'competition': 1628406, 'Challenge': 706954, 'AMA': 38660, 'Lady Gaga': 157974, 'The Cure': 18916, 'Acustico': 18916, 'Live': 2264116, 'Ao Vico': 18916, 'Ao Vivo': 18916, 'Joanne World Tour': 18916, 'HBO': 456348, 'Night of Too Many Stars': 1823777, 'Ron Funches': 214150, 'Abbi Jacobson': 1854516, 'Adam Sandler': 1932563, 'Ben Stiller': 1823777, 'Chris Jackson': 214150, 'Chris Rock': 1823777, 'Edie Falco': 214150, 'Ellie Kemper': 1823777, 'Hasan Minhaj': 214150, 'Howie Mandel': 214150, 'JJ Abrams': 1823777, 'John Mulaney': 1823777, 'John Oliver': 1823777, 'Jordan Klepper': 1823777, 'Olivia Munn': 1823777, 'Rob Corddry': 1823777, 'Robert De Niro': 214150, 'Will Forte': 214150, 'chill': 69884, 'musica': 19744, 'letras': 19744, 'vaporwave': 19744, 'soft': 19744, 'suave': 19744, 'relax': 160969, 'sounds': 99419, 'amas': 2865084, 'amas 2017': 1607450, 'PINK': 19744, 'Skycrapper Presentation': 19744, 'Dangerous': 19744, 'Pink': 19744, 'p!nk': 73746, 'drawings': 851438, 'weird': 3957538, 'gross': 325569, 'horror': 329457, 'commercials': 325569, 'meats': 325569, 'sam reich': 325569, 'CH Shorts': 1123536, 'gross thanksgiving sketch': 325569, 'gross thanksgiving food': 325569, 'ch thanksgiving sketch': 325569, 'ch thanksgiving weird': 325569, 'sally hansen': 1891954, 'drug store': 1891954, 'drugstore fail': 1891954, 'chrome': 4276937, 'chrome nails': 4276937, 'nail powder': 4276937, 'chrome powder for nails': 1891954, 'holo nails': 1891954, 'holographic powder': 1891954, 'drug store test': 1891954, 'testing drug store products': 1891954, 'sally hansen holographic': 1891954, 'sally hansen chrome kit': 1891954, 'not sponsored': 1891954, 'drug store review': 1891954, 'sally hansen review': 1891954, 'American Influencer Awards': 66888, 'AIA': 66888, 'Novo Theatre': 66888, 'Influencer': 66888, 'Live Stream': 66888, 'Live Show': 66888, 'Celebrities': 66888, 'Red Carpet': 184781, 'Award Show': 74023, 'sweet digs home tour': 42224, 'apartment tour': 367260, 'home decor': 552029, 'DIY room decor': 42224, 'rental': 42224, 'decorating': 411942, 'city living': 42224, 'new home': 42224, 'living room ideas': 42224, 'interior design ideas': 42224, 'taylor nicole dean': 738565, 'taylor dean': 738565, 'bathing my pets': 446368, 'bathing a hedgehog': 446368, 'bathing reptiles': 446368, 'bathing snakes': 446368, 'soaking snakes': 446368, 'giving snakes a bath': 446368, 'hedgehog taking a bath': 446368, 'mouse taking a bath': 446368, 'cleaning a mouse': 446368, 'snakes': 446368, 'reptiles': 446368, 'exotic pets': 446368, 'all my pets': 446368, 'all my animals': 446368, 'tarantula': 446368, 'pacman frog': 446368, 'frog care': 446368, 'frogs as pets': 446368, 'frogs': 446368, 'fish tanks': 446368, 'saltwater tanks': 446368, 'aquariums': 446368, 'cheese the fish': 446368, 'mental health': 896262, 'taylor dean mental health': 446368, 'bathing animals': 446368, 'animals taking a bath': 446368, 'submarine': 29041, 'search': 807540, 'missing': 29041, 'Argentine': 29041, '44': 29041, 'crew': 29041, 'members': 29041, 'on': 318955, 'board': 648952, 'ARA': 29041, 'signal': 29041, 'pings': 29041, 'NEWS': 29041, 'chip kelly': 16431, 'florida gators': 16431, 'kelly': 319413, 'chip': 16431, 'gators': 16431, 'oregon': 51803, 'gators chip kelly': 16431, 'chip kelly florida': 16431, 'chip kelly florida gators': 16431, 'chip gators': 16431, 'chip florida': 16431, 'chip kelly espn': 16431, 'gators coach': 16431, 'oregon chip kelly': 16431, 'ducks chip kelly': 16431, 'philadelphia chip kelly': 16431, 'Tesla truck': 2665597, 'Tesla semi truck': 1923738, 'self-driving': 1923738, 'autonomous': 1923738, 'trucking': 2379027, 'semi': 2379027, 'roadster': 1923738, 'tesla roadster': 2224145, '2017 roadster': 1923738, 'Spoiler Review': 44805, 'vpcwochit': 13910, 'wochit': 13910, 'usatsyn': 19243, 'usatoday_custom': 19243, 'vpc': 17634, 'obituary': 17634, 'mel tillis': 13910, 'obit': 13910, 'country music hall of fame': 13910, 'usatyoutube': 19243, 'breaking': 104100, 'live coverage': 180328, 'live news': 95900, 'Mugabe': 180328, 'Zimbabwe': 180328, 'Zanu-PF': 95900, 'Mnangagwa': 95900, 'Grace': 95900, 'Zimbabweans': 95900, 'zimbabwe news': 180328, 'zimbabwe latest news': 95900, 'zimbabwe breaking news': 95900, 'zimbabwe ruling party': 95900, 'Late night': 6104220, 'closer Look': 6104220, 'Sexual harassment': 1577521, 'Congress': 2018626, 'Al Franken': 3449502, 'Alabama': 3463624, 'Senate Race': 1577521, 'Jackie Speier': 1577521, 'Wyoming': 1577521, 'Mike Enzi': 1577521, 'Trenton Garmon': 1577521, 'new orleans videos': 17627, 'Nifty': 50934, 'BuzzFeed Nifty': 50934, 'Friendsgiving': 50934, 'snacks': 50934, 'appetizers': 50934, 'dinner': 1237825, 'pumpkins': 50934, 'decoration': 624346, 'yarn': 50934, 'crafting': 50934, 'crackers': 50934, 'cracker spread': 50934, 'vegetables': 50934, 'dip': 50934, 'chips and dip': 50934, 'cider': 50934, 'Boeing787': 1126715, 'Sam Chui': 1126715, 'B787': 1126715, 'Private Jet': 1126715, 'Business Jet': 1126715, 'Deer Air': 1126715, 'Luxury Travel': 1126715, 'UAS': 1126715, 'Dubai Air Show': 1126715, 'Tech Insider': 869564, 'TI': 869564, 'Tech': 921003, 'Science': 919318, 'Innovation': 893530, 'Digital culture': 869564, 'Design': 869564, 'Tesla Motors': 741859, 'Semi truck': 741859, 'Electric truck': 741859, 'Cars': 746383, 'Truck': 741859, 'Transportation': 741859, 'Electric semi truck': 741859, 'ByStorm Entertainment/RCA Records': 684347, 'Miguel': 300653, 'Pineapple Skies': 300653, 'R&B': 1594226, 'mali music and jennifer hudson': 4970, 'jennifer hudson': 737574, 'mali music': 4970, 'matoma': 15353, 'hakunamatoma': 15353, 'hakuna matoma': 15353, 'good vibes': 15353, 'Jessie': 483212, 'Queen': 554684, 'Lava': 483212, 'Music/Republic': 483212, 'Only You': 18525, 'Parson James': 18525, 'Vlogger': 80851, 'Teen': 80851, 'Beauty and Lifestyle': 80851, 'Vlogging': 80851, 'Video Blogging': 80851, 'maggie lindemann': 80851, 'obsessed': 80851, 'new music': 894254, 'spotify': 80851, 'pat mcgrath': 80851, 'Coping': 6383, 'FRANKIE': 6383, 'Georgia Dome': 578128, 'Implosion': 578128, 'November 20': 578128, 'The Weather Channel': 578128, 'Guy Losing It Live on Air': 578128, 'Swearing': 578128, 'MARTA': 578128, 'bus': 638637, 'ironic': 578128, 'musical fiction': 3013500, 'fiction': 1351831, '21AMukeXNIg': 771035, 'wlS6Ix7mA0w': 771035, 'Anitta': 771035, 'J Balvin': 771035, 'Downtown': 807410, 'anitta downtown': 771035, 'movie hells': 327936, 'bedazzled': 327936, 'all dogs go to heaven': 327936, 'bill and ted bogus journey': 327936, 'Swaim': 327936, 'Michael Swaim': 327936, 'Mike Swaim': 327936, 'Dan O’brien': 327936, 'Dan Obrien': 327936, 'Katy Willert': 327936, 'Katie Willert': 327936, 'Willert': 327936, 'Soren': 327936, 'Soren Bowie': 327936, 'Bowie': 327936, 'Fan Theory': 327936, 'After Hours Original Cast': 327936, 'After Hours Original crew': 327936, 'Hot Takes': 327936, 'After Hours': 327936, 'DOB': 327936, 'types of hell': 327936, 'funny hell': 327936, 'dog hell': 327936, 'robot hell': 327936, 'hades': 327936, 'satan': 327936, 'south park hell': 327936, 'south park heaven': 327936, 'futurama heaven': 327936, 'entertaining hell': 327936, 'hairstyle': 1544233, 'entertainment tonight': 1297033, 'emotional': 1257634, 'Awards': 1946978, 'et': 1928837, 'beliebers': 1257634, '2017 American Music Awards': 1444256, 'blonde': 3045516, 'etonline': 1300755, 'selena gomez wolves': 1271720, 'jelena': 1271720, 'cat-music': 1257634, 'selena amas': 1257634, 'televised': 1257634, 'selena gomez': 3088006, 'bombs': 934366, 'arguments': 353570, 'grandparents': 353570, 'explosions': 353570, 'action movies': 353570, 'reunions': 353570, 'raphael chestang': 1480932, 'ally beardsley': 797967, 'family dinner': 353570, 'thanksgiving dinner': 458080, 'family fights': 353570, 'tense conversations': 353570, 'rafe disarms dinner': 353570, 'rafe bomb disarm': 353570, 'every blank ever': 1239150, 'smosh every blank ever': 1239150, 'every ever': 1239150, 'family gathering': 582747, 'every family ever': 582747, 'every family gathering ever': 582747, 'every thanksgiving ever': 582747, 'ian': 979054, 'ian hecox': 979054, 'smosh': 1140458, 'smosh ebe': 582747, 'every blank ever smosh': 582747, 'every kid ever': 582747, 'every politician ever': 582747, 'gmm food': 836846, 'rhett': 616272, 'link': 616272, 'ear biscuits': 339761, 'mythical show': 339761, 'cooking a turkey on a car engine': 339761, 'cooking a turkey': 339761, 'how to cook a turkey': 339761, 'car engine cooking': 339761, 'cooking on a car engine': 339761, 'car engine oven': 339761, 'rhett link cooking a turkey on a car engine': 339761, 'gmm cooking a turkey on a car engine': 339761, 'car engine': 339761, 'car cooking': 339761, 'how to cook': 906605, 'sp:dt=2017-11-19T20:30:00-05:00': 1056136, 'sp:ti:home=Dal': 1984887, 'sp:ti:away=Phi': 2326613, 'philly': 2326613, 'philadelphia': 2326613, 'eagles': 1135235, 'wentz': 2326613, 'dak prescott': 1056136, 'dez': 1984887, 'beasley': 1056136, 'td run': 1056136, 'ajayi': 1056136, 'blount': 1056136, 'Bruno Mars': 1132975, 'Live At The Apollo Theater': 301265, 'Apollo Theater': 301265, 'Bruno': 301265, '24k Magic': 301265, '24kmagic': 301265, 'Apollo Special': 301265, 'the weather channel': 60509, 'georgia dome': 60509, 'implosion': 60509, 'Georgia': 102954, 'al franken': 1398887, 'roy moore': 1334706, 'the view': 1061206, 'hot topics': 904509, "women's rights": 108339, 'sexual harassment': 3992083, 'sexual misconduct': 1678680, 'mashed potatoes': 870793, 'side dish': 123251, 'creamy': 123251, 'ultra creamy mashed potatoes': 123251, 'yukon gold': 123251, 'best side dish': 123251, 'best thanksgiving classics': 123251, 'mashed potatoes recipe': 123251, 'how to make mashed potatoes': 123251, 'mashed potatoes thanksgiving': 123251, 'thanksgiving recipes': 227761, 'thanksgiving side dishes': 123251, 'potato recipes': 123251, 'mashed potato': 123251, 'mashed potato recipe': 123251, 'how to make mashed potato': 123251, 'thanksgiving sides': 123251, 'thanksgiving ideas': 123251, 'staufen': 372127, 'germany': 372127, 'black forest': 372127, 'geothermal energy': 372127, 'geothermal drilling': 372127, 'anhydrite': 372127, 'aquifer': 372127, 'gypsum': 372127, 'subsidence': 372127, 'property damage': 372127, 'amazing places': 372127, 'c&hshorts': 4497754, 'c&h shorts': 4497754, 'c and h shorts': 4497754, 'cyanide and happiness shorts': 4497754, 'cyanide': 4497754, 'happiness': 4693160, 'C&H': 4497754, 'cy&h': 4497754, 'cyanide and happiness': 4497754, 'explosm': 4497754, 'exlposm': 4497754, 'cyanide & happiness': 4497754, 'explosm.net': 4497754, 'explosm animated': 4497754, 'explosm comics': 4497754, 'cartoon movies': 4497754, 'explosmentertainment': 4497754, 'channelate': 638074, 'chan8': 638074, 'channelate shorts': 638074, 'channel8': 638074, 'thankful': 638074, 'Melvin Sanicas': 128287, 'Andrew Foerster': 128287, 'flu': 128287, 'flu shot': 128287, 'influenza': 128287, 'vaccine': 128287, 'CDC': 128287, 'WHO': 128287, 'virus': 459507, 'flu season': 128287, 'H1N1': 128287, 'DNA': 128287, 'RNA': 128287, 'influenza virus': 128287, 'antigen': 128287, 'antibody': 128287, 'herd immunity': 128287, 'work': 749306, 'transportation': 1287246, 'automobile': 455289, 'surveillance': 455289, 'monitoring': 455289, 'eld': 455289, 'eld or me': 455289, 'operation black and blue': 455289, 'electronic logging device': 455289, 'driver': 2941046, 'worker': 455289, 'device': 455289, 'FMCSA': 455289, 'DOT': 455289, 'federal motor carrier safety administration': 455289, 'department of transportation': 455289, 'otto': 455289, 'uber': 710373, 'tesla': 1112748, 'waymo': 455289, 'daimler': 455289, 'einride': 455289, 'embark': 455289, 'transit': 1287246, 'prime': 1196222, 'delivery': 459813, 'mercedes': 907262, 'wearables': 455289, 'the grand tour': 1493313, 'jeremy clarkson': 1493313, 'grand tour': 705880, 'richard hammond': 1192906, 'james may': 1192906, 'clarkson': 1192906, 'hammond': 1192906, 'may': 1192906, 'gt': 451973, 'porsche': 451973, 'amg': 451973, 'biased': 451973, 'mark': 1613070, 'webber': 451973, 'mark webber': 451973, 'f1': 451973, 'driver wanted': 451973, 'unbiased': 451973, 'series 2': 451973, 'american music awards': 1821245, 'popular': 54002, 'views': 54002, 'legends': 54002, 'vocals': 192565, 'belts': 54002, 'range': 54002, 'demi lovato': 54002, 'studio': 315508, 'xtina': 54002, 'christina aguilera': 136125, 'everybody hurts': 54002, 'AMA Awards': 29254, 'Diana Rose': 29254, 'Linken Park': 29254, 'Selena Gomez': 304011, 'singing': 4825925, 'AMA highlights': 29254, 'AMA winners': 29254, 'Kenan Thompson': 3844894, 'Chris Redd': 2524884, 'Barack Obama': 2364742, 'President Obama': 2364742, 'Hassan Minhaj': 1609627, 'jon stewart': 1609627, 'autism': 1617650, 'benefit': 1609627, 'stand up': 1609627, 'hbo': 1609627, 'Sarah Silverman': 1609627, 'Steve Carell': 1609627, 'amy schumer': 1718413, 'stars for autism': 1609627, 'charity events': 1609627, 'charity': 2052408, 'autistic': 1617650, 'raise funds': 1609627, 'support': 1622737, 'live show': 1609627, 'madison square garden': 1609627, 'NOTMS': 1609627, 'veritasium': 1072652, 'mitosis': 251683, 'cell division': 251683, 'dna': 582903, 'chromosomes': 251683, 'dynein': 251683, 'kinetochore': 251683, 'cell biology': 251683, 'cells': 251683, 'replication': 251683, 'molecular machines': 251683, 'nanotechnology': 251683, 'Josh Groban': 71126, 'Happy Xmas (War Is Over)': 43667, 'john lennon': 43667, 'noel': 71126, 'christmas song': 71126, 'new song': 43667, 'baby': 1263220, 'first': 932749, 'babies': 259293, 'first time': 169239, 'slow motion video': 169239, 'Billboard': 1587706, 'billboard channel': 3266159, 'billboard magazine': 3262463, 'official billboard channel': 3266159, 'bts interview': 1587706, 'bangtan': 1587706, 'bangtan boys': 1587706, 'rap monster': 1587706, 'k-pop': 3136668, 'jin': 1587706, 'jungkook': 1587706, 'bighit': 1587706, 'jimin': 1587706, 'suga': 1587706, 'beyond the scene': 1587706, 'bts funny moments': 1587706, 'cabello': 1587706, 'camila cabello havana': 2285906, 'awards': 2976413, 'ama': 1685120, 'america music awards 2017': 1587706, '2017 amas': 1669829, 'red carpet': 1696157, 'american music awards red carpet': 1587706, 'dance moves': 1587706, 'mc Donalds': 90054, 'happy meal': 90054, 'parenting': 182259, 'dining out': 90054, 'toddlers': 90054, 'new zealand': 205975, 'bigmac': 90054, 'OnePlus 5t': 430457, 'OnePlus 5t durability test': 430457, 'Flagship': 430457, 'Mobile phone': 430457, 'how durable': 430457, 'scratch test': 430457, 'bend test': 430457, 'liquid': 589827, 'water damage': 430457, 'cracked screen': 430457, 'midrange flagship': 430457, 'best smartphone': 819243, 'Breaking Bad': 15058, 'Bryan Cranston': 15058, 'malcom in the middle': 15058, 'a life in parts': 15058, 'Walter White': 15058, 'All the Way': 15058, 'Joe Pascal': 15058, 'Vince Gilligan': 15058, 'Charles Manson': 15058, "Marvel's Agents of SHIELD Season 5": 33620, "Marvel's Agents of SHIELD Season 5 Trailer": 33620, "Marvel's Agents of SHIELD Season 5 Official Trailer": 33620, "Marvel's Agents of SHIELD Season 5 Promo": 33620, "Marvel's Agents of SHIELD ABC": 33620, "Marvel's Agents of SHIELD TV series": 33620, 'season 5': 33620, 'simonandmartina': 224651, 'martina': 224651, 'simon and martina': 224651, 'Tokyo': 224651, 'eatyourkimchi': 224651, 'eat your kimchi': 224651, 'eat your sushi': 224651, 'eatyoursushi': 224651, 'Japanese Water': 224651, 'Flavored Water': 224651, 'Alisha Marie': 999867, 'Obsessed': 999867, '10 ways to tell if': 999867, 'ways to tell if': 999867, 'alisha': 1874142, 'alishamarie': 999867, 'fleurdeforce': 228779, 'fleur de force': 228779, 'fleurdevlog': 228779, 'fleur de vlog': 228779, 'for him': 118227, 'gifts': 563060, 'present': 2153990, 'mike': 460767, 'mike de force': 118227, 'prezzies': 118227, 'de force': 118227, 'male gifts': 118227, 'gifts for men': 118227, 'gifts for dad': 118227, 'dji drone': 118227, 'playstation': 1405569, 'vr headset': 118227, 'gran turismo': 118227, 'American': 684701, 'Soul': 78979, 'Access Hollywood': 121271, 'Nick Jonas': 1103383, '2017 AMAS': 119497, 'Christina Aguilera': 153389, 'Hailee Steinfeld': 127945, 'Diana Ross': 119497, 'Zedd': 119497, 'Shawn Mendes': 616239, 'Whitney Houston': 119497, 'Alessia Cara': 123193, 'The Bodyguard': 119497, 'Skylar Grey': 119497, 'Portugal The Man': 119497, 'Box Office': 162626, 'Disaster': 100504, 'Disappointment': 100504, 'Expectations': 100504, 'Passenger': 144177, 'All the Little Lights': 144177, 'Whispers': 144177, 'Brighton': 144177, 'Guitar': 144177, 'Folk': 144177, 'Busking': 144177, 'Simple Song': 144177, 'The Boy Who Cried Wolf': 144177, 'Lucy Rose': 144177, 'Let Her Go': 144177, 'Ed Sheeran': 26133199, 'Hearts on Fire': 144177, 'the boy who cried wolf': 144177, 'galway girl': 144177, 'holes': 144177, 'shape of you': 553591, 'divide': 144177, 'I Hate': 144177, 'middle of the bed': 144177, 'shiver': 144177, 'second chance': 144177, "something's changing": 144177, 'hotel california': 144177, 'stop motion': 144177, 'when we were young': 144177, 'losing my religion': 144177, 'wrong direction': 144177, "victoria's secret angels": 129902, 'vs angels': 1034368, 'dara hart': 129902, 'dogpound': 129902, 'hotel room workout': 129902, 'hotel work out': 129902, 'train like an angel': 129902, 'body weight workout': 129902, 'model workout': 129902, 'slim workout': 129902, 'mcdonalds': 567518, 'mcdonalds drinks': 191764, 'mcdonalds hi-c': 191764, 'mcdonalds hic': 191764, 'mcdonalds hi-c discontinued': 191764, 'mcdonalds got rid of hi-c': 191764, 'where to get mcdonalds hi-c': 191764, 'mcdonalds hi-c availablilty': 191764, 'hi-c': 191764, 'hic': 191764, 'hi-c orange': 191764, 'hi-c orange lavaburst': 191764, 'mcdonalds orange': 191764, 'mcdonalds orange drink': 191764, 'mcdonalds orange drink discontinued': 191764, 'mcdonalds orange where to get it': 191764, 'what is mcdonalds orange drink': 191764, 'orange drink': 191764, 'hic orange lavaburst': 191764, 'mcdonalds orange availability': 191764, 'Tom Brady': 104713, 'Athlete': 16456, 'Diet': 16456, 'Health': 44072, 'Nutrition': 16456, 'TB12': 16456, 'Food': 190083, 'Workout': 16456, 'how to style outerwear': 134020, 'how to style jackets': 134020, 'how to style coats': 134020, 'fall lookbook': 134020, 'jackets': 134020, 'nordstrom rack': 134020, 'vanity fair summit': 9454, 'vanity fair summit 2017': 9454, 'mark cuban': 9454, 'bob iger': 9454, 'reid hoffman': 9454, 'kevin systrom': 9454, 'brad keywell': 9454, 'david zaslav': 9454, 'dee dee myers': 9454, 'ted saranos': 9454, 'richard plepler': 9454, 'yael aflalo': 9454, 'daniella vitale': 9454, 'bill mcglashan': 9454, 'futurism': 1015973, 'predicting the future': 9454, 'disaster': 1374376, 'survival': 1625328, 'survivalist': 248027, 'preparation': 26230, 'natural disaster': 47109, 'nifty': 193120, 'buzzfeed nifty': 193120, 'burger': 413047, 'hamburger': 78860, 'taste': 78860, 'tasting': 78860, 'diner': 78860, 'cheeseburger': 78860, 'emmy': 78860, 'emmymade': 78860, 'emmymadeinjapan': 78860, 'fries': 83247, 'french fries': 83247, 'Grubhub': 78860, 'order': 78860, 'take out': 78860, 'carry out': 78860, 'Thegn Thrand': 105115, 'sword launch': 105115, 'leroy': 698200, 'sanchez': 698200, 'HAVANA': 698200, 'havana camila cabello': 698200, 'camila havana': 698200, 'habana': 698200, 'leroy sanchez': 698200, 'leroy sanches': 698200, 'leroy sanchez 2017': 698200, 'leroy sanchez havana': 698200, 'spanish': 698200, 'spanglish': 698200, 'espanol': 698200, 'fifth harmonie': 698200, 'havana cover': 698200, 'havana spanish': 698200, 'havana male cover': 698200, 'leroy havana': 698200, 'camila cabello cover': 698200, 'havana acoustic': 698200, 'havana acoustic guitar': 698200, 'fifth harmony cover': 698200, 'fizz': 71637, 'funniest': 129600, 'silliest': 71637, 'silly': 301947, 'too cute': 71637, 'adorable': 129600, 'meowing': 71637, 'yelling': 71637, 'falling': 71637, 'blooper': 162317, 'lindsey': 1709451, 'violin': 1790262, 'dubstep': 1709451, 'sterling': 1709451, 'stirling': 1709451, 'becky g': 831425, 'cmon': 831425, 'come on': 831425, 'warmer in the winter': 1419537, 'original christmas': 831425, 'barbie': 831425, '3D printed': 56759, '3D print': 56759, 'Air engine': 56759, 'Engine': 56759, 'Compressed air': 56759, 'Pneumatic': 56759, 'Diy': 56759, 'Airhogs': 56759, 'clear pumpkin pie': 346110, 'diy pumpkin pie': 346110, 'how to make pumpki pie': 346110, 'crystal clear pumpkin pie': 346110, 'crystal clear': 346110, 'diy clear pumpkin pie': 346110, 'polyphonic': 138563, 'freddie mercury': 138563, 'freddie': 138563, 'we are the champions': 138563, 'tie your mother down': 138563, 'bohemian rhapsody': 191372, 'all dead': 138563, 'all dead all dead': 138563, 'under pressure': 138563, 'somebody to love': 138563, 'crazy little thing called love': 138563, 'i want to break free': 138563, 'greatest singer': 138563, 'best singer': 138563, 'rock history': 138563, 'freeform': 7978, 'shows': 146032, 'channel': 146611, 'network': 271683, 'episodes': 146032, 'ABC Family': 3200325, 'New black-ish': 7978, 'spin-off': 7978, 'grown-ish': 7978, 'original comedy': 7978, 'blackish': 839459, 'Zoey Johnson': 35380, 'Yara Shahidi': 462536, 'adulthood': 7978, 'Trevor Jackson': 7978, 'Chris Parnell': 35380, 'Dre': 7978, 'young adult': 7978, 'Ana': 7978, 'Francia Raisa': 35380, 'Emily Arlook': 35380, 'Nomi Segal': 7978, 'Vivek': 7978, 'Jordan Buhat': 7978, 'autocomplete': 7166299, 'autocomplete interview': 8295001, 'autocorrect': 5670852, 'google autocomplete': 6799554, 'joe keery interview': 5670852, 'joe keery stranger things': 5670852, 'wired autocomplete': 6799554, 'gaten matarazzo interview': 5670852, 'gaten matarazzo': 5989101, 'gaten': 5670852, 'webs most searched': 5670852, 'google interview': 5670852, 'stranger things cast interview': 5670852, 'joe keery stranger things 2': 5670852, 'stranger things stars': 5670852, 'Beautiful Trauma': 2484089, 'trifle': 852717, 'chandler': 852717, 'monica': 852717, 'cornbread': 852717, 'moussaka': 852717, 'mince': 852717, "rachel's trifle": 852717, 'binging with babish': 852717, 'binging with babish FRIENDS': 852717, 'babish': 852717, 'rachels trifle': 852717, 'food from FRIENDS': 852717, 'homemade jam': 852717, 'homemade jelly': 852717, "rachel's trifle recipe": 852717, "rachel's trifle from friends": 852717, "rachel's trifle scene": 852717, 'rachels trifle scene': 852717, 'cornbread recipe': 852717, 'skillen cornbread': 852717, 'custard': 852717, 'custard recipe': 852717, "shepard's pie": 852717, 'whipped cream': 852717, 'pear qwerty horse': 852717, 'friendsgiving': 957227, 'phoebe': 852717, 'moistmaker': 852717, 'CBS This Morning': 1714170, 'Charlie Rose': 1714170, 'Gayle King': 212414, "Norah O'Donnell": 212414, 'allegations': 214231, 'response': 212414, 'Washington post': 212414, 'PBS': 3096602, 'first we feast': 183935, 'pizza': 301581, 'cast-iron': 183935, 'what is': 183935, 'brad': 183935, 'brad leone': 424101, "it's alive": 183935, 'alive': 183935, 'fermented': 183935, 'live food': 183935, 'test kitchen': 213131, 'fermentation': 183935, 'probiotics': 183935, 'make': 1026958, 'pizza recipe': 183935, 'cast iron pizza': 183935, 'how to make pizza': 183935, 'best pizza recipe': 183935, 'sean in the wild pizza': 183935, 'first we feast brad': 183935, 'sean in the wild brad': 183935, 'cast iron skillet pizza': 183935, 'hot sauce challenge': 183935, 'cast iron': 183935, 'first we feast hot ones': 183935, 'Raw': 1922121, 'Matt Hardy': 1922121, 'Elias': 680569, 'Mustafa Ali': 680569, 'Sheamus': 680569, 'Dana Brooke': 680569, 'Asuka': 680569, 'Bo Dallas': 680569, 'Curtis Axel': 680569, 'Seth Rollins': 680569, 'cesaro': 680569, 'enzo amore': 680569, 'roman reigns': 680569, 'sasha banks': 680569, 'bayley': 680569, 'monday night raw': 1922121, 'braun strowman': 680569, 'raw top 10': 680569, 'alexa bliss': 680569, 'finn bálor': 680569, 'Paige': 680569, 'Paige returns': 680569, 'Mandy Rose': 680569, 'Sonya Deville': 680569, 'Debut': 680569, 'NXT': 1387733, 'love story': 114917, 'marriage': 131919, 'smiling': 114917, 'wedding planning': 114917, 'advice': 952794, 'nerdfighters': 457399, 'vlogbrothers': 611166, 'love stories': 114917, 'Nicholas Amendolare': 160530, 'Garrett Hardin': 160530, 'tragedy of the commons': 160530, 'W. F. Lloyd': 160530, 'overgrazing': 160530, 'antibiotics': 160530, 'coal': 160530, 'Casey Neistat': 25968310, 'Casey': 290800, 'Casey Neistat Vlog': 290800, 'Peter McKinnon': 290800, 'Vlog': 290800, 'Amsterdam': 290800, 'Teaching casey how to vlog': 290800, 'Travel Vlog': 290800, 'Filmmaking': 290800, 'BRoll': 290800, '1DXMK2': 290800, 'Casey Neistat Vlogger': 290800, 'Peter and Casey': 290800, 'Netherlands': 290800, 'Cinematic': 290800, 'How to make your vlogs cinematic': 290800, 'Razer Phone': 1238903, 'Razer phone review': 850117, 'razer': 850117, 'razer phone': 850117, 'razer smartphone': 850117, 'gaming phone': 1238903, 'gaming smartphone': 850117, 'best gaming phone': 850117, 'Razer Phone feature': 850117, '120Hz': 850117, 'promotion': 850117, 'Razer gaming': 1238903, 'Razer Android': 850117, 'Razer Phone speakers': 850117, 'Razer speakers': 850117, 'Nexbit': 850117, 'Nexbit Robin': 850117, 'Razer Phone vs': 850117, 'battery': 850117, 'battery life': 850117, 'Sofia Vergara': 1002131, 'Sofia': 1002131, 'Vergara': 1002131, 'Sofia Vergara underwear line': 1002131, 'Sofia Vergara EBY': 1002131, 'Seven Bar Foundation': 1002131, 'nachos': 457087, 'how to make nachos': 240166, 'nbc the voice': 59462, 'watch the voice episode': 59462, 'watch the voice video': 59462, 'miley cyrus coach': 59462, 'jennifer hudson coach': 59462, 'blake shelton coach': 59462, 'adam levine coach': 59462, 'carson daly host': 59462, 'season 13 episode 19': 59462, 'live top 12 eliminations': 59462, 'the voice live': 59462, 'the voice vote': 59462, 'voice live vote': 59462, 'pitch perfect 3': 886936, 'the voice': 732604, 'the voice nbc': 540345, 'the voice season 13': 540345, 'the voice new season': 540345, 'The Voice 2017': 540345, 'The Voice USA': 540345, 'The Voice Season 13': 406971, 'The voice winners': 540345, 'adam levine': 651095, 'miley cyrus': 540345, 'blake shelton': 948385, 'fear box': 1664870, 'liza koshy 2017': 1364956, 'liza koshy scary': 734594, 'liza koshy derek blasberg': 734594, 'liza koshy reaction': 1364956, 'liza koshy react': 734594, 'liza koshy best moments': 1364956, 'liza koshy fear box': 1364956, 'liza koshy scary moments': 734594, 'liza koshy scared': 734594, 'liza koshy vanity fair': 734594, 'liza koshy getting scared': 734594, 'weird stuff': 734594, 'the walking dead no mans land by hannah stocking anwar jibawi inanna sarkis': 1969041, 'dead': 1835635, 'no': 1835635, 'mans': 1835635, 'by': 2441357, 'stocking': 3928329, 'craziest wisdom teeth removal story': 1969041, 'worst school presentations': 1398046, 'halloween roast battle': 308230, 'The Walking Dead: No Man’s Land by Hannah Stocking': 308230, 'Anwar Jibawi & Inanna Sarkis': 308230, 'nuclear': 362961, 'waste': 355773, 'atomic': 355773, 'nuke': 355773, 'spent fuel': 355773, 'nuclear fuel': 355773, 'fuel rods': 355773, 'storage': 355773, 'nuclear waste storage': 355773, 'long term nuclear waste messaging': 355773, 'nuclear semiotics': 355773, 'nuclear waste disposal': 355773, 'explained': 889563, 'nuclear energy': 355773, 'clean energy': 431553, 'nuclear physics': 355773, 'us department of energy': 355773, 'explainer': 385907, 'onkalo': 355773, 'Onkalo spent nuclear fuel repository': 355773, 'yucca mountain': 355773, 'how to make pumpkin pie': 76859, 'thanksgiving dessert': 76859, 'desserts': 104475, 'how to make pie': 76859, 'the walking dead no mans land by inanna sarkis hannah stocking anwar jibawi': 907902, 'wonder woman vs superwoman': 907902, 'secret life 2': 907902, 'The Walking Dead: No Man’s Land by Inanna Sarkis': 907902, 'Hannah Stocking & Anwar Jibawi': 907902, 'jeffree star': 651506, 'manny mua': 1290417, 'thanksgiving makeup tutorial': 651506, 'cut crease': 651506, 'cut crease tutorial': 651506, 'thanksgiving slay': 651506, 'how-to': 2756700, 'tips': 6977520, 'hair': 1276996, 'sara sampaio': 791508, 'sara sampaio makeup': 270142, 'sara sampaio hair': 270142, 'tutorial makeup': 270142, 'makeup tut': 270142, 'victorias secret': 1641043, 'victorias secret model': 270142, 'victorias secret models': 653242, 'vs model': 270142, 'sara sampaio victorias secret': 270142, 'bombshell makeup': 270142, 'moisterizer': 270142, 'victorias secret show': 270142, 'victorias secret angel': 270142, 'sara sampaio model': 270142, 'sara sampaio interview': 270142, 'There’s Nothing Holdin Me Back': 496742, 'There’s Nothing Holding Me Back': 496742, 'Mendes': 2205796, 'TNHMB': 496742, 'AMAs': 3107353, 'NASA': 726863, 'Jet Propulsion Laboratory': 679202, 'JPL': 679202, 'space': 2002549, 'exploration': 679202, 'planets': 679202, 'asteroid': 679202, 'asteroids': 1214009, 'interstellar': 679202, 'object': 679202, 'solar system': 679202, 'ʻOumuamua': 679202, 'Oumuamua': 679202, '1I/2017 U1': 679202, 'C/1980 E1': 679202, 'lindley johnson': 679202, 'NEO': 679202, 'CNEOS': 679202, 'kelly fast': 679202, 'paul chodas': 679202, 'chodas': 679202, 'Freestyle': 210815, 'Palladio': 210815, 'Escala': 210815, 'tyler oakley': 418492, 'tyleroakley': 418492, 'gay': 1462565, 'Q&A': 1894659, 'question': 418492, 'answer': 418492, 'cc': 418492, 'captioned': 418492, 'ingrid nilsen': 48804, 'daniel preda': 48804, 'volunteering': 48804, 'gives back': 48804, 'raymond braun': 48804, 'catfish': 48804, 'mtv': 220608, 'kingsley': 275328, 'wassabi productions': 48804, 'laurDIY': 48804, 'Greta Gerwig': 90980, 'Wrote': 54392, 'Letter': 54392, 'Justin Timberlake': 54392, 'Asking': 54392, 'Use': 54392, 'Lady Bird': 197660, 'Frances Ha': 54392, 'Greenberg': 54392, 'Mistress America': 54392, '20th Century Women': 54392, 'Jackie': 54392, 'Tarte': 51966, 'Eyeshadow': 38219, 'Foundation': 38219, 'first person': 119161, 'wd40': 119161, 'pov': 119161, 'idiot': 119161, 'hands': 119161, 'stupid boy': 119161, 'funny fail': 119161, 'arts and crafts': 119161, 'stupid man': 119161, 'hello': 119161, 'angry': 119161, 'mad': 119161, 'frustrated': 119161, 'triggered': 119161, 'bloody mary': 119161, 'crying': 4389687, 'lmao': 119161, 'ragnarok': 34719, 'spiderman': 1793046, 'antman': 34719, 'infinity war': 39983431, 'iron man': 1813436, 'hulk': 34719, 'super powers': 34719, 'comics': 38033396, 'teleportation': 34719, 'telekinesis': 34719, 'xmen': 60909, 'basic': 2400704, 'Mugabe resigns': 84428, "Zimbabwe's President Mugabe resigns": 84428, 'mugabe news': 84428, 'President Robert Mugabe': 84428, 'President Robert Mugabe news': 84428, 'breaking news mugabe': 84428, 'news coverage': 84428, 'news live': 84428, 'africa news': 84428, 'latest news': 84428, 'YTN': 52869, '뉴스': 52869, '정치': 52869, 'Connor Franta': 276964, 'ConnorFranta': 276964, 'exposing': 198861, 'exposed': 289106, 'expose': 159746, 'haha': 159746, 'fuuny': 159746, 'innocent': 159746, 'sorrrrry': 159746, 'pg': 159746, 'boys': 345477, 'loving': 276964, 'relatable': 159746, 'cell phone': 159746, 'candid': 159746, 'browsing history': 159746, 'search history': 159746, 'internet history': 159746, 'idk': 276964, 'strange': 289994, 'byeeeeeee': 159746, 'nbc': 33853, 'news channel': 54488, 'news station': 54488, 'newspaper': 54488, 'nightly news': 4727, 'president donald trump pardons turkey': 3346, 'turkey pardon': 3346, 'pardon': 3346, 'trump turkey': 3346, 'drumstick': 3346, 'thanksgiving turkey': 3346, 'trump thanksgiving': 3346, 'trump pardons turkey': 3346, 'trump pardons thanksgiving turkey': 3346, 'alex gilbert': 193082, 'birth father': 193082, 'russia': 472909, 'adopted': 193082, 'Colleen': 115921, 'ballinger': 342820, 'colleenb123': 115921, 'colleenvlogs': 115921, 'tour': 4132788, 'latest News': 379104, 'Happening Now': 379104, 'CNN': 379104, 'newsroom': 21565, 'White House': 1997644, 'President': 47666, 'Endorsement': 3892, 'Sexual misconduct': 3892, 'Allegations': 1505648, 'pumpkin cookies': 27225, 'rocket': 42737, 'birthday cake': 42737, 'markers': 4452, 'drill press': 4452, 'cross slide vice': 4452, 'end mills': 4452, 'keyless chuck': 4452, 'hacking': 4452, 'model making': 4452, 'tips and tricks': 37291, 'machining': 4452, 'Bridgeport': 4452, 'lathe': 4452, 'black friday': 2133287, 'black friday sales': 403292, 'How Likely Are You to Die during a Black Friday Sale': 403292, 'black friday 2017': 1572243, 'black friday statistics': 403292, 'black friday facts': 403292, 'black friday spend': 403292, 'money': 1715448, 'spend': 403292, 'black friday death toll': 403292, 'black friday accident': 403292, 'terry crews': 451381, 'good morning america': 451381, 'mariah carey': 1230624, 'gene simmons': 451381, 'the wendy williams show': 451381, 'rick and morty': 1984555, 'season 3': 3929380, 'season three': 1980168, 'adult swim': 1980168, 'rick': 1980168, 'morty': 1980168, 'justin roiland': 1980168, 'dan harmon': 1980168, 'mr. poopybutthole': 1980168, 'extended': 1980168, 'season 3 finale': 1980168, 'alternate': 1980168, 'bonus scene': 1980168, 'adam ruins everything': 1013330, 'are': 1013330, 'adam ruins expiration dates': 1013330, "expiration dates don't mean anything": 1013330, 'whatd do expiration dates mean': 1013330, 'expiration dates fake': 1013330, 'expiration dates debunked': 1013330, 'adam debunks expiration dates': 1013330, 'Jim Carrey': 1501756, 'Andy Kaufman': 1501756, 'Honest Trailers': 1501756, 'FCC': 2486935, 'Pai': 1501756, 'Net Neutrality': 1501756, 'Ajit Pai': 3186031, 'Comcast': 1501756, 'FTC': 1501756, 'Federal Communications Commission': 1501756, 'Federal Trade Commission': 1501756, 'Sexual Harassment': 1501756, 'Sexual Assault': 1501756, 'Bloomberg': 1508735, 'Washington Post': 1501756, 'Yvette Vega': 1501756, 'sxephil': 1501756, 'the philip defranco show': 1501756, 'philip defranco show': 1501756, 'philip defranco': 1501756, 'DeFranco': 1501756, 'proto putty': 589827, 'proto': 589827, 'putty': 589827, 'silicone': 749197, 'corn starch': 589827, 'liquid nitrogen': 589827, 'furnace': 589827, 'backyard foundry': 589827, 'torture test': 589827, 'experiment': 1833979, 'stress': 589827, 'freeze': 589827, 'grant thompson': 1206522, 'king of random': 1206522, 'the king of random': 1206522, 'random happens': 1502962, 'tkor': 1502962, 'thekingofrandom': 1206522, 'random': 2196242, 'build project': 589827, 'weekend project': 589827, 'try this at home': 589827, 'grant thompson king of random': 1206522, 'science experiment': 589827, "Sia Santa's Coming For Us Holiday": 684778, 'https://www.gofundme.com/pmxux-hope-for-holly': 349459, "tv's": 259918, 'gaming': 2553829, 'uravgconsumer': 259918, 'uac': 259918, 'your average consumer': 259918, 'black friday tech deals': 302828, 'best black friday tv deals': 259918, 'tech 2017': 688056, 'best black friday deals 2017': 259918, '4k tv': 259918, 'xbox one s': 259918, 'playstation 4': 259918, 'budget tech': 259918, 'dslr camera': 259918, 'tech under 100': 259918, 'xbox one': 259918, 'ps4 pro': 259918, 'black friday deals': 1676232, 'amazon deals': 612680, 'amazon black friday': 259918, 'gunpla': 259918, 'the walking dead no mans land by anwar jibawi hannah stocking inanna sarkis': 619503, 'The Walking Dead: No Man’s Land by Anwar Jibawi': 619503, 'Hannah Stocking & Inanna Sarkis': 619503, 'Roy Moore.': 1382432, 'Charlie rose': 1382432, 'Senate': 1418206, 'Subway Busking': 1134859, 'Maroon 5': 1134859, 'James Valentine': 1134859, 'Adam Levine': 1134859, 'Red Pill Blues': 1134859, 'Overexposed': 1134859, 'pop rock': 1134859, 'funk rock': 1134859, 'Sugar': 1134859, 'Crazy Little Thing Called Love': 1134859, 'jet packinski liza koshy': 630362, 'jet packinski reaction': 630362, 'jet packinski iii': 630362, 'jet packinski funny moments': 630362, 'jet packinski best moments': 630362, 'liza koshy jet packinski': 630362, 'liza koshy jet': 630362, 'liza koshy reacting': 630362, 'liza koshy reacts': 630362, 'jet liza koshy': 630362, 'allen pan': 420655, 'sufficiently advanced': 420655, 'sufficientlyadvanced': 104510, 'backyard scientist': 104510, 'backyard science': 104510, 'molten aluminum': 104510, 'deep fried turkey': 146888, 'deep fried turkey gone wrong': 104510, 'gravy train': 256249, 'gravy': 256249, 'friendsgiving recipes': 104510, 'friendsgiving vlog': 104510, 'friendsgiving ideas': 104510, 'gravy recipe': 104510, 'molten': 104510, 'molten metal': 116978, 'molten aluminum vs': 104510, 'molten aluminum casting': 104510, 'shitty robot': 310933, 'gravy boat': 151739, 'SNS': 1709054, 'cars 3': 1273343, 'cars': 2236498, 'pixar': 1317173, 'the emoji movie': 1586719, 'emoji movie': 1586719, 'the emoji movie review': 1586719, 'the emoji movie plot': 1586719, 'emoji movie review': 1586719, 'the emoji movie honest trailer': 1586719, 'inside out': 9445304, 'wreck-it ralph': 1586719, 'inside out 2': 1586719, 'the emoji movie sequel': 1586719, 'the lego movie': 1586719, 'Lady': 614355, 'Gaga': 605722, 'Cure': 605722, 'Joanne': 605722, 'Tour': 2189496, 'will': 902902, 'be': 605722, 'right': 605722, 'side': 605722, 'I’ll': 605722, 'fix': 6921271, 'cyber monday': 439366, 'best black friday deals': 309852, 'cyber monday 2017': 439366, 'best buy': 309852, 'Brat': 2437554, 'Chicken Girls': 2437554, 'Chicken Girls Episdode 8': 2437554, 'Chicken Girls 8': 2437554, 'Chicken Girls Season 2': 2437554, 'Hannie': 2437554, 'Annie LeBlanc': 2437554, 'Hayden Summerall': 2437554, 'Hayden Annie Chicken Girls': 2437554, 'Brat Chicken Girls': 2437554, 'Brooke Butler': 2437554, 'Carson Lueders': 2437554, 'kandee johnson': 109530, 'kandee': 109530, 'NEW': 89763, 'KYLIE JENNER': 89763, 'KYLIE LIP KIT': 89763, 'KYLIE HOLIDAY': 89763, 'makeup review': 1651152, 'testing makeup': 89763, 'kylie cosmetics': 89763, 'kylie cosmetics holiday 2017': 89763, 'holiday makeup': 131262, 'holiday wet set': 89763, 'sugar lip': 89763, 'spice lip': 89763, 'kylie cosmetics review': 89763, 'testing out': 89763, 'kandy johnson': 109530, 'candy johnson': 109530, 'kylie cosmetics holiday lip kits': 89763, 'SURPRISE VIDEO': 89763, 'kylie review': 89763, 'women owned makeup brands': 295351, 'abh': 295351, 'anastasia beverly hills': 310554, 'makeup brands': 295351, 'indie makeup brands': 295351, 'full face tutorials': 295351, 'the nerdwriter': 143128, 'nerdwriter': 143128, 'youtube nerdwriter': 143128, 'nerd writer': 143128, 'nerdwriter1': 143128, 'video essays': 143128, 'maus comic': 143128, 'art spiegelman maus': 143128, 'maus art spiegelman': 143128, 'maus art spiegelman comic': 143128, 'art spiegelman': 143128, 'maus': 143128, 'maus nerdwriter': 143128, 'art spiegelman nerdwriter': 143128, 'arti spiegelman graphic novel': 143128, 'art spiegelman comix': 143128, 'maus graphic novel': 143128, 'maus comix': 143128, 'graphic novel': 143128, 'comix': 143128, 'comix maus': 143128, 'comix page': 143128, 'how to design a comix page': 143128, 'graphic novel maus': 143128, 'art spiegelman comic': 143128, 'maus case study': 143128, 'comic': 143128, 'comix art spiegelman': 143128, 'channing': 1299846, 'tatum': 1299846, 'channing tatum': 1299846, '12 days': 1219137, 'giveways': 444833, '12 days of giveaways': 444833, 'ellen giveaways': 444833, 'hoildays': 444833, 'christmas gifts': 8136735, 'Whitney Houston Tribute': 33892, 'justiceleague': 594981, 'justice league box office': 663917, 'justice league box office bomb': 594981, 'justice league box office numbers': 594981, 'justice league sucked': 594981, 'justice league film': 594981, 'justice league flop': 594981, 'justice league flopped': 594981, 'justice league fail': 594981, 'justice league failure': 594981, 'justice league box office flop': 594981, 'justice league box office fail': 594981, 'justice league box office failure': 594981, 'justice league profit': 594981, 'justice league bombed': 594981, 'justice league bomb': 594981, 'justice league info': 594981, 'Ruby Riot': 707164, 'Liv Morgan': 707164, 'Sarah Logan': 707164, 'Becky Lynch': 707164, 'Naomi': 707164, 'sp:st=wrestling': 2966452, 'sp:dt=2017-11-21T20:00:00-04:00': 1724900, 'sp:ev=wwe-smack': 1724900, 'sp:ath=wwe-beckly': 707164, 'sp:ath=wwe-naomi': 707164, 'sp:ath=wwe-rubri': 707164, 'sp:ath=wwe-livm': 707164, 'ruby': 831544, 'riot': 707164, 'liv': 707164, 'morgan': 707164, 'sarah': 707164, 'smackdown': 2318262, 'beautiful': 2040718, 'lips': 3291827, 'dear': 93664, 'evan': 93664, 'hansen': 93664, 'broadway': 297316, 'going to broadway': 93664, 'mrianda sings': 93664, 'Bird (Animal)': 42513, 'Kookaburra (Organism Classification)': 42513, 'Wildlife (Film Subject)': 42513, 'Australia (Country)': 42513, 'Penatonix': 1629374, 'PTX': 1629374, 'PTXofficial': 1629374, 'Mitch Grassi': 1629374, 'Kirstie Maldonado': 1629374, 'Scott Hoying': 1629374, 'Avi Kaplan': 1629374, 'Kevin': 1629374, 'Olusola': 1629374, 'K-O.': 1629374, 'Cello': 1629374, 'Cellobox': 1629374, 'Beatbox': 1629374, 'A Cappella': 1629374, 'Harmony': 1629374, 'Acapella': 1647603, 'Acappela': 1629374, 'Choir': 1629374, 'Singing Competition': 1629374, 'The Sing-Off': 1629374, 'Sing-Off': 1629374, 'Reality TV': 1629374, 'Sing': 1629374, 'Singing': 1629374, 'Chorus': 1629374, 'Shawn Stockman': 1629374, 'Ben Folds': 1629374, 'Ben Folds 5': 1629374, 'X-Factor': 1629374, 'The Voice': 1629374, 'Voice': 1629374, 'Pitch Perfect': 2618494, 'deal guy': 171043, 'best deals': 171043, 'top 10 black friday': 42910, 'top 10 black friday 2017': 42910, 'top 10 tech deals': 42910, 'top 10 black friday tech deals': 42910, 'top 10 black friday 2017 tech deals': 42910, 'tech deals': 171043, 'black friday 2017 tech deals': 42910, 'best tech deals': 42910, 'top tech deals': 42910, 'black friday 2017 deals': 42910, 'best deals black friday': 42910, 'amazon tech deals': 42910, 'best black friday tech deals': 42910, 'deals': 44291, 'black': 379456, 'friday': 114174, 'walmart tech deals': 42910, 'secret santa': 431883, 'tree': 1070497, 'decs': 431883, 'hp sprocket': 431883, 'hamper': 431883, 'sneaking into': 199355, 'sneaking in': 199355, 'universal studios': 199355, 'universal': 199355, 'disneyland': 270778, 'los angeles zoo': 199355, 'amusement park': 199355, 'sneaking into amusement park': 199355, 'hi vis jacket': 199355, 'yes theory': 199355, 'exclusive hollywood pool party': 199355, 'sneak into': 199355, 'nightscape': 199355, 'sneaking into movie theatre': 199355, 'universal amusement park': 199355, 'happy potter roller coaster': 199355, 'sneaking into a museum': 199355, 'sneaking into restaurant': 199355, 'life hacks for free': 199355, 'sneaking in for free': 199355, 'social engineering': 199355, 'clever prank': 199355, 'funny prank': 199355, 'The Crown': 151813, 'Royal Family': 41474, 'British': 41474, 'The Queen': 54824, 'Matt Smith': 54824, 'John Lithgow': 41474, 'Claire Foy': 165163, 'Drama': 2929291, 'CRWNS2MT': 41474, 'Philip': 41474, 'Philip Trailer': 41474, 'The Crown Season 2': 41474, 'PLvahqwMqN4M1uQ5JITdkmNrxZnwtUG-DP': 1084881, 'parris goebel': 41474, 'the royal family': 41474, 'royalty': 41474, 'crown show netflix': 41474, 'whisper challenge': 98851, 'Car': 14071, 'Totaled': 14071, 'Memes': 14071, 'Songs': 14071, 'CarCrashMusic': 14071, 'Accident': 14071, 'Fun': 14071, 'Unfortunate': 14071, 'Repeat': 131771, 'Mi\u200b \u200bGente': 131771, 'J\u200b \u200bBalvin\u200b \u200b&\u200b \u200bWilly\u200b \u200bWilliam': 131771, 'Tommy Edison': 19261, 'Blind Film Critic': 19261, 'Ben Churchill': 19261, 'blind': 19261, 'blindness': 19261, 'visually impaired': 19261, 'blind youtuber': 19261, 'disability': 19261, 'disabled': 19261, 'fan mail': 19261, 'mailbag': 19261, 'mail time': 19261, 'mail monday': 19261, 'lps': 19261, 'littlest pet shop': 19261, 'PinkBunnyGirl43': 19261, 'Guessing Objects I Got In The Mail': 19261, 'mylifeaseva': 571588, 'things parents do': 571588, 'things parents do on christmas': 571588, 'things parents do on halloween': 571588, 'your parents on christmas': 571588, 'child you vs high school you': 571588, 'eva gutowski': 571588, 'alisha marie': 1445863, 'mia stammer': 571588, 'remi': 571588, 'child vs teen you': 571588, 'then vs now': 571588, 'parents on christmas': 571588, 'christmas video': 571588, 'christmas funny': 571588, 'christmas diy': 571588, 'holiday diy': 571588, 'holiday funny': 571588, '12 Strong': 1042458, '12 Strong Movie': 1042458, 'Michael Peña': 1042458, 'Navid Negahban': 1042458, 'Trevante Rhodes': 1042458, 'Rob Riggle': 1042458, 'Elsa Pataky': 1042458, 'Geoff Stults': 1042458, 'Nicolai Fuglsig': 1042458, 'Jerry Bruckheimer': 1042458, 'War Movies': 1042458, 'True Story': 1042458, 'Alcon': 1042458, 'Alcon Entertainment': 1042458, 'Black Label Media': 1042458, 'Lionsgate': 1042458, 'Warner Bros': 1070743, 'Warner Bros Pictures': 1070743, 'Horse Soldiers': 1042458, 'U.S. Army': 1042458, 'U.S. Special Forces': 1042458, 'Task Force Dagger': 1042458, 'Green Berets': 1042458, 'twitch': 87397, 'zendaya': 582383, 'curly hair routine': 545535, 'curly hairstyles': 545535, 'curly hair tutorial': 545535, 'zendaya hair tutorial': 545535, 'zendaya hair': 545535, 'zendaya hairstyles': 545535, 'zendaya hair routine': 545535, 'zendaya makeup tutorial': 545535, 'hair tutorial': 545535, 'hair transformation': 572433, 'hair hacks': 545535, 'hair tutorial for black women': 545535, 'hair tutorial easy': 545535, 'zendaya coleman': 545535, 'zendaya replay': 545535, 'zendaya wild n out': 545535, 'zendaya and tom holland': 545535, 'zendaya and zac efron': 545535, 'soccer': 10267804, 'fussball': 1624478, 'futebol': 1624478, 'football boots': 1624478, 'football cleats': 1624478, 'boots': 1624478, 'cleats': 1624478, 'adidas': 1624478, 'adidas football': 1624478, 'allin': 1624478, 'adidas futebol': 1624478, 'ace boots': 1624478, 'x boots': 1624478, 'messi boots': 1624478, 'ace17': 1624478, 'x17': 1624478, 'messi17': 1624478, 'nemeziz boots': 1624478, 'nemesis': 1624478, 'adidas nemeziz': 1624478, 'copa17': 1624478, 'pogba': 1624478, 'predator': 1624478, 'new predator': 1624478, 'adidas predator': 1624478, 'paul pogba': 1624478, 'mesut ozil': 1624478, 'adidas ad': 1624478, 'preds': 1624478, 'pred': 1624478, 'screen junkies news': 206129, 'screenjunkies news': 206129, 'dc box office': 68936, 'wonder woman 2': 68936, 'aquaman movie': 68936, 'wonder woman sequel': 68936, 'portugal the man': 97414, 'portugal. the man': 97414, 'woodstock': 97414, 'feel it still': 97414, 'john gourley': 97414, 'feel it still band': 97414, 'Meaning of Life': 58677, 'Love So Soft': 58677, 'AMAs 2017': 58677, 'Miss Independent': 58677, 'Kelly': 58677, 'Clarkson': 58677, 'Yolanda Gampp': 866203, 'Yolanda Gamp': 866203, 'How To Cake It': 866203, 'Cakes': 866203, 'Cake': 866203, 'Sugar Stars': 548337, 'How To Cake It By Yolanda': 866203, 'Buttercream': 866203, 'Vanilla Cake': 548337, 'Chocolate': 866203, 'Vanilla': 548337, 'Recipe': 1003910, 'Chocolate Cake Recipe': 866203, 'Simple Syrup': 769953, 'thanksgiving ham': 452087, 'cake art': 452087, 'amazing cake decoration': 452087, 'satisfying cake decoration': 452087, 'thanksgiving foods': 452087, 'fondant': 761882, 'mashed potatos': 452087, 'cakeover': 452087, 'roasted ham': 452087, 'roasted ham cake': 452087, 'thanksgiving ham cake': 452087, 'thanksgivin dessert': 452087, 'food cakes': 452087, 'thanksgiving baking': 452087, 'thr': 80122, 'the hollywood reporter': 80122, 'hollywood reporter': 80122, 'close up': 80122, 'saoirse ronan': 107147, 'saoirse ronan lady bird': 66181, 'saoirse ronan interivew': 66181, 'saoirse': 66181, 'ronan': 66181, 'harassment': 1575321, 'equal pay': 66181, 'equal': 66181, 'pay': 66181, 'income inequality': 66181, 'women in hollywood': 66181, 'equality': 67288, 'close up with thr': 80122, 'thr roundtables': 80122, 'roundtable': 80122, 'oscar': 80122, '2018 roundtables': 66181, 'oscars roundtable': 80122, 'Find You': 112466, 'Jonas': 364134, 'green day': 118094, 'green': 1206061, 'day': 689278, 'Ratko Mladic': 18438, 'Mladic': 18438, 'genocide': 18438, 'guilty of genocide': 18438, 'Bosnia war': 18438, 'Bosnia': 18438, 'Butcher of Bosnia': 18438, 'the hague': 18438, 'the sentence of mladic': 18438, 'serbia': 18438, 'serbian': 18438, 'bbc latest news': 18438, 'bbc breaking news': 18438, 'mladić': 18438, 'Ratko Mladić': 18438, 'Critics': 37844, 'movie news': 37844, 'Quentin Tarantino': 78847, 'Sony': 37844, 'Deal': 37844, 'King Kong': 37844, 'Godzilla': 37844, 'Parenting tips': 37705, 'Child development': 37705, 'Child behavior': 37705, 'Success': 37705, 'Discipline': 37705, 'Relationships': 37705, 'most expensive steak': 160591, 'wagyu steak': 160591, 'how to cook wagyu': 160591, 'kobe beef': 160591, 'beef': 839407, 'steakhouse': 160591, 'meat lovers': 160591, 'new york food': 160591, 'best restaurants new york': 160591, 'best steak': 160591, 'where to eat new york': 160591, 'wagu': 160591, 'old homestead': 160591, 'restaurant history': 160591, 'japanese auction': 160591, 'rarest foods': 160591, '2 chainz': 160591, 'CBS 4 News Morning': 1095, 'La David Johnson': 1095, 'Joan Murray': 1095, 'Soldiers Ambushed': 1095, 'Niger': 1095, 'Amazon Alexa': 2869, 'Amazon Echo Show': 2869, 'Echo Show': 2869, 'YouTube videos': 2869, 'amas 2017 ama american music a': 82123, 'american music awards abc': 82123, '2017 amas live': 82123, 'american music awards red carp': 82123, 'american music awards 2017': 82123, 'whitney houston': 82123, 'american music awards news': 82123, 'american music awards host': 82123, 'Yule': 342587, 'Shoot': 342587, 'Your': 342587, 'Eye': 342587, '80s commercial': 1480, 'kamr': 1480, 'amarillo': 1480, 'texas': 37855, 'miami vice': 1480, 'thunderbird': 1480, 'beauty with mi': 61391, 'full face makeup': 41532, 'trendy': 143802, 'eyeshadow': 15203, 'eyeshadow tutorial': 15203, 'eyeshadow palette': 15203, 'too faced': 15203, 'rihanna makeup': 15203, 'best eyeshadow': 15203, 'best makeup': 74698, 'palette': 15203, 'best of beauty': 15203, 'holiday 2017': 41532, 'swatches': 32575, 'new eyeshadow palette': 15203, 'new makeup': 41532, 'beauty writer': 24160, 'Wochit': 8448, 'E!': 21764, 'Top Stories': 8448, 'Pop Culture': 15583, 'Breaking News': 8448, 'Breaking': 8448, 'Interviews': 15583, 'E! Style Collective': 8448, 'Jason Kennedy': 8448, 'Catt Sadler': 8448, 'Sibley Scoles': 8448, 'nichrome': 3391, 'nichrome wire': 3391, 'resistance wire': 3391, 'acrylic bending': 3391, 'lexan bending': 3391, 'electronics enclosure': 3391, 'diy enclosure': 3391, 'diy electronics enclosure': 3391, 'plexiglass bending': 3391, 'Hackaday': 2367, 'AUV': 2367, 'Open Source Underwater Glider': 2367, 'ROV': 2367, 'ocean research drone': 2367, 'Hackaday Prize': 2367, 'hobbyist': 5584, '3D printer': 5584, 'self reliance': 5584, 'open source': 5584, 'traditional animation': 350853, 'fast food': 1384316, 'jolly ranchers': 296440, 'how to make your own candy': 296440, 'can you cast jolly ranchers': 296440, 'grant Thompson': 296440, 'hard candy': 296440, 'suckers': 296440, 'make your own': 299677, 'lollipop': 296440, 'giant candy': 296440, 'big lollipop': 296440, 'world’s biggest': 296440, 'chicken mould': 296440, 'silicone mold': 296440, 'lollipop recipe': 296440, 'diy jolly rancher candy': 296440, 'candy recipes': 296440, 'jolly rancher shot glasses': 296440, "world's largest": 296440, 'giant': 296440, 'candy casting': 296440, 'thumbsuckers': 296440, 'candy play buttons': 296440, 'edible shapes': 296440, 'rainbow': 296440, 'giant lollipop': 296440, 'Denzel Washington': 184579, 'Wonders': 184579, 'Exactly': 184579, 'Drake': 184579, 'Tattooed': 184579, 'Face': 184579, 'Roman J. Israel': 184579, 'Training day': 184579, 'American Gangster': 184579, 'Man on Fire': 184579, 'Inside Man': 184579, 'The Magnificent Seven': 184579, 'The Equalizer': 184579, 'Games With Guests': 184579, 'Roman J. Israel Esq': 184579, 'He Got Game': 184579, 'Fences': 184579, 'Viola Davis': 184579, 'airpods dancing ad': 687321, 'airpods snow ad': 687321, 'airpods holiday ad': 687321, 'airpods sway': 687321, 'apple dancing ad': 687321, 'apple snow ad': 687321, 'apple holiday': 687321, 'apple holiday ad': 687321, 'apple sway': 687321, 'iphone dancing ad': 687321, 'iphone snow ad': 687321, 'iphone holiday': 687321, 'iphone holiday ad': 687321, 'iphone sway': 687321, 'iphone x snow ad': 687321, 'iphone x holiday': 687321, 'iphone x holiday ad': 687321, 'iphone x sway': 687321, 'christopher grant': 687321, 'lauren yelango-grant': 687321, 'sam smith ad': 687321, 'sam smith palace': 687321, 'move someone this': 687321, 'sway': 687321, 'mousetrap car': 588908, 'thebackyardscientist': 601376, 'Mousetrap': 588908, 'street science': 588908, 'mousetrap': 588908, 'Neymar': 434617, 'Neymar entrevista': 434617, 'Neymar fala': 434617, 'Neymar se irrita': 434617, 'Neymar abandona a entrevista': 434617, 'PSG': 434617, 'Celtic': 434617, 'Neymar caneta': 434617, 'Neymar goal': 434617, 'Neymar gol': 434617, 'Neymar PSG': 434617, 'Real Madrid': 434617, 'Neymar Real Madrid': 434617, 'Paris Saint Germain vs Celtic': 434617, 'UEFA': 434617, 'champions league': 434617, 'Liga dos campeões': 434617, 'Jinder Mahal': 1017736, 'Samir Singh': 1017736, 'Sunil Singh': 1017736, 'sp:ath=wwe-girsi': 1017736, 'sp:ath=wwe-harsi': 1017736, 'sp:ath=wwe-ajst': 1017736, 'sp:ath=wwe-jinma': 1017736, 'jinder': 1017736, 'mahal': 1017736, 'champion': 1017736, 'rematch': 1017736, 'styles': 1017736, 'aj': 1017736, 'wwe smackdown': 1017736, 'aj styles vs jinder mahal': 1017736, 'wwe smackdown live': 1017736, 'Jonathan Cheban': 179997, 'hackaday': 2078, 'mechanical keyboard': 41174, 'hack': 1487725, 'hacker': 3503, 'maker': 203781, 'maker space': 2078, 'hacker space': 2078, 'Royal Academy of Arts': 17164, 'art': 1684293, 'visual arts': 17164, 'Jackson Pollock': 17164, 'Abstract Exhibition': 17164, 'ford fairlane': 5997, 'loaded': 5997, 'gun': 270899, 'toss': 5997, 'Mcdonalds': 4387, 'Wendys': 4387, 'Burger king': 4387, 'hardees': 4387, 'kfc': 4387, 'chick fil a': 4387, "carl's jr": 4387, 'jack in the box': 4387, 'dairy queen': 4387, 'sonic': 4387, 'taco bell': 46765, 'in-and-out burger': 4387, "rally's": 4387, "checker's": 4387, "arby's": 4387, 'ketchup': 4387, 'bbq sauce': 4387, 'mustard': 4387, 'sauce': 4387, 'condiments': 4387, 'nuggets': 4387, 'fry': 4387, 'mcnuggets': 4387, 'tenders': 4387, 'chicken': 2122600, 'dipping': 4387, 'vent': 4387, 'holder': 4387, 'road trip': 4387, 'ranch': 4387, '#safesauce': 4387, 'dipclip': 4387, 'milkmen': 4387, 'sweet and sour': 4387, 'spicy chicken': 873455, 'szechuan sauce': 4387, 'popeyes': 4387, 'honey mustard': 4387, 's’awesome sauce': 4387, 'sriracha': 25824, 'buffalo': 4387, 'mac sauce.': 4387, 'meal': 12787, 'feast': 12787, 'Black Eyed Peas': 4143, 'Masters Of The Sun: The Zombie Chronicles': 4143, 'Augmented Reality': 4143, 'Graphic Novels': 4143, 'Mobile App': 4143, 'Social Justice': 4143, 'Common': 431299, 'Mary J Blidge': 4143, 'KRS-One': 4143, 'Snoop': 4143, 'Snoop Dogg': 4143, 'Rakim': 4143, 'India Love': 4143, 'Charlamagne Tha God': 4143, 'Stan Lee': 6230958, 'Michael Rapaport': 4143, 'Rosario Dawson': 4143, 'Jaden Smith': 4143, 'Pete Rock': 4143, 'John DiMaggio': 4143, 'Redman': 4143, 'Flavor Flav': 4143, 'Eric B.': 4143, 'panel': 49914, 'mount': 49914, '22mm': 49914, 'LED': 49914, 'voltmeter': 49914, 'volt': 49914, 'meter': 49914, 'ac': 49914, '110v': 49914, '120v': 49914, '220v': 49914, '230v': 49914, '240v': 49914, 'yellow': 49914, 'blue': 89029, 'white': 49914, 'digital': 49914, 'display': 49914, 'diagnostic': 49914, 'power': 1239514, 'indicator': 49914, 'Mase': 283379, 'The Oracle': 283379, "Cam'Ron": 283379, 'Diss': 283379, 'MA$E': 283379, 'ok go': 2618344, 'concerts': 2618344, 'obsession': 2860542, 'paper': 2618344, 'printer': 2816699, 'greenpeace': 2618344, 'live stream': 2618344, '360 video': 2618344, 'fun videos for kids': 2618344, 'vzinside360live': 2618344, 'macysparade': 2618344, "macy's thanksgiving day parade": 2618344, 'thanksgiving day parade': 2618344, 'parade': 2618344, "macy's balloons": 2618344, 'thanksgiving parade 360 video': 2618344, 'verizon 360 video': 2618344, 'verizon parade': 2618344, "macy's parade": 2618344, 'nbc parade': 2618344, 'seth meyers': 3380250, 'Bruce Springsteen': 183357, 'Broadway': 183357, 'jitterbug dance': 183357, 'Ashe': 183357, 'dogs': 4116311, 'Albert': 183357, 'pinocchio': 183357, 'Stand-Up Battle': 279273, 'Jerry Seinfeld': 279273, 'wait up': 279273, 'Star Wars': 1753639, 'lightsaber': 279273, 'pizza hut': 279273, 'woman': 3455338, 'skit': 6224095, '“lilly': 2933128, 'singh”': 2933128, '“youtube': 2933128, 'superwoman”': 2933128, 'carlie': 1278294, 'when you meet your exs new girlfriend': 1273356, 'girlfriend kloss': 1273356, 'lily': 1273356, 'lilly karlie kloss': 1273356, 'superwoman karlie kloss': 1273356, 'ex new girlfriend': 1273356, 'new girlfirend karlie kloss': 1273356, 'lilly karlie': 1273356, 'karly kloss': 1273356, 'when you meet karlie': 1273356, 'meet my ex': 1273356, '12 collabs of christmas': 2147631, '12 christmas collab': 1273356, 'christmas collabs lilly singh': 1273356, 'christmas lilly': 1273356, 'superwoman 12 collabs': 1273356, 'Interscope Records': 621447, 'Warner Brothers Records': 621447, 'Smallfoot': 427156, 'Smallfoot Movie': 427156, 'Bigfoot': 427156, 'Yeti': 427156, 'Channing Tatum': 427156, 'LeBron James': 475743, 'Danny DeVito': 427156, 'Ely Henry': 427156, 'Jimmy Tatro': 427156, 'Karey Kirkpatrick': 427156, 'Glenn Ficarra': 427156, 'John Requa': 427156, 'Phil Lord': 6653971, 'Chris Miller': 6653971, 'Warner Brothers': 455441, 'Warner Animation Group': 427156, 'WAG': 427156, 'beauty evolution': 102270, 'celebrity style': 102270, 'pop star': 201847, 'wigs': 102270, 'makeovers': 102270, 'haute couture': 26736, 'trnsformations': 26736, 'music awards': 26736, 'prism': 26736, 'teenage dream': 26736, 'hot n cold': 26736, 'california gurls': 26736, 'recording artist': 26736, 'dark horse': 26736, 'kissed a girl': 26736, 'transform': 1394128, 'transforms': 26736, 'body transformation': 26736, 'olaf': 154871, "olaf's frozen adventure": 198701, 'ilsa': 154871, 'elsa': 154871, 'hans': 154871, 'kristoff': 154871, 'sven': 154871, 'olaf in summer': 154871, "when we're together": 154871, 'let it go': 154871, 'do you want to build a snowman': 154871, 'love is an open door': 154871, 'for the first time in forever': 154871, 'josh gad': 154871, 'idina menzel': 154871, 'Kristen bell': 154871, 'jonathan groff': 154871, 'coco': 198701, 'Disney animation': 154871, 'Disney Pixar coco': 154871, 'alex jones': 101069, 'infowars': 10283, 'prison planet': 10283, 'nwo': 10283, 'Alex Jones (radio Host)': 10283, 'new world order': 10283, 'globalist elite': 10283, 'Alex Jones (presenter)': 10283, 'mike adams': 10283, 'natural news': 10283, 'Pepsi': 10283, 'aborted fetus': 10283, 'aborted babies': 10283, 'food enhancer': 10283, 'babies as food flavouring': 10283, 'soda testing': 10283, 'pepsi vs coke': 10283, 'gmo and soda': 10283, 'Coca-Cola (Invention)': 10283, 'Beach': 86744, 'Boys': 86744, 'Brian': 86744, 'Wilson': 86744, 'Dennis': 86744, 'Al': 86744, 'Jardine': 86744, 'Mike': 248108, 'Pet': 86744, 'Sounds': 86744, 'Tony': 86744, 'Asher': 86744, 'Wrecking': 86744, 'Crew': 86744, 'Roll': 86744, 'Session': 86744, "60's": 86744, 'Los': 86744, 'Angeles': 86744, 'scubadiving': 26294, 'scuba': 26294, 'kelp forest': 26294, '360°': 26294, '360° Norwegian Kelp Forest Soundscape': 26294, '#OurBluePlanet': 26294, 'soundscape': 26294, 'underwater': 26294, 'sea': 26294, 'All Star': 22963, 'Origami': 22963, 'Shrek': 22963, 'Smash Mouth': 22963, 'hoops': 556237, 'finals': 556237, 'zach lee': 27186, 'cavaliers': 960549, 'pistons': 27186, 'SDC': 27186, 'andre drummond': 27186, 'lebron james': 684270, 'elle': 847205, 'elle magazine': 847205, 'elle video': 847205, 'elle magazine videos': 847205, 'romee strijd': 827359, "victoria's secret fashion show": 1384430, 'vs fantasy bra': 305993, "victoria's secret fashion show 2017 fall": 305993, 'romee strijd workout': 305993, 'romee strijd interview': 305993, "romee strijd victoria's secret": 305993, "romee strijd victoria's secret angel": 305993, "victoria's secret runway": 305993, 'Charlie Brooker': 2697065, 'Humans': 1917148, 'arkep': 823882, 'Jesse Plemons': 2697065, 'John Hillcoat': 2305426, 'Black Museum': 2489842, 'Arkangel': 2697065, 'Jodie Foster': 2697065, 'Crocodile': 2551819, 'U.S.S. Callister': 2098203, 'USS Callister': 2489842, 'Jimmi Simpson': 2305426, 'Metalhead': 2697065, 'Orphan Black': 1502176, 'San Junipero': 2697065, 'Hang the DJ': 2305426, 'Michaela Joel': 2305426, 'the twilight zone': 1709399, 'TAGS: Black Mirror': 1191193, 'Timothy Van Patten': 2076710, 'Babs': 2261126, 'Cristin Milioti': 2697065, 'Letitia Wright': 2076710, 'David Slade': 2076710, 'Billy Magnussen': 2305426, 'Olusanmokun': 2261126, 'Toby Haynes': 2697065, 'Colm McCarthy': 2076710, 'Mr. Robot': 1502176, 'casually explained': 917612, 'wealth': 1159810, 'levels of wealth': 917612, 'richest people': 917612, '1%': 917612, '99%': 917612, '99% and 1%': 917612, 'rich vs poor': 917612, 'socioeconomic status': 917612, 'wealth pyramid': 917612, 'how to get rich': 917612, 'does money make you happy': 917612, 'are rich people happier': 917612, 'stand up comedy': 917612, 'animated comedy': 917612, 'threadbanger': 2548833, 'Corinne Leigh': 2218709, 'Rob Czar': 2218709, 'man vs pin': 2218709, 'pinterest': 2218709, 'pinterest fails': 2218709, 'man vs house': 721104, 'lowes': 721104, 'fixer upper': 721104, 'omg we bought a house': 721104, 'the weekender': 721104, 'howto': 307530, 'clothes': 2979646, 'bunny': 282446, 'j beauty': 152198, 'k beauty': 152198, 'korean beauty': 152198, 'japanese makeup': 152198, 'tony moly': 152198, 'korean skincare': 152198, 'beauty routine': 183963, 'skincare routine': 314374, 'sanrio': 152198, 'sailor moon': 152198, 'yesstyle': 152198, 'yes style': 152198, 'wish': 2858874, 'schizophrenia': 276444, 'shizo': 276444, 'mental illness': 436784, 'depression anxiety': 276444, 'healthy mind': 276444, 'unhealthy': 276444, 'pyschology': 276444, 'pyschatrist': 276444, 'mental health professional': 276444, 'diagnosis': 276444, 'treatment': 1593298, 'treat': 276444, 'voices': 276444, 'voice': 816049, 'hearing': 276444, 'hear': 276444, 'hearing things': 276444, 'understand': 276444, 'people try': 276444, 'auditory hallucination': 276444, 'hallucination': 276444, 'simulation': 276444, 'simulator': 276444, 'depression': 581661, 'disorder': 276444, 'bipolar disorder': 276444, 'mental': 568551, 'anxiety': 742001, 'psychologist': 276444, 'psychiatry': 276444, 'psychiatrist': 276444, 'musicals': 122841, 'singers': 122841, 'songs': 1111961, 'piano': 200874, 'theatre': 122841, 'theater': 319722, 'once the musical': 122841, 'once': 122841, 'the producers': 122841, 'holy musical b@tman': 122841, 'holy musical batman': 122841, 'be more chill': 122841, 'urinetown': 122841, 'women on the verge of a nervous breakdown': 122841, '21 chump street': 122841, 'hadestown': 122841, 'death note': 122841, 'deathnote': 122841, 'VR': 1035913, 'funnny': 1035913, 'break tv': 1035913, 'cool': 3202152, 'broken': 8593014, 'shit': 1035913, 'phil is not on fire': 1476167, 'phil is not on fire 9': 1476167, 'dan and phil': 2056963, 'phil lester': 2056963, 'dan howell': 2056963, 'pinof': 1476167, 'pinof9': 1476167, 'philisnotonfire9': 1476167, 'philisnotonfire': 1476167, 'whiskers': 1476167, 'questions': 1693088, 'daniel howell': 1476167, 'danisnotonfire': 2056963, 'amazingphil': 2056963, 'interactive introverts': 1476167, 'dan and phil tour': 1476167, 'electric car': 357052, 'comuta car': 357052, 'citicar': 357052, 'lithium batteries': 357052, 'compact car': 357052, 'small car': 357052, 'weird car': 1062221, 'fun cars': 357052, 'chef vitale': 84924, 'chef laura vitale': 84924, 'laura vitale': 84924, 'chef laura': 84924, 'seasalt': 45929, 'sea salt': 45929, 'pretzel': 45929, 'bark': 45929, 'chocolate bark': 45929, 'how to bake': 695896, 'homemade salted': 45929, 'homemade bark': 45929, 'homemade salted bark': 45929, 'chocolate bark dessert': 45929, 'holiday dessert': 45929, 'fairly oddparents style': 76217, 'Kalisto': 593362, 'Sasha Banks': 593362, 'Triple H': 593362, 'Fandango': 593362, 'Alicia Fox': 593362, 'Goldberg': 593362, 'Jerry the King': 593362, 'Lawler': 593362, 'Charlotte Flair': 593362, 'Paul Heyman': 593362, 'wwe superstars': 593362, 'raw': 2016508, 'wwe quiz': 593362, 'wwe wrestlers': 593362, 'wwe trivia': 593362, 'guessing game': 593362, 'john cena': 1722064, 'funny games': 593362, 'fail compilation': 23108, 'domino fail': 23108, 'domino fail compilation': 23108, 'chain reaction': 482071, 'rube goldberg machine': 482071, 'marble run': 482071, 'marble machine': 23108, 'massive chain reaction': 23108, '2 story chain reaction': 23108, 'domino fall': 482071, 'marble track': 23108, 'rube goldberg': 482071, 'chain reactions': 482071, 'domino': 482071, 'dominos': 486595, 'dominoes': 482071, 'hevesh5': 482071, 'domino tricks': 482071, 'insane domino tricks': 482071, 'Domino Rally (Brand)': 23108, 'amazing triple spiral': 482071, 'domino spiral': 482071, '骨牌': 482071, 'ドミノ': 482071, 'Dominostein': 482071, 'домино': 482071, 'ντόμινο': 482071, '각설탕': 482071, 'Pitagora': 482071, 'Suichi': 482071, 'ピタゴラスイッチ': 482071, 'dog bath': 480037, 'mud bath dog': 143375, 'naughty dog': 143375, 'mud bath': 143375, 'dirty dog': 143375, 'mucky dog': 143375, 'swamp dog': 143375, '4 Ways to Style a Black Bodysuit | Ingrid Nilsen': 44330, 'missglamorazzi': 44330, 'fashion fourplay': 44330, 'fashion inspiration': 44330, 'fashion inspo': 44330, 'black bodysuits': 44330, 'style bodysuit': 44330, 'Sound': 4938, 'Unique': 4938, 'Unique Music': 4938, 'Unique Vibes': 4938, 'Vibes': 4938, 'Electronic Dance Music': 4938, 'Electronic': 4938, 'Electronic Music': 4938, 'Trap': 1366155, 'Chill': 4938, 'Chillstep': 4938, 'Future': 4938, 'Future Bass': 4938, 'Carlie Hanson - Only One (Lyrics)': 4938, 'carlie hanson': 4938, 'only one': 4938, 'carlie only one': 4938, 'hanson': 4938, 'carlie hanson only one': 4938, 'carlie hanson only one lyrics': 4938, 'only one lyrics': 4938, 'lyrics only one carlie hanson': 4938, 'lyrics only one carlie': 4938, 'carlie only one lyrics': 4938, 'carlie hanson lyrics': 4938, 'only one carlie hanson': 4938, 'only one lyrics carlie hanson': 4938, 'iPhone Mod': 636454, 'Glowing Speaker Mod': 636454, 'Glowing': 636454, 'Speaker': 636454, 'Mod': 636454, 'iPhone X Mod': 636454, 'iPhone 7 Mod': 636454, 'Glowing Speaker': 636454, 'Glowing Speakers': 636454, 'Make Your Speakers Glow': 636454, 'iPhone Glowing Mod': 636454, 'RGB': 636454, 'iPhone RGB Mod': 636454, 'Glowing iPhone Logo': 636454, 'Glowing iPhone Apple Logo': 636454, 'Glowing Logo': 636454, 'Logo': 636454, 'Glowing Apple Logo': 636454, 'Glowing Apple Logo Mod': 636454, 'RGB Visualizer': 636454, 'iPhone RGB Visualizer': 636454, 'iPhone X Glowing Apple Logo': 636454, 'Glowing Logo Mod': 636454, 'Glowing Speakers iPhone Mod': 636454, 'iPhone 7 Plus Mod': 636454, 'Glowing iPhone 7 Plus Mod': 636454, 'a Capella': 989120, 'acapella': 1029237, 'Anna Kendrick': 1816594, 'Rebel Wilson': 1816594, 'Skylar Astin': 989120, 'Adam Devine': 989120, 'Brittany Snow': 1816594, 'Anna Camp': 1816594, 'Ester Dean': 989120, 'Alexis Knapp': 989120, 'John Michael Higgins': 989120, 'Elizabeth Banks': 989120, 'Hana Mae Lee': 989120, 'Mean Girls': 989120, 'Bring It On': 989120, 'American Pie': 989120, 'Bridesmaids': 989120, 'sing a long': 989120, 'sing-a-long': 989120, 'The Sing Off': 989120, 'Get Pitch Slapped': 989120, 'Workaholics': 989120, 'Hairspray': 989120, 'Project X': 832700, 'Pitch Perfect 2': 832700, 'Pitch Perfect 2 - Official Trailer': 832700, 'PP2': 832700, 'Harry Connick Jr.': 3560, 'Harry': 3560, 'HCJ': 3560, 'HarryTV': 3560, 'Harry TV': 3560, 'sarah michelle gellar': 3560, 'name': 3560, 'sarah michelle prinze': 3560, 'crossbow': 17592, 'tank': 17592, 'crossbow homemade': 17592, 'crossbow pubg': 17592, 'crossbow hunting deer': 17592, 'crossbow hunting': 17592, 'crossbow shooting': 17592, 'homemade crossbow': 17592, 'turbo conquering mega eagle': 17592, 'the slingshot channel': 17592, 'joerg sprave': 17592, 'armor': 17592, 'bow and arrow': 17592, 'bow and arrow vs crossbow': 17592, 'bow and arrow trick shots': 17592, 'bow and arrow hunting': 17592, 'bow and arrow choke': 17592, 'bow and arrow kid': 17592, '1000 lb crossbow': 17592, 'turbo mega eagle': 17592, 'SAOIRSE': 26482, 'RONAN': 26482, 'MORONIC': 26482, 'IRONIC': 26482, 'IZZIE': 26482, 'COULD': 26482, 'NEVER': 26482, 'BE': 26482, 'WOMAN': 26482, 'EL': 26482, 'NOVIO': 26482, 'DE': 26482, 'MI': 26482, 'MADRE': 26482, 'jontron': 2519348, 'weird workout videos': 2519348, 'workout': 2519348, 'marky mark': 2717248, 'marky mark workout': 2519348, 'prancercise': 2519348, 'mariko takahashi': 2519348, 'poodle': 2519348, 'zuiikin english': 2519348, 'japanese workout': 2519348, 'estelle getty': 2519348, 'millineals hate': 332886, 'millineals killing': 332886, 'millineals are good': 332886, 'truth of millineals': 332886, 'fact about millineals': 332886, 'funny millineals': 332886, 'carmen angelica': 332886, 'bad millineals': 332886, 'millineals dumb': 332886, 'millineals smart': 332886, 'millineals facts': 332886, 'millineals secret': 332886, 'diamond industry': 332886, 'expensive': 1445438, 'PP3': 156420, 'Pitch Perfect 3': 156420, 'Riff off': 156420, 'Dorkly': 931112, 'pokemon disappointed': 931112, 'pokemon': 1079518, 'dorkly bits': 931112, 'serperior': 931112, 'shuckle': 931112, 'shuckle ghost': 931112, 'haunter': 931112, 'octillery evolution': 931112, 'pokemon disappointed evolution': 931112, 'Mount Agung': 82532, 'Mount Agung erupts': 82532, 'Bali': 82532, 'Bali volcano': 82532, 'international flights': 82532, 'spewing ask': 82532, 'volcanic ash': 82532, 'volcanic ash from Mount Agung': 82532, 'alert status': 82532, 'flights': 82532, 'disruption': 82532, 'natural disasters': 82532, 'volcanic eruptions': 82532, 'Bali volcanoes': 82532, 'Lambok': 82532, 'islands': 82532, 'tourists': 82532, 'stranded': 82532, 'emergency relief agencies': 82532, 'disaster relief agencies in Bali': 82532, 'markiplier makes': 580931, 'breakfast': 1085059, 'charity livestream': 580931, 'j.t. barrett': 256851, 'j.t.': 256851, 'barrett': 256851, 'john o’korn': 256851, 'o’korn': 256851, 'chris evans': 95487, 'evans': 95487, 'urban meyer': 256851, 'urban': 1088808, 'meyer': 256851, 'jim harbaugh': 256851, 'jim': 256851, 'harbaugh': 256851, 'ohio state buckeyes': 375537, 'ohio': 256851, 'state': 256851, 'buckeyes': 256851, 'Michigan wolverines': 256851, 'Michigan': 256851, 'wolverines': 256851, 'cfb': 461144, 'ncaa football': 480477, 'cfb highlights': 461144, 'ncaa highlights': 461144, 'fox sports': 461144, 'fs1': 461144, 'sp:ty=play': 299780, 'sp:dt=2017-11-25T12:00:00-05:00': 256851, 'sp:li=cfb': 461144, 'sp:ti:home=Mich': 256851, 'sp:ti:away=OhioSt': 461144, 'failed pitches': 601493, 'writers’ room': 601493, 'meetings': 601493, 'illuminati eye': 601493, 'insults': 601493, 'fuck you': 601493, 'passive aggressive': 1045890, 'katie marovitch': 1127362, 'siobhan thompson': 601493, 'mike trapp': 601493, 'grant o’brien': 601493, 'Hardly Working': 1127362, 'sketch is too real': 601493, 'sketch hits close to home': 601493, 'hardly working is this about me': 601493, 'katie does mean sketch': 601493, 'Hacks': 146011, 'Plunger': 146011, 'Takeout': 146011, 'bobby pins': 146011, 'home hacks': 146011, 'play doh': 146011, 'tic tacs': 146011, 'comic books': 37984736, 'nerdy': 37984736, 'geeky': 37984736, 'super hero': 37984736, 'agents of SHIELD': 248455, 's.h.i.e.l.d.': 248455, 'sneak peak': 248455, 'agents of SHIELD s5': 248455, 'clark gregg': 248455, 'Miss Universe': 437779, 'Pia Wurzbach': 437779, 'Pia Alonzo Wurzbach': 437779, 'Miss Philippines': 437779, 'Binibining Pilipinas': 437779, 'Miss World': 437779, 'Miss Earth': 437779, 'Beauty Pageant': 437779, 'Beauty Queen': 437779, 'Beauty Contest': 437779, 'Bikini Competition': 437779, 'Evening Gown': 437779, 'Iris Mittenaere': 437779, 'Miss Universe 2016': 437779, 'bethany mota': 987801, 'bethany moto': 987801, 'bethany': 987801, 'mota': 987801, 'macbarbie07': 987801, 'black friday haul': 987801, 'forever 21': 987801, 'bath and body works': 987801, 'pacsun': 987801, 'trying on': 987801, 'winter outfits': 987801, 'dad': 2538680, 'father': 1491606, 'family fun': 1491606, 'simply nailogical family': 1491606, 'simply nailogical dad': 1491606, 'dad does my nails': 1491606, 'my dad does my': 1491606, 'dad does my makeup': 1491606, 'dad does my voiceover': 1491606, 'meet my dad': 1491606, 'simplydadlogical': 1491606, 'amking': 740933, 'behind': 740933, 'getaway': 740933, 'get': 753723, 'thief': 740933, "james's": 740933, 'stole': 740933, 'keeping up with the gonzalezs pt 3': 11419332, 'keeping': 4754605, 'up': 4949180, 'gonzalezs': 4754605, 'pt': 4754605, 'Keeping Up With The Gonzalez’s (Pt. 3) | Lele Pons': 4754605, 'Rudy Mancuso & Inanna Sarkis': 4754605, 'mornings': 229631, 'smart duvet': 229631, 'shared': 831957, 'hans monderman': 831957, 'urbanism': 831957, 'planning': 831957, 'city': 831957, 'cities': 831957, 'town': 831957, 'roads': 831957, 'streets': 831957, 'naked streets': 831957, 'europe': 831957, 'structure': 831957, '99% invisible': 831957, 'ben hamilton-baille': 831957, 'radiotopia': 831957, 'safety': 970247, 'walkable': 831957, 'living': 1670508, 'transport': 831957, 'shared streets': 831957, 'hanyuqiao': 16823, 'how to make a gift box out of paper easy': 11028, 'how to make gift box out of paper': 11028, 'how to make a small gift box out of paper': 11028, 'Gift Box': 11028, 'How to make': 11028, 'paper box': 11028, 'craft': 11028, 'diy gift box': 11028, 'how to make gift box': 11028, 'gift box idea': 11028, 'easy gift box idea': 11028, 'how to make : gift box': 11028, 'small gift boxes': 11028, 'homemade gift box': 11028, 'how to make gift box at home': 11028, 'how to make handmade gift boxes': 11028, 'how to make a gift box step by step': 11028, 'sharing things': 318249, 'stranger things parody': 318249, 'sesame street parodies': 318249, 'sesame street parody': 318249, 'sesame street stranger things parody': 318249, 'tv show parodies': 318249, 'sesame street sharing things': 318249, 'the upside down': 318249, 'cookiegorgon': 318249, 'stranger things eleven': 403621, 'stranger things dustin': 318249, 'stranger things mike': 318249, 'stranger things will': 318249, 'stranger things barb': 403621, 'millie bobby brown': 403621, 'caleb mclaughlin': 318249, 'noah schnapp': 318249, 'finn wolfhard': 318249, 'stranger things lucas': 318249, 'clothmap': 72789, 'drew scanlon': 72789, 'são paulo': 72789, 'sao paulo': 72789, 'Santa Ifigênia': 72789, 'Santa Efigenia': 72789, 'pirated games': 72789, 'pirated game consoles': 72789, 'grey market': 72789, 'andré campos': 72789, 'jogabilidade': 72789, 'gus lanzetta': 72789, 'Rafael Quina': 72789, 'bootleg': 72789, 'brazilian game consoles': 72789, 'brazilian games': 72789, 'sega': 72789, 'telejogo': 72789, 'dynavision': 72789, 'telegame': 72789, 'guitar idol': 72789, 'smash mouth all star': 72789, 'clones': 72789, 'turbo game': 72789, 'nes clone': 72789, 'master system clone': 72789, 'dactar': 72789, 'chapolim x dracula': 72789, 'ghost house': 72789, 'mortal kombat nes': 72789, 'street fighter nes': 72789, 'sonic 4 snes': 72789, 'Gatorade': 957603, 'Serena Williams': 957603, 'Sisters In Sweat': 957603, '#SistersInSweat': 957603, 'Olympia': 957603, 'Keep Playing': 957603, 'Serena Baby': 957603, 'Alexis Olympia Ohanian Jr': 957603, 'Alexis Jr': 957603, 'Serena letter': 957603, 'Refinery29': 957603, 'Teen Vogue': 957603, 'Allison Williams': 957603, 'Elaine Welteroth': 957603, 'Tory Burch': 957603, 'Piera Gelardi': 957603, 'Susan Wojcicki': 957603, 'YouTube CEO': 957603, 'message to all girls': 957603, 'baby girl': 957603, 'Win From Within': 957603, '#WinFromWithin': 957603, 'Scene (drama)': 87105, 'Scene (UK TV Series)': 87105, 'Avi': 87105, 'X264': 87105, 'A scene from Suicide Club 2': 87105, 'Mpeg4': 87105, 'Suicide': 87105, 'group': 87105, 'suicide': 87105, 'awesome suicide': 87105, 'great': 87105, 'suicide video': 87105, 'Nice': 87105, 'Good': 87105, 'Amazing': 87105, 'Sad': 87105, 'Cry': 87105, 'Awesome (window Manager)': 87105, 'May': 87105, 'May Cry': 87105, 'NSFW – Language': 4524, 'Australia': 158566, 'bicycle': 4524, 'bikes': 4524, 'Becky G': 1361217, 'Daddy Yankee': 26143375, 'Wisin': 1361217, 'Camila Cabello': 9219802, 'Pitbull': 1365984, 'Yandel': 1361217, 'Bad Bunny': 1361217, 'Dom Omar': 1361217, 'Enrique Iglesias': 1361217, 'Weekend': 1361217, 'Jay z': 1361217, 'Latin music': 1361217, 'reggaeton': 1361217, 'Latin Trap': 1361217, 'Farrucko': 1361217, 'star wars': 9179164, 'smosh star wars': 656403, 'every star wars ever': 656403, 'every star wars fan ever': 656403, 'every fan ever': 656403, 'star wars every blank ever': 656403, 'every blank ever star wars': 656403, 'star wars cosplay': 656403, 'every cosplay ever': 656403, 'star wars fans': 656403, 'every jedi every': 656403, 'every star wars sith ever': 656403, 'ellie and jared': 491113, 'ellie mecham': 491113, 'jared mecham': 491113, 'jared and ellie': 491113, 'ellie': 491113, 'jared': 491113, 'mecham': 491113, 'pregnancy': 2739607, 'pregnant': 1979763, 'pcos': 491113, 'infertility': 491113, 'fertility': 491113, 'miscarriage': 491113, 'pregnancy announcement': 491113, 'pregnancy announcements': 491113, 'ellie and jared pregnant': 491113, 'ellie and jared pregnancy announcement': 491113, 'spyker c8': 705169, 'spyker': 705169, 'spyker c8 spyder': 705169, 'c8 spyder': 705169, 'spyker c12': 705169, 'spyker car': 705169, 'spyker sports car': 705169, 'exotic car': 705169, 'demuro': 705169, 'week 12': 1715346, 'wk 12': 1715346, 'green bay': 1172709, 'packers': 1172709, 'pittsburgh': 2166144, 'steelers': 2166144, 'hundley': 1172709, 'williams': 1172709, 'roethlisberger': 2166144, 'td drive': 2985823, 'sp:dt=2017-11-26T20:30:00-05:00': 1172709, 'sp:ti:home=Pit': 1172709, 'sp:ti:away=GB': 1172709, 'steelers win': 2166144, 'boswell': 1194662, 'fg': 1172709, 'field goal': 1172709, 'Meghan Markle': 665832, 'prince harry': 1471032, 'princess diana': 411001, 'prince harry engaged': 117714, 'prince harry meghan markle engaged': 117714, 'meghan markle': 953486, 'engaged': 117714, 'harry': 548956, 'royal wedding': 579365, 'prince': 1198505, 'meghan': 342821, 'markle': 342821, 'prince harry and meghan markle': 517434, 'harry and meghan': 342821, 'royal': 342821, 'british royals': 117714, 'proposed': 117714, 'meghan markle’s engagement ring': 117714, 'engagement ring': 117714, 'royal family': 1177019, 'things you might not know': 460003, 'hold music': 306872, 'audio compression': 306872, 'cell phones': 306872, 'mobile phones': 306872, 'telephone exchanges': 306872, 'apple iphone': 3412680, 'iphone review': 3412680, 'iphone unboxing': 3412680, 'x review': 3412680, 'x unboxing': 3412680, 'iphone 8 plus': 3412680, 'unbox therapy': 3412680, 'unboxtherapy': 3412680, 'iphone 8 vs x': 3412680, 'iphone x vs 8 plus': 3412680, 'iphone 7 vs 8': 3412680, "don't buy iphone x": 3412680, 'iphone x worth it': 3412680, 'reasons not to buy iphone x': 3412680, 'Harry and Meghan': 219570, 'FULL Interview': 219570, 'Prince Harry': 479938, 'girlfriend gives me a makeover': 161404, 'boyfriend gives me a makeover': 161404, 'surprise makeover': 161404, 'make over': 211942, 'steve harrington': 161404, 'mustache': 338281, 'women in comedy': 153682, 'funny women': 153682, 'sex': 153682, 'just between us jbu just between us comedy gaby dunn allison raskin': 153682, 'gaby dunn': 91925, 'allison raskin': 91925, 'gallison': 91925, 'feelings': 91925, 'friendship': 91925, 'malaysia': 91925, 'tattoos': 1408779, 'tattoo': 1408779, 'nursing': 553261, 'economy': 560006, 'unemployment': 553261, 'labor': 1551489, 'healthcare': 1036811, 'nurse': 553261, 'male nurse': 553261, 'men in nursing': 553261, 'emotion': 713601, 'empathy': 553261, 'caring': 553261, 'emotional intelligence': 553261, 'emotional competency': 553261, 'nursing shortage': 553261, 'gender and emotion': 553261, 'gender norms': 553261, 'emotion expression': 553261, 'crocep': 367311, 'microphone': 412324, 'lapel': 412324, 'lavalier': 412324, 'mic': 283371, 'lav mic': 203696, 'neck mic': 203696, 'collar mic': 203696, 'noise': 203696, 'reduction': 203696, 'audio noise': 203696, 'video noise': 203696, 'gain': 412324, 'attenuate': 203696, 'attenuator': 203696, 'razerphone': 388786, 'razor phone': 388786, 'mobile device': 388786, 'mobile tech': 388786, 'Razer': 388786, '120hz display': 388786, 'Boy band': 1548962, 'BTS band': 1548962, 'bts army': 2323266, 'mic drop lyrics': 1548962, 'BTS tour': 1548962, "cam'ron": 664666, 'camron': 664666, 'killa cam': 664666, "cam'ron 2017": 664666, "cam'ron music": 664666, 'harlem': 664666, 'dipset': 664666, 'new york rap': 664666, 'dinner time': 664666, "can'ron dinner time": 664666, "dinner time cam'ron": 664666, 'mase diss track': 664666, "cam'ron mase": 664666, "cam'ron mase diss track": 664666, 'mass diss': 664666, "cam'ron mase diss": 664666, "cam'ron official": 664666, 'mase 2017': 664666, 'mase official': 664666, 'dinner time official': 664666, 'dinner time 2017': 664666, 'battle with charlie puth': 2242465, 'battle': 769417, 'charlie': 1924220, 'puth': 1809045, 'Battle with Charlie Puth | Rudy Mancuso': 1809045, 'cyber': 128133, 'cyber monday deals': 129514, 'early cyber monday': 128133, 'early cyber monday deals': 128133, 'best cyber monday': 128133, 'best cyber monday deals': 129514, 'best cyber monday 2017': 128133, 'best cyber monday 2017 deals': 128133, 'amazon cyber monday': 129514, 'walmart cyber monday': 128133, 'best buy cyber monday': 128133, 'cyber monday tech deals': 128133, 'best cyber monday tech': 128133, 'best cyber monday tech deals': 128133, 'cyber monday sales': 129514, 'top cyber monday deals': 128133, 'the x factor': 1306372, 'x factor': 1603678, 'X factor UK': 1306372, 'x factor 2017': 1306372, 'simon cowell': 1306372, 'nicole': 1306372, 'sharon': 1306372, 'louis': 1306372, 'auditions': 1306372, 'judges': 1306372, 'season 14': 1306372, 'series 14': 1306372, 'X Factor UK 2017': 1306372, 'X Factor 2017 audition': 1306372, 'The x factor 2017': 1306372, 'XFactor 2017': 1306372, 'revolve awards show': 333229, 'revolve': 333229, 'november vlog': 333229, 'jenn im wedding': 333229, 'jenn im wedding dress': 333229, 'Calgary Stampeders vs Toronto Argonauts': 29733, 'Calgary Stampeders vs Toronto Argonauts 2017': 29733, 'Calgary Stampeders': 29733, 'calgary stampeders highlights': 29733, 'calgary stampeders highlights 2017': 29733, 'Toronto Argonauts': 29733, 'Toronto Argonauts 2017': 29733, 'Toronto Argonauts highlights': 29733, 'Toronto Argonauts highlights 2017': 29733, '105th Grey Cup': 29733, 'CFL': 29733, 'CFL 2017': 29733, 'CFL Grey Cup': 29733, 'CFL Grey Cup 2017': 29733, 'mi anne chan': 17372, 'beauty unboxing': 26329, 'demonstration': 17372, 'unboxing haul': 17372, 'haul 2017': 17372, 'free makeup': 17372, 'colourpop': 17372, 'unboxing video': 17372, 'packages': 17372, 'christmas haul': 29424, 'grwm': 88636, 'get ready with me': 555393, 'Jamaica': 12649, 'Boos': 12649, 'Miss Universe 2017': 12649, 'Colombia': 12649, 'Philippines': 12649, 'Thailand': 96705, 'Vice Ganda': 12649, 'Maine Mendoza': 12649, 'South Africa': 12649, 'west midlands police': 167753, 'vr fail': 810165, 'santa': 8577655, 'mall': 27779, 'toronto': 84375, 'rant': 4570439, 'which programming language to learn first': 124380, 'what programming language to learn first': 124380, 'top programming languages 2017': 124380, 'top programming languages 2018': 124380, 'top programming languages 2019': 124380, 'top programming languages 2020': 124380, 'what programming language should I learn first': 124380, 'which programming language should I learn first': 124380, 'python': 1490379, 'swift': 124380, 'Java': 124380, 'kotlin': 124380, 'SQL': 124380, 'golang': 124380, 'go': 381439, 'javascript': 124380, 'WEB': 3634, 'INDONESIA': 3634, 'BALI': 3634, 'VOLCANO': 3634, 'TRAVEL': 3634, 'katana': 826996, 'samuri': 826996, 'sword': 826996, 'samurai sword': 826996, 'flooring': 826996, 'hardwood flooring': 826996, 'wooden katana': 826996, 'from hardwood flooring': 826996, 'teak': 826996, 'blade': 899510, 'iltms': 826996, 'i like to make stuff': 826996, 'bob clagett': 826996, 'ninja': 826996, 'ninja sword': 826996, 'Bending water': 5965, 'controlling elements': 5965, 'Avatar': 2847840, 'Plasma': 5965, 'Discovery': 5965, 'High Voltage': 5965, '“High Voltage”': 5965, '“Voltage multiplier”': 5965, 'Tesla Coil': 5965, 'Vandergraff Generator': 5965, '“Vandergraff Generator”': 5965, 'Electrostatic': 5965, '“static electricity”': 5965, 'Lightning': 5965, 'Electronics': 5965, 'Plasma globe': 5965, '“plasma globe”': 5965, 'How to': 5965, 'Tabletop': 5965, 'table top': 5965, 'mini': 1616844, '3v': 5965, '3 volt tesla coil': 5965, 'mico coil': 5965, 'bug zapper': 5965, 'instructions': 5965, 'calum scott': 950926, 'you are the reason': 950926, 'you are the reason lyrics': 950926, 'only human': 950926, 'dancing on my own': 950926, 'britains got talent': 950926, 'shane meadows': 187052, 'liam gallagher': 187052, 'oasis': 3624774, 'as you were': 187052, 'come back to me': 187052, 'glasgow': 187052, 'king tuts': 187052, 'live music': 187052, 'pcb': 39115, 'trace': 39115, 'footprint': 39115, '3d': 39115, 'etch': 39115, 'develop': 39115, 'cetus': 39115, 'linear': 39115, 'rail': 520898, '405': 39115, 'nm': 39115, 'diode': 39115, 'transistor': 39115, 'heat': 43751, 'sink': 39115, 'precision': 39115, 'accuracy': 39115, 'resolution': 39115, 'arduino': 336983, 'cnc': 39115, 'stepper': 39115, 'motor': 198485, 'heated': 39115, 'build': 553789, 'platform': 39115, 'modification': 39115, 'extruder': 39115, 'pin': 369239, 'engraving': 39115, 'engraver': 39115, 'etching': 39115, 'developing': 39115, 'module': 39115, 'mw': 39115, 'supply': 39115, 'constant': 39115, 'current': 664010, 'source': 39115, 'forward': 860084, 'voltage': 655810, 'ma': 39115, 'photosensitive': 39115, 'layer': 39115, 'bungard': 39115, 'stl': 39115, 'open': 39115, 'flatcam': 39115, 'milling': 39115, 'isolation': 39115, 'routing': 39115, 'uv': 39115, 'ray': 39115, 'eagle': 39115, 'bga': 39115, 'The Real Housewives of Atlanta': 306414, 'Atlanta': 348859, 'doctors': 744393, 'Bravo TV': 358774, 'Reality Television': 358774, 'Full Episodes': 358774, 'Sneak Peeks': 358774, 'Bravo Show': 358774, 'The Real Housewives Of Atlanta (TV Program)': 306414, 'NeNe Leakes (TV Personality)': 306414, 'real housewives franchise': 306414, 'Sheree Whitfield': 306414, 'Cynthia Bailey': 432837, 'Kenya Moore': 306414, 'Kim Fields': 306414, 'Phaedra Parks': 306414, 'Porsha Williams': 306414, 'Shamea Morton': 306414, 'Atlanta (City/Town/Village)': 306414, 'Kim Zolciak-Biermann': 306414, 'Kim': 306414, "Kenya's Husband": 306414, "Doesn't Exist": 306414, 'bob maxson': 306414, 'Linkin Park': 1139249, 'Too Faced': 13747, 'Jerrod Blandino': 13747, 'Too': 13747, 'Faced': 13747, 'Jerrod': 13747, 'Blandino': 13747, 'Mua': 13747, 'Makeover': 13747, 'Cosmetics': 13747, 'How To': 13747, 'Makeup Tutorial': 13747, 'Makeup Tips': 13747, 'Makeup Tricks': 13747, 'Benefit': 13747, 'Nars': 13747, 'NYX': 13747, 'Kylie Lip': 13747, 'Kylie Cosmetics': 365784, 'Urban Decay': 13747, 'Smashbox': 13747, 'Glossier': 13747, 'KKW Beauty': 13747, 'KKW': 13747, 'ColourPOP': 13747, 'Simple Makeup': 13747, 'Desi Perkins': 13747, 'Face mask': 13747, 'Glam glow': 13747, 'Best mask': 13747, 'Glitter mask': 13747, 'Glow job': 13747, 'Radiant mask': 13747, 'Lush mask': 13747, 'Sheet mask': 13747, 'Mud mask': 13747, 'Face Mask': 13747, 'Peel-off mask': 13747, 'Hydrating mask': 13747, 'Glittery mask': 13747, 'Favorite mask': 13747, 'cheese expert': 1112552, 'parmesan cheese': 1112552, 'goat cheese': 1112552, 'gruyere cheese': 1112552, 'feta cheese': 1112552, 'blue cheese': 1112552, 'cheap cheese vs expensive cheese': 1112552, 'cheap vs expensive': 1112552, 'expensive cheese': 1112552, 'cheap cheese': 1112552, 'cheap cheeses': 1112552, 'expensive cheeses': 1112552, 'cheap vs expensive food': 1112552, 'cheese explained': 1112552, 'cheese test': 1112552, 'cheese tasting': 1112552, 'cheese taste': 1112552, 'cheese making process': 1112552, 'cheesemonger': 1112552, 'cheese taste test': 1112552, 'cheese game': 1112552, 'epicurious': 1112552, 'recipes': 1539317, 'Nick': 251668, 'Say': 78674, 'All': 78674, 'You': 6404050, 'Want': 78674, 'For': 78674, 'sevdaliza': 90206, 'ison': 90206, 'thesuspendedkid': 90206, 'childrenofsilk': 90206, 'thatothergirl': 90206, 'sirensofthecaspian': 90206, 'theformula': 90206, 'radiohead': 90206, 'portishead': 90206, 'massiveattack': 90206, 'pjharvey': 90206, 'jamesblake': 90206, 'kanyewest': 90206, 'fkatwigs': 90206, 'shanedawsontv': 6083541, 'shane': 6083541, 'dawson': 6083541, 'similar': 6083541, 'jenna': 7044887, 'marbles': 6083541, 'smash': 6083541, 'disaster artist': 1347008, 'room': 3045288, 'tommy wisseau': 1080388, 'the room lisa': 1080388, 'the room review': 1080388, 'the disaster artist review': 1080388, 'arrival': 1054285, 'amy adams': 1054285, 'jeremy renner': 1054285, 'arrival movie': 1054285, 'Nick Robinson': 868892, 'Katherine Langford': 868892, 'Alexandra Shipp': 868892, 'Jorge Lendeborg': 868892, 'Miles Heizer': 868892, 'Keiynan Lonsdale': 868892, 'Logan Miller': 868892, 'Jennifer Garner': 868892, 'Josh Duhamel': 868892, 'Tony Hale': 868892, 'Greg Berlanti': 868892, 'Wyck Godfrey': 868892, 'Marty Bowen': 868892, 'Dawson’s Creek': 868892, 'Brothers & Sisters': 868892, 'Becky Albertalli': 868892, 'Simon vs. the Homo Sapiens Agenda': 868892, 'LoveSimon': 868892, '20th Century FOX': 868892, 'coming out story': 868892, 'gay coming out story': 868892, 'base jump': 550842, 'base': 550842, 'wingsuit': 550842, 'fred fugen': 550842, 'vince reffet': 550842, 'soul flyers': 528664, 'wingsuit flyers': 550842, 'mid-air': 550842, 'base jumping': 550842, 'wingsuit flying': 550842, 'skydive': 550842, 'jumps': 550842, 'jumping': 550842, 'skydiving': 1128318, 'flying squirrel': 550842, 'hero5 session': 550842, 'hero 5': 550842, 'rad': 591204, 'basejump': 550842, 'roof': 550842, 'douggs': 550842, 'plane': 710082, 'parachuting': 550842, 'silver': 550842, 'double': 550842, 'tandem': 550842, 'switzerland': 550842, 'action camera': 550842, 'go pro': 591204, 'friend zone texts': 1017187, 'friend zoned': 1017187, 'funny texts': 1017187, 'TEENS READ 10 FUNNY FRIEND ZONE TEXTS (REACT)': 1017187, 'friendzone': 1017187, 'texts': 1017187, 'friend': 1135420, 'zone': 1278693, 'watch voice video': 480883, 'the voice live top 11 eliminat': 133374, 'team adam': 480883, 'team miley': 480883, 'team jennifer': 480883, 'team blake': 480883, 'ashland craft': 133374, 'brooke simpson': 133374, 'janice freeman': 133374, 'man i feel like a woman': 133374, 'carson daly': 480883, 'the voice auditions': 480883, 'superstar': 133374, 'blind auditions': 133374, 'battle rounds': 133374, 'duet': 173491, 'finance': 153801, 'advertising': 145601, 'project for awesome': 145601, 'p4a': 145601, 'nonprofits': 145601, 'fundraising': 145601, 'Munchies': 641975, 'Munchiestv': 641975, 'drinks': 641975, 'VICE': 641975, "chef's night out": 641975, 'matty matheson': 641975, 'action bronson': 641975, 'vicevideos': 641975, 'CHEFS': 641975, 'MUNCHIES': 641975, 'meal kits': 641975, 'Matty Matheson': 641975, 'Steak': 399646, 'Sandwiches': 399646, 'meat': 838794, 'steak sandwich': 399646, 'kale': 399646, 'perfect steak sandwich': 399646, 'chimichurri': 399646, 'tom reimann': 442841, 'old guns': 203615, 'james cagney': 203615, 'old effects': 203615, 'buster keaton': 203615, 'charlie chaplin': 203615, 'bullets': 203615, 'special effects history': 203615, 'old school effects': 203615, 'best old movies': 203615, 'sag aftra': 203615, 'sag history': 203615, 'killed on set': 203615, 'on set disasters': 203615, 'worst movie sets': 203615, 'practical effects': 203615, 'squibs': 203615, 'movie history': 681508, 'cecil b demille': 203615, 'birth of a nation': 203615, 'Variety': 145112, 'Variety Studio': 145112, 'Actors on Actors': 145112, 'jennifer lawrence fans': 108786, 'jennifer lawrence news': 108786, 'jennifer lawrence adam sandler': 108786, 'jennifer lawrence amy schumer': 108786, 'jennifer lawrence rude': 108786, 'jennifer lawrence hunger games': 108786, 'jennifer lawrence variety': 108786, 'joe sugg': 148406, '90s toys': 148406, 'furby': 148406, 'nsync': 148406, 'dirty pop': 148406, 'hit clips': 148406, 'ricky dillon': 148406, 'alfie deyes': 681996, 'gameboy color': 148406, 'gameboy': 148406, 'toy review': 148406, 'caspar lee': 148406, 'how to be british': 148406, 'toy challenge': 148406, 'lion king': 148406, 'reboot': 148406, 'show me more': 774304, 'show me more show': 1183718, '12days': 774304, 'Daisy': 206135, 'Ridley': 206135, 'Star': 206135, 'Wars:': 206135, 'Last': 1735048, 'Jedi': 544043, 'carrie': 228088, 'william': 2327176, 'kane brown deluxe': 211889, 'heaven': 211889, 'setting the night on fire': 149677, "what's mine is yours": 211889, 'kane brown live': 211889, 'used to love you sober': 211889, 'what ifs': 211889, 'kane brown and lauren alaina': 211889, 'lauren alaina': 211889, 'chris young': 211889, 'kane brown and chris young': 211889, 'kane brown and brad paisley': 211889, 'Found You': 149677, 'Kane Brown': 149677, 'RCA Records Label Nashville': 211889, 'dunkin donuts': 317866, 'giant cakes': 317866, 'peppermint chocolate cake': 317866, 'Peppermint Mocha': 317866, 'surprise inside': 317866, 'realistic cakes': 317866, 'cake decoration ideas': 317866, 'candy cane': 317866, 'peppermint mocha cake': 317866, 'surprise inside cakes': 317866, 'easy baking': 317866, 'DIY cakes': 317866, 'satisfying cake decoration 2017': 317866, 'best cake ideas': 317866, 'best cake decorating ideas': 317866, 'the seam hider': 317866, 'youtube quiz': 129360, 'youtuber quiz': 129360, 'truth or dare': 129360, 'youtube crush': 129360, 'molly burk': 129360, 'mario': 347242, 'super mario': 309795, 'mario run': 309795, 'mario bros': 309795, 'how to decorate': 309795, 'how to make a cake': 309795, 'frosting': 309795, 'icing': 309795, 'step by step': 520915, 'have to try': 309795, 'helpful': 309795, 'perfect': 1348389, 'peach': 309795, 'fresh': 1184451, 'from scratch': 649967, 'wardrobe': 309795, 'themed': 309795, 'toadstool': 309795, 'luigi': 309795, 'new game': 309795, 'switch': 595694, 'ios': 311404, 'app': 309795, 'layered': 309795, 'mario odyssey': 309795, 'mario 64': 309795, 'dead-end company': 35467, 'colorful fans': 35467, 'dancing bear': 35467, 'John Mayer': 130379, 'Brazil': 35467, 'annoying travel': 35467, 'collaborate': 35467, 'The Shady Bear': 35467, 'habit': 35467, 'travel habit': 35467, 'John Mayer on Andy Cohen’s': 35467, 'Andy Cohen’s Annoying Habit': 35467, 'Dead and Co': 35467, 'John Mayer wwhl': 130379, 'John Mayer and Andy Cohen': 35467, 'John Mayer on watch what happens live': 35467, 'wwhl': 35467, 'Katy Perry and John Mayer': 35467, 'Bob Saget': 35467, 'dianna cowren': 164406, 'physics girl': 3834458, 'cereal spoon': 164406, 'spoon': 164406, 'milk': 1327249, 'soggy': 164406, 'laser time': 164406, 'peristaltic pump': 164406, 'cereal machine': 164406, 'cereal robot': 164406, 'breakfast robot': 164406, 'milk spoon': 164406, 'Whisper Challenge': 30244, 'Daisy Ridley': 2053428, 'Murder On The Orient Express': 30244, 'games with guests': 270674, 'headphones': 30244, 'leia': 52197, 'Episode 8': 557178, 'The Last Jedi': 787188, 'Lando Calrissian': 30244, 'how to make pancakes': 110091, 'pancakes': 110091, 'egg less pancakes': 110091, 'banana pan cakes': 110091, 'american pancakes': 110091, 'how to cook easy pancakes': 110091, 'grandpa': 152536, 'Fluffy Pancake Recipe': 110091, 'pancakes recipe': 110091, 'banana': 507420, 'homemade pancakes': 110091, 'easy pancakes': 110091, 'perfect pancakes': 110091, 'breakfast recipes': 110091, 'simple pancake recipe': 110091, 'fluffy pancake': 110091, 'american food': 110091, 'american recipes': 110091, 'best pancakes': 110091, 'Allrecipes': 110091, 'with out oven': 110091, 'children favorite recipes': 110091, 'pancake': 110091, 'pancake art challenge': 1159024, 'pancake art': 1159024, 'elle mills': 770606, 'elleofthemills': 770606, 'sexuality': 887824, 'lgbtq+': 770606, 'creed': 151767, 'jordan': 151767, 'michael b jordan': 151767, 'michael jordan': 151767, 'the wire': 151767, 'apollo creed': 151767, 'black panther': 206926, 'marvel universe': 151767, '73 questions with michael b jordan': 151767, 'michael b jordan interview': 151767, '73 questions interview': 151767, 'michael jordan parents': 151767, 'michael b jordan parents': 151767, 'michael jordan actor': 151767, 'michael b jordan actor': 151767, 'michael b jordan 73': 151767, 'erik killmonger': 151767, 'killmonger': 151767, 'killmonger michael b jordan': 151767, 'michael b jordan nba 2k': 151767, 'houston': 542637, 'texans': 542637, 'baltimore': 542637, 'ravens': 542637, 'mnf': 542637, 'monday night football': 542637, 'hopkins': 542637, 'savage': 542637, 'suggs': 542637, 'flacco': 542637, 'allen': 542637, 'collins': 542637, 'ravens win': 542637, 'sp:dt=2017-11-27T20:30:00-05:00': 542637, 'sp:ti:home=Bal': 542637, 'sp:ti:away=Hou': 542637, 'mike boyd': 748482, 'saber': 208877, 'sabre': 208877, 'champagne': 208877, 'bottle': 368117, '500k': 208877, 'this week': 208877, 'learned': 208877, 'learn quick': 748482, 'bucks fizz': 208877, 'moet': 208877, "Simon's Cat": 117411, "Simon's Cat Logic": 117411, 'Cats': 249922, "Simon's": 117411, '#simonscatlogic': 117411, 'simonscatlogic': 117411, 'Simons Katze Logik': 117411, 'Logika Kota Simon': 117411, 'кот Саймона логика': 117411, 'lógica gato de simon': 117411, 'la logique de chat de Simon': 117411, 'cats protection': 117411, 'cat behaviour': 117411, 'cat tips': 117411, 'cat owner help': 117411, 'cat welfare': 117411, 'cat observation': 117411, 'feline festive': 117411, 'los angeles studio': 32839, 'studio tour': 32839, 'small homes': 32839, 'small apartments': 32839, 'tour vlog': 32839, 'home vlog': 32839, 'damon and jo': 75994, 'koreatown apartment': 32839, 'central la': 32839, 'condo tour': 32839, 'flat tour': 32839, 'small space design ideas': 32839, 'small apartment design tips': 32839, 'design hacks': 32839, 'how to design an apartment': 32839, 'tiny homes': 32839, 'house hunters': 32839, 'mtv cribs': 32839, 'youtubers homes': 32839, 'damon dominique': 52807, 'jo franco': 52807, 'travel youtubers': 32839, 'life in los angeles': 32839, 'california': 1168038, 'Wassabi': 388115, 'Alex Wassabi': 388115, 'Wassabi Productions': 388115, 'Alex Wassabi YouTube': 388115, 'Alex Wassabi vlogs': 388115, 'Daily Vlogger': 388115, 'Wassabians': 388115, 'Smile': 388115, 'Mkay bye': 388115, 'youtube couple': 388115, 'puppy': 925116, 'puppies': 388115, 'mic drop': 388115, 'insane': 388115, 'Grammys 2018': 21644, 'Grammy nominations 2018': 21644, 'Kendrick lamar': 21644, 'Bruno mars': 21644, 'jay z': 25340, 'newsfeed': 308449, 'clevver news': 308449, 'trophy life': 21644, 'Darren Criss I Don’t Mind': 31034, 'I Don’t Mind Darren Criss': 31034, 'New music Darren Criss': 31034, 'I Don’t Mind Official Video': 31034, 'Darren Criss I Don’t Mind video': 31034, 'Darren Criss Official Video': 31034, 'Darren Criss': 31034, 'Darren Criss music': 31034, 'Darren Criss Homework': 31034, 'Homework Darren Criss': 31034, 'Darren Criss Dance': 31034, 'Dance Darren Cris': 31034, 'Darren Cris': 31034, 'Darren Chris': 31034, 'Darren Criss Glee': 31034, 'I Don’t Mind': 31034, "I don't mind offiical music video": 31034, 'darren criss official videos': 31034, "don't mind music video": 31034, "i don't mind music video": 31034, 'smartphone giveaway': 1067141, 'iphone x giveaway': 1067141, 'Note 8 giveaway': 1067141, 'phone giveaway': 1067141, 'Cyber Monday': 1067141, 'MKBHD giveaway': 1067141, 'Cool': 673729, 'Cute': 507768, 'Dogs': 605810, 'feel good': 507768, 'Humor': 671029, 'Weird': 507768, 'Win': 507768, 'impatient': 507768, 'honk': 507768, 'horn': 507768, 'owner': 507768, 'attention': 507768, 'Nanaimo': 507768, 'British Columbia': 507768, 'Canada': 507768, 'finding a boyfriend for the holidays': 1660811, 'finding': 1089816, 'charlie puth': 1089816, 'circle of love': 1089816, 'Imagine Dragons Thunder Evolve Ellen DeGeneres Ellen show': 400308, 'andrew': 56596, 'huang': 56596, 'andrew huang': 56596, 'producer': 1546171, 'canadian': 1249143, 'ontario': 56596, 'dotan': 56596, 'dotan negrin': 56596, 'negrin': 56596, 'piano around the world': 56596, 'keys': 56596, 'keyboard': 95692, 'native instruments': 56596, 'ni': 56596, 'komplete': 56596, 'kontrol': 56596, 'komplete kontrol': 56596, 's61': 56596, 'mkii': 56596, 'mk2': 56596, 'maschine': 56596, 'ableton': 56596, 'jam': 98131, 'improvised': 56596, 'beat': 56596, 'dnb': 56596, 'drum and bass': 56596, 'drum n bass': 56596, 'drum': 56596, 'making beats': 56596, 'recording': 56596, 'trance': 56596, 'Zero Mass Water': 75780, 'scarcity': 75780, 'solar panels': 75780, 'sustainable energy': 75780, 'green energy': 75780, 'green tech': 75780, 'Lauren Goode': 75780, 'next level': 75780, 'next level with lauren goode': 75780, 'Cody Friesen': 75780, 'Ashok Gadgil': 75780, 'UC Berkeley': 75780, 'Come Through and Chill': 383694, 'Miguel feat. J. Cole & Salaam Remi': 383694, 'best electric skateboard 2017 2018': 43358, 'boosted board v2 extended battery range review unboxing': 43358, 'acton blink s s2 lite review': 43358, 'sony rx0 video test review unboxing': 43358, 'sony rx0 vs gopro 6': 43358, 'Best Electric Skateboard 2017 - (May 2017)': 43358, 'boostedboard review test ride': 43358, 'riding boosted board in nyc new york city': 43358, 'best nyc vloggers': 43358, 'tech youtuber nyc new york city': 43358, 'sara dietschy': 43358, 'sarah peachy': 43358, 'sara peachy': 43358, 'sarah dietschy': 43358, 'electric skateboard casey neistat': 43358, 'STEVE': 27646, 'Steve Harvey Show': 27646, 'steve harvey show': 27646, 'Steve Harvey Talk Show': 27646, 'steve harvey talk show': 27646, 'Steve Harvey': 27646, 'steve harvey': 27646, 'Harvey': 27646, 'tv host': 27646, 'Laugh': 27646, 'tv personality': 27646, 'hey steve': 27646, 'kuwtk': 27646, 'jennifer lawrence': 27646, 'chelsea handler': 723025, 'chelsea lilly': 723025, 'lily singh chelsea': 723025, '12 collabs of xmas': 3547950, '12 collabs chelsea handler': 723025, 'lilly singh chelsea handler': 723025, 'talk show chelsea': 723025, 'interviews were honest': 723025, 'honest talk shows': 723025, 'super woman 12 collabs': 723025, 'lilly singh 12 collabs of christmas 2017': 723025, 'handler': 723025, 'talk show hosts': 723025, 'talk show interviews': 723025, 'chelsea handler talk show': 723025, 'talk show chelsea handler': 723025, 'chelsea handler 2017': 723025, 'ft chelsea handler': 723025, 'great big story': 454900, 'gbs': 454900, 'lag': 454900, 'Dublin': 135307, 'Runner': 135307, 'Running': 135307, 'Epilepsy': 135307, 'Epileptic': 135307, 'Seizure': 135307, 'Sports & Action': 135307, 'competitive runner': 135307, 'Katie Cooke': 135307, 'Ireland': 135307, 'Irish': 135307, 'banking': 132396, 'banks': 125651, 'bio': 125651, 'biologist': 125651, 'bitcoin': 367849, 'currency': 374594, 'expert': 125651, 'cryptocurrency': 367849, 'cryptography': 125651, 'blockchain': 367849, 'blockchain expert': 125651, 'cryptographer': 125651, 'crypto currency': 125651, 'block chain': 125651, 'satoshi nakamoto': 125651, 'online banking': 125651, 'blockchain network': 125651, 'finn brunton': 125651, 'grad student': 125651, 'blockchain system': 125651, 'blockchain computer': 125651, 'computer network': 125651, 'bettina warburg': 125651, 'peer to peer': 125651, 'peer to peer network': 125651, 'bitcoin expert': 125651, 'bitcoin price': 125651, 'bitcoin currency': 125651, 'Paddington': 28285, 'Paddington 2': 28285, 'Paddington Bear': 28285, 'Paddington Brown': 28285, 'Hugh Bonneville': 28285, 'Sally Hawkins': 28285, 'Julie Walters': 28285, 'Peter Capaldi': 28285, 'Hugh Grant': 28285, 'Ben Whishaw': 28285, 'Madeleine Harris': 28285, 'Samuel Joslin': 28285, 'Imelda Staunton': 28285, 'Paul King': 28285, 'David Heyman': 28285, 'Heyday Films': 28285, 'Marmalade': 28285, 'StudioCanal': 28285, 'Legofreedom1': 8116, 'crisis on earth x': 8116, 'arrow': 8116, 'supergirl': 8116, 'flash duet': 8116, 'supergirl duet': 8116, 'flash musical': 8116, 'crisis on earth x arrow': 8116, 'legends of tomorrow': 8116, 'barry and iris wedding': 8116, "barry and iris's wedding": 8116, 'running home to you': 8116, 'the flash wedding': 8116, 'the flash crisis on earth x': 8116, 'supergirl crisis on earth x': 8116, 'crisis on earth x part 3': 8116, 'crisis on earth part 4': 8116, 'crisis on earth x part 1': 8116, 'crisis on earth part 2': 8116, 'pagey': 8116, 'melissa benoist': 8116, 'supergirl singing': 8116, 'melissa benoist singing': 8116, '2018 grammy': 14645, 'grammys': 452751, 'grammy nominees': 14645, '60th annual gammy awards': 14645, 'nothing more': 14645, 'foo fighters': 14645, 'metallica': 14645, 'mastodon': 14645, 'metallica grammy': 14645, 'foo fighters grammy': 14645, 'hard rock': 14645, 'metal': 261252, 'heavy metal': 14645, 'loudwire': 14645, 'hm': 122522, 'hmlovesfashion': 122522, 'john turturro': 122522, 'jessie williams': 122522, 'womens clothes': 122522, 'ladies clothes': 122522, 'ladies fashion': 122522, 'womens fashion': 122522, 'online shopping for women': 122522, 'womens clothing online': 122522, 'online clothes shopping': 122522, 'long': 122522, 'Timothée chalamet': 12751, 'andy cohen': 12751, 'lourdes': 12751, 'lola': 12751, 'call me by your name': 119933, 'how to take a screenshot on iPhone': 25980, 'how to take a screenshot on iPad': 25980, 'how to take a screenshot on iPod Touch': 25980, 'iPhone screenshot': 25980, 'iPad screenshot': 25980, 'iPod Touch screenshot': 25980, 'iOS Screenshot': 25980, 'Take a photo of iPhone Screen': 25980, 'ARIAS': 60828, 'HD Video': 60828, 'HQ Audio': 60828, 'ARIA Awards': 60828, 'Australian Record Industry Awards': 60828, 'High Quality Audio': 60828, 'High Definition Video': 60828, 'Concert': 654094, '2016 election': 9136, 'pant suit': 9136, 'hrc': 9136, 'hillary rodham clinton': 9136, 'hillary 2016': 9136, 'hillary 2020': 9136, 'hillary clinton 2016': 9136, 'hillary clinton 202': 9136, 'hillary clinton president': 9136, 'president clinton': 9136, 'hillary clinton interview': 9136, 'hillary clinton interview 2017': 9136, 'hillary clinton trump': 9136, 'clinton trump': 9136, 'clinton interview': 9136, 'hillary clinton teen vogue': 9136, 'hillary rodham': 9136, 'teen vogue': 367754, 'teenvogue.com': 367754, 'Mike Weber': 161364, 'Weber': 161364, 'Dwayne Haskins': 161364, 'Dwayne': 881326, 'Haskins': 161364, 'j.k. dobbins': 161364, 'Alfred': 26849, 'Origin Story': 26849, 'Butler': 26849, 'Orphan': 26849, 'Workhouse': 26849, 'Batman Begins': 26849, 'The Dark Knight': 26849, 'lucy moon': 98610, 'lucy': 98610, 'everyday smokey eye tutorial': 37876, 'smokey eye tutorial': 728228, 'wearable smokey eye': 37876, "let's talk money": 37876, 'talking about money': 37876, 'milk makeup': 37876, 'the ordinary': 37876, 'cruelty free': 49928, 'glass': 72485, 'glass making': 72485, 'melting': 72485, 'glass batch': 72485, 'photochromic': 72485, 'applied science': 72485, 'ben krasnow': 72485, 'krasnow': 72485, 'phototropic': 72485, 'how to make glass': 72485, 'borate': 72485, 'silicate': 72485, 'borosilicate': 72485, "jack avery daniel seavey taking you zach herron corbyn besson jonah marais why don't we": 626351, 'why dont we': 626351, 'whydontwemusic': 626351, 'WDW': 626351, 'boyband': 626351, 'boy band': 626351, 'boybands': 626351, 'music videos': 626351, 'taking you': 626351, 'why dont we boys': 626351, "why don't we boys": 626351, 'kiss you this christmas': 626351, "a why don't we christmas": 626351, 'a why dont we christmas': 626351, "why don't we christmas": 626351, 'kiss you this christmas video': 626351, 'kiss you this christmas music video': 626351, "why don't we mashups": 626351, 'saintly': 176871, 'orleans': 176871, 'saints': 176871, 'vivica': 176871, 'anjanetta': 176871, 'alan': 176871, 'grier': 176871, 'football. washington': 176871, 'redskins': 1105622, 'Damascus steel': 72514, 'Damascus': 72514, 'blacksmithing': 72514, 'forging': 72514, 'blacksmith': 72514, 'handmade': 84566, 'custom': 111610, 'hardening': 72514, 'knife making': 72514, 'Maglite': 72514, 'flashlight': 72514, 'Damascus Tesla.': 72514, 'shurap': 72514, 'Dmitry Shevchenko': 72514, 'lyric video': 530539, 'lyrics / lyric video': 443055, 'james arthur': 740361, 'naked': 740361, 'james arthur naked lyrics': 443055, 'james arthur naked': 443055, 'naked james arthur': 443055, 'james arthur naked lyric video': 443055, 'james arthur naked lyrics / lyric video': 443055, 'naked lyrics james arthur': 443055, 'naked james arthur lyrics': 443055, 'james arthur lyrics': 443055, 'Furby': 3595, 'ethincal hacking': 3595, 'lamborghini': 148944, 'urus': 17746, 'suv': 17746, 'avengers: infinity war': 37736281, 'marvel studios': 37736281, 'new snapchat': 867588, 'version 2': 867588, 'snap inc': 867588, 'brandnew': 867588, 'software': 867588, 'snap': 867588, 'new update': 867588, 'redesign': 867588, 'reorganize': 867588, 'Snapchat': 867588, 'matt lauer terminated from nbc': 971123, 'matt lauer': 1272697, 'fired': 2871730, 'matt lauer fired': 1187455, 'nbc fired matt lauer': 971123, 'savannah guthrie': 971123, 'matt lauer nbc': 971123, 'matt lauer today show': 1187455, 'termination of employment': 971123, 'terminated': 971123, 'matt': 2212675, 'lauer': 971123, 'matt lauer fired from today show': 1187455, 'matt lauer gets fired': 971123, 'savannah': 971123, 'fluidized bed': 3670052, 'liquid sand': 3670052, 'fluidized sand bed': 3670052, 'powder coat painting': 3670052, 'mark rober': 3670052, 'nasa': 4140308, 'carnival scam science': 3670052, 'powder coating': 3670052, 'orbeez pool': 3670052, 'hot tub': 3670052, 'liquid sand hot tub': 3670052, 'air bubbles': 3670052, 'aerated water': 3670052, 'aerated sand': 3670052, 'casper': 3670052, 'vsauce': 3670052, 'hostile architecture': 718902, 'bench design': 718902, 'defensive design': 718902, 'urban architecture': 718902, 'crime prevention through environmental design': 718902, 'camden bench': 718902, 'homeless design': 718902, 'benches': 718902, 'natural surveillance': 718902, 'dystopic planning': 718902, 'hostile urban architecture': 718902, 'social control': 718902, 'deterrent design': 718902, 'bird spikes': 718902, 'new york city design': 718902, 'MTA enhanced subway initiative': 718902, 'anti-sleeping spikes': 718902, 'public spaces': 718902, 'uncomfortable benches': 718902, 'mark wahlberg': 197900, 'all the money in the world trailer': 197900, 'all the money in the world': 197900, 'hd trailer': 197900, 'ridley scott': 197900, 'michelle williams': 197900, 'imdb': 197900, 'best movies 2017': 221087, 'All the money in the world (mystery-crime film)': 197900, 'david scarpa': 197900, 'fletcher chase': 197900, 'jean paul getty': 197900, 'gail harris': 197900, 'j. paul getty': 197900, 'true story': 197900, 'kidnapping': 197900, 'ransom': 197900, 'billionaire': 197900, 'scott free productions': 197900, 'Sam': 1958391, 'Smith': 2535867, 'One': 1209233, 'Song': 1928050, 'Jordyn Woods': 352037, 'Kylie': 352037, 'Telegraph': 279561, 'World News': 784766, 'UK Newspaper': 279561, 'Telegraph YouTube': 279561, 'Daily News': 279561, 'Video News': 279561, 'World Events': 279561, 'All The Money In The World': 106614, 'Christopher Plummer': 106614, 'today show': 85242, 'kathie lee gifford': 83425, 'hoda kotb': 83425, 'garrison keillor': 83425, 'louis c.k.': 83425, 'lebron': 3155272, 'ejected': 276279, 'career': 293281, 'lebron james ejected': 276279, 'lebron james cavaliers': 509603, 'cleveland cavaliers': 509603, 'cavs': 509603, 'Saoirse Ronan': 3977597, 'Had to Drink': 138890, 'Before': 138890, 'Watching': 138890, 'First Time': 138890, 'Brooklyn': 1034242, 'Hanna': 138890, 'The Grand Budapest Hotel': 138890, 'Atonement': 138890, 'kendall jenner': 1662621, 'khloe kardashian': 1488650, 'kim kardashian': 2769354, 'kardashians': 1488650, 'jenners': 1488650, 'hollybuzz': 1488650, 'hollywoodlife': 1488650, 'victoria secret': 383100, 'adriana lima': 904466, 'taylor hill': 383100, 'candice swanepoel': 904466, 'stella maxwell': 383100, 'victorias secret sleepover': 383100, 'sleepover': 383100, 'angel': 557071, 'victorias secret angels': 383100, 'models': 591327, 'adriana lima model': 383100, 'adriana lima backstage': 383100, "victoria's secret backstage": 383100, 'TV Series': 29654, 'Fantasy': 29654, 'Horror': 29654, 'Season 2': 298799, 'guitarists': 29654, 'garage': 29654, 'rock band': 29654, 'Post Animal': 29654, 'Winona Ryder': 29654, 'Finn Wolfhard': 29654, 'Millie Bobby Brown': 29654, 'MattF': 29654, 'AOL Advertising': 29654, 'BUILDseriesNYC': 29654, 'AOL Inc': 29654, 'AOL': 29654, 'AOLBUILD': 29654, '#Aolbuild': 29654, 'build speaker series': 29654, 'aol build': 29654, 'content': 29654, 'aolbuildlive': 29654, 'BUILDSeriesNYC': 29654, 'Armie': 102804, 'Hammer': 102804, 'Armie Hammer': 102804, 'Timothee': 102804, 'Chalamet': 102804, 'Timothee Chalamet': 102804, 'actors': 102804, 'max brenner chocolate': 231724, 'smores pizza': 231724, 'fondue': 231724, 'chocolate pizza': 231724, 'max brenner nyc': 231724, 'NYC': 288665, 'smores': 231724, 'marshmallow': 231724, 'sundae': 231724, 'waffles': 231724, 'strawberry': 231724, 'white chocolate': 231724, 'Tasty': 565911, 'BuzzFeed Tasty': 565911, 'travis': 80882, 'mills': 80882, 'boyfriend tag': 80882, 'coco movie': 43830, 'coco movie review': 43830, 'coco review': 43830, 'coco remember me': 43830, 'coco movie reaction': 43830, 'coco reaction': 43830, "olaf's quest": 43830, "olaf's frozen adventure 2017": 43830, 'coco 2017 pixar': 43830, 'coco 2017 movie': 43830, "olaf's frozen adventure review": 43830, 'movie review': 43830, 'black nerd': 43830, 'blacknerd': 43830, 'black nerd comedy': 43830, 'blacknerdcomedy': 43830, 'andre black nerd': 43830, 'black nerd review': 43830, 'coco 2017': 43830, 'top': 3133234, 'thabo': 262865, 'sefolosha': 262865, 'bogdan': 262865, 'bogdanovic': 262865, 'eric': 262865, 'bledsoe': 262865, 'tyler': 262865, 'ulis': 262865, 'oubre': 262865, 'jr': 262865, 'j.r.': 262865, 'giannis': 443542, 'antetokounmpo': 443542, "de'aaron": 262865, 'the room honest trailers': 51216, 'the room honest trailer': 51216, 'vs fashion show 2017': 521366, "victoria's secret fashion show 2017": 521366, 'angel candice': 521366, 'bella hadid': 521366, 'alessandra ambrosio': 521366, 'alessandra': 521366, 'sanne vloet': 521366, 'elsa hosk': 521366, 'romee': 521366, 'fantasy bra': 521366, 'ed razek': 521366, 'annalora': 521366, 'annalora von pentz': 521366, 'katie cali': 521366, 'vsfs 2017': 521366, 'lil aldridge': 521366, 'lily aldridge': 521366, 'cindy bruna': 521366, 'ming xi': 521366, 'liu wen': 521366, 'Nicki Minaj’s': 94912, 'Minaj offline': 94912, 'guys flirted': 94912, 'DM relationship': 94912, 'the membrane': 94912, 'Twitter': 94912, 'puncturing': 94912, 'little flirt': 94912, 'famous person': 94912, 'real life': 459449, 'spoken to singer': 94912, 'tweeted': 94912, 'semi-flirtatious': 94912, 'singer John Mayer': 94912, 'Nicki Minaj’s Twitter Flirting': 94912, 'John Mayer And Nicki Minaj’s': 94912, 'Nicki Minaj and John Mayer tweets': 94912, 'Nicki Minaj and John Mayer': 94912, 'John Mayer watch what happens live': 94912, 'avengers 4': 70136, 'kevin fiege': 70136, 'lady bird': 3872517, 'avatar': 4514728, 'avatar sequel': 70136, 'mark ellis': 122945, 'perri nemiroff': 70136, 'jon schnepp': 176648, 'ashley mova': 176648, 'Entertainment Tonight': 43121, 'et tonight': 43121, 'sweetwater secrets': 43121, 'et sweetwater secrets': 3722, 'riverdale season 2': 43121, 'riverdale sweetwater secrets': 3722, 'ashleigh murray': 3722, 'josie': 3722, 'josie and the pussycats': 3722, 'chapter 20': 3722, 'tales from the dark side': 3722, 'hisses and kisses': 3722, 'cole sprouse': 103193, 'camila mendes': 584333, 'lili reinhart': 584333, 'kj apa': 43121, 'archie comics': 43121, 'cw': 43121, 'jughead': 3722, 'bughead': 43121, 'archie': 3722, 'varchie': 43121, 'archieronnie': 43121, 'veronica lodge': 3722, 'jetty': 3722, 'betty cooper': 3722, 'nitrov2': 25923, 'cw crossover': 25923, 'crisis on earth-x': 25923, 'dctv crossover': 25923, 'crisis on earth-x crossover': 25923, 'ironied': 30690, 'tyler parr': 30690, 'omeleto shorts': 30690, 'omeleto': 30690, 'homeless man': 30690, 'social worker': 30690, 'omeleto drama': 30690, 'how its made': 18797, 'how to made': 18797, 'bath bombs': 12052, 'new product': 12052, 'lush': 12052, 'everday items': 12052, 'bath bomb': 12052, 'lush christmas': 12052, 'bath tub': 12052, 'bathtub': 12052, 'hair care': 348598, 'handmade soap': 12052, 'zero waste': 12052, 'haircare': 122604, 'dandruff': 12052, 'long hair': 12052, 'shampoo for curly hair': 12052, 'dry hair': 12052, 'healthy hair': 12052, 'fast hair growth': 12052, 'lush christmas 2017': 12052, 'bath time': 12052, 'top gear': 300407, 'tesla model x': 300407, 'feel': 12790, 'unique': 12790, 'fragrance': 12790, 'feelunique': 12790, 'grooming': 12790, 'Feelunique': 12790, 'styling': 221017, 'make-up': 50237, 'FUN': 12790, 'funsquad': 12790, 'Howto': 12790, 'look': 12790, 'swatching': 12790, 'man': 45175, 'firm': 12790, 'tone': 12790, 'nichola joss': 12790, 'facial': 12790, 'massage': 12790, 'skin': 72910, 'at': 12790, 'facialist': 12790, 'facegym': 12790, 'caudalie': 12790, 'estee': 12790, 'lauder': 12790, 'emma': 12790, 'hardie': 12790, 'advanced': 12790, 'repair': 12790, 'techniques': 12790, 'radiant': 12790, 'youthful': 12790, 'ready': 29792, 'me': 348653, 'evening': 12790, 'plump': 12790, 'dewy': 12790, 'finish': 176559, 'Lyric video': 131445, 'visualizer': 131445, 'Logic': 131445, 'Rag N Bone Man': 131445, 'Visionary': 131445, 'Bright Movie': 131445, 'Movie soundtrack': 131445, 'car chase': 41471, 'police car chase': 41471, 'police chase': 41471, 'police car': 41471, 'car chase scene': 41471, 'car chase crash': 41471, 'car crash': 41471, 'chase': 41471, 'police chase crash': 41471, 'los angeles chase': 41471, 'los angelese police chase': 41471, 'police chase motorcycle': 41471, 'best police chase': 41471, 'best car chase': 41471, 'cops chase': 41471, 'fast pursuit': 41471, 'hot pursuit': 41471, 'the chase': 41471, 'chase scene': 41471, 'police pursuit': 41471, 'the new yorker': 41471, 'new yorker': 41471, 'new yorker video': 41471, 'harajuku': 62785, 'akihabara': 29220, 'japan vlog': 29220, 'squishie': 29220, 'squishies': 29220, 'pool of squishies': 29220, 'cupquake vlog': 29220, 'tiffyquake': 29220, 'tiffy': 29220, 'moosh': 29220, 'moosh tokyo': 29220, 'howtodraw': 25084, 'conanobrien': 25084, 'jimmyfallon': 25084, 'trippy': 25084, 'Nuke': 43774, 'Nukes': 43774, 'Nuclear': 43774, 'Nuclear buildup': 43774, 'Nuclear arms': 43774, 'Nuclear attack': 43774, 'Nuclear bomb': 43774, 'Nuclear crisis': 43774, 'Politics': 148273, 'Missile': 43774, 'Missile defense': 43774, 'Military and defense': 43774, 'Military': 43774, 'War': 43774, "My Name Isn't": 11042, 'Spike Lee': 11042, 'Anthony Ramos': 11042, 'Jamie Overstreet': 11042, 'NYU': 11042, 'Lyriq Bent': 11042, 'Kierna Mayo': 11042, 'Mars Blackmon': 11042, "She's Gotta Have It": 11042, 'Cleo Anthony': 11042, 'Michael Angela Davis': 11042, 'Greer Childs': 11042, 'Eisa Davis': 11042, 'Tonya Lewis Lee.': 11042, 'DeWanda Wise': 11042, 'Fort Greene': 11042, 'prince william': 783708, 'rings': 52336, 'kate middleton': 277443, 'royals': 452056, 'weddings': 52336, 'harry v william': 52336, 'prince harry and prince william engagement': 52336, 'prince harry and meghan engagement': 52336, 'harry and william comparison': 52336, 'william and kate': 52336, 'prince william proposal': 52336, 'suits': 52336, 'meghan engagment ring': 52336, 'princess diana ring': 52336, 'rainsford': 6593, 'rainey': 6593, 'rainey qualley': 6593, 'twin shadow': 6593, 'intentions': 6593, 'billboard news': 3696, 'billboard': 1678453, 'music news': 3696, 'grammy awards': 3696, '2018 grammys': 3696, 'grammys 2018': 3696, 'grammy awards 2018': 3696, '2018 grammy awards': 3696, 'grammy nominations': 3696, 'jay-z': 227966, 'jayz': 3696, 'Childish Gambino': 3696, 'childish': 3696, 'gambino': 3696, 'Kendrick Lamar': 24785854, 'bruno mars': 3696, 'lorde': 3696, 'kendrick': 831170, 'lamar': 3696, 'Despacito': 24785854, 'julia michaels': 3696, 'sza': 3696, 'khalid': 3696, 'lil uzi vert': 3696, '4:44': 3696, 'damn.': 3696, 'kevan kenney': 3696, 'Petula Clark': 36375, 'PArody': 36375, 'the cuddle squad': 36375, 'hammered': 36375, 'Austin': 199209, 'UCB': 36375, 'macklemore': 36375, 'ryan lewis': 36375, 'downtown official music video': 36375, 'song parody': 36375, 'DACA': 1456, 'Chriselle Lim': 31765, 'TheChriselleFactor': 31765, 'Michelle Pham': 31765, 'Stylist': 31765, 'Styling': 31765, 'Wardrobe Stylist': 31765, 'Blogger': 31765, 'Fashion Blogger': 31765, 'Blogging': 31765, 'fashion blog': 31765, 'beauty blogger': 31765, 'korean skin': 31765, 'kbeauty': 31765, 'ootn': 31765, 'short hair': 31765, 'asian': 31765, 'street style': 31765, 'layering': 31765, 'kpop star': 147543, 'fashion guru': 31765, 'luxury fashion': 31765, 'holiday outfits': 31765, 'christmas outfits': 31765, 'new years eve looks': 31765, 'night out outfits': 31765, 'holiday party': 31765, 'party outfits': 31765, 'Chuck': 127521, 'Norris': 127521, 'Carlos': 127521, 'Ray': 127521, 'MrNorrisVideos': 127521, 'of': 2084473, 'Warcraft': 127521, 'WOW': 127521, 'Commercial': 127521, 'Ad': 127521, 'Ads': 127521, 'Advert': 127521, 'Advertisement': 127521, 'Interview': 187691, 'Blizzard': 127521, 'cyber monday algorithm': 1381, 'cyber monday custom': 1381, 'cyber monday 1': 1381, 'cyber mondays': 1381, 'cyber monday royal': 1381, 'cyber monday deals 2017': 1381, 'cybermonday': 1381, 'новости': 180711, 'ТСН': 180711, 'TSN': 180711, 'АТО': 180711, 'Майское': 180711, 'Гладосово': 180711, 'Светлодарская дуга': 180711, 'mario batali': 918829, 'hot wings': 2492149, 'last dab': 2492149, 'chicken wings': 869068, 'malto mario': 869068, 'leftover sandwich': 869068, 'thanksgiving leftovers': 869068, 'wall of shame': 869068, 'hall of shame': 869068, 'food network': 869068, 'The New York Times': 307556, 'NY Times': 307556, 'NYT': 307556, 'Times Video': 307556, 'New York Times video': 307556, 'nytimes.com': 307556, 'jay z blueprint': 224270, 'dean baquet': 224270, 't magazine': 224270, "trump's america": 224270, 'therapy': 525835, 'thanos': 2212431, 'tommy': 1126349, 'wiseau': 1126349, 'rogen': 842514, 'franco': 1126349, 'director': 2346030, 'cult': 1684238, 'secrets': 842514, 'seth rogen': 842514, 'tommy wiseau': 1126349, 'james franco': 1179158, 'alexa chung': 344244, "it's on with alexa chung": 344244, 'alexa chung interview': 344244, 'spicy food': 344244, 'peppers': 344244, 'buying ebay': 635414, 'ebay shopping': 635414, 'mystery unboxing': 635414, 'mystery box': 635414, 'ebay haul': 635414, 'magic trick': 635414, 'top 5': 1698742, 'gift ideas': 2416710, 'no swearing': 1698742, 'no cursing': 1698742, 'Hi5 Studios': 1698742, 'Hi 5 Studios': 1698742, 'Hi5': 1698742, 'Matthias': 1698742, 'Matthiasiam': 1698742, 'Mathias': 1698742, 'strange products': 1698742, 'eBay': 635414, 'kid friendly': 635414, 'real reaction': 635414, '#M763': 635414, 'ebay items': 635414, 'mini flame thrower': 635414, 'weird things you can buy': 635414, 'funny reactions': 635414, 'crazy toys': 635414, 'cool tech gadgets': 635414, 'closer look': 4344512, 'pass': 1371506, 'tax plan': 1371506, 'tax code': 1371506, 'corporations': 1371506, 'Navajo Code Talkers': 1371506, 'poor person': 1371506, 'Steve Mnuchin': 1371506, 'Treasury Secretary': 1371506, 'James Bond': 1371506, 'Gary Cohn': 1371506, 'tax reform': 1371506, 'super”': 1659772, 'manjeet': 2534047, 'paramjeet': 2534047, 'parents”': 698426, '“types': 1659772, 'people”': 1659772, 'nick jonas': 698426, 'Nick Jonus': 698426, 'lilly and nick': 698426, '12collabsofxmas': 1572701, '12 collabs': 718394, 'collabs of christmas': 698426, 'lilly singh christmas collabs': 698426, 'when you catch your boyfriend': 698426, 'when your boyfriend': 698426, 'nick jonas boyfriend': 698426, 'catch nick jonas': 698426, 'betrayal': 698426, 'trustworthy nick': 698426, 'akana': 392890, 'ana': 392890, 'annaakana': 392890, 'ok not ok': 127011, 'ok': 127011, 'being okay with not being okay': 127011, 'Avengers': 505186, 'assemble': 433714, 'phase 3': 433714, 'new place': 292197, 'newborn kittens': 292197, 'newborn cats': 292197, 'nursing cats': 292197, 'caring for baby kittens': 292197, 'caring for baby cats': 292197, 'two week old kittens': 292197, 'kitten tips': 292197, 'caring for kittens': 292197, 'taylor nicole dean cat': 292197, 'taylor nicole dean kittens': 292197, 'taylor house tour': 292197, 'room tour': 292197, 'taylor apartment tour': 292197, 'taylor dean room tour': 292197, 'pet room tour': 292197, 'meet my new pets': 292197, 'my new pets': 292197, 'rylo camera': 1355237, '360 camera': 1355237, 'hyperlapse': 1355237, 'gopro fusion': 1355237, 'Chelsea': 295714, 'Handler': 264975, 'Kate Middleton': 264975, 'j balvin willy william mi gente alesso remix with anwar jibawi': 2121041, 'balvin': 6311485, 'willy': 2121041, 'mi': 2121041, 'gente': 2121041, 'alesso anitta is that for me live at ultra brasil 2017 vr180 experience': 2121041, 'alesso anitta is that for me official music video': 2121041, 'alesso big slap festival 2017': 2121041, 'J. Balvin': 2121041, 'Willy William - Mi Gente (Alesso Remix) with Anwar Jibawi': 2121041, 'drops': 2121041, 'edm drops': 2121041, 'animal video': 1839894, 'the dodo': 1839894, 'Rescue': 1707383, 'Animal Rescue': 1839894, 'dog scared': 1040790, 'shelter dog': 1040790, 'dog adoption': 1040790, 'Dog rescue': 1377452, 'dogs 101': 1377452, 'dogs purpose': 1377452, 'rescue dog': 1377452, 'dog saved': 1707383, 'dogs are awesome': 1377452, 'puppy rescue': 1707383, 'pup rescue': 1377452, 'hope for paws': 1377452, 'pet rescue': 1377452, 'faith in humanity': 1377452, 'dog love': 1040790, 'puppy love': 1040790, 'abused dog': 1040790, 'dog abuse': 1040790, 'puppy abuse': 1040790, 'dog cries': 1040790, 'dog crying': 1040790, 'sad dog': 1377452, 'dog trust': 1040790, 'loyal dog': 1040790, 'dogs are the best': 1040790, 'dog cry': 1040790, 'saddest dog': 1040790, 'kanye dog': 1040790, 'kanye dog crying': 1040790, 'kanye crying': 1040790, 'kanye cries': 1040790, 'John Boyega': 1712563, 'Shows Off': 2029882, 'Michael Jackson': 331652, 'Dance Moves': 331652, 'musical performance': 331652, 'the roots': 331652, 'the last jedi': 5640994, 'detroit': 395551, 'chewbacca': 331652, 'finn': 2396837, 'daisy ridley': 2725038, 'colin': 364537, 'furze': 364537, 'colinfurze': 364537, 'full size': 364537, 'tie fighter': 364537, 'tie silencer': 364537, 'actual size': 364537, 'burley house': 364537, 'working': 364537, 'bb9e': 364537, 'reveal': 364537, 'fans': 422500, 'rougue one': 364537, 'the force awakens': 2076316, 'kylo ren': 1752910, 'spaceship': 702445, 'aids': 331220, 'hiv': 331220, 't cells': 331220, 'cd4 t cells': 331220, 'retrovirus': 331220, 'rna': 331220, 'disease': 783667, 'immune system': 331220, 'immune': 331220, '1980s': 331220, 'cdc': 331220, 'human immunodeficiency virus': 331220, 'acquired immune deficiency syndrome': 331220, 'Michael Aranda': 518163, 'botton': 65783, 'self love': 65783, 'putting yourself first': 65783, 'priorities': 65783, 'parent': 92205, 'parents': 2047781, 'how to parent yourself': 65783, 'growth': 307981, 'what do I want to do': 65783, 'help': 357890, 'self parenting tips': 65783, 'نصائح الأبوة والأمومة النفس': 65783, 'dicas de auto-educação': 65783, 'स्वयं पैरेन्टिंग टिप्स': 65783, "conseils d'auto-parentalité": 65783, '自我教育技巧': 65783, 'selbst erziehende Tipps': 65783, 'reparenting': 65783, 'raising kids': 65783, 'corinne': 330124, 'leigh': 330124, 'czar': 330124, 'light box art': 330124, 'paper cut': 330124, 'paper cuts hurt': 330124, 'crafts and stuff': 330124, 'Liu Yifei Mulan': 99566, 'Crystal Liu Mulan': 99566, 'Disney Live Action Mulan Cast': 99566, 'Live Action Mulan Cast': 99566, 'Live Action': 99566, 'Mulan': 99566, 'Cast': 99566, '2019': 99566, 'Liu Yifei': 99566, 'Crystal Liu': 99566, 'China': 341764, 'Chinese': 99566, 'Reaction': 99566, 'Today': 99566, 'Live Action Mulan Reaction': 99566, 'Live Action Mulan Review': 99566, 'Disney Live Action': 99566, 'Beyond The Trailer': 99566, 'Grace Randolph': 99566, 'Preview': 2210148, 'First Look': 99566, 'Part': 99566, 'Full Movie': 99566, 'Scene': 703433, 'Update': 99566, 'This Week': 99566, 'I Pretended To Be A Model On A Shoot For Jigsaw': 409601, 'jigsaw': 409601, 'saw': 409601, 'photoshoot vlog': 409601, 'saw nurse vlog': 409601, 'mykie': 1075139, 'glam&gor': 1075139, 'glam and gor': 1075139, 'lifestyle vlog': 409601, 'saw blood drive': 409601, 'daily vlog': 451442, 'lionsgate': 409601, 'magnetic': 1538576, 'magnetic nails': 1538576, 'magnetic nail polish': 1538576, 'multi-chrome': 3923559, 'multi-chrome nails': 1538576, 'colour changing': 1538576, 'color changing nails': 1538576, 'magic nails': 1538576, 'magic nail polish': 1538576, 'moving nails': 1538576, 'fun lacquer': 1538576, 'shifting nails': 1538576, 'color shifting': 1538576, 'mood paint': 1538576, 'mood nails': 1538576, 'CYN': 28983, 'With': 28983, 'Unsub': 28983, '(Caroline)': 28983, 'Magikarp': 47215, 'PLvahqwMqN4M3d_PTQywnw74cLqK82BLEO': 391639, 'bmep': 391639, 'friden': 122641, 'electronic calculator': 122641, 'desktop': 122641, 'Millie': 99243, 'elmofilms': 99243, 'sims 4': 99243, 'the sims': 99243, 'sims': 99243, 'sims freeplay': 99243, 'millie t': 99243, 'sim for a day': 99243, '24 hour challenge': 99243, 'ikea': 99243, 'toysrus': 99243, 'PG': 119809, 'the andy griffith show': 3724, 'the jim nabors show': 3724, 'jim nabors': 3724, 'obits': 3724, 'homeless': 32385, 'people': 1737522, 'to harvard': 32385, 'nice': 32385, 'denmark': 14111, 'wanderlust': 14111, 'noble vines': 14111, 'Focus Features': 3490423, 'Movie Trailers': 3490423, 'Trailers': 3490423, 'Independent Film': 26190, 'Cinema': 3490423, 'Clips': 3542783, 'Featurettes': 3490423, 'thoroughbreds': 26190, 'thoroughbred': 26190, 'olivia cooke': 3463912, 'anya taylor joy': 26190, 'anton yelchin': 26190, 'cory finley': 26190, 'fantastic fest': 26190, 'london film festival': 26190, 'sundance': 114426, 'heathers': 26190, 'american psycho': 26190, 'mean girls': 216917, 'horse': 26190, 'connecticut': 26190, 'rich': 320748, 'rich girls': 26190, 'betches': 26190, 'star trek': 120432, 'spring breakers': 26190, 'edge of seventeen': 26190, 'new mutant': 26190, 'itonya': 26190, 'horse movie': 26190, 'split': 26190, 'ready player one': 3953279, 'white house live': 10179, 'white house press briefing': 106434, 'white house briefing': 10179, 'sarah sanders': 106434, 'sarah huckabee sanders': 10179, 'prince harry engagement': 399720, 'royal engagement': 461651, 'fridge': 174613, 'Ming Xi': 201689, 'Ming Xi victorias secret': 201689, 'victorias secret fashion show 2017': 201689, 'Ming Xi fall': 201689, 'minis': 1610879, 'mini shorts': 590227, 'green light': 590227, 'traffic light': 590227, 'stop light': 590227, 'Galantis': 197485, 'Throttle': 197485, 'Tell me you love': 197485, 'official music video': 216115, 'galantis official': 197485, 'come on and say it now': 197485, 'come on say it now': 197485, 'galantis tell me you love me': 197485, 'throttle tell me you love me': 197485, 'galantis throttle': 197485, 'throttle official': 197485, 'throttle music': 197485, 'new galantis': 197485, 'galantis song': 197485, 'tell me you love me galantis': 197485, 'tell me you love me throttle': 197485, 'galantis throttle collab': 197485, 'tell me u love me': 197485, 'galantis oh baby': 197485, 'galantis its easy': 197485, 'say it a million times tell me you love me': 197485, "POPSUGAR Girls' Guide": 36588, 'PSGG': 36588, 'POPSUGAR Girls Guide': 36588, 'POPSUGAR': 36588, 'saoirse ronan pronounciation': 36588, 'Ladybird': 36588, 'fall 2017 movies': 36588, 'saoirse ronan interview': 36588, 'lady bird review': 36588, 'lady bird christine': 36588, 'celebrity name pronounciation': 36588, 'funny celebrity interviews': 36588, 'lady bird movie': 36588, 'how to add email attachments on iPhone': 7126, 'how to add mail attachments on iPhone': 7126, 'how to send attachments on iPhone': 7126, 'how to sent email attachments on iPhone': 7126, 'Send attachments in mail on iPhone': 7126, 'how to send a photo in mail on iPhone': 7126, 'how to send a video in mail on iPhone': 7126, 'how to send a document in mail on iPhone': 7126, 'how to send email attachments on iPad': 7126, 'how to add email attachments on iPad': 7126, 'how to send mail attachments on iPad': 7126, 'DraftExpress': 1432, 'NBA Draft': 1432, 'Draft Express': 1432, 'NCAA': 1432, 'Prospects': 1432, 'draftexpress.com': 1432, 'Jonathan': 1432, 'Givony': 1432, 'scouting': 1432, 'prospect': 1432, 'footage': 427298, 'spacewalk': 26527, 'truTV': 7829, 'tru': 7829, 'funny because its tru': 7829, 'truTV truTV comedy': 7829, 'truTV schedule': 7829, 'truTV television': 7829, 'truTV truTV app': 7829, 'truTV channel': 7829, 'truTV episodes': 7829, 'truTV clips': 7829, 'truTV show clips': 7829, 'truTV nyc': 7829, 'At Home with Amy Sedaris': 7829, 'Amy': 3597, 'Special': 3597, 'Tell Me You Love Me': 6296393, 'Tell': 6296393, 'G-Eazy & Halsey': 1554273, 'Him & I': 1554273, 'joy behar': 279827, 'michael flynn': 376082, 'robert mueller': 279827, 'special counsel': 279827, 'guilty': 279827, 'FBI': 279827, 'federal prosecutor': 279827, 'Matt lauer': 1855536, 'Today Show': 1855536, 'John Conyers': 1892481, 'Weinstein': 1855536, 'Bob Costas': 1855536, 'roy': 856874, 'moore': 856874, 'alabama': 1085123, 'doug': 856874, 'jones': 2851150, 'molester': 856874, 'sexual': 937495, 'assault': 1079100, 'christian': 856874, 'doug jones': 1038596, 'child molester': 856874, 'christian values': 856874, 'jimmy kimmel roy moore': 856874, 'alabama senate race': 868673, 'carol': 591523, 'bells': 588112, 'of the': 588112, 'women in music': 1674757, 'women in music 2017': 1674757, 'wim': 1674757, 'wim 2017': 1674757, 'woman of the year': 1674757, 'wolves': 1688843, 'bad liar': 1674757, 'pop music': 1762241, 'acceptance speech': 1674757, 'selena': 1688843, 'gomez': 1688843, 'selena gomez women in music': 1674757, 'selena gomez woman of the year': 1688843, 'selena gomez acceptance speech': 1688843, 'beyonce live': 8128270, 'Reactception': 865993, 'Old Channels': 865993, 'React to Themselves': 865993, 'YOUTUBERS REACT TO THEIR OLD YOUTUBE CHANNEL PROFILE #2': 865993, 'college kids react': 4458178, '2017 MAMA': 7706501, '2017마마': 7706501, 'MAMA': 7706501, '마마': 7706501, 'Mnet': 7706501, '엠넷': 7706501, 'Mnet Asian Music Awards': 7706501, 'week 13': 3192663, 'wk 13': 3192663, 'washington': 928751, 'thursday night football': 928751, 'tnf': 928751, 'morris': 928751, 'bryant': 928751, 'cowboys win': 928751, 'sp:dt=2017-11-30T20:25:00-05:00': 928751, 'sp:ti:away=Was': 928751, 'hangep': 207223, 'Black Mirror': 1114233, '1990s': 352661, 'lesson': 352661, 'inside edition': 426847, '1993': 352661, 'elementary school': 352661, 'linda ellerbee': 352661, 'cat-royalwedding': 352661, 'ivory': 352661, 'soap': 352661, 'social studies': 352661, 'ie royal wedding': 352661, 'throwback': 385377, 'ed': 409414, 'sheeran': 409414, 'the shape of you': 409414, 'Ellen DeGeneres': 409414, 'Juicy': 121274, 'Kamasutra': 121274, '(Audio)': 121274, "Mo'": 121274, 'Faces': 121274, 'Rap/Hip-Hop': 121274, 'Five': 202697, 'Finger': 202697, 'Death': 209832, 'Metal': 202697, 'Gone': 202697, 'Away': 202697, 'GreatestHits': 202697, 'Offspring': 202697, 'vydia': 305054, 'Gone Away': 202697, 'Five Finger Death Punch': 202697, 'A Decade Of Destruction': 202697, 'Prospect Park': 202697, 'Nick Hipa': 202697, 'Bryan Holland': 202697, 'vevo': 305054, 'viola': 80811, 'wicked': 80811, 'the color purple': 80811, 'matt lauer fired from nbc': 216332, 'matt lauer apology': 216332, 'matt lauer statement': 216332, 'inappropriate sexual behavior': 216332, 'matt lauer 2017': 216332, 'inappropriate': 216332, 'nbc fires matt lauer': 216332, 'matt lauer fired from today': 216332, 'midnight sun': 1216410, 'bella thorne': 3097719, 'patrick schwarzenegger': 1216410, 'rob riggle': 1216410, 'teen': 1594285, 'march 23': 1216410, 'Healthcare': 709836, 'health care': 1147815, 'Obamacare': 709836, 'trumpcare': 709836, 'affordable care act': 1150941, 'ACA': 709836, 'private insurance': 709836, 'health insurance': 1150941, 'single payer': 709836, 'single-payer health care': 709836, 'government health care': 709836, 'medicaid': 709836, 'medicare': 709836, 'veterans affairs': 709836, 'individual mandate': 709836, 'premiums': 709836, 'managing health cost': 709836, 'United States health': 709836, 'medicare for all': 709836, 'Bernie Sanders': 709836, 'uninsured': 709836, 'doctors visit': 709836, 'angioplasty': 709836, 'master price list': 709836, 'Martin Garrix': 1418342, 'Wizard': 1418342, 'Proxy': 1418342, 'BFAM': 1418342, 'Tremor': 1418342, 'GRX': 1418342, 'Ultra': 1418342, 'EDC': 1418342, 'Dance music': 1418342, 'Martin': 1418342, 'Garrix': 1418342, 'Hardwell': 1418342, 'Tiesto': 1418342, 'Avicii': 1418342, 'Virus': 1418342, 'Beatport': 1418342, 'DJ Mag': 1418342, 'David Guetta': 1972313, 'in the name of love': 1418342, 'jamie scott': 1418342, 'romy dya': 1418342, 'so far away': 1418342, 'ice cream rolls': 129052, 'rolled ice cream': 129052, 'at home': 340172, 'diy ice cream roll': 129052, 'ice-cream rolls': 129052, 'rolled ice-cream': 129052, 'rolled icecream': 129052, 'thia rolled ice cream': 129052, 'rolled ice cream near me': 129052, 'rolled up ice cream': 129052, 'roll ice cream': 129052, 'what is rolled ice cream?': 129052, 'how to make rolled ice cream': 129052, 'rolled ice cream machine': 129052, 'thai ice cream rolls': 129052, 'ann reardon': 129052, 'how to cook that': 129052, 'Ice Cream Recipes HOW TO COOK THAT Ann Reardon starburst chocolate': 129052, 'Microsoft': 95050, 'Microsoft Excel': 95050, 'Excel': 95050, 'Tech & Science': 95050, 'Lifestyle & Entertainment': 179106, 'Weird & Fun Knowledge': 319593, 'Biography & Profile': 235537, 'Tatsuo Horiuchi': 95050, 'Simple': 57539, 'more': 18630, 'part two': 18630, 'megan markle': 225107, 'princess kate': 225107, 'princess charlotte': 225107, 'prince george': 225107, 'prince charles': 225107, 'kate': 225107, 'queen elizabeth': 225107, 'uk news': 394634, 'the royals': 225107, 'kensington palace': 225107, 'the queen': 225107, 'Mattlauer': 261544, 'Conan': 261544, 'Jolly': 138633, 'JOLLY': 138633, 'jolly': 138633, '졸리': 138633, '조쉬': 138633, '조시': 138633, '올리': 138633, 'josh': 138633, 'englishman': 138633, '외국인': 138633, '영국남자': 138633, '새로운': 138633, '채널': 138633, 'second': 138633, '사생활': 138633, '한국말': 138633, '영국': 138633, '런던': 138633, 'Nowhere': 48394, 'Girl': 48394, 'the script rain': 151733, 'the script hall of fame': 151733, "the script the man who can't be moved": 151733, 'the script breakeven': 151733, 'the script nothing': 151733, 'the script superheroes': 151733, 'the script for the first time': 151733, 'the script rain lyrics': 151733, 'the script if you could see me now': 151733, 'Arms Open': 110706, 'Science & Technology': 6084, 'Cooking Vinyl': 6598, 'Lissie': 6598, 'Rock Singer/Songwriter': 6598, 'No More': 95919, 'PRETTYMUCH feat. French Montana': 95919, 'Syco Music': 1081917, 'FIFA': 222827, 'Soccer': 222827, 'Futbol': 222827, 'Futebol': 222827, 'Fußball': 222827, 'Fussball': 222827, 'Calcio': 222827, 'Voetbal': 222827, 'كرة': 222827, 'فوتبول': 222827, 'winter beauty haul': 110552, 'books uk': 110552, 'space nk': 110552, 'candles': 110552, 'diptyque': 110552, 'body': 110552, 'high end': 110552, 'winter saviours': 110552, 'winter skin': 110552, 'HihoKids': 162834, 'HiHoKids': 162834, 'forkids': 162834, 'kidsvideos': 162834, 'kidstry': 162834, 'children': 1809227, 'kidsdescribe': 162834, 'ernie': 162834, 'ernietries': 162834, 'kidfriendly': 162834, 'familyfriendly': 162834, 'Maddox': 162834, 'Maddoxweddle': 162834, 'kidactivities': 162834, 'kidsplay': 162834, 'kidsmakefriends': 162834, 'trynewthings': 162834, 'kidsguess': 162834, 'what’sinthebox': 162834, 'kidsfun': 162834, 'Cut': 162834, 'WatchCut': 162834, 'Desmond': 162834, 'Crystal': 162834, 'Justin': 162834, 'GG': 162834, 'Jayque': 162834, 'Steven': 162834, 'Carolina': 162834, 'TBS Network': 831481, 'TBS Shows': 831481, 'Shows': 831481, 'TBS Funny': 831481, 'TBS New': 831481, 'New TBS': 831481, 'james corden': 831481, 'the late late show': 831481, 'drop the mic': 831481, 'hailey baldwin': 831481, 'method man': 831481, 'chrissy metz': 831481, 'nicole richie': 831481, 'anthony anderson': 831481, 'this is us': 831481, 'modern family': 831481, 'rascal flatts': 831481, 'baldwin': 2101958, 'wu tang': 831481, 'wutang wu': 831481, 'tang': 831481, 'clan rap': 831481, '101': 831481, 'vanessa hudgens': 831481, 'michael bennett': 831481, 'zac efron': 868329, 'drop the mic vanessa': 831481, 'genius': 808174, 'rap genius': 87484, 'verified': 87484, 'official lyrics': 87484, 'Lyric videos': 87484, 'new pop music': 87484, 'Moriah Pereira poppy': 87484, 'poppy rio': 87484, 'chris greatti': 87484, 'titanic sinclair': 1787882, 'titanic': 87484, 'zo chats': 87484, 'zo ai': 87484, 'am i doing this right': 87484, 'Windsor in May': 61931, 'prince harry and meghan wedding': 61931, 'Meghan Markle news': 61931, "St George's Chapel": 61931, 'royal family news': 61931, 'Season': 5925, 'Wally': 5925, 'West': 5925, 'Jesse': 5925, 'Quick': 5925, 'Harrison': 5925, 'Wells': 5925, 'Iris': 5925, 'Episode': 67046, 's04': 5925, 'S04': 5925, 'Elongated': 5925, 'Man': 5925, 'Crisis on Earth-X': 5925, 'Snart Ray Kiss': 5925, 'Snart Kiss': 5925, 'Leonard Snart': 5925, 'Earth-X': 5925, 'Meghan Markle (TV Actor)': 12515, 'United': 12515, 'Nations': 12515, '(Membership': 12515, 'Organization)': 12515, 'Politics (TV Genre)': 12515, 'Women (Organization)': 12515, 'Gender': 12515, 'Equality': 12515, '(Literature': 12515, 'Subject)': 12515, 'last jedi': 1427324, 'supercut': 6320, 'wengie': 1048933, 'toothpaste': 7364482, 'toothpaste hacks': 7364482, 'you should know': 1048933, 'tooth paste': 1048933, 'back to school': 1048933, 'toothpaste life hacks': 1048933, 'testing life hacks': 1048933, 'sister': 1048933, 'brother': 1048933, 'best pranks': 1048933, 'diy edible': 1048933, 'edible school supplies': 1048933, 'funny pranks': 1048933, 'twin': 1048933, 'gummy vs real food': 1048933, 'easy diys': 1048933, 'prank wars': 1048933, 'food pranks': 1048933, 'back to school life hacks': 1048933, 'you need to try': 1048933, 'amazing hacks': 1048933, 'satisfying': 458963, 'oddly satisfying': 458963, 'dominoes falling': 458963, 'satisfying videos': 458963, 'satisfying domino video': 458963, 'most satisfying video': 458963, 'most oddly satisfying': 458963, 'most satisfying video in the world': 458963, 'Domino Rally': 458963, '1000000 dominoes': 458963, 'Episode 1732': 3802381, 'Alec Baldwin': 95016, 'Michael Flynn': 95016, 'Mike Day': 95016, 'Billy Bush': 95016, 'Alex Moffat': 95016, 'Vladimir Putin': 95016, 'Hillary Clinton': 95016, 'Kellyanne Conway': 95016, 'Melania Trump': 95016, 's43e7': 3802381, 'episode 7': 3802381, 'Christine McPherson': 3802381, 'irish': 3975329, 'US News': 603620, 'bedroom makeover': 227188, 'fairy lights DIY': 227188, 'fairy lights headboard': 227188, 'fairy lights': 227188, 'canopy': 227188, 'DIY canopy': 227188, 'fairy lights canopy': 227188, 'fairy lights idea': 227188, 'bedroom DIY': 227188, 'headboard DIY': 227188, 'twinkle lights DIY': 227188, 'twinkle lights headboard': 227188, 'twinkle lights canopy': 227188, 'string lights DIY': 227188, 'bohemian bedroom': 227188, 'dreamy bedroom': 227188, 'bedroom decor': 227188, 'bedroom design': 227188, 'mr kate': 227188, 'mr. kate': 227188, 'mrkate': 227188, 'mister kate': 227188, 'bedroom ideas': 227188, 'pinterest bedroom': 227188, 'pinterest diy': 227188, 'star': 1493342, 'wars': 1155434, 'wars:': 264632, 'last': 1133481, 'jedi': 1133481, 'porgs': 264632, 'hamill': 1133481, 'adam': 1133481, 'daisy': 1133481, 'ridley': 1133481, 'serkis': 264632, 'Rian Johnson': 1764612, 'Mark Hamill': 2081004, 'Adam Driver': 1903156, 'Oscar Isaac': 2909283, 'Andy Serkis': 791566, 'Gwendoline Christie': 1041442, 'Kelly Marie Tran': 1206111, 'laura dern': 2118697, 'star wars: the last jedi': 564546, 'jordan peele': 607076, 'get out': 611454, 'jordan peele get out': 607076, 'jordan peele interview': 607076, 'jordan peele 2017': 607076, 'get out fan theories': 607076, 'get out theories': 607076, 'get out easter eggs': 607076, 'easter eggs': 607076, 'fan theories': 607076, 'debunk fan theories': 607076, 'get out theory': 607076, 'get out movie': 607076, 'get out detail': 607076, 'being john malkovich': 607076, 'get out jordan peele': 607076, 'key and peele': 607076, 'jordan peele get out interview': 607076, 'jordan peele horror movie': 607076, 'get out fan theory': 607076, 'tiny cooking': 747542, 'tiny food': 747542, 'tiny steak': 747542, 'tiny potato': 747542, 'buzzfeed tasty': 747542, 'spaghetti and meatballs': 747542, 'meatballs': 747542, 'tiny meal': 747542, 'tiny dinner': 747542, 'rice': 1240821, 'tiny': 1328338, 'ニンジャバットマン': 755014, 'batman ninja': 755014, '神風動画': 755014, '水﨑淳平': 755014, '中島かずき': 755014, '岡崎能士': 755014, 'バットマン': 755014, 'vlogmas': 860691, 'vlogmas 2017': 430347, 'kandee vlogmas': 19767, 'holiday videos': 19767, 'candyland': 19767, 'kandeeland': 19767, 'color': 477893, 'eastman': 477893, 'technicolor': 477893, 'wizard of oz': 477893, 'adventures of robin hood': 477893, 'two strip technicolor': 477893, 'three strips technicolor': 477893, 'film history': 477893, 'kalmus': 477893, 'natalie kalmus': 477893, 'how technicolor worked': 477893, 'what was technicolor': 477893, 'what technicolor meant': 477893, 'mit': 477893, 'cameras': 731800, 'dorothy': 477893, 'gone with the wind': 477893, 'vox almanac': 477893, 'phil edwards': 477893, 'technicolour': 477893, 'color correction': 477893, 'color grading': 477893, 'color film': 477893, 'colour': 503673, 'how to color grade': 477893, 'history of film': 477893, 'the wizard of oz': 477893, 'communitychannel': 164324, 'natalie tran': 164324, 'community channel': 164324, 'nat tran': 164324, 'natalie': 164324, 'tran': 164324, 'creators for change': 164324, 'white man asian female': 164324, 'white man': 164324, 'asian female': 164324, 'interracial relationships': 164324, 'Rey': 1240047, 'Kylo Ren': 780000, 'Finn': 1116279, 'Captain Phasma': 242741, 'Rose Tico': 769675, 'Lightsaber': 769675, 'day in my life': 37594, 'spend the day with me': 37594, 'mr student body president': 37594, 'arden rose': 37594, 'arose': 37594, 'arden': 37594, 'special': 80039, 'Sia Candy Cane Lane Holiday': 693228, 'crossovers': 48587, 'Kyrie Irving': 48587, 'handles': 48587, 'Dwight Howard': 48587, 'LeBron': 48587, 'Kemba': 48587, 'John Wall': 48587, 'crossovers of the month': 48587, 'Derrick Rose': 48587, 'Carmelo Anthony': 48587, 'Melo': 48587, 'Chris Paul': 48587, 'CP3': 48587, 'Damian Lillard': 48587, 'Lillard': 48587, '30 before 30': 36865, 'life goals': 36865, 'wishlist': 36865, 'bucket list': 36865, 'lucy moon university': 36865, 'japanese donald trump': 55556, 'mike diva': 55556, 'anna akana': 55556, 'mikediva': 55556, 'kazoo kid': 55556, 'kazoo kid trap': 55556, 'sexy sax man': 55556, 'Karlie Kloss': 15859, 'Klossy': 15859, 'klossy office': 15859, 'victoria secret model': 15859, 'kode with klossy': 15859, 'code with klossy': 15859, 'molly burke': 15859, 'braille': 15859, 'decording': 15859, 'code': 15859, '––learning': 15859, 'accessibility': 15859, 'can i be him': 297306, 'back from the edge': 297306, "say you won't let go": 297306, 'sun comes up': 297306, 'x faktor': 297306, 'James Arthur': 297306, 'tessa violet': 132405, 'video blogger': 132405, 'meekakitty': 132405, 'original music': 132405, 'youtube musician': 132405, 'KIm K': 103737, 'K. Michelle': 103737, 'K.Michelle Music': 103737, 'Official Audio': 103737, 'Warner Music Group': 103737, 'The People I Used To Know': 103737, 'KIMBERLY: The People I Used To Know': 103737, 'Birthday': 103737, 'Either Way': 103737, 'Make This Song Cry': 103737, 'sneakerhead': 849385, 'complex originals': 849385, 'sneakers': 849385, 'current affairs': 849385, 'young man': 849385, 'edgy': 849385, 'complex tv': 849385, 'ashanti': 357736, 'everyday struggle': 86314, 'joe budden': 50494, 'dj akademiks': 50494, 'nadeska alexis': 50494, 'MOBO': 68039, 'Cardi B': 24718311, 'MOBO Awards': 68039, 'Live performance': 661305, 'bodak yellow': 68039, 'dance again': 24996, 'jenny from the block': 24996, 'j lo': 24996, 'latinas': 24996, 'hispanic': 24996, 'latin america': 24996, 'latinos': 24996, 'latina mom': 24996, 'famous people': 24996, "i'm a celeb": 24996, 'transformations': 75534, 'natural makeup': 33953, 'pug': 537001, 'pugs': 214222, 'pug life': 3411, 'pet spoof': 3411, 'pup': 3411, 'doggy': 3411, 'aww': 3411, 'Pie': 1515, 'disney princess pies': 1515, 'disney princess cake': 1515, 'disney princess desserts': 1515, 'disney princess party': 1515, 'hand pies': 1515, 'pies are awesome': 1515, 'pie art': 1515, 'raspbery pi': 1237, 'pi zero': 1237, 'pygame': 1237, 'ADS1115': 1237, 'raspberry pi camera': 1237, 'night vision': 1237, 'IGNITION': 3778, 'IGNITION2017': 3778, 'Media': 3778, 'Marketing': 3778, '5-Minute Crafts': 6315549, 'Do it yourself': 6315549, 'trucos': 6315549, 'trucos de belliza': 6315549, 'proyectos faciles': 6315549, 'useful things': 6335408, 'lifehacks': 6315549, 'DIY projects': 6335408, 'DIY activities': 6315549, 'Handcraft': 6315549, 'old into new': 6315549, 'save money': 6315549, 'money-saving': 6315549, 'reuse': 6315549, 'recycle': 6315549, 'repurpose': 6315549, 'way': 6315549, 'ways': 6315549, 'fix a phone': 6315549, 'phone hacks': 6554775, 'scratches': 6315549, 'broken screen': 6315549, 'eraze': 6315549, 'plastic bottles': 6315549, 'fork': 6315549, 'kitchen hacks': 6351302, 'cola': 6315549, 'colgate': 6315549, 'plastic bags': 6315549, 'cleaning': 6315549, 'the invention of chapstick': 199519, 'chapstick': 199519, 'gus chapstick': 199519, 'losing chapstick': 199519, 'reddit youtube haiku': 199519, 'funny meme': 199519, 'meme p': 199519, 'Water Bottle Gadgets': 546025, 'Gadgets': 546025, 'Gadget': 546025, 'Test': 546025, 'Water Bottle': 546025, 'gadgets review': 546025, '8 Water Bottle Gadgets put to the Test': 546025, 'Coffee Mug': 546025, 'Portable Water Bottle': 546025, 'Lemon Water Bottle': 546025, 'Electric Protein Shaker': 546025, 'Self Stirring Mug': 546025, 'Stainless Steel Vacuum Cup': 546025, 'Ohio State Buckeyes': 204293, 'Ohio State': 251907, 'Buckeyes': 251907, 'Wisconsin Badgers': 204293, 'Badgers': 204293, 'Big 10 Championship': 204293, 'Big 10': 204293, 'Championship': 204293, 'sp:dt=2017-12-02T20:00:00-05:00': 204293, 'sp:ti:home=Wisc': 204293, 'leyden jar': 616695, 'leyden': 616695, 'jar': 616695, 'electricity': 616695, 'zap': 616695, 'lightning': 619395, 'static electricity': 616695, 'leiden jar': 616695, 'capacitor': 616695, 'static': 616695, 'electrostatics': 616695, 'electrostatic': 616695, 'leyden jar (invention)': 616695, 'experiments': 1006583, 'high voltage': 616695, 'generator': 616695, 'graaff': 616695, 'van': 616695, 'capacitor (invention)': 616695, 'energy': 616695, 'spark gap': 616695, 'coil': 616695, 'metep': 228716, 'pizza pouch': 113122, 'the pizza pouch': 113122, 'the pizza bag': 113122, 'pizza bags': 113122, 'portable pizza': 113122, 'wearable pizza': 113122, 'diy wearable pizza': 113122, 'diy pizza pouch': 113122, 'diy portable pizza': 113122, 'diy pizza': 113122, 'latino hunger games': 6664727, 'latino': 2474283, 'Latino Hunger Games | Lele Pons': 2474283, 'buzzfeed blue': 221797, 'buzzfeedblue': 221797, 'wilderness': 326388, 'outdoors': 221797, 'Miles bonsignore': 221797, 'off the grid': 221797, 'how to survive': 221797, 'teach': 221797, 'teacher': 221797, 'survivalism': 221797, 'bug out': 221797, 'bug out bag': 221797, 'survival skills': 221797, 'prepping': 221797, 'survival knife': 221797, 'zombie survival': 221797, 'survival training': 221797, 'survival school': 221797, 'survival games': 221797, 'off grid': 221797, 'primitive skills': 221797, 'survival gear': 221797, 'best survival': 221797, 'permaculture': 221797, 'homesteading': 221797, 'shelter': 221797, 'survival kit': 221797, 'hiking': 221797, 'roommates': 444397, 'housing': 444397, 'tutorials': 466691, 'messy': 444397, 'horrible people': 444397, 'shane crown': 444397, 'roommate fails': 444397, 'shitty roommate projects': 444397, 'unfinished projects': 444397, 'food ranger': 341584, 'street food': 341584, 'the food ranger': 341584, 'trevor james': 341584, 'street food videos': 341584, 'best street food': 341584, 'street food around the world': 341584, 'street': 470617, 'indian street food': 341584, 'indian food': 341584, 'street food india': 341584, 'food ranger india': 341584, 'india': 1237361, 'kolkata': 341584, 'curry': 2721571, 'kolkata street food': 341584, 'kolkata food': 341584, 'street food indian': 341584, 'street food kolkata': 341584, 'indian cooking': 341584, 'india street food': 341584, 'calcutta': 341584, 'indian street food kolkata': 341584, 'bengali street food': 341584, 'india food tour': 341584, 'indian food tour': 341584, 'what if': 494592, 'the moon': 494592, 'destroy the moon': 494592, 'moon exploded': 494592, 'moon blew up': 494592, 'no more moon': 494592, 'astronomy': 494592, 'what if the moon exploded': 494592, 'can we destroy the moon': 494592, 'harrison': 868849, 'Star Wars: The Last Jedi': 1996434, 'han solo': 1739043, 'princess leia': 868849, 'cupcake christmas tower': 149886, 'Pizza kit': 74720, 'Paolo Nespoli': 74720, 'international space station': 74720, 'Expedition 53': 74720, 'pizza in space': 74720, 'beamazed': 47661, 'be amazed': 47661, 'astronaut': 47661, 'space travel': 47661, 'top10': 2006705, 'outer space': 47661, 'black hole': 47661, 'Spacesuit': 47661, 'what happens': 47661, 'vacuum': 47661, 'planet': 47661, 'Cosmonaut': 47661, 'gravity': 786230, 'International Space Station': 47661, 'space myths': 47661, 'universe': 47661, '10s': 47661, 'lack of oxygen': 47661, 'Time Travel': 114918, 'Wormholes': 114918, 'Outer Space': 114918, 'Space': 114918, 'Space Exploration': 114918, 'Brian Greene': 114918, 'World Science Festival': 114918, 'Albert einstein': 114918, 'tokyo drift': 131198, 'nihon': 131198, 'nihon nights': 131198, 'illegal': 131198, 'tuning': 131198, 'tuned car': 131198, 'tuning car': 131198, 'fast and furious': 131198, 'teriyaki boyz': 131198, 'drift': 131198, 'the fast and the furious': 131198, 'nippon paint': 131198, 'spotless': 131198, 'horsepower': 131198, 'fast cars': 131198, 'drifting': 131198, 'lights': 131198, 'tune': 131198, '350z': 131198, 'mad mike': 131198, 'mike whiddett': 131198, 'mazda': 131198, 'rx7': 131198, 'mazda mx-5': 131198, 'need speed': 131198, 'turbo': 131198, 'mad mike whiddett': 131198, 'speed': 385105, 'burnout': 131198, 'nfs': 131198, 'rotary': 290568, 'motoring': 131198, 'convertible': 131198, 'turismo': 131198, 'goodwood festival of speed': 131198, 'asia': 131198, 'big': 682561, 'waves': 26726, 'big wave': 22090, 'big waves': 22090, 'monster': 22090, 'nazare': 22090, 'nazaré': 22090, 'praia do norte': 22090, 'portugal': 22090, 'surf': 22090, 'surfing': 264288, 'surfer': 22090, 'storm': 24790, 'drone footage': 22090, 'los angeles drone film festival': 22090, 'motherboard': 22090, 'official nominee': 22090, 'towin': 22090, 'jetski': 22090, 'jet': 22090, 'ski': 22090, 'swell': 22090, 'capsize': 22090, 'injured': 22090, 'danger': 22090, 'Tom Daley': 27616, 'Tom': 27616, 'Daley': 27616, 'Tom Daley TV': 27616, 'Diver': 27616, 'Diving': 27616, 'World Champion Diver': 27616, 'Olympics': 27616, 'Recipes': 27616, 'Mark Ferris': 27616, 'christmas pudding': 27616, 'chocolate cake': 123866, 'christmas pudding alternative': 27616, 'christmas tree': 569319, 'ferris': 27616, 'holly': 27616, 'balls': 27616, 'chocolate christmas pudding': 27616, 'christmas ideas': 27616, 'Infinity War': 71472, 'Aliens': 30469, 'Thanos': 30469, 'Lucille Ball': 30469, 'Biopic': 71472, 'Deapool': 30469, 'Deadpool 2': 30469, 'this morning': 198437, 'holly willoughby': 198437, 'phillip schofield': 198437, 'ruth langsford': 198437, 'eamonn holmes': 198437, 'chat shows - topic': 198437, 'chat show - topic': 198437, 'talk shows - topic': 198437, 'This Morning Funny Moments': 198437, 'this morning interview': 198437, 'Royal Engagement': 198437, 'Meghan Markle and Prince Harry': 198437, 'Prince Harry and Meghan Markle': 198437, 'World Aids Day': 198437, 'This Morning Alison Hammond': 198437, 'Alison Hammond Interview': 198437, 'Alison Hammond Funny': 198437, 'stranger things kids': 85372, 'stranger things 3': 85372, 'stranger things season 3': 85372, 'stranger things theory': 85372, 'stranger things prediciton': 85372, 'stranger things meme': 85372, 'monster stranger things': 85372, 'demagorgan': 85372, 'stranger things talk': 85372, 'duffer brothers': 85372, 'nostalgia': 85372, 'Xscape': 24972, 'Dream': 24972, 'Killa': 24972, 'live on the moon': 256786, 'lunar colony': 256786, 'moon colony': 256786, 'civilization': 256786, 'people live on the moon': 256786, 'jaxa': 256786, 'how long is a day on the moon': 256786, 'moon temperature': 256786, 'lunar': 256786, 'radiation': 256786, 'solar flare': 256786, 'oxygen on the moon': 256786, 'Space Launch System': 256786, 'thirty': 261506, 'mile': 261506, 'thirty mile zone': 261506, 'tmz': 261506, 'studio zone': 261506, 'los angeles studio zone': 261506, 'unions': 261506, 'union': 1259734, 'union rules': 261506, 'sag-aftra': 261506, 'skillshare': 261506, 'Bureaucracy': 261506, 'film industry': 261506, 'motion picture industry': 261506, 'invisible': 1162324, 'discount': 261506, 'colin kaepernick': 92102, 'nathan peterman': 92102, 'aaron rodgers': 92102, 'green bay packers': 92102, 'arizona cardinals': 92102, 'houston texans': 92102, 'jacksonville jaguars': 92102, 'denver broncos': 92102, 'tennessee titans': 92102, 'san francisco 49ers': 92102, 'chart party': 92102, 'jon bois': 92102, 'sb nation': 92102, 'joe flacco': 92102, 'baltimore ravens': 92102, 'First Aid Kit': 13128, 'Fireworks': 13128, 'Ruins': 13128, 'First Aid Kit Ruins': 13128, 'First Aid Kit Fireworks': 13128, 'innovation': 7188, 'UChicago': 7188, 'University of Chicago': 7188, 'Enrico Fermi': 7188, 'Atomic Age': 7188, 'nuclear chain reaction': 7188, 'iMovie': 88257, 'Cleveland Browns': 88257, 'Phillip Rivers': 88257, 'weed': 1376988, 'addiction': 90224, 'Danny Woodhead': 88257, 'Stephen A Smith': 88257, 'Johny Manziel': 88257, 'Johnny Manziel': 88257, 'comeback': 88257, 'Roger Godell': 88257, 'episode 8': 2960625, 'star wars episode 8 return of the jedi': 1133142, 'star wars trailer': 1133142, 'harry potter': 1133142, 'ronald mcdonald': 1133142, 'x men': 1133142, 'star wars new': 1133142, 'revenge of the sith': 1133142, 'a new hope': 1133142, 'the force': 1500659, 'public': 1133142, 'pranking': 1133142, 'star wars in public prank': 1133142, 'rackaracka': 1133142, 'water bottle': 65409, 'clean water': 65409, 'QUARTZ Bottle': 65409, 'self-cleaning water bottle': 65409, 'Joe Rogan': 51984, 'Eddie Bravo': 51984, 'Bobb Sapp': 51984, 'Antônio Rodrigo Nogueira': 51984, 'The Joe Rogan Experience': 51984, 'Arduino': 4767, 'Visuino': 4767, 'QRS Piano Rolls': 4767, 'Ampico Piano Rolls': 4767, 'Knabe Grand Piano': 4767, 'Dachshund': 4767, 'Lil': 682894, 'Uzi': 682894, 'Vert': 682894, 'Lil Uzi Vert': 682894, 'Uzi Vert': 682894, 'Lil Uzi': 682894, 'TWLG': 682894, 'The Way Life Goes': 682894, 'The Way Life Goes Remix': 682894, 'Lil Uzi Vert Remix': 682894, 'Nicki Minaj Lil Uzi Vert': 682894, 'Lil Uzi Vert Nicki Minaj Remix': 682894, 'Nicki Minaj Remix': 682894, 'The Perfect LUV Tape': 682894, 'Lil Uzi Vert Vs. The World': 682894, 'Luv is Rage': 682894, 'LUV': 682894, 'LUV is Rage': 682894, 'LUV is Rage 2': 682894, 'Rage 2': 682894, 'Jurassic World': 24342486, 'Fallen Kingdom': 20878253, 'Jurassic Park': 20878253, 'T-Rex': 20878253, 'Dinosaur': 20878253, 'J.A. Bayona': 20878253, 'Steven Spielberg': 20878253, 'Colin Trevorrow': 20878253, 'Chris Pratt': 20878253, 'Bryce Dallas Howard': 20878253, 'BD Wong': 20878253, 'James Cromwell': 20878253, 'Ted Levine': 20878253, 'Justice Smith': 20878253, 'Geraldine Chaplin': 20878253, 'Daniella Pineda': 20878253, 'Toby Jones': 20878253, 'Jeff Goldblum': 20878253, 'Rafe Spall': 20878253, 'Derek Connolly': 20878253, 'Isla Nublar': 20878253, 'Raptor': 20878253, 'murda inc': 307242, 'ja rule': 307242, 'say less': 307242, 'stuck': 307242, 'thomas the tank engine': 481783, 'wooden railway': 481783, 'ERTL': 481783, 'collectors': 481783, 'collectable': 481783, 'railroad': 481783, 'miniature': 481783, 'toy': 651127, 'hot wheels': 481783, 'mattel': 481783, 'ramp': 481783, 'loop': 481783, 'skater': 481783, 'roller coaster': 481783, 'frontflip': 481783, 'locomotive': 481783, 'cart': 481783, 'thomas and friends': 481783, 'hit entertainment': 481783, 'sodor': 481783, 'lady': 481783, 'magic': 599290, 'polar express': 481783, 'lionel': 481783, 'air': 1218499, 'backyard skateboarding': 481783, 'hot rod': 481783, 'evel knievel': 481783, 'dangerous': 481783, 'will it': 497085, 'rhett link will it': 497085, 'gmm will it': 497085, 'will it christmas tree': 497085, 'good mythical morning will it': 497085, 'rhett link will it christmas tree': 497085, 'gmm will it christmas tree': 497085, 'sithmas': 497085, 'menorah': 497085, 'chinese takeout': 497085, 'rhett link christmas': 497085, 'gmm christmas': 497085, 'gmm challenges': 497085, 'will it taco': 497085, 'guy learns': 539605, 'break glass': 539605, 'la beast': 539605, 'wine glass': 539605, 'eagles.seattle': 1270477, 'russell': 1270477, 'seahawks win': 1270477, 'sp:dt=2017-12-03T20:30:00-05:00': 1270477, 'sp:ti:home=Sea': 1270477, 'One More Light': 523885, 'Crawling': 523885, 'Chester Bennington': 523885, 'Mike Shinoda': 523885, 'Joe Hahn': 523885, 'Phoenix': 523885, 'Dave Farrell': 523885, 'Brad Delson': 523885, 'Rob Bourdon': 523885, 'Live Album': 523885, 'Aidy Bryant': 3707365, 'sick dog': 336662, 'dog loves bath': 336662, 'special needs dog': 336662, 'special dog': 336662, 'mrcornelius': 336662, 'funny dog': 336662, 'funny dog videos': 336662, 'good dog': 336662, 'grumpy dog': 336662, 'grumpy dog videos': 336662, 'Alessandra Ambrosio': 211403, 'Fashion Show': 211403, 'Wax Sculpture': 211403, 'Madame Tussauds': 211403, 'Shanghai': 211403, 'fair workweek': 998228, 'minimum wage': 998228, 'service': 998228, 'retail': 998228, 'organized labor': 998228, 'wages': 998228, 'hours': 998228, 'scheduling': 998228, 'center for popular democracy': 998228, 'low-wage': 998228, 'low-income': 998228, 'carrie gleason': 998228, 'david autor': 998228, 'Hollywood News': 56736, 'Fame': 56736, 'Entertainment News': 56736, 'tamika scott': 56736, 'tiny harris': 56736, 'xscape bet awards': 56736, 'xscape music': 56736, 'xscape performance': 56736, 'xscape fall': 56736, 'tamika scott fall': 56736, 'tamika scott interview': 56736, 'xscape': 56736, 'xscape live': 56736, 'celeb falls': 56736, 'celebrity wardrobe malfuntion': 56736, 'tmz 2017': 56736, 'raw video': 56736, 'Grace VanderWaal': 479732, 'So Much More Than This': 479732, 'Syco Music/Columbia Records': 479732, 'anxiety disorder': 160340, 'nervous': 160340, 'social anxiety': 160340, 'what is anxiety': 160340, 'do i have anxiety': 160340, 'fear': 160340, 'panic': 250965, 'panic attack': 160340, 'anxiety symptoms': 160340, 'fear center': 160340, 'amygdala': 160340, 'fight-or-flight response': 160340, 'personality': 160340, 'gotham': 153131, 'nottinghamshire': 153131, 'washington irving': 153131, 'salmagundi': 153131, 'bill finger': 153131, 'merry tales of the mad-men of gotam': 153131, 'gotam': 153131, 'gottam': 153131, 'king john': 153131, 'mad men of gotham': 153131, 'wise men of gotham': 153131, 'fake life hacks': 239226, 'life hack myth': 239226, 'life hack busted': 239226, 'life hack false': 239226, 'wrong life hacks': 239226, 'life hack scam': 239226, 'fake life hack': 239226, 'life whacks': 239226, 'life wacks': 239226, 'jellyfish sting': 239226, 'toothpaste zit': 239226, 'busted life tips': 239226, 'advice column': 239226, 'terrible ideas': 239226, 'tiffany': 174569, 'haddish': 300992, 'tiffany haddish': 174569, 'barbar streisand': 174569, 'girls strip': 174569, 'oprah': 216947, 'arizona state': 19333, 'arizona state football': 19333, 'pac-12': 19333, 'Christmas Time Is Here (with T': 27459, 'tony bennett': 27459, 'lasagna roses': 914633, 'hellthyjunkfood': 914633, 'edible roses': 914633, 'food roses': 914633, 'affordable': 847508, 'first impressions': 8957, 'everyday look': 8957, 'how to apply': 59495, 'makeup challenge': 395048, 'youtube makeup': 8957, 'winter makeup tutorial': 8957, 'how to do makeup': 8957, 'Money': 616093, 'wisconsin badgers': 71072, 'nick bosa': 71072, 'jt barrett': 71072, 'CFP': 47614, 'College Football Playoff': 47614, 'State': 47614, 'OSU': 47614, 'The Ohio State': 47614, 'Ohio State CFP': 47614, 'Ohio State Playoff': 47614, 'OSU Snub': 47614, 'Ohio State Snub': 47614, 'college football 2017': 47614, 'college football playoffs': 47614, 'college football playoff 2017': 47614, 'alabama football': 47614, 'alabama crimson tide': 47614, 'college football playoff rankings': 47614, 'buying stuff from wish': 2706676, 'wish haul': 2706676, 'fashion haul': 2706676, 'nail mail': 2706676, 'nail haul': 2706676, 'testing stuff from wish': 2706676, 'testing nail products': 2706676, 'wish nails': 2706676, 'nail wish': 2706676, 'peel-off nails': 2706676, 'nail painting': 2706676, 'nail polish': 2706676, 'nail polish haul': 2706676, 'nail vinyls': 2706676, 'Shay Mitchell': 3192347, 'Shay': 3192347, 'Mitchell': 3192347, 'Shay Mitchell YouTube': 3192347, 'YouTube Shay Mitchell': 3192347, 'Shannon Mitchell': 3192347, 'ShayM': 3192347, 'Shay Mitch': 3192347, 'Pretty Little Liars': 3192347, 'PLL': 3192347, 'Emily Fields': 3192347, 'Amore and Vita': 3192347, 'Audition': 3192347, 'Audition Tape': 3192347, 'pontiac line': 4631, 'old pontiac line commercial': 4631, 'old pontiac commercial': 4631, 'old car commercials': 4631, 'old car commercial': 4631, 'boldly': 2086165, 'permanent': 1316854, 'beauty and style': 1316854, 'lip color': 1316854, 'skin treatment': 1316854, 'tattooing': 1316854, 'face tattoo': 1316854, 'lip tattoo': 1316854, 'lip liner': 1316854, 'lip gloss': 1336071, 'permanent lipstick': 1316854, 'permanent makeup': 1316854, 'tattooed makeup': 1316854, 'gigi': 759844, 'gigigorgeous': 759844, 'gregory': 759844, 'gregorygorgeous': 759844, 'guru': 759844, 'surrogate': 759844, 'scandal': 4045662, 'article': 759844, 'rumor': 759844, 'nats getty': 759844, 'nigi': 759844, 'lesbian couple': 772954, 'gossip blogs': 759844, 'fake story': 759844, 'perform': 35820, 'grime': 35820, 'stefflon don': 35820, 'stormzy': 35820, 'almazan kitchen': 141225, 'campfire': 141225, 'meditation': 141225, 'asmr': 141225, 'best recipe': 141225, 'best cooking': 141225, 'recept': 141225, 'рецепт': 141225, 'serbian food': 141225, 'AlmazanKitchen': 141225, 'Almazan knife': 141225, 'The Godlike Steak': 141225, "YOU WON'T BELIEVE!": 141225, 'ULTIMATE STEAK': 141225, 'stone fried in the forest': 141225, 'how to make best steak': 141225, '1000$ steak': 141225, '1000$ vs 2$': 141225, 'medium rare': 141225, 'rare steak': 141225, 'raw meat': 141225, 'ramsey': 141225, 'barbeque': 310263, 't bone': 141225, '4K cooking': 141225, '4k resolution': 141225, 'best steak ever': 141225, 'Off My Feet (Official Audio)': 102357, 'Pia Mia': 102357, 'Pia Mia Recordings': 102357, 'None': 102357, 'Pia Mia Perez': 102357, 'spider-man': 1758327, 'spider-man Homecoming': 1758327, 'homecoming': 1758327, 'spiderman homecoming': 1758327, 'chicken tikka masala': 360880, 'britain': 894670, 'tea': 1312020, 'the santa clause': 495312, 'the santa clause 2': 495312, 'christmas movie': 495312, 'tim allen': 495312, 'dude perfect': 8643326, 'dude perfect stereotypes': 8643326, 'dude perfect water bottle flip': 8643326, 'bottle flip': 8643326, 'water bottle flip': 8643326, 'dude perfect bottle flip': 8643326, 'dude perfect basketball': 8643326, 'dp': 8643326, 'dude perfect world record': 8643326, 'edition': 8643326, 'nerf': 8643326, 'trick shots': 8643326, 'trick shot': 8643326, 'ping pong': 8643326, 'bowling': 8643326, 'bubble wrap': 8643326, 'spinner': 8643326, 'spinners': 8643326, 'fidget spinners': 8643326, 'dude': 8643326, 'golf': 8643326, 'all': 8643326, 'course': 8643326, 'baseball': 8643326, 'team coby': 8643326, 'panda': 8643326, 'pool': 8643326, 'putting': 8643326, 'all sports golf battle': 8643326, 'callep': 239087, 'cincinnati': 993435, 'bengals': 993435, 'shazier': 993435, 'injury': 1179613, 'big ben': 993435, 'bell': 993435, 'dalton': 993435, 'sp:dt=2017-12-04T20:30:00-05:00': 993435, 'sp:ti:home=Cin': 993435, 'sp:ti:away=Pit': 993435, 'i tonya': 1365999, 'tonya harding': 1564435, 'figure skating': 1365999, 'chris pratt': 2880514, 'olympics': 9057901, 'LIP PLUMPER': 456286, 'Lip Pumping': 456286, 'How to Plump Your Lips': 456286, 'Kylie Jenner Lips': 456286, 'Kardashian Lips': 456286, 'The Ultimate Plump It!': 456286, 'Plump It!': 456286, 'Broke': 112186, 'Lecrae': 112186, 'Reach Records/Columbia': 112186, 'mlg highlights': 420943, 'mlg': 420943, 'stephen curry': 420943, 'stephen curry injury': 420943, 'stephen curry ankle injury': 420943, 'stephen': 2379987, 'warriors vs pelicans': 420943, 'pelicans': 420943, 'warriors': 420943, 'bryce dallas howard': 132458, 'jurassic world': 1514515, 'fallen kingdom': 1514515, 'jurassic park': 1514515, 'tanya burr': 141529, 'fashion awards': 141529, 'tanya': 141529, 'what i eat in a day': 141529, 'dress': 2279359, 'princess': 163482, 'natural': 149729, 'democrats': 478050, 'democratic party': 36945, 'the voice live top 10 performa': 300142, 'keisha renee': 300142, 'all by myself': 300142, 'working out': 1140251, 'getting fit': 84442, 'fit': 84442, 'gym': 90777, 'how to get fit': 84442, 'running on freeway': 84442, 'best workout': 84442, 'how to get a six pack': 84442, 'how to get abs': 84442, 'abs': 84442, '6 pack': 84442, 'ripped abs': 84442, 'wobbly arms': 84442, 'karina garcia': 962042, 'slime': 962042, 'diy slime': 962042, 'christmas slime': 962042, 'holiday slime': 962042, 'christmas tree slime': 962042, 'butter slime': 962042, 'crunchy slime': 962042, 'clear slime': 962042, 'how to slime': 962042, 'candy cane slime': 962042, 'gingerbread slime': 962042, 'winter slime': 962042, 'Bray Wyatt': 1241552, 'sp:dt=2017-12-04T20:00:00-04:00': 1241552, 'sp:ev=wwe-raw': 1241552, 'sp:ath=wwe-maha': 1241552, 'sp:ath=wwe-brwy': 1241552, 'hardy': 1241552, 'bray': 1241552, 'wyatt': 1241552, 'wwe raw': 1241552, 'broken matt hardy': 1241552, 'wwe monday night raw': 1241552, 'delete': 1241552, 'wwe raw results': 1241552, 'hardy boyz': 1241552, 'matt hardy delete': 1241552, 'the hardy boyz': 1241552, 'awoken': 1241552, 'woken': 1241552, 'EXO': 1840613, '엑소': 1840613, 'エクソ': 1840613, 'COUNTDOWN': 1840613, 'Electric Kiss': 1840613, 'SUHO': 1840613, 'CHANYEOL': 1840613, 'D.O.': 1840613, 'BAEKHYUN': 1840613, 'KAI': 1840613, 'XIUMIN': 1840613, 'CHEN': 1840613, 'SEHUN': 1840613, '수호': 1840613, '찬열': 1840613, '디오': 1840613, '백현': 1840613, '카이': 1840613, '시우민': 1840613, '첸': 1840613, '세훈': 1840613, 'Bergen': 539308, 'Homecoming': 539308, 'Unmasked': 539308, 'Tove Styrke': 539308, 'Jonas Barsten': 539308, 'Veronika Heilund': 539308, 'one year': 539308, '1 year': 539308, 'live performance': 539308, 'anniversary': 539308, 'Sing me to sleep': 539308, 'Allan': 539308, 'Valker': 539308, 'Kristen Wiig': 36326, 'Treated': 110339, 'Better': 110339, 'Blonde': 110339, 'Season of the Witch': 110339, 'Vampire Acadmey': 110339, 'Wreckers': 110339, 'Going Postal': 110339, "The Girl in The Spider's Web": 110339, 'TWICE Heart Shaker': 19748577, 'TWICE 하트셰이커': 19748577, '트와이스 Heart Shaker': 19748577, '트와이스 하트셰이커': 19748577, 'Heart Shaker': 19748577, '하트셰이커': 19748577, 'TWICE teaser': 1552618, 'TWICE 티저': 1552618, '트와이스 teaser': 1552618, '트와이스 티저': 1552618, 'Heart Shaker teaser': 1552618, 'Heart Shaker 티저': 1552618, '하트셰이커 teaser': 1552618, '하트셰이커 티저': 1552618, 'TWICE Merry Happy': 19748577, '트와이스 Merry Happy': 19748577, '트와이스 메리 해피': 19748577, 'TWICE 리패키지': 1552618, '트와이스 리패키지': 1552618, 'TWICE repackage': 1552618, 'TWICE MV': 19748577, 'TWICE Music Video': 19748577, 'TWICE 뮤직비디오': 19748577, 'TWICE 뮤비': 19748577, '트와이스 MV': 19748577, '트와이스 뮤직비디오': 19748577, '트와이스 뮤비': 19748577, 'Heart Shaker MV': 19748577, 'Heart Shaker Music Video': 19748577, 'Heart Shaker 뮤직비디오': 19748577, '하트셰이커 MV': 19748577, '하트셰이커 뮤직비디오': 19748577, '하트셰이커 뮤비': 19748577, 'JYP': 1552618, 'Roger': 241569, 'Horton': 241569, 'Honest Ad': 241569, 'Honest Ads': 241569, 'Honest': 241569, 'commercial Parody': 241569, 'commercial parodies': 241569, 'commercial spoof': 241569, 'commercial spoofs': 241569, 'I’m roger by the way': 241569, 'Hi I’m roger': 241569, 'tampon myth': 241569, 'periods': 628682, 'tampon commercials': 241569, 'period commercials': 241569, 'tampax': 241569, 'kotex': 241569, 'moon cup': 241569, 'period kit': 241569, 'tampon vs pads': 241569, 'kissfmuk': 14086, 'kiss fm': 14086, 'kiss fm uk': 14086, 'kissfm': 14086, 'kiss': 14086, 'kiss uk': 14086, 'playlist': 14086, 'top 40': 14086, 'mix': 14086, 'selena gomez cover': 14086, 'selena gomez interview': 14086, 'the weeknd': 14086, 'sabrina': 56464, 'sabrina the teenage witch': 14086, 'IISuperwomanII': 874275, 'celebrity gossip': 1040770, 'talk about celebs': 874275, 'Celebrity talk': 874275, 'celebrity people': 874275, 'how people talk gossip': 874275, 'celebs gossip': 874275, '12collabsofchristmas': 874275, '12collabs': 874275, 'christmas collabs': 874275, 'lilly christmas collabs': 874275, 'celeb collabs': 874275, 'how people talk about celebrity gossip': 874275, 'youtube superwoman': 3699200, 'bears ears': 28378, 'national monument': 28378, 'native americans': 28378, 'utah': 28378, 'southern utah': 28378, 'John Green': 35892, 'Hank Green': 35892, 'Crash Course': 35892, 'crashcourse': 35892, 'sociology': 35892, 'social sciences': 35892, 'age stratification': 35892, 'demographics': 35892, 'us census': 35892, 'aging': 35892, 'senescence': 35892, 'kayleymelissa': 24941, 'kayley melissa': 24941, 'letsmakeitup1': 24941, 'kaley melissa': 24941, 'kaleymelissa': 24941, 'kayley': 24941, 'melissa': 308776, 'kayleymelisa': 24941, 'accessories': 24941, 'hair accessories': 24941, 'best of': 24941, 'party hair': 24941, 'holiday hair accessories': 24941, 'headband': 24941, 'elastic': 24941, 'barette': 24941, 'uncomfortable': 117218, 'families': 117218, 'mom and dad': 117218, 'disscusion': 117218, 'teenagers': 117218, 'topic': 117218, 'out of love': 117218, 'byeeee': 117218, 'magnolia pictures': 8023, 'dakota fanning': 8023, 'toni collette': 8023, 'alice eve': 8023, 'please stand by': 8023, 'ben lewin': 8023, 'Michael Golamco': 8023, 'writing': 8023, 'screenwriting': 8023, 'patton oswalt': 8023, 'nat geo live': 69519, 'national geographic live': 69519, 'national geographic': 210144, 'nat geo': 210144, 'natgeo': 210144, 'photographers': 69519, 'scientists': 69519, 'morgan freeman': 69519, 'morgan freeman breakthrough prize': 69519, 'morgan freeman nat geo': 69519, 'the story of us': 69519, 'breakthrough prize': 69519, 'Wiz Khalifa': 69519, 'mila kunis': 69519, 'ashton kutcher': 69519, 'kerry washington': 69519, 'ron howard': 69519, 'nana ou-yang': 69519, 'john urschel': 69519, 'miss usa kara mccullough': 69519, 'mark zuckerberg': 69519, 'sergey bring': 69519, 'yuri milner': 69519, 'julia milner': 69519, 'priscilla chan': 69519, 'anne qojcicki': 69519, 'nat geo breakthrough': 69519, 'food skills': 92000, 'enchiladas': 92000, 'mexican food': 92000, 'how to make enchiladas': 92000, 'real mexican food': 92000, "el atoradero's": 92000, 'tortillas': 92000, 'denisse lina chavez': 92000, 'accidents': 8200, 'affairs': 8200, 'ap': 8200, 'associated': 8200, 'barbara': 8200, 'commentary': 8200, 'disasters': 8200, 'evacuate': 58388, 'evacuations': 8200, 'fires': 10985, 'forces': 8200, 'general': 8200, 'headlines': 8200, 'local': 8200, 'national': 12836, 'north': 8200, 'press': 8200, 'regional': 8200, 'reports': 8200, 'states': 8200, 'thousands': 8200, 'united': 8200, 'video-us': 8200, 'wildfires': 10985, 'silverdome': 63899, 'silverdome fail': 63899, 'silverdome implode': 63899, 'silverdome implosion': 63899, 'silverdome destroyed': 63899, 'detroit lions silverdome': 63899, 'lions silverdome': 63899, 'pontiac silverdome': 63899, 'detroit silverdome': 63899, 'lions': 63899, 'detroit lions': 63899, 'stadium implosion': 63899, 'souls flyers': 22178, 'Jack and Dean': 130633, 'OMFGItsJackAndDean': 130633, 'Jack Howard': 130633, 'Dean Dobbs': 130633, 'dodie': 367090, 'episode': 969184, 'call center': 130633, 'call centre': 130633, 'telephone operator': 130633, 'jackanddean': 130633, 'Men’s Fashion Tips & Winter 2017 Style Guide': 48365, 'Doctor Mike': 48365, 'Dr. Mike': 48365, 'Instagram doctor': 48365, 'doctor.mike': 48365, 'Mikhail Varshavski': 48365, 'physician': 48365, 'medicine': 724878, 'men’s fashion': 48365, 'men’s fashion tips': 48365, 'style guide': 48365, 'winter 2017 style guide': 48365, 'men’s fashion lookbook': 48365, 'men’s lookbook': 48365, 'winter lookbook': 48365, 'winter style guide': 48365, 'winter 2017': 48365, 'mens fashion': 48365, 'Grand': 222557, 'Neil Young': 72321, 'Reprise Records': 72321, 'Promise of the Real': 72321, 'The Vistor': 72321, 'Already Great': 72321, 'lukas graham': 72321, 'wealthy': 52360, 'beverly hills': 52360, 'Vanderpump Rules': 52360, 'staff': 52360, 'Lisa Vanderpump': 52360, 'Ariana Madix': 52360, 'Jax Taylor': 52360, 'Katie Maloney': 52360, 'Kristen Doute': 52360, 'Scheana Shay': 52360, 'James Kennedy': 52360, 'Tom Sandoval': 52360, 'beverly hills restaurants': 52360, 'real housewives of beverly hills': 52360, 'RHOBH': 52360, 'Tom Schwartz': 52360, 'Sur Restaurant': 52360, 'Sur-vers': 52360, 'PL-nJHoTivBRQOrCNmPsGep35Ihxa-dU8-': 52360, 'Confronts': 52360, 'Talking Crap': 52360, 'USA': 1040220, 'Burger': 35235, 'King': 35235, 'Drive-thru': 35235, 'outrageously': 35235, 'employee': 35235, 'manager': 35235, 'Ohio': 35235, 'Rewind': 24828864, 'Rewind 2017': 24782158, 'youtube rewind 2017': 28570394, '#YouTubeRewind': 24782158, 'Rewind 2016': 24782158, 'Dan and Phil': 24828864, 'Grace Helbig': 24782158, 'HolaSoyGerman': 24782158, 'Lilly Singh': 24782158, 'Markiplier': 24782158, 'Swoozie': 24782158, 'Rhett Link': 24782158, 'Liza Koshy': 24962297, 'Lele Pons': 24782158, 'Jake Paul': 24782158, 'Logan Paul': 24828864, 'KSI': 24828864, 'Joey Graceffa': 24782158, 'Poppy': 24782158, 'Niana Guerrero': 24782158, 'Fidget Spinners': 24782158, 'Slime': 24782158, 'Backpack Kid': 24782158, 'April the Giraffe': 24782158, '#Rewind': 24782158, 'Shape of you': 24782158, 'YouTubeRewind': 24782158, 'Humble': 24782158, 'Jurassic': 1382057, 'jurassic world 2': 1382057, 'jurassic world 2 trailer': 1382057, 'Jurassic world fallen kingdom trailer': 1382057, 'jurassic world fallen kingdom': 1382057, 'fallen kingdom trailer': 1382057, 'jurassic park 2': 1382057, 'dino': 1382057, 'dinosaur': 1382057, 'dinosaurs': 1382057, 't rex': 1382057, 'trex': 1382057, 'jurassic world film theory': 1382057, 'film theorists': 1382057, 'jurassic world matpat': 1382057, 'jurassic world theory': 1382057, 'jurassic park theory': 1382057, 'Pokémon': 660509, 'Pokémon GO': 660509, 'Niantic': 660509, 'Niantic Labs': 660509, 'Pikachu': 660509, 'The Pokémon Company': 660509, 'The Pokémon Company International': 660509, 'Impression': 1698230, 'Ghostbusters': 1698230, 'Holtzman': 1698230, 'Finding Dory': 1698230, 'Office Christmas Party': 1698230, 'bm4main': 439207, 'house burned down': 1098246, 'house fire': 1098246, 'thomasfire': 1098246, 'thomas fire': 1397336, 'burned down': 1098246, 'help me': 831235, 'elmo': 42378, 'dolly the sheep': 42378, 'dolly parton': 42378, 'flintstones': 42378, 'teenage witch': 42378, 'ryan reynolds': 42378, 'judge judy': 42378, "blue's clues": 42378, 'space jam': 42378, 'jerry maguire': 42378, 'deep blue': 42378, 'ibm': 42378, "oprah's book club": 42378, 'the spice girls': 42378, 'tomb raider': 42378, 'asoiaf': 42378, 'lara croft': 42378, 'hotmail': 42378, 'tube man': 42378, 'Singer': 189204, 'Plead the Fifth': 99577, 'pal': 99577, 'singer Taylor Swift': 99577, 'feud': 99577, 'team Taylor or team Kim': 99577, 'Camilla': 99577, 'Sam Smith plead the fifth': 99577, 'Sam Smith wwhl': 189204, 'Sam Smith watch what happens live': 189204, 'plead the fifth wwhl': 99577, 'Kim Kardashian wwhl': 99577, 'Kim Kardashian and Taylor Swift': 99577, 'The Handsy Man': 187987, 'sexism': 187987, 'misogyny': 187987, 'feminist': 187987, 'respect': 187987, 'runaway bride': 570995, 'runaway': 570995, 'bride': 570995, 'Runaway Bride | Hannah Stocking': 570995, 'president donald trump': 96255, 'grace helbig': 196778, 'the grinch': 190727, 'humming challenge': 190727, 'chanukkah': 190727, 'christmas carols': 190727, 'carol of the bells': 190727, 'jingle bells': 829638, 'jingle bell rock': 190727, 'mean girls the musical': 190727, 'how the grinch stole christmas': 190727, 'cindy lou who': 190727, 'frosty the snowman': 190727, 'cafe': 253907, 'motorized': 253907, 'furniture': 1092458, 'drive': 253907, 'table': 1673254, 'chair': 253907, 'sofa': 253907, 'loveseat': 253907, 'ottoman': 253907, 'hidden': 253907, 'customers': 253907, 'waiter': 253907, 'hostess': 253907, 'crash': 496105, 'crashing': 253907, 'newyorkcity': 253907, 'grandtour': 253907, 'mobility': 362935, 'shop': 834703, 'Greenwich': 253907, 'village': 253907, 'Ilana Glazer': 30739, 'Share': 30739, 'UCB Memories': 30739, 'Broad City': 30739, 'season 4': 30739, 'improv comedy': 30739, 'supermarket': 30739, 'McDonalds': 30739, 'Amy Poehler': 30739, 'High School Talent Show': 30739, 'how to get stuff done': 163769, 'productive': 163769, 'productivity': 163769, 'multitasking': 163769, 'getting stuff done': 163769, 'stuff': 163769, 'done': 163769, 'self help': 163769, 'problem': 533790, 'geopolitics': 533790, 'issue': 533790, 'geo': 533790, 'borders': 533790, 'tibet': 533790, 'nepal': 533790, 'bhutan': 533790, 'bangladesh': 533790, 'east pakistan': 533790, 'british empire': 533790, 'partition': 533790, 'international relations': 533790, 'international': 533790, 'CBS 2 News Evening': 31711, 'Thomas Fire': 31711, 'Ventura County': 31711, 'structures': 31711, 'David Goldstein': 31711, 'Randy Paige': 31711, 'Jeff Nguyen': 31711, 'star trek director': 41433, 'star trek sequel': 41433, 'star trek beyond': 41433, 'star trek discovery': 41433, 'lonely': 179854, 'loneliness': 179854, 'why am i lonely': 179854, 'how not to be lonely': 179854, "it's hard being smart": 179854, "c'est dur d'être intelligent": 179854, 'es ist schwer': 179854, 'schlau zu sein': 179854, '很聪明很难': 179854, 'यह मुश्किल हो रहा है स्मार्ट': 179854, 'es difícil ser inteligente': 179854, 'é difícil ser inteligente': 179854, 'meetup': 179854, 'intelligent': 179854, 'cleverness': 179854, 'smart': 179854, 'high iq': 179854, 'iq': 179854, 'intelligent people': 179854, 'intellect': 179854, 'arrogant': 179854, 'qunetin tarantino': 52809, 'bryan singer': 52809, 'rian johnson': 562521, 'zachary levi': 52809, 'shazam': 54418, 'a boy named shel': 52809, 'firefighters': 65463, 'wildfire': 332474, 'Thomas': 50188, 'Ventura': 50188, 'California': 67643, '25': 50188, '000': 50188, 'acres': 50188, 'Mahmoud Abbas': 44955, 'Palestine': 3509188, 'Palestine National Authority': 44955, 'Jerusalem': 44955, 'Benjamin Netanyahu': 44955, 'Israel': 3509188, 'Middle East': 44955, 'holy': 44955, 'Syracuse University': 12994, 'SU': 12994, 'Syracuse': 12994, 'Cuse': 12994, 'Cuse TV': 12994, 'Cuse.com': 12994, 'ACC': 12994, "men's basketball": 12994, 'bradley beal': 36922, 'washington wizards': 36922, 'career high': 36922, 'planet earth': 104591, 'planet earth 2': 104591, 'planet earth II': 104591, 'sir david attenborough': 104591, 'pelican': 104591, 'pelican invades tent in the galapagos': 104591, 'Galápagos islands': 104591, 'blue planet II behind the scenes': 104591, 'wild': 104591, 'animal film': 104591, 'bbc worldwide': 104591, 'lifetime': 37447, 'lifetime shows': 37447, 'lifetime tv': 37447, 'lifetime channel': 37447, 'mylifetime': 37447, 'glam masters lifetime': 37447, 'lifetime new series': 37447, 'lifetime new beauty competition': 37447, 'beauty queen': 37447, 'make up competition': 37447, 'makeup beauty competition': 37447, 'beauty contest': 37447, 'beauty gurus': 37447, 'glam masters': 37447, 'makeup diy': 37447, 'lifetime new show': 37447, 'lifetime glam masters': 37447, 'glam master': 37447, 'kim kardashian glam masters': 37447, 'kim kardashian makeup': 37447, 'outback steakhouse': 9387, 'outback bowl': 9387, 'victory': 9387, 'mascots': 9387, 'pep talk': 9387, 'college cootball': 9387, 'sec': 9387, 'big 10': 9387, "bloomin' onion": 9387, 'coconut shrimp': 9387, 'Carrie Fisher': 608054, 'E! Live from the Red Carpet': 7135, 'Celebrity Gossip': 7135, 'Oscars': 7135, 'Grammys': 7135, 'Golden Globes': 7135, 'Emmys': 7135, 'elf': 26422, 'elfontheshelf': 26422, 'ontheshelf': 26422, 'buddytheelf': 26422, 'elfonshelf': 26422, 'dadvent': 26422, 'december': 26422, 'dec': 26422, 'merrychristmas': 26422, 'merry': 26422, '12daysofchristmas': 26422, 'pub': 26422, 'littlelegend': 26422, 'ginerbreadhouse': 26422, 'elfshead': 26422, 'elfpub': 26422, 'doginapub': 26422, 'christmasdecorations': 26422, 'mum': 26422, 'mumanddad': 26422, 'mumdad': 26422, 'loveit': 26422, 'whatisthat': 26422, 'sausageroll': 26422, 'shewillloveit': 26422, 'shewillhateit': 26422, 'yesmate': 26422, 'yes': 26422, 'mate': 26422, 'lad': 26422, 'ladbaby': 26422, 'laddad': 26422, 'sorrynotsorry': 26422, 'magicofchristmas': 26422, 'Saoirse': 8633, 'Ronan': 8633, 'Bird': 8633, 'mashup': 3888, 'tv spot': 3888, 'clark zhu': 3888, 'movie trailer mashup': 3888, '2017 movies': 27075, '2017 films': 3888, 'Fox Friends': 1217, 'After Show': 1217, 'On Air': 1217, 'Primary Opinion': 1217, 'Fox News': 1217, 'MickMake': 3237, 'embedded': 3237, 'explanation': 3237, 'analyse': 3237, 'maker board': 3237, 'programming': 3237, 'MCU': 3237, 'do-it-yourself': 3237, 'EasyEDA': 3237, 'How to make a PCB': 3237, 'hot food': 1458368, "rubik's cube": 1458368, '1-800 logic': 1458368, 'rabbit saved': 299090, 'la conchita': 299090, 'animal rescue': 299090, 'E.T.': 628082, 'the extra terrestrial': 628082, 'spielberg': 628082, 'universal basic income': 1006519, 'ubi': 1006519, 'society': 1006519, 'wellfare': 1006519, "don't talk": 1192992, 'talking': 1250955, 'movie theatre': 1192992, 'watching': 1192992, 'Mad Lib Theater': 595820, 'Trainwreck': 595820, 'The marine': 595820, '12 Rounds': 595820, 'WWE Raw': 595820, 'stay golden': 595820, 'rob roy': 595820, 'KUKA': 159194, 'lbr iiwa': 159194, 'robot arm': 159194, 'industrial robot arm': 159194, 'holiday cards': 159194, 'patreon': 159194, 'christmas cards': 159194, 'how to make nice christmas cards': 159194, 'campo santo': 35817, 'firewatch': 35817, 'video games': 525184, 'egypt': 35817, 'videogames': 35817, 'adventure game': 35817, 'birth': 437979, 'Harriet Washington': 437979, 'Thomas Jefferson': 437979, 'James Marion Sims': 437979, 'WEB DuBois': 437979, 'black women': 774525, 'black history': 437979, 'medical racism': 437979, 'Tuskegee': 437979, 'Jim Crow': 437979, 'sterilization': 437979, 'Fannie Lou Hamer': 437979, 'Margaret Sanger': 437979, 'medical system': 437979, 'Anarcha': 437979, 'slavery': 437979, 'slaves': 437979, 'experimentation': 437979, 'institutional racism': 437979, 'gynecology': 437979, 'speculum': 437979, 'Southern Poverty Law Center': 437979, 'Mississippi appendectomy': 437979, 'Norplant': 437979, 'David Duke': 437979, 'ProPublica': 437979, 'racial disparities': 437979, 'infant': 437979, 'invisible box challenge': 1067226, 'chris redd': 160142, 'melissa villasenor': 160142, 'kyle mooney': 160142, 'beck bennett': 160142, 'alex moffat': 160142, 'Melissa Villaseñor': 160142, 'box challenge': 329669, 'step challenge': 160142, 'invisible step challenge': 160142, 'real friends': 7858585, 'camilizers': 7858585, 'never be the same': 7858585, 'all these years': 7858585, 'she loves control': 7858585, 'consequences': 7858585, "something's gotta give": 7858585, 'in the dark': 7858585, 'into it': 7858585, 'havana feat young thug': 1796416, 'Real Friends': 1796416, "women's health": 387113, 'menstrual cycle': 387113, 'menstrual': 387113, 'cycle': 387113, 'pms': 387113, 'uterus': 387113, 'period blood': 387113, 'period cycle': 387113, 'girls period': 387113, 'menstrual hygiene': 387113, 'irregular periods': 387113, 'time of the month': 387113, 'irregular menstruation': 387113, 'guess': 387113, 'quiz': 402080, 'buzzfeed kelsey': 387113, 'kelsey': 387113, 'cramps': 387113, 'puberty': 387113, 'building our dream home': 316768, 'building dream home': 316768, 'house shopping': 316768, 'dream home': 316768, 'mansion': 316768, 'vlogmas day 6': 316768, 'dk4l pranks': 316768, 'dk4l challenges': 316768, 'dk4l vlogs': 316768, "de'arra and ken": 316768, 'buying our house': 316768, "we're building a house": 316768, 'gordon': 1423146, 'chef Ramsay': 1423146, 'it’s raw': 1423146, 'its raw': 1423146, 'shrimp': 1423146, 'raw chicken': 1423146, 'Nino': 1423146, 'kitchen nightmares': 1423146, 'kitchen nightmares us': 1423146, 'Allison': 106333, 'Janney': 106333, 'Allison Janney': 106333, 'neil': 385734, 'patrick': 385734, 'harris': 566411, 'met': 385734, 'neil patrick harris': 385734, 'drunk donald trump': 385734, 'santa claus': 782041, 'how i met your mother': 385734, 'a series of unfortunate events': 385734, 'gingerbread waffle recipe': 38995, 'homemade waffles': 38995, 'how to make waffles': 38995, 'how to make gingerbread waffles': 38995, 'easy waffles': 38995, 'easy gingerbread waffles': 38995, 'how to cook waffles': 38995, 'how to make waffles at home': 38995, 'waffles from scratch': 38995, 'how to bake waffles': 38995, 'gingerbread recipes': 38995, 'waffle recipes': 38995, 'easy waffle': 38995, 'cooking show': 38995, 'Lily James': 83530, 'Admits': 83530, 'Playing': 83530, 'Young': 135443, 'Meryl Streep': 83530, 'Intimidating': 83530, 'Baby Driver': 83530, 'Cinderella': 83530, 'Pride and Prejudice and Zombies': 83530, 'Wrath of the Titans': 83530, 'Darkest Hour': 83530, 'Downton Abbey': 83530, 'Mama Mia': 83530, 'tariq nasheed': 90786, 'tommy sotomayor': 90786, 'problack': 90786, 'blm': 90786, 'black lives matter': 90786, 'sjw': 90786, 'social justice warrior': 90786, 'fox news': 90786, 'fake news': 90786, 'all lives matter': 90786, 'antifa': 90786, 'internet nutrality': 90786, 'corey holcomn': 90786, 'wille d': 90786, 'dr umar johnson': 90786, 'dl hughley': 90786, 'conscious community': 90786, 'its okay to be white': 90786, 'liberal': 140547, 'alex jones fake news cnn': 90786, 'professor griff': 90786, 'higher learning': 90786, 'michael rapaport': 90786, 'remy': 90786, 'david banner': 90786, 'corey holcomb': 90786, 'stephan a smith': 90786, 'keisha bottoms': 90786, 'damn album': 90786, 'E! Online': 6181, 'Keeping up with the Kardashians': 6181, 'scripted': 6181, 'events': 6181, 'reality TV': 23183, 'WAGS Atlanta': 6181, 'sufjan stevens': 198436, 'sufjan': 198436, 'you tube': 443919, 'youtube rewind': 3788236, 'you tube rewind': 443919, 'rewind 2017': 3788236, 'BTW': 443919, 'rewind BTS': 443919, 'muselk vlog': 443919, 'mrmuselk vlog': 443919, 'muselk overwatch': 443919, 'muselk tf2': 443919, 'rewind 2016': 443919, 'simplynailogical': 1376521, 'cristine': 1376521, 'rewind': 636501, 'youtube vlog': 636501, 'rewind vlog': 636501, 'new york vlog': 636501, 'simplyvloglogical': 636501, 'simplynailogical vlog': 636501, 'Christopher A. Wray': 32172, 'wray': 32172, 'christopher wray': 32172, 'christopher wray testimony': 32172, 'FBI Director Christopher A. Wray': 32172, 'FBI Director': 32172, 'Morning Joe': 141605, 'Joe Scarborough': 141605, 'Mika Brzezinski': 141605, 'Willie Geist': 141605, 'MSNBC': 191366, 'MSNBC news': 141605, 'MSNBC live': 141605, 'MSNBC TV': 141605, 'US news': 142692, 'politics news': 141605, 'political news': 141605, 'elections': 141605, 'time 2017 person of the year silence breakers': 141605, 'violence against women': 141605, 'person of the year': 220409, 'me too': 220409, '#metoo': 222226, 'time person of the year 2017': 220409, 'silence breakers': 166002, 'rose mcgowan': 141605, 'time person of the year': 244806, 'Pittsburgh Dad': 186178, 'Curt Wootton': 186178, 'Chris Preksta': 186178, 'Steelers': 186178, 'Penguins': 186178, 'Pens': 186178, 'Pirates': 186178, 'Kennywood': 186178, 'Pittsburgh': 186178, 'sitcom': 186178, 'Ryan Shazier': 186178, 'hit': 265853, 'Cincinnati Bengals': 186178, 'Ben Roethlisberger': 186178, "Le'veon Bell": 186178, 'Antonio Brown': 186178, 'JuJu Smith-Schuster': 186178, 'Vontaze Burfict': 186178, 'Andy Dalton': 186178, 'AJ Green': 186178, 'Monday Night Football': 186178, 'Patti LaBelle': 89627, 'goddaughter': 89627, 'Mariah Carey': 229959, 'legend': 89627, 'Luther Vandross': 89627, 'Mariah Carey new years eve': 89627, 'Patti LaBelle wwhl': 89627, 'Patti Labelle watch what happens live': 89627, 'mariah': 229959, 'Patti LaBelle and Sam Smith': 89627, 'watch what happens live after show': 89627, 'first take live': 657084, 'obviously': 233324, 'mvo': 233324, 'nba mvp': 233324, 'nba mvp season': 233324, 'lebron james mvp': 233324, 'mario cereal': 126489, 'super mario cereal': 126489, 'super mario odyssey': 126489, 'cheerleader': 520613, 'cat-offbeat': 357352, 'jessica kellgren-hayes': 13110, 'jessica kellgren fozard': 13110, 'jessica and claudia': 13110, 'depression support': 13110, 'jessie and claud': 13110, 'mental health support': 13110, 'inspiration': 33676, 'inspirational': 73182, 'christmas giveaway': 13110, 'advent calendar': 13110, 'video advent calendar': 13110, 'santa outfit': 13110, 'christmassy': 13110, 'samaritans': 13110, 'christmas blues': 13110, 'it gets better': 13110, 'mind': 13110, 'motivation': 40011, "CES 2018 Honda's New Robotics Concept": 20188, 'Honda': 20188, 'CES 2018': 20188, 'Consumer Electronics Show': 20188, 'Honda Innovation': 20188, 'Honda Robots': 20188, 'Honda Robotics': 20188, 'Honda 3E': 20188, 'Honda Concept': 20188, 'AI': 20188, 'Mobility': 20188, 'The CW': 138054, 'The CW Network': 138054, 'Magic': 61121, 'Thriller': 138054, 'Heaven': 61121, 'Hell': 61121, 'Series': 61121, 'CW': 61121, 'Riverdale': 956887, 'Archie Comics': 61121, 'Archie': 956887, 'Jason Blossom': 61121, 'Archie Andrews': 61121, 'Jughead Jones': 61121, 'Betty Cooper': 61121, 'Veronica Lodge': 61121, 'Cheryl Blossom': 61121, 'Mad World': 61121, 'Train': 84056, 'Railway': 84056, 'Railroad': 84056, 'Travel & Adventure': 84056, 'Market': 84056, 'Bangkok': 84056, 'Food market': 84056, 'Food & Drink': 84056, 'SCIENTISTS WANT TO BAN GLITTER!! The best day of my life!': 279447, 'global glitter ban': 279447, 'banning glitter': 279447, 'scientists ban glitter': 279447, 'comedy skit glitter': 279447, 'mykie glitter': 279447, 'biodegradeable glitter': 279447, 'noglitterlitter': 279447, 'glitter litter': 279447, 'glitter activist': 279447, 'zombae': 279447, 'zombaes': 279447, 'week': 57963, 'dolan twins': 57963, 'fan': 638759, 'jessie': 57963, 'paege': 57963, 'try not': 57963, 'undercover': 57963, 'collins key': 57963, 'giveaways': 57963, 'giveaway 2017': 57963, 'ethan dolan': 57963, 'grayson dolan': 57963, 'dolan': 57963, 'twins': 57963, 'free stuff': 57963, 'guilty party': 57963, 'miles mckenna': 57963, 'kian': 57963, 'colleen ballinger': 226899, 'colleen': 226899, 'psychosoprano': 226899, 'no lipstick': 226899, 'without lipstick': 226899, 'mirror': 1197099, 'what girls think': 226899, 'what': 727105, 'thinking': 226899, 'mirrors': 226899, 'ugly': 1389742, 'magnifying': 226899, 'zits': 226899, 'thoughts': 226899, '50 facts': 207573, 'turtles all the way down': 196881, 'the fault in our stars': 196881, 'paper towns': 196881, 'books': 781945, 'reading': 196881, 'movie studios': 196881, 'filmmaking': 219175, 'invisible box': 907084, 'invisible box video': 169527, 'box videos': 169527, 'invisible box trend': 169527, 'new viral trend': 169527, 'viral videos': 169527, 'planking': 169527, 'ice bucket challenge': 169527, 'mannequin challenge': 169527, 'cinnamon eating': 169527, 'invisible box prank': 169527, 'invisible box trick': 169527, 'viral news': 169527, 'guardian invisible box': 169527, 'invisible box step': 169527, 'invisible box youtube': 169527, 'the guardian': 2050348, 'granola': 29196, 'holiday cookies': 29196, 'christmas cookies': 29196, 'from the test kitchen': 29196, 'granola cluster': 29196, 'easy cookies': 29196, 'granola cookie': 29196, 'granola cookie recipe': 29196, 'granola recipe': 29196, 'how to make granola': 29196, 'best granola': 29196, 'crunchy granola': 29196, 'granola cluster cookies': 29196, 'granola cluster cookie recipe': 29196, 'granola bar': 29196, 'granola bar recipe': 29196, 'how to make granola bars': 29196, 'net neutrality': 2729675, 'net neutrality petition': 60221, 'net neutrality explained': 60221, 'net neutrality vote': 1045400, 'net neutrality repeal': 60221, 'net neutrality bill': 60221, 'net neutrality meaning': 60221, 'net neutrality definition': 60221, 'net neutrality trump': 60221, 'net neutrality john oliver': 60221, 'net neutrality 2017': 60221, 'net neutrality cgp grey': 60221, 'net neutrality debate': 60221, 'net neutrality fcc': 60221, 'net neutrality last week tonight': 60221, 'last week tonight': 60221, 'ajit pai': 1045400, 'fcc': 1744496, 'beme': 60221, 'bemenews': 60221, 'youtube casey neistat': 60221, 'casey neistat cnn': 60221, 'a. o. scott': 4378, 'Wesley Morris': 4378, 'critics': 4378, 'alissa ashley': 38432, 'alissa ashley makeup': 38432, 'hooded eye makeup': 38432, 'makeup for hooded eyes': 38432, 'melodysheep': 21953, 'kawaii': 33565, 'Marteen': 21437, 'We Cool': 21437, 'rnb': 21437, 'hiphop': 21437, 'teenager': 21437, 'kehlani': 21437, 'wecool': 21437, 'mac miller': 21437, 'Stunts': 163261, 'cheer': 163261, 'step': 163261, 'steps': 163261, 'Texas': 163261, 'Lumio': 58106, 'book lamp': 58106, 'holiday gift ideas': 58106, 'christmas in new york': 23869, 'youtube space new york': 23869, 'Failure': 41003, 'Bryan Singer': 41003, 'Fired': 41003, 'Fox': 41003, 'Movie news': 175116, 'briefing': 4636, 'noaa': 4636, 'nws': 4636, 'wfo': 4636, 'san': 4636, 'diego': 4636, 'NWS San Diego': 4636, 'snow': 1265451, 'fire weather': 4636, 'fire danger': 4636, 'wind': 4636, 'wet': 4636, 'santa ana': 4636, 'monsoon': 4636, 'floods': 4636, 'Grown-ish': 27402, 'Grown-ish Trailer': 27402, 'Grown-ish Official Trailer': 27402, 'Grown-ish Promo': 27402, 'Grown-ish Freeform': 27402, 'Grown-ish TV series': 27402, 'Freeform': 27402, 'Freeform series': 27402, 'Freeform tv series': 27402, 'Deon Cole': 27402, 'christmas gift': 1485647, 'gift wrapping': 1516202, 'wrap with me': 1485647, 'gift hacks': 1485647, 'life hacks you have to try': 1485647, 'life changing': 1485647, 'wrapping presents': 1485647, 'how to wrap a gift': 1485647, 'how to wrap': 1485647, 'how to wrap presents': 1485647, 'wrapping my christmas presents': 1485647, 'chirstmas hacks': 1485647, 'diagonal wrapping': 1485647, 'toilet paper tube': 1485647, 'gift diy': 1485647, 'wrapping diy': 1485647, 'gift wrapping tutorial': 1485647, 'gift wrapping ideas': 1485647, 'japan gift wrapping hack': 1485647, 'gift wrapping hack': 1485647, 'christmas life hacks': 1485647, 'Transgender': 1107, 'caste system': 1107, 'National Safety Council': 1967, 'Opioid misuse': 1967, 'NSC': 1967, 'Stop Everyday Killers': 1967, 'Opioids': 1967, 'Overdose': 1967, 'epidemic': 1967, 'opioid epidemic': 1967, 'prescription opioids': 1967, 'drug abuse': 1967, 'prevention': 1967, 'StopEverdayKillers': 1967, 'Public Safety': 1967, '22000': 1967, 'pill faces': 1967, 'pill carving': 1967, 'pills': 1967, 'prescription pills': 1967, 'drug addiction': 1967, 'presciption drugs': 1967, 'prescribed to death': 1967, 'prescribedtodeath': 1967, 'Warn Me Label': 1967, 'best carol burnett show bloopers': 3644, 'carol burnett': 3644, 'carol burnett show': 3644, 'carol burnett bloopers': 3644, 'carol burnett show bloopers': 3644, 'bloopers': 3644, 'WHTA': 2929, 'tyrese': 2929, 'Hot 107.9': 2929, 'HotSpotATL': 2929, 'Amy Sedaris': 4232, 'Justin Theroux': 4232, 'Astronaut Relationship Jokes': 4232, 'ft. Justin Theroux': 4232, 'Why did the astronaut leave his wife?': 4232, 'One sexually': 4232, 'frustrated astronaut': 4232, 't gives his answer.': 4232, 'Zoetrope': 895352, 'Flipbook': 895352, 'Low fi': 895352, 'New York City': 915918, 'Street art': 895352, 'Carlos Ramirez': 895352, 'Persistence of vision': 895352, 'Big idea': 895352, 'Catfish': 895352, 'flip book': 895352, 'creative process': 895352, 'self doubt': 895352, 'inner demon': 895352, 'Migos': 24650272, 'Nicki': 24650272, 'Minaj': 24650272, 'Cardi': 24650272, 'MotorSport': 24650272, 'Motor Sport': 24650272, 'running wild': 1236676, 'man vs wild': 1236676, 'bear grylls': 1236676, 'alita': 2841875, 'battle angel': 2841875, 'alita: battle angel': 2841875, 'james cameron': 2841875, 'Robert Rodriguez': 2882374, 'Sin City': 2841875, 'Rosa Salazar': 2841875, 'Christoph Waltz': 2841875, 'Keean Johnson': 2841875, 'Jon Landau': 2841875, 'Yukito Kishiro': 2841875, 'manga': 2841875, 'Michelle Rodriguez': 2841875, 'Jackie Earle Haley': 2841875, 'Eiza González': 2841875, 'Mahershala Ali': 9068690, 'Ed Skrein': 2841875, 'Casper Van Dien': 2841875, 'Jeff Fahey': 2841875, 'the game awards': 2676049, 'game awards 2017': 1287342, 'videogame awards': 1287342, 'video game awards': 2676049, 'geoff keighley': 1287342, 'game awards live': 1287342, 'livestream': 1287342, 'xbox': 1287342, 'best games of 2017': 1287342, 'best videogames 2017': 1287342, 'trailers': 1453463, 'world premiere': 1287342, 'gaming awards': 1287342, 'TGA': 1287342, 'announcement': 1287342, 'mouse': 389888, 'mouse proof': 389888, 'mouse hole': 389888, 'russia investigation': 1288731, 'Resigns': 1288731, 'Testifies': 1288731, 'Investigation': 1288731, 'trump jr.': 1288731, 'trump jr': 1288731, 'ACL': 1288731, 'a closer look': 1288731, 'hope not': 1288731, 'hair gel': 1288731, '8 hours': 1288731, 'sean hannity': 1288731, 'emails': 1288731, 'information': 1288731, 'garbled': 1288731, 'file': 1288731, 'joy con': 1472194, 'award show': 1388707, 'tga': 1388707, 'video game industry': 1388707, 'game of the year': 1472194, 'Best Game Direction': 1388707, 'Best Art Direction': 1388707, 'The Game Awards 2017': 1388707, 'breath of the wild': 1472194, 'the legend of zelda': 1472194, 'dlc': 1472194, 'dlc 2': 1472194, 'the champions ballad': 1472194, 'lozbotw': 1472194, 'expansion pass': 1472194, 'revali': 1472194, 'mipha': 1472194, 'urbosa': 1472194, 'daruk': 1472194, 'shrine': 1472194, 'equips': 1472194, 'kass': 1472194, "champions' ballad": 1472194, 'ancient': 1472194, 'Untouchable': 3969182, 'Eminem Untouchable': 3969182, 'Eminem 2017': 3969182, 'New Eminem': 3969182, 'New Eminem 2017': 3969182, 'New Eminem Untouchable': 3969182, 'Untouchable Eminem': 3969182, 'Revival 2017': 3969182, 'Eminem Revival': 3969182, 'Eminem single': 3969182, 'Eminem single 2017': 3969182, 'Untouchable Revival': 3969182, 'Eminem Songs': 3969182, 'Eminem album': 3969182, 'Eminem all songs': 3969182, 'Cooler Heads': 240430, 'James Franco': 1986386, '127 hours': 240430, 'spider man': 240430, 'why him': 240430, 'pineapple express': 240430, 'the interview': 240430, 'planet of the apes': 240430, 'zeroville': 240430, 'the long home multiple man': 240430, 'the deuce': 240430, 'gravity tractor': 534807, 'nasa asteroids': 534807, 'asteroid collision': 534807, 'asteroid collision plan': 534807, 'asteroid mission': 534807, 'asteroid impact mission': 534807, 'dart mission': 534807, 'asteroid redirect': 534807, 'meteor impact': 534807, 'didymoon': 534807, 'near earth objects': 534807, 'nasa cneos': 534807, 'nasa jpl asteroids': 534807, 'hodges meteorite': 534807, 'chelyabinsk meteor': 534807, 'chelyabinsk': 534807, 'asteroid threat': 534807, 'meteor hit': 534807, 'nasa meteor': 534807, 'meteor crater': 534807, 'impact crator': 534807, 'nasa asteroid plan': 534807, 'chicxulub': 534807, 'deep impact asteroid': 534807, 'asteroid dinosaurs': 534807, 'armageddon asteroid': 534807, 'every': 930919, 'in the life': 533590, 'brighton': 533590, 'nala': 533590, 'pointlessblog': 533590, 'interiors': 533590, 'day two': 533590, 'edinburgh': 533590, 'Palace': 749158, 'dave': 283835, 'mccarthy': 283835, 'melissa mccarthy': 283835, 'dave franco': 283835, 'Gwen': 297290, 'Stefani': 297290, 'Gwen Stefani': 297290, 'sexiest man alive': 297290, 'no doubt': 297290, 'gwen stefani music': 297290, 'blake': 297290, 'shelton': 297290, 'you make it feel like christmas': 297290, 'photoshop': 297290, 'naked cowboy': 297290, 'fanfiction reading': 48436, 'Louis Tomlinson': 985998, 'Miss You': 985998, 'PUBG': 367796, 'Battle Royale': 367796, 'kelly ripa': 59472, 'anderson cooper': 11299, 'live with kelly and ryan': 59472, 'ryan seacrest': 59472, 'nick and warren': 117391, 'Warren Du Preez': 117391, 'Nick Thornton Jones': 117391, 'Chill Picks': 13350, 'Actors': 13350, 'choose': 13350, 'Shaymin': 13350, 'ayahuasca': 13350, 'Peru': 13350, 'fabulous': 13350, 'rewatched': 13350, 'realize': 13350, 'castle': 13350, 'Taylor': 13350, 'crown': 13350, 'MITRE': 13350, 'frankly': 13350, 'expensive show': 13350, 'marzia': 210811, 'cutiepie': 210811, 'cutiepiemarzia': 210811, 'cutie': 210811, 'marzipans': 210811, 'dan and phil games': 580796, 'gaming channel': 580796, 'truth bombs': 580796, 'truth': 580796, 'board game': 580796, 'pj': 580796, 'kickthepj': 580796, 'sophie': 580796, 'our secret fan fictions': 580796, 'macro': 580796, 'uncle': 652060, 'internet support group': 580796, 'wattpad': 580796, 'tabletop': 580796, 'card': 671881, 'banter': 580796, 'roasting': 580796, 'booster': 580796, 'expansion': 580796, 'moments': 580796, 'Lindsey Vonn': 119646, 'G-Eazy feat. Charlie Puth': 1006354, 'Sober': 1006354, 'riverdale fan theories': 541212, 'lili reinhart interview': 541212, 'camila mendes interview': 541212, 'lili reinhart riverdale': 541212, 'camila mendes riverdale': 541212, 'Whitney Avalon': 49783, 'Last Jedi': 49783, 'Musical': 49783, 'The Force Awakens': 60108, 'Jedi Mind Trick': 49783, 'Stormtrooper': 49783, 'Princess Rap Battle': 49783, 'Original Song': 49783, 'Comedy Music': 49783, 'Brendan Milburn': 49783, '7-eleven makeup': 871037, '7/11 makeup': 871037, 'seven-eleven': 871037, '7-eleven': 871037, 'cheap makeup': 871037, 'safiya beauty': 871037, 'full face of first impressions': 871037, 'full face using': 871037, 'full face using 7-eleven makeup': 871037, 'Mannymua': 871037, 'mannymua733': 871037, '7 eleven makeup': 871037, '7-11': 1708289, 'tati': 871037, 'tati westbrook': 871037, 'beauty products': 871037, 'simply me beauty': 871037, 'hit or miss': 871037, 'hot or not': 871037, 'worse makeup ever': 871037, 'testing 7/11 makeup': 871037, 'simply me beauty by 7 eleven': 871037, 'trying 7 eleven makeup': 871037, '7 eleven makeup tutorial': 871037, '7 eleven makeup review': 871037, 'leona lewis': 119502, 'dinah': 119502, 'jane': 119502, 'leona': 119502, 'leona lewis music': 119502, 'dinah jane music': 119502, 'christmas medley': 119502, 'Leona Lewis and Dinah Jane': 119502, 'Leona Lewis Medley': 119502, 'Dinah Jane Medley': 119502, 'medley': 119502, 'bleeding love': 119502, 'LEGO': 39096, 'legos': 39096, 'mechanics': 39096, 'mechanical': 39096, 'moc': 39096, 'my own creation': 39096, 'creativity': 99019, 'kinetic art': 39096, 'technic': 39096, 'cooler master': 39096, 'quick fire': 39096, 'rapid': 39096, 'cherry mx': 39096, 'cherry mx brown': 39096, 'simple syrup': 96250, 'live baking': 96250, 'baking challenge': 96250, 'blindfolded baking challenge': 96250, 'cakebook': 96250, 'book signing': 96250, 'Sen. Al Franken': 16445, 'al franken speech': 16445, 'al franken speaking': 16445, 'al franken live': 16445, 'al franken senate': 16445, 'al franken resignation': 16445, 'al franken resigns': 16445, 'al franken resignation announcement': 16445, 'franken': 16445, 'al franken resignation speech': 16445, '7 Eleven Makeup': 837252, '7 Eleven': 837252, '711': 837252, 'Simply Me Beauty': 837252, 'Grant Gustin': 76933, 'Jesse L Martin': 76933, 'Comic': 76933, 'Best of Both WorldsThe Flash Season 2 Trailer': 76933, 'Arrow vs Flash': 76933, 'Barry Allen': 76933, 'Lighting': 76933, 'Super Speed': 76933, 'Central City': 76933, 'Candice Patton': 76933, 'Danielle Panabaker': 76933, 'Iris West': 76933, 'Caitin Snow': 76933, 'Carlos Valdes': 76933, 'Tom Cavangh': 76933, 'Dr. Harrison Wells': 76933, 'Joe West': 76933, 'Jesse L. Martin': 76933, 'Rick Cosnett': 76933, 'Tom Felton': 76933, 'Deleted Scene': 76933, 'Deleted': 76933, 'Tuesday': 76933, 'Merrell Twins': 50799, 'Twins': 50799, 'Merrelltwins': 50799, 'The Merrell Twins': 50799, 'short natural hair': 50538, 'makeup hairstyle': 50538, 'actresses': 50538, 'modeling': 432736, 'celebrity transformation': 50538, 'Greyson Chance': 19561, 'Paparazzi': 19561, 'Liam': 243001, 'Payne': 243001, 'Bedroom': 243001, 'Floor': 243001, 'charli': 68513, 'xcx': 115175, 'pop 2': 115175, 'out of my head': 68513, 'tove lo': 68513, 'alma': 68513, 'tove': 68513, 'low': 68513, 'number 1 angel': 68513, 'boom clap': 115175, 'lil snowman': 140332, "mariah carey's all i want for christmas is you": 140332, 'all i want for christmas is you': 779243, 'the star': 140332, 'baby please come home': 140332, 'santa claus is comin to town': 140332, 'always be my baby': 140332, 'all i want movie': 140332, 'Lil Snowman': 140332, 'me too movement': 103201, 'weinstein': 78804, 'rape': 78804, 'movement': 78804, 'sexual violence': 78804, 'campaign': 78804, 'sexual harrassment': 78804, 'person of the year 2017': 78804, 'time person of the year list': 78804, 'adam driver': 181616, 'screen rant': 9812, 'Tom Thum': 18229, 'Tom Thummer': 18229, 'Human Beatbox': 18229, 'Beatboxing': 18229, 'Beat': 18229, 'Vocal': 18229, 'Artist': 611495, 'Tune': 18229, 'Beatboxer': 18229, 'Mouthsounds': 18229, 'Microphone': 18229, 'Bass': 18229, 'Sound Effects': 18229, 'VidCon': 133433, 'VidCon US': 133433, 'VidCon Europe': 133433, 'VidCon Australia': 133433, 'Gabbie Hanna': 133433, 'TheGabbieShow': 133433, 'David Dobrik': 133433, 'photography': 585941, 'duan mackenzie': 60072, 'nyc vlogger': 60072, 'mccaren park': 60072, 'greenpoint': 60072, 'williamsburg': 60072, 'inpsiring': 60072, 'good photography': 60072, 'photography tips': 60072, 'positive': 60072, 'just go shoot': 60072, 'film photography': 60072, 'conde nast': 60072, 'traveling': 60072, 'photoshoot': 60072, 'Berlin': 41535, 'BVG': 41535, 'U Bahn': 41535, 'regulatory': 172948, 'alignment': 172948, 'regulatory alignment': 172948, 'regulatory divergence': 172948, 'brexit': 172948, 'brexit talks': 172948, 'brexit fail': 172948, 'theresa may': 172948, 'north ireland': 172948, 'northern ireland': 172948, 'irish border': 172948, 'hard border': 172948, 'brexit bill': 172948, 'dup': 172948, 'raspberrypi': 3114, 'tomy': 3114, 'hardwarehacks': 3114, 'retro': 3114, 'robot': 3114, 'googleaiy': 3114, 'ai': 3114, 'YOUTUBERS REACT TO YOUTUBE REWIND 2017': 2707816, 'the shape of 2017': 2707816, '#youtuberewind': 2707816, 'Jessica Jones': 269145, 'Krysten Ritter': 269145, 'Trish Walker': 269145, 'Rachael Taylor': 269145, 'Malcolm Ducasse': 269145, 'Eka Darville': 269145, 'Jeri Hogarth': 269145, 'Carrie-Anne Moss': 269145, 'date announce': 269145, "International Women's Day": 269145, 'PLvahqwMqN4M09L4sQuTfb7xZLnOur4uxL': 269145, 'PLvahqwMqN4M0VjHwHTdmk40e5Aqx6Umjc': 269145, 'my home': 838551, 'inside my home': 838551, 'rosanna pansino': 838551, 'where to buy': 838551, 'renovation': 838551, 'cribs': 838551, 'real': 838551, 'remodel': 838551, 'decorate': 838551, 'interior': 838551, 'matching': 838551, 'estate': 838551, 'bedroom': 838551, 'closet': 838551, 'shower': 838551, 'desk': 838551, 'office': 838551, 'office space': 838551, 'work space': 838551, 'bath': 838551, 'layout': 838551, 'overhaul': 838551, 'designer': 838551, 'modern': 838551, 'girly': 838551, 'hailee steinfeld wont let me go': 257059, 'puppets': 257059, 'hailee': 257059, 'steinfeld': 257059, 'wont': 257059, 'let': 257059, 'best floyd mayweather interview': 257059, 'how to be honest': 257059, 'i am diego new album coming soon official trailer': 257059, 'awkward puppets': 257059, 'awkwardpuppets': 257059, 'diegoshow': 257059, "Hailee Steinfeld Won't Let Me Go": 257059, "Won't": 257059, 'Let Me Go': 257059, 'watt': 257059, 'Celebrates': 154042, 'Hot Christmas': 154042, 'x-men': 154042, 'wolverine': 207745, 'muscle': 362442, 'Australian': 154042, 'les miserables': 154042, 'eddie the eagle': 154042, 'the front runner': 154042, 'broadway 4d': 154042, 'Episode 1733': 1489575, 'James Franco Monologue': 9423, 'Seth Rogen': 9423, 'Jonah Hill': 9423, 'Steve Martin': 9423, 's43e8': 1489575, 'Pineapple Express': 1489575, 'Future World': 1489575, 'Sausage Party': 1489575, 'Seth Rogan': 1489575, 'SZA': 1489575, 'CTRL': 1489575, 'Love Galore': 1489575, 'Supermodel': 1489575, 'DESI PERKINS': 379810, 'PATRICK STARRR': 379810, 'MAC COSMETICS': 379810, 'MAKEUP COLLECTION': 379810, 'MAKEUP REVIEW': 379810, 'MAKEUP TUTORIAL': 379810, 'MAKEUP COLLAB': 379810, 'BEAUTY INFLUENCER': 379810, 'PATRICK STARRR X MAC': 379810, 'BEAUTY': 379810, 'MAKEUP PRODUCTS': 379810, 'MAC PRODUCTS': 379810, 'MAKEUP TUT': 379810, 'iPlayer': 84275, 'graham norton': 84275, 'the graham norton show': 84275, 'BBC graham norton': 84275, 'rebel wilson': 84275, 'kevin hart': 1701679, 'the rock': 2909200, 'jack black': 172511, 'noel gallagher': 84275, 'bbc1': 84275, 'bbc 1': 84275, 'body percussion': 84275, 'pitch perfect': 84275, 'dawn french': 84275, 'Jessica Chastain': 84275, 'buzzfeedboldly': 208227, 'Dark Skin': 208227, 'Nyakim Gatwech': 208227, 'Queen of the Dark': 208227, 'Photography': 208227, 'Winter Dunn': 208227, 'Pictures': 208227, 'Photoshoot': 208227, 'Beautiful': 208227, 'african american': 208227, 'pigment': 208227, 'self confidence': 208227, 'women try': 208227, 'styled': 208227, 'styled by': 208227, 'fashion industry': 208227, 'high fashion': 208227, 'acting': 208227, 'hair nah': 336546, "don't touch black hair": 336546, 'solange knowles': 336546, "don't touch my hair": 336546, 'knowles': 336546, 'seat at the table': 336546, 'gamer': 336546, 'computer games': 336546, 'momo pixel': 336546, 'black girl': 336546, 'natural hair': 336546, 'african american hair': 336546, 'discrimination': 336546, 'singing my makeup routine': 638911, 'james charles singing': 638911, 'i want a hippopotomus for christmas': 638911, 'sleigh ride': 638911, "baby it's cold outside": 638911, 'singing makeup': 638911, 'flashback music': 638911, 'infomercials': 130248, 'infomercial': 130248, 'does it work': 130248, 'grav3yardgirl': 130248, 'favorite': 130248, 'as seen on tv': 130248, 'just for fun': 130248, 'wubble bubble': 130248, 'new kids toys': 130248, 'water balloons': 130248, 'does this thing really work': 130248, 'flawless hair remover': 130248, 'flawless finish': 130248, 'rocketwave': 130248, 'rocketwave notebook': 130248, 'oonies': 130248, 'Christmas toy': 130248, 'fingerlings': 130248, 'line art cutting': 205650, 'RIU': 205650, 'INSIDER art': 205650, 'snoopy': 205650, 'chimbo': 44618, 'chrissmas': 44618, 'chrismtas': 44618, 'soho': 44618, 'cake shop': 44618, 'professional bakery': 44618, 'meringues': 44618, 'merangs': 44618, 'merings': 44618, 'maringues': 44618, 'egg white': 44618, 'whsked': 44618, 'whisked': 44618, 'french meringue': 44618, 'sprinkles': 44618, 'decorations': 44618, 'piping': 44618, 'nozzles': 44618, 'Unipiper': 35372, 'Portlandia': 35372, 'Keep Portland Weird': 35372, 'Unicycle': 35372, 'Bagpipes': 35372, 'portland': 35372, 'AT-AT': 35372, 'hoth': 35372, 'luke skywalker': 2430622, 'John Maclean': 33074, 'Maclean': 33074, 'Bigger Lips': 33074, 'How to get bigger lips': 33074, 'Lips': 33074, 'Enlarge Lips': 33074, 'Massive Lips': 33074, 'Lip Correction': 33074, 'Enlarging Lip Liner with Lip Pencil': 33074, 'kpop idols': 115778, 'kpop idol': 115778, 'jjcc': 115778, 'prince mak': 115778, 'big bang': 130151, 'henry prince mak': 115778, 'exo': 115778, 'super junior': 115778, 'twice': 115778, 'black pink': 115778, 'kpop group': 115778, 'kpop dance': 115778, 'male idols': 115778, 'slave contract': 115778, 'jyp': 115778, 'yg': 115778, 'sm': 115778, 'gna': 115778, 'gina': 115778, 'dark side of kpop': 115778, 'got7': 115778, 'jackson wang': 115778, 'benbrown': 28731, 'mr ben brown': 28731, 'mrbenbrown': 28731, 'visual vibes': 28731, 'Cinematography': 28731, 'audi r8 v10plus': 28731, 'audi r8 v10+': 28731, 'cape town film shoot': 28731, 'antoine truchet': 28731, 'audi supercar': 28731, 'audi rs6': 28731, 'audi r8': 28731, 'red dragon': 28731, 'panavision primo': 28731, 'gh5 video': 28731, 'fake it till you make it': 28731, 'ben brown advice': 28731, 'ben brown audi': 28731, 'ben brown BTS': 28731, 'visual Vibes': 28731, 'downright': 28731, 'downright productions': 28731, 'ladies night': 6051, 'mamrie hart': 6051, 'alicia gaynor': 6051, 'grace hancock': 6051, 'ive got this round more tales of debauchery': 6051, 'you deserve a drink': 6051, 'dirty thirty': 6051, 'camp takota': 6051, 'Michael': 190152, 'Bublé;': 190152, 'Warner': 190152, 'Bros.': 190152, 'Records;': 190152, 'Reprise': 190152, 'WBR;': 190152, 'Michael Bublé': 190152, 'Michael Buble': 190152, 'Christmas Music': 190152, 'Full Album': 190152, 'Full': 190152, 'Holiday Music': 190152, 'Shania Twain': 190152, 'White Christmas': 190152, 'deluxe': 190152, 'taylor r': 124439, 'taylor youtuber': 124439, 'taylor r vlogs': 124439, 'taylor richard model': 124439, 'taylor richard': 124439, 'female daily vloggers': 124439, 'female daily vlog': 124439, 'female vlog': 124439, 'hong kong vlog': 124439, 'hong kong vlog 2017': 124439, 'hong kong vloggers': 124439, 'christmas tree eyebrows': 124439, 'xmas tree eyebrows': 124439, 'tree eyebrows': 124439, 'christmas tree brow': 124439, 'beauty trends 2017': 124439, 'weird beauty trends 2017': 124439, 'instagram trends': 124439, 'christmas makeup tutorial': 124439, 'christmas makeup': 124439, 'taylor r makeup': 124439, 'danny masterson the ranch fire': 1774, 'house of cards final season': 1774, 'house of cards season 6': 1774, 'the ranch netflix': 1774, 'danny masterson fired netflix': 1774, 'danny masterson': 1774, 'danny masterson netflix': 1774, 'house of cards': 1774, 'the ranch': 1774, 'kevin spacey': 26171, 'kevin spacey fired': 1774, 'danny masterson fired': 1774, 'ready player one movie': 3437722, 'rpo': 3437722, 'rp1 movie': 3437722, 'ernest cline': 3927089, 'tye sheridan': 3437722, 'steven spielberg': 3927089, 'dan farah': 3437722, 'vr goggles': 3437722, 'avatar creator': 3437722, 'gunter': 3437722, 'anorak': 3437722, "anorak's announcement": 3437722, 'james halliday': 3437722, 'jade key': 3437722, 'copper key': 3437722, 'crystal key': 3437722, 'quest': 3437722, 'sci-fi': 3437722, 'simon pegg': 3437722, 'tj miller': 3437722, 'warner bros pictures': 3437722, 'amblin entertainment': 3437722, 'Santa Claus': 1480152, 'bad lip reading': 1949212, 'eleven': 1949212, 'lip dub': 1949212, 'lip sync': 1949212, 'dubbing': 1949212, 'poppy': 1700398, 'i dressed according to my zodiac sign for a week': 2137830, 'zodiac sign': 2137830, 'astrology': 2137830, 'astrological fashion': 2137830, 'zodiac fashion': 2137830, 'star sign': 2137830, 'astrological compatibility': 2137830, 'scorpio': 2137830, 'pisces': 2137830, 'gemini': 2137830, 'taurus': 2137830, 'aries': 2137830, 'virgo': 2137830, 'leo': 2137830, 'sagittarius': 2137830, 'libra': 2137830, 'capricorn': 2137830, 'gown': 2137830, 'safia': 2137830, 'safiya ladylike': 2137830, 'safiya buzzfeed': 2137830, 'safiya clothes': 2137830, 'for a week': 2137830, 'ironman': 971920, 'captain america': 971920, 'bacon': 334187, 'bacon recipes': 334187, 'bacon ring': 334187, 'bbq': 334187, 'meatball': 334187, 'bacon wrapped meatball': 334187, 'bacon burger': 334187, 'cheese burger': 334187, 'grilling': 334187, 'skewers': 334187, 'starving polar bear': 2021446, 'polar bear video': 1880821, 'arctic starving bear': 1880821, 'guardian climate change': 1880821, 'polar bear': 2021446, 'polar bear starving': 1880821, 'guardian': 3043664, 'why is it illegal to feed polar bears': 1880821, 'hungry': 1880821, 'artists': 525869, 'museums': 525869, 'denial': 525869, 'mind blown': 525869, 'mad skills': 525869, 'paul robalino': 525869, 'ellie panger': 525869, 'christopher schuchert': 525869, 'jason nguyen': 525869, 'katie robbins': 525869, 'diy chrome nails': 2384983, 'chrome powder': 2384983, 'powder for nails': 2384983, 'nail buffing': 2384983, 'silver chrome': 2384983, 'mixing together': 3125003, 'mixing': 3125003, 'mixing all my': 3125003, 'cristine the science queen': 2384983, 'simplynerdlogical': 2384983, 'PC': 256381, 'PS3': 256381, 'PS4': 256381, 'GBC': 256381, 'SEGA': 256381, 'Vita': 256381, 'Capcom': 256381, 'Switch': 256381, 'Konami': 256381, 'Wii U': 256381, 'Shenmue': 256381, 'Destiny': 256381, 'Nintendo': 256381, 'Xbox One': 256381, 'Xbox 360': 256381, 'Activision': 256381, 'Fallout 4': 256381, 'Resident Evil': 256381, 'Mortal Kombat': 256381, 'Bungie Software': 256381, 'Bethesda Softworks': 256381, 'NetherRealm Studios': 256381, 'Super Mario 3D World': 256381, 'Bethesda Game Studios': 256381, 'stray cats': 132511, 'cat rescue': 132511, 'cats meowing': 132511, 'cat noises': 132511, 'cats talking': 132511, 'cats rescued': 132511, 'cat sounds': 132511, 'kitten videos': 132511, 'kitten rescue': 132511, 'kittens meowing': 132511, 'kittens playing': 132511, 'kitten crying': 132511, 'kitten meows': 132511, 'EMS cat': 132511, 'stray cat ems station': 132511, 'cat ems station': 132511, 'EMS': 132511, 'fire station cat': 132511, 'killer the cat': 132511, 'cat killer': 132511, 'FDNY': 132511, 'cat laser pointer': 132511, 'funny cat videos': 132511, 'cat toys': 132511, 'cat queen': 132511, 'new york fire department': 132511, 'weigh': 186943, 'mass': 186943, 'scales': 186943, 'orbit': 186943, 'laws of motion': 186943, 'springs': 186943, 'jennxpenn': 397329, 'jennxpenn2': 397329, 'jennxpenngames': 397329, 'jenn': 397329, 'jen': 397329, 'penn': 397329, 'pen': 397329, 'mcallister': 397329, 'jennxpen': 397329, 'jenxpenn': 397329, 'jenxpen': 397329, 'jennpenn': 397329, 'entertaining': 397329, 'attempting': 397329, 'attempt': 397329, 'trend': 639527, 'single': 397329, 'tweets': 755947, 'floss dance': 397329, 'fidget spinner': 397329, 'applying makeup': 397329, 'condom': 397329, 'tampon': 397329, 'full face of': 397329, 'googly eyes': 397329, 'swiggly eyebrows': 397329, 'eyebrow': 397329, 'eyebrows': 397329, 'wiggly': 397329, 'swiggly': 397329, 'apply': 397329, 'applying': 397329, 'full': 397329, 'face': 397329, 'floor is lava': 397329, 'floor': 397329, 'is': 397329, 'Fires': 14670, 'Forest fires': 14670, 'Alita': 40499, 'Alita Battle Angel': 40499, 'Kevin Feige': 40499, 'dwayne johnson': 2824925, 'the rock 2017': 2824925, 'youtube the rock': 2824925, 'youtube dwayne johnson': 2824925, 'lilly singh dwayne johnson': 2824925, 'lilly singh the rock': 2824925, 'the rock lilly singh': 2824925, 'dwayne lilly singh': 2824925, 'dwayne lilly': 2824925, 'jumanji dwayne johnson': 2824925, 'dwayne the rock johnson': 2824925, 'therock': 2824925, 'the rock youtube': 2824925, 'dwayne johnson youtube': 2824925, 'the rock jumanji': 2824925, 'dwayne johnson jumanji': 2824925, 'dwayne johnson the rock': 2824925, 'Tiffany': 319680, 'Haddish': 319680, 'memoir': 319680, 'Black': 319680, 'unicorn': 319680, 'virtuoso': 91085, 'thevirts': 91085, 'huron': 91085, 'daren': 91085, 'flourish': 91085, 'cards': 91085, 'revealed': 91085, 'ellusionist': 91085, 'theory11': 91085, 'flourishes': 91085, 'cardistry': 91085, 'Card Trick': 91085, 'Card Flourish': 91085, 'cardistry tutorial': 91085, 'launch edition': 91085, 'penguin magic': 91085, 'card flourishes': 91085, 'kevin ho': 91085, 'daren yeow': 91085, 'huron low': 91085, 'spring summer': 91085, 'playing cards': 91085, 'waterwheel': 91085, 'dan and dave': 91085, 'the virts': 91085, 'dealersgrip': 91085, 'learn cardistry': 91085, 'fontaine deck': 91085, 'fontaine cards': 91085, 'cardistry tutorials': 91085, 'cardistry for beginners': 91085, 'fw17': 91085, 'Liquid paper': 91085, 'fw17 virt': 91085, 'ISIS defeated in Iraq': 6963, 'ISIS': 6963, 'defeated': 6963, 'ISIS defeated': 6963, 'Iraq': 6963, 'liberated': 6963, 'militants': 6963, 'fighters': 6963, 'islamic state': 6963, 'cbc': 6963, 'cbc news': 6963, 'STM32': 1425, 'exixe': 1425, 'nixie tube': 1425, 'nixie': 1425, 'hardware': 1425, 'gps': 1425, 'gnss': 1425, 'raspberry pi': 1425, 'IN-14': 1425, 'IN-12': 1425, 'AreWeFamousNow': 20566, 'Karim Metwaly': 20566, 'Karim Jovian': 20566, 'Karim': 20566, 'KJovian': 20566, 'dollar for your story': 20566, 'a dollar for your story': 20566, '$1 for your story': 20566, 'Stories': 20566, 'Jovian Entertainment': 20566, 'dollar': 20566, 'dreams': 55810, 'saddness': 20566, '1 dollar': 20566, 'Clean': 20566, 'one dollar': 20566, 'tell me your story': 20566, 'story time': 20566, 'New York $1': 20566, 'New York Stories': 20566, 'someone to talk to': 20566, 'share': 20566, 'shares': 20566, 'shockwave': 12468, '18000fps': 12468, 'frames per second': 12468, 'phantom camera': 12468, 'the slowmo guys': 12468, 'gizmoslip': 12468, 'high speed': 12468, 'grid': 12468, 'grid type shockwave': 12468, 'amazing slow motion': 12468, 'rad science': 12468, 'Flite Test': 90458, 'remote controlled': 90458, 'unmanned': 90458, 'rc': 90458, 'uav': 90458, 'rc hobby': 90458, 'rc shop': 90458, 'holy grail': 87660, 'knightfall': 87660, "assassin's creed": 87660, 'assassins creed': 87660, 'parkour': 87660, 'france': 87660, 'history channel': 87660, 'templar': 87660, 'indiana jones': 87660, 'devinsupertramp': 87660, 'acu': 87660, 'assassins creed unity': 87660, 'ubisoft': 87660, 'devin graham': 87660, 'paris': 87660, 'free running': 87660, 'ronnie shalvis': 87660, "assassin's creed meets parkour": 87660, 'in real life': 87660, 'unity': 87660, 'assassin': 87660, 'flips': 87660, 'art of motion': 87660, 'tarana burke me too': 24397, 'tarana burke': 24397, 'alyssa milano': 24397, 'lara setrakian': 24397, 'eleanor mcmanus': 24397, 'juliet huddy': 24397, 'rebecca weir': 24397, 'heather unruh': 24397, 'dominique huett': 24397, '2017 person of the year': 24397, 'the script arms open video': 41027, 'the script arms open benny benassi x mazzz & rivaz remix': 41027, 'the script arms open remix': 41027, 'benny benassi': 41027, 'benny': 41027, 'benassi': 41027, 'sneaker shopping': 798891, 'scott disick': 798891, 'the lord': 798891, 'let the lord be with you': 798891, 'joe la puma': 798891, 'kourtney kardashian': 798891, 'flight club': 798891, 'air jordan': 798891, 'nike': 798891, 'air jordan 1': 798891, 'bapesta': 798891, 'supreme x nike air more uptempo': 798891, 'air pippen 1': 798891, "air force 1 '07 lv8": 798891, 'supreme x louis vuitton': 798891, 'film movie': 489367, 'gurren lagann': 489367, 'mass effect': 489367, 'tali': 489367, 'logan paul vlog': 7691902, 'logan paul youtube': 7691902, 'parrot': 7691902, 'maverick': 7691902, 'maverick clothes': 7691902, 'logan paul music': 7691902, 'santa diss track': 7691902, 'diss track': 7691902, 'best diss track': 7691902, 'logan paul diss track': 7691902, 'maverick merch': 7691902, 'red merch': 7691902, 'red drop': 7691902, 'be a maverick': 7691902, 'santa music': 7691902, 'christmas diss track': 7691902, 'john boyega': 2325783, 'mark hamill': 2025869, 'wired autocomplete interview': 1495447, 'last jedi cast': 1795361, 'star wars cast': 1795361, 'star wars the last jedi': 2958042, 'kelly marie tran': 2020560, 'the last jedi cast': 1854065, 'the last jedi autocomplete': 1495447, 'the last jedi wired': 1495447, 'mark hamill interview': 1495447, 'mark hamill autocomplete': 1495447, 'mark hamill joker': 1495447, 'joker voice': 1495447, 'last jedi autocomplete': 1495447, 'jed': 1495447, 'angry grandpa': 4117084, 'the angry grandpa': 4117084, 'theangrygrandpashow': 4117084, 'kidbehindacamera': 4117084, 'pickleboy': 4117084, 'i wont wear a jacket': 957494, 'gus jacket': 957494, 'no jacket': 957494, 'wont wear coat': 957494, 'i wont wear a coat': 957494, 'short funny video': 957494, 'i eat cigarettes': 957494, 'dont eat that bad marijuanas': 957494, 'gus rap song': 957494, "people who don't wear jackets": 957494, 'funny song': 957494, 'rap parody': 957494, 'rap music': 957494, 'reddit funny': 957494, 'gus meme': 957494, 'meme playlist': 957494, 'port authority explosion': 924987, 'nyc port authority explosion': 425866, 'nypd': 924987, 'explosion': 924987, 'pipe bomb': 645986, 'manhattan news': 425866, 'port authority': 924987, 'breaking news today': 425866, 'port authority bus terminal': 924987, 'explosion new york': 425866, 'timesquare': 425866, 'blast': 425866, 'terror': 425866, 'times square': 645986, 'ny police': 425866, 'nypd responds': 425866, '12/10_TWD': 363466, 'sewers': 363466, 'bite': 363466, 'bitten': 363466, 'mid-season finale': 363466, 'finale': 363466, 'CNBC': 499121, 'Mad Money': 499121, 'Squawk Box': 499121, 'Power Lunch': 499121, 'Opening Bell': 499121, 'Closing Bell': 499121, 'Financial News': 499121, 'Finance News': 499121, 'Stock News': 499121, 'Stocks': 499121, 'Trading': 499121, 'Investing': 499121, 'Stock Market': 499121, 'new york police': 499121, 'bus terminal': 499121, 'pipe bomb port authority': 279001, 'pipe bomb explosion': 499121, 'pipe bomb nyc': 279001, 'nyc pipe bomb': 279001, 'pipe bomb bus terminal': 279001, 'bus station': 279001, 'bus terminal nyc': 279001, 'pipe explosion': 279001, 'Billy Kimmel': 441105, 'tax cuts': 441105, 'CHIP': 441105, "children's health insurance program": 441105, 'obamacare': 441105, 'heart surgery': 441105, 'open enrollment': 441105, 'jimmy kimmel baby': 441105, 'Facebook': 84968, 'Keaton Jones': 402111, 'bullying': 3559230, 'PLvahqwMqN4M3Jr3bj3zjP7RjVyMXBd0iN': 143461, 'Fuller House': 143461, 'Full House': 143461, 'stephanie tanner': 143461, 'jodie sweetin': 143461, 'kimmy gibbler': 143461, 'andrea barber': 143461, 'uncle jesse': 143461, 'dj tanner': 143461, 'fullfer house season 3': 143461, 'season 3b': 143461, 'fuller house netflix new season': 143461, 'progressive': 49761, 'cable': 49761, 'cable news': 49761, 'Velshi & Ruhle': 49761, 'Ali Velshi': 49761, 'Stephanie Ruhle': 49761, 'MSNBC LIVE': 49761, 'celebrity chef mario batali sexual misconduct': 49761, 'mario batali sexual misconduct accusation': 49761, 'the chew': 49761, 'mario batali accused': 49761, 'chef mario batali': 49761, 'celebrity chef': 49761, 'mario batali sexual harassment': 49761, 'wildlife': 140625, 'explore': 140625, 'discover': 140625, 'starving': 140625, 'arctic': 140625, 'sea ice': 140625, 'melt': 140625, 'Paul Nicklen': 140625, 'Heart-Wrenching': 140625, 'Iceless Land': 140625, 'spotted': 140625, 'photographer': 140625, 'expedition': 140625, 'the Baffin Islands': 140625, 'PLivjPDlt6ApRfQqtRw7JkGCLvezGeMBB2': 140625, 'PLivjPDlt6ApRiBHpsyXWG22G8RPNZ6jlb': 140625, 'PLivjPDlt6ApTjurXykShuUqp7LQcj9s8s': 140625, 'Polar Bear on Iceless Land': 140625, 'polar bear was spotted': 140625, 'expedition in the Baffin Islands': 140625, 'NeNe Leakes': 126423, '#RHOA': 126423, 'Kenya': 126423, 'Rosa': 126423, 'comedy show': 126423, 'Tiffany Haddish snl host': 126423, 'Tiffany Haddish SNL': 126423, 'Real Housewives of Atlanta': 126423, 'Atlanta Housewives': 126423, 'the real housewives of atlanta': 126423, 'real housewives': 126423, 'atlanta housewives season 10': 126423, 'kenya moore': 126423, 'bravotv': 126423, 'wireless': 208628, 'wireless lav': 208628, 'bias': 208628, 'quality': 208628, 'weight loss': 382371, 'no bones': 208400, 'what if you had no. bones': 208400, 'bone': 208400, 'bone marrow': 208400, 'skeleton': 208400, 'exoskeleton': 208400, 'jennifer': 110750, 'hudson': 110750, 'dream girls': 110750, 'american idol': 192259, 'christmas lights': 110750, 'dreamgirls': 192259, 'zero-g': 192359, 'zero g': 192359, 'zero gravity': 192359, 'weightlessness': 192359, 'zero g plane': 192359, 'zero-g plane': 192359, 'zero g flight': 192359, 'zero-g flight': 192359, 'microgravity': 192359, 'ESA': 192359, 'european space agency': 192359, 'fly your thesis': 192359, 'F-WNOV': 192359, 'novespace': 192359, 'parabola': 192359, 'parabolic flight': 192359, 'Jingle Bell Ball 2017': 593266, 'Jingle Bell Ball': 593266, 'JBB': 593266, 'Capital': 593266, 'FM': 593266, 'Capital FM': 593266, 'Capital’s Jingle Bell Ball': 593266, 'Live show': 593266, 'Live music': 593266, 'O2': 593266, 'Arena': 593266, 'Gig': 593266, 'JBB2017': 593266, 'Coca-Cola': 593266, 'Jingle Bells Ball': 593266, 'Jingle Bells': 593266, 'Jingle Ball': 593266, 'Musician': 593266, 'Band': 593266, 'Live Music Video': 593266, 'SONY PICTURES': 6226815, 'SPIDER-MAN': 6226815, 'SPIDER-MAN: INTO THE SPIDER-VERSE': 6226815, 'Shameik Moore': 6226815, 'SPIDER-MAN: INTO THE SPIDER-VERSE OFFICIAL TRAILER': 6226815, 'Miles Morales': 6226815, 'Peter Parker': 6226815, 'Spider-man: Homecoming': 6226815, 'Spider-man animated': 6226815, 'Spiderman': 6226815, 'Marvel Cinematic Universe': 6226815, 'Black Spider-man': 6226815, 'Spider-man cartoon': 6226815, 'Sony Pictures Animation': 6226815, 'Animated Movie': 6226815, 'Liev Schreiber': 6226815, 'Ultimate Spider-man': 6226815, 'Spiderverse': 6226815, 'Spider-man sequel': 6226815, 'padilla': 149231, 'anthony padilla youtube': 149231, 'youtube anthony padilla': 149231, 'anthony': 149231, 'smosh anthony': 149231, 'reflection': 149231, 'honest': 149231, 'moustache': 149231, 'if your reflection were honest': 149231, 'if were honest': 149231, 'youtube if were honest': 149231, 'youtube were honest': 149231, 'if were honest youtube': 149231, 'were honest youtube': 149231, 'anthonypadilla': 149231, 'reflection were honest': 149231, 'reflection honest': 149231, 'youtube skit': 149231, 'youtube padilla': 149231, 'padilla youtube': 149231, 'padildo': 149231, 'manhattan explosion': 296641, 'Port Authority Bus Terminal': 76521, 'Port Authority Bus Terminal explosion': 76521, 'new york city explosion': 76521, 'live streaming': 11994, 'cbsn': 11994, 'Mario Batali': 11994, 'sexual harassment claims': 11994, 'The Chew': 11994, 'GoPro': 40362, 'Hero4': 40362, 'Hero5': 40362, 'Hero Camera': 40362, 'HD Camera': 40362, 'stoked': 40362, 'cam': 40362, 'hero4 session': 40362, 'Hero5 Session': 40362, 'high definition': 40362, 'high def': 40362, 'be a hero': 40362, 'beahero': 40362, 'hero five': 40362, 'karma': 40362, 'gpro': 40362, 'seagull': 40362, 'seagull drone': 40362, '4k': 40362, '4K': 40362, 'telemetry': 40362, 'Seagull Theft': 40362, 'theft': 305264, 'gopro theft': 40362, 'makeup hacks': 19859, 'skin care': 79979, 'natural medicine': 19859, 'organic skincare': 19859, 'green beauty': 19859, 'natural skin care': 19859, 'beauty tips': 19859, 'skin care routine': 19859, 'winter skin care': 19859, 'acne': 79979, 'skin care products': 19859, 'best skin care': 19859, 'coconut oil': 19859, 'acne skin care': 19859, 'skin care how to': 19859, 'dry skin': 19859, 'DIY beauty': 19859, 'face mask': 19859, 'polar': 1192547, 'polar ice caps': 1192547, 'polar bear canada': 1192547, 'polar bear starving video': 1192547, 'baffin island': 1192547, 'sea legacy': 1192547, 'polar bear starving to death': 1192547, 'polar bears': 1192547, 'polar bears starving': 1192547, 'does my makeup': 469681, 'trump sexual harassment': 1817, 'donald trump sexual assault': 1817, 'gretchen carlson': 1817, 'gretchen carlson sexual harassment': 1817, 'metoo': 1817, 'sexual assault allegations': 2904, 'sexual harassment allegations': 1817, 'World Premiere': 102310, 'one chip challenge': 149631, 'paqui': 149631, 'als': 149631, 'Inthefrow': 80945, 'In the frow': 80945, '19 CHRISTMAS PARTY OUTFITS: ASOS HAUL AND TRY ON': 80945, 'ASOS HAUL': 80945, 'ASOS TRY ON': 80945, 'PARTY OUTFITS': 80945, 'ASOS': 80945, 'FESTIVE OUTFITS': 80945, 'TRY ON': 80945, 'ASOS HAUL 2017': 80945, 'ASOS REVIEW': 80945, 'ASOS TRY ON HAUL': 80945, 'CHRISTMAS DRESSING': 80945, 'PARTY OUTFIT IDEAS': 80945, 'ASOS HAUL AND TRY ON': 80945, 'ASOS UNBOXING': 80945, '19 PARTY LOOKS': 80945, '19 PARTY OUTFITS': 80945, 'CHRISTMAS PARTY': 80945, 'CHRISTMAS PARTY OUTFITS': 80945, 'OUTFITS': 80945, 'candace lowry': 173971, 'buzzfeed candace': 173971, 'gigi hadid': 173971, "victoria's secret model": 173971, 'diet': 471894, 'runway': 173971, 'crash diet': 173971, 'celebrity diet': 173971, 'diets': 173971, 'model diets': 173971, 'popsugar': 173971, 'insane diet': 173971, 'miranda kerr': 173971, "harper's bazaar": 173971, 'kerr': 173971, 'google trending': 173971, 'Moby': 41958, 'Moby Everything Was Beautiful And Nothing Hurt': 41958, 'Everything Was Beautiful And Nothing Hurt Moby': 41958, 'Moby Like A Motherless Child': 41958, 'Like A Motherless Child Moby': 41958, 'Moby Chill': 41958, 'Moby Video': 41958, 'Moby Official Video': 41958, 'Official Video Moby': 41958, 'Moby Music Video': 41958, 'Music Video Moby': 41958, 'Black & White Music Video': 41958, 'Brett': 51913, 'BMX': 51913, 'angry birds': 19361, 'rovio': 19361, 'knitting': 19361, 'samantha': 129033, 'maria': 129033, 'sammi': 129033, 'come': 76446, 'westfield': 76446, 'buy': 76446, 'high': 418947, 'thirty seconds to mars': 686559, 'walk on water': 686559, 'jared leto': 686559, '30 seconds to mars': 686559, 'best videos of the year': 686559, 'best of the year': 686559, 'best videos compilation': 686559, 'apple inc.': 1609, 'mobile phone app': 1609, 'captain marvel': 1609, 'mergers and acquisitions': 1609, 'marvel family': 1609, 'acoustic fingerprinting': 1609, 'apple inc': 1609, 'universal windows platform apps': 1609, 'samsung electronics': 1609, 'iphones': 1609, 'technological innovation': 1609, 'shazam (service)': 1609, 'music information retrieval': 1609, 'grumpy': 216921, 'the world': 216921, 'corn dogs': 216921, 'secret talent': 216921, 'John Boyega Interview': 8696, 'The Last Jedi Red Carpet': 112893, 'The Last Jedi World Premiere': 112893, 'Episode VIII': 535630, 'Movie Review': 535630, 'New Scene': 8696, 'Exclusive Clip': 8696, 'Phasma': 8696, 'Kylo': 8696, 'Skywalker': 356929, 'airplanes': 2700, 'compress': 159240, 'compressed': 159240, 'compressed air': 159240, 'engine': 159240, 'air engine': 159240, 'compressed air engine': 159240, '3D': 159240, '3D Printed': 159240, 'wing': 159240, 'Homemade': 159240, 'air hogs': 159240, 'tom stanton': 159240, 'Daniel Bruhl': 3464233, 'Rosamund Pike': 3464233, 'Israeli': 3464233, '7 Days in Entebee': 3464233, 'Entebee movie': 3464233, 'Entebbe movie': 3464233, 'Palestinian': 3464233, 'Zero Dark Thirty': 3464233, 'Argo': 3464233, 'Narcos': 3464233, 'Jose Padilha': 3464233, 'rescue mission movie': 3464233, 'seven days in entebbe': 3464233, '13 hours': 3464233, 'Black Hawk down': 3464233, 'Jurassic Park trailer': 3464233, 'Jurassic world trailer': 3464233, 'Jurasic Park Trailer': 3464233, 'Jurasic world trailer': 3464233, 'new trailers': 3464233, 'teeth': 90680, 'false teeth': 90680, 'dentures': 90680, 'caught on tape': 90680, 'caught on video': 90680, 'caught on camera': 90680, "Trump's False Teeth Come Loose During Speech": 90680, 'rare': 90680, 'Ford Mustang': 10020, 'best tongue twister': 30269, 'english tongue twisters': 30269, 'funny tongue twisters': 30269, 'tongue twister challenge': 30269, 'tongue twister chinese': 30269, 'tongue twister english': 30269, 'around the world': 30269, 'tongue twister': 30269, 'word games': 30269, 'languages': 30269, '70 people': 30269, 'seventy people': 30269, 'tongue twisters': 30269, 'world countries': 30269, 'top countries': 30269, 'countries of the world': 45236, 'best countries': 30269, 'countries in the world': 30269, 'accents': 30269, 'accent': 30269, 'conde nast traveler': 30269, 'travel videos': 30269, 'travel guide': 30269, 'Trevor': 553866, 'Trevor Moran': 553866, 'Trevor Moran YouTube': 553866, 'YouTube Trevor Moran': 553866, 'Trevor Moran O2L': 553866, 'O2L Trevor Moran': 553866, 'My gender': 553866, 'Sinner': 553866, 'Sinner Trevor Moran': 553866, 'buzzfeed pop': 337908, 'Light Saber': 337908, 'Yoda': 337908, 'Po': 337908, 'star wars episode 8': 504403, 'star wars explained': 337908, 'star wars theory': 1036298, 'star wars 8': 1336212, 'supreme leader snoke': 337908, 'snoke the last jedi': 337908, 'bb8': 337908, 'palpatine': 337908, 'carrie fisher': 1208102, 'vader': 337908, 'kylo ren the last jedi': 337908, 'sith': 337908, 'snoke': 337908, 'star wars episode 8: the last jedi': 337908, 'rogue one': 1151705, 'a star wars story': 1151705, 'Emirates first class': 2880365, 'Tennessee': 317143, 'Facebook video': 317143, 'social media backlash': 317143, 'honest trailer star wars': 698390, 'star wars original trilogy': 698390, 'return of the jedi': 698390, 'star wars sequel': 698390, 'star wars review': 698390, 'star wars explain': 698390, 'star wars 6': 698390, 'star wars return of the jedi': 698390, 'new star wars': 698390, 'solo': 698390, 'kevin': 719962, 'Jumanji': 999484, 'Jumanji the movie': 719962, 'baby daddy': 719962, '2017 is the best?': 117875, 'project for awesome 2017': 117875, 'Chris Stuckmann': 526934, 'OST': 526934, 'Score': 526934, 'John Williams': 526934, 'Luke Skywalker': 600919, 'Poe Dameron': 526934, 'Leia': 537259, 'Han Solo': 526934, 'Battle': 526934, 'Snoke': 526934, 'Domhnall Gleeson': 526934, 'Laura Dern': 526934, 'Benicio Del Toro': 526934, 'Force Awakens': 526934, 'Lip Sync Battle': 831710, 'Impersonation': 831710, 'Caught': 831710, 'Attention': 1111232, 'Spider-Man: Homecoming': 831710, 'Mary Jane': 831710, 'MJ': 831710, 'Michelle': 831710, 'K.C. Undercover': 831710, 'K.C. Cooper': 831710, 'Shake It Up!': 831710, 'Rocky Blue': 831710, 'Zapped': 831710, 'Zoey Stevens': 831710, 'The Greatest Showman': 831710, 'murph is uber driver': 255084, 'uber drama murph and em': 255084, 'weird uber driver em and murph': 255084, 'terrible uber driver hot date': 255084, 'pop tv': 255084, 'week 14': 1371239, 'wk 14': 1291564, 'miami': 1291564, 'dolphins': 1291564, 'brady': 1291564, 'cutler': 1291564, 'dolphins highlights': 1291564, 'underdog': 1291564, 'sp:dt=2017-12-11T20:30:00-05:00': 1291564, 'sp:ti:home=Mia': 1291564, 'new technology': 428138, 'gadget': 428138, 'new tech 2017': 428138, 'strange tech': 428138, 'teen gift': 428138, 'new inventions': 428138, 'tech gifts': 428138, 'electronic gadget': 428138, 'future gadgets': 428138, '#M781': 428138, 'unboxing tech': 428138, 'What Is The Most Common Crime In Different Countries': 264902, 'common crime': 264902, 'criminals': 264902, 'crimes in india': 264902, 'crime around the world': 264902, 'murder (cause of death)': 264902, 'murder': 264902, 'homicide': 264902, 'iMac Pro': 1349879, 'iMac Pro Impressions': 1349879, 'iMac Pro vs iMac': 1349879, 'iMac Pro vs': 1144752, 'iMac Pro benchmarks': 1144752, 'iMac': 1144752, 'Pro iMac': 1144752, 'Mac Pro': 1349879, 'iMac Pro vs Mac Pro': 1349879, 'benchmarks': 1144752, 'Geekbench': 1144752, 'iMac Pro setup': 1144752, 'iMac setup': 1144752, 'setup tour': 1144752, 'ventura': 267011, 'ventura strong': 267011, 'burning house': 267011, 'socal': 267011, 'mousse': 242329, 'chocolate mousse': 242329, 'home cooking': 242329, 'dessert recipe': 242329, 'how to make mousse': 242329, 'the voice live semi-final resu': 47367, 'burden down': 128876, 'healthy diet': 297923, 'healthy food': 297923, 'greenhouse gas': 297923, 'carbon emissions': 297923, 'university of california': 297923, 'vegetarian': 297923, 'gluten free': 297923, 'pescatarian': 297923, 'mediterranean food': 297923, 'poultry': 297923, 'uc davis': 297923, 'keaton jones': 3157119, 'keaton jones bullying': 1198075, 'kimberly jones': 35232, 'keaton': 3157119, 'bully': 3157119, 'tennessee': 1198075, 'bullies': 1198075, 'keaton bullying video': 1198075, 'keaton bullying': 1198075, 'keaton jones video': 1198075, 'powerful': 1994276, 'keaton jones chris evans video': 1198075, 'bullied boy video': 1198075, 'keaton jones story': 35232, 'keaton jones interview': 35232, 'Sean in the wild': 169038, 'bbq nyc': 169038, 'barbaque': 169038, 'rooster teeth': 169038, 'barbara dunkelman': 169038, 'burnie burns': 169038, "fletcher's": 169038, 'match fisher': 169038, 'southern cooking': 169038, 'matt fisher': 169038, 'TWICE': 18195959, '트와이스': 18195959, 'Heart Shaker M/V': 18195959, 'Heart Shaker 뮤비': 18195959, '하트셰이커 M/V': 18195959, '하트셰이커 Music Video': 18195959, 'TWICE M/V': 18195959, '트와이스 M/V': 18195959, '트와이스 Music Video': 18195959, 'Merry Happy': 18195959, 'Merry & Happy': 18195959, '메리해피': 18195959, 'Apple iMac Pro': 205127, 'Space Gray': 205127, 'Space Gray iMac Pro': 205127, 'iMac Pro Review': 205127, 'iMac Pro Setup': 205127, 'Desk Setup': 205127, 'iMac Pro 2017': 205127, 'iMac Pro Unboxing': 205127, 'Mac Pro 2017': 205127, 'MKBHD iMac Pro': 205127, 'iMac Pro 10 Core': 205127, 'iMac Pro Benchmark': 205127, 'iMac Pro Speedtest': 205127, 'iMac Pro vs MacBook Pro': 205127, 'iMac Pro Unboxing and Review': 205127, 'Unboxing': 205127, 'Dream Desk': 205127, 'Ultimate Desk Setup': 205127, 'Desk Tour': 205127, 'spotlight': 81509, "and i'm telling you i'm not going": 81509, 'remember me': 81509, 'jhud': 81509, 'Burden Down': 81509, 'Jennifer Hudson': 81509, 'daisyridley': 166495, 'daisy ridleyinterview': 166495, 'markhamill': 166495, 'mark hamillinterview': 166495, 'lauradern': 166495, 'laura derninterview': 166495, 'johnboyega': 166495, 'john boyegainterview': 166495, 'gwendolinechristie': 166495, 'benicio deltoro': 166495, 'rianjohnson': 166495, 'star wars interview': 166495, 'kidsinterview': 166495, 'celebrity (media genre)': 166495, 'midtown explosion': 220120, 'explosion in nyc': 220120, 'nyc explosion': 220120, 'new york port authority': 220120, 'tori': 40117, 'jacob': 40117, 'collier': 40117, 'harmony': 40117, 'iharmu': 40117, 'colab': 40117, 'qwertyuiop': 40117, 'asdfghjkl': 40117, 'zxcvbnm': 40117, 'Perfect': 114325, 'Vice': 242198, 'VNT': 242198, 'VICE News Tonight': 242198, 'VICE News': 242198, 'FUD': 242198, 'investor': 242198, 'investment': 242198, 'crackdown': 242198, 'Jay Kang': 242198, 'bitcoin mining': 242198, 'btc': 242198, 'maccabeats': 77201, 'a cappella': 77201, 'castle on the hill': 77201, 'hanukah': 77201, 'chanukah': 77201, 'candlelight': 77201, 'rey': 427468, 'fin': 211120, 'swtfa': 211120, 'swtlj': 211120, 'mug cake': 211120, 'microwave': 211120, 'no bake': 211120, 'green tea': 211120, 'bun': 211120, 'up do': 211120, 'force': 211120, 'character': 211120, 'no cook': 211120, 'scifi': 211120, 'Biscocho': 211120, 'Azúcar': 211120, 'Receta': 211120, 'Hornear': 211120, 'Pastel': 211120, 'bb-8': 211120, 'century city': 30555, "santa's elves": 30555, 'donation': 30555, 'toys for tots': 30555, 'kyle krieger': 30555, 'my life as eva': 30555, 'Eva Gutowski': 30555, 'the bachelor': 6925, 'the bachelorette': 6925, 'bachelor nation': 6925, 'abc television': 6925, 'reality tv': 6925, 'chris harrison': 6925, 'bachelor': 6925, 'The Bachelor': 6925, 'The Bachelor Season 22': 6925, 'Arie Jr': 6925, 'Tig Notaro': 103742, 'Louis CK': 103742, 'One Mississippi': 103742, 'cyberbully': 1959044, 'cyberbullying': 1959044, 'tears': 1959044, 'Kimberly Jones': 1959044, 'ham down clothes': 1959044, 'pour milk on me': 1959044, 'horrible': 1959044, 'nion County middle schooler': 1959044, 'union city': 1959044, 'highschool': 1959044, 'lunch': 1959044, 'top5': 1959044, 'ricegum': 1959044, '#dramaalert': 1959044, 'ufc': 1959044, 'knockouts': 1959044, 'kyrie': 1959044, 'durant': 1959044, 'draymond': 1959044, 'jre': 1959044, 'joe rogan': 1959044, 'expirience': 1959044, 'podcast': 1959044, 'california fire': 1974319, 'MESSAGE': 1959044, 'SPEECH': 1959044, 'The cuddle squad': 83947, 'blue apron parody': 83947, 'blue apron commercial': 83947, 'commercial parody': 83947, 'fake commercial': 83947, 'banned on TV': 83947, 'banned commercial': 83947, 'ilikeweylie': 62061, 'my best advice to my young self': 62061, 'my best advice': 62061, 'advice to my young self': 62061, 'what i would say to my young self': 62061, 'teenage advice': 62061, 'wahlietv': 62061, 'wah and weylie': 62061, 'weylie': 62061, 'Star Wars The Last Jedi': 10325, "Luke's Journey": 10325, 'George Lucas': 10325, 'Return of the Jedi': 10325, 'Empire Strikes Back': 10325, 'Princess Leia': 10325, 'anthem lights': 71008, 'anthem lights covers': 71008, 'anthem lights mashups': 71008, 'anthem lights medley': 71008, 'mary did you know': 71008, 'us democratic party': 11799, 'al jazeera': 11799, 'aljazeera': 11799, 'us republican party': 11799, 'aljazeera english': 11799, 'al jazeera english': 11799, 'americasnews': 11799, 'united states senate special election in alabama 2017': 11799, 'birmingham': 11799, 'us & canada': 11799, 'shihab rattansi': 11799, 'politics & law': 11799, 'us': 1174642, 'Sheryl Crow': 41174, 'The Dreaming Kind': 41174, 'Be Myself': 41174, 'GMA': 160889, 'Good Morning America': 41174, 'Sandy Hook': 41174, 'Sandy Hook Promise': 41174, 'Donations': 41174, 'Gun Violence': 41174, 'Prevent Gun Violence': 41174, 'Donate': 41174, 'Youth': 41174, 'Children': 41174, 'Safety': 41174, 'Support': 41174, 'rudy mancuso maia mitchell sirens official music video': 1523236, 'maia': 1523236, 'mitchell': 1523236, 'sirens': 1523236, 'Rudy Mancuso & Maia Mitchell - Sirens (Official Music Video)': 1523236, 'rjfs': 22294, 'rocketjump': 22294, 'film school': 22294, 'rocketjump film school': 22294, 'film production': 22294, 'video production': 22294, 'video tutorial': 22294, 'cinema': 22294, 'filmmaking tutorial': 22294, 'the gate deconstructed': 26304, 'One Little Indian Records': 26304, 'Andrew Huang': 26304, 'the gate': 26304, 'nobel peace prize concert': 25383, 'john legend': 25383, 'god only know': 25383, 'peace': 25383, 'norway': 85306, 'sweden': 25383, 'zara larsson': 25383, 'gary vee': 6335, 'meal prep': 6335, 'comedy central': 6335, 'work out': 1062144, 'p90x': 6335, 'dave chappelle': 6335, 'logang': 6335, 'jake paulers': 6335, 'vat19': 14967, 'nvjds': 14967, 'awesome': 14967, 'vat19nvj': 14967, 'vat19nvjds': 14967, 'all countries': 14967, 'madrid': 14967, 'spain': 14967, 'cleveland': 14967, 'protege': 14967, 'prodigy': 14967, 'genius boy': 14967, 'sporcle quiz': 14967, 'sporcle': 14967, 'world record': 14967, 'fastest': 14967, 'ever': 14967, 'google home mini': 137073, 'home mini': 137073, 'google home': 137073, 'aux port': 137073, 'mod': 137073, 'ave': 137073, 'google home aux': 137073, 'google home music': 137073, 'music streaming': 137073, 'micropython': 137073, 'soldering': 137073, 'solder': 137073, 'hair me out': 26898, 'pink hair': 26898, 'shadow root': 26898, 'blonde hair': 26898, 'hair salon': 26898, 'hair dye': 98162, 'pastel hair': 26898, 'pastel pink hair': 26898, 'how to dye hair pink': 26898, 'how to dye your hair pink': 26898, 'colored hair': 26898, 'pink hair dye': 26898, 'unicorn hair': 26898, 'how to pastel pink hair': 26898, 'tranformation': 26898, 'balayage': 26898, 'colored roots': 26898, 'olaplex': 26898, 'pink hair tutorial': 26898, 'hair color ideas': 26898, '2017 hair trend': 26898, 'microbiology': 30134, 'genetics': 30134, 'bioengineering': 30134, 'biochemistry': 30134, 'zoology': 30134, 'anatomy': 30134, 'physiology': 30134, 'havana feat. young thug': 6062169, 'Never Be the Same': 6062169, 'Birdbox studio': 22695, 'Merry Christmas': 22695, 'Free range': 22695, 'Winter': 22695, 'Short film': 22695, 'Marcus Butler': 46706, 'More Marcus': 46706, 'YouTube Rewind': 46706, 'jake Paul': 46706, 'disstrack': 46706, 'lele pons': 46706, 'merchandise': 139989, 'zero integrity': 139989, 'materialism': 139989, 'house prices in london': 139989, 'ducks': 139989, 'tshirts': 139989, 'Jeff VanderMeer': 2110582, 'Southern Reach Trilogy': 2110582, 'Natalie Portman': 2110582, 'Jennifer Jason Leigh': 2110582, 'Tessa Thompson': 2110582, 'Tuva Novotny': 2110582, 'Alex Garland': 2110582, 'Annihilation': 2110582, 'Annihilation Movie': 2110582, 'Release': 2110582, 'Ex Machina': 2110582, '28 Days Later': 2110582, 'Paramount Pictures': 2110582, 'Sci Fi': 2110582, 'Science Fiction Movies': 2110582, 'Action Movies': 2110582, 'New Movie': 2110582, 'lavar ball': 764775, 'big baller brand': 764775, 'lonzo ball': 1188535, 'joe biden': 450201, 'cancer': 450201, 'meghan mccain': 450201, 'beau biden': 450201, 'hunter biden': 450201, 'biden family': 450201, 'jill biden': 450201, 'john mccain': 450201, 'Amber Ruffin': 141112, 'Senate Election': 141112, 'google year in search': 618753, 'year in search': 618753, 'zeitgeist': 618753, 'year in review': 618753, 'biggest moments of 2017': 618753, 'Pitch perfect': 827474, 'pitch perfect the movie': 827474, 'bra': 827474, 'camp': 827474, 'rebel': 827474, 'brittany': 827474, 'serial killer': 827474, 'serial': 827474, 'killer': 827474, 'trait': 827474, 'gwendoline christie': 299914, 'john boyega star wars': 299914, 'fear box vanity fair': 299914, 'bearded dragons': 299914, "what's in the box": 299914, 'john boyega 2017': 299914, 'john boyega interview': 299914, 'gwendoline christie 2017': 299914, 'gwendoline christie star wars': 299914, 'john boyega reaction': 299914, 'finn star wars': 299914, 'angels': 289914, 'we': 289914, 'have': 289914, 'heard': 289914, 'angels we have heard on high': 289914, 'DJ Earworm': 124338, 'United State of Pop': 124338, 'United States of Pop': 124338, 'Top 25': 124338, '2017 hits': 147525, 'top hits 2017': 124338, 'youtube.com': 841724, 'leader': 841724, 'im poppy records': 841724, 'bleach blonde': 841724, 'bleach blonde baby': 841724, 'musical artist': 841724, 'compliment': 358618, 'domhnall gleeson': 358618, 'nice tweets': 358618, 'compliment battle': 358618, 'star wars compliment battle': 358618, 'mark hamill funny': 358618, 'mark hamill funny moments': 358618, 'star wars cast funny': 358618, 'john boyega finn': 358618, 'brian johnson': 60586, 'jenn johnson': 60586, 'bethel music': 60586, 'bethel church': 60586, 'bethel': 60586, 'reckless love': 60586, 'reckless love cory asbury': 60586, 'reckless love steffany': 60586, 'steffany gretzinger steffany gretzinger': 60586, 'reckless love bethel': 60586, 'mini short': 1020652, 'clean your room': 1020652, 'jaclynhill1': 690352, 'contour face': 690352, 'cat eye makeup': 690352, 'cat makeup': 690352, 'holiday gift guide': 690352, 'jaclyn hill palette': 690352, 'christmas gift ideas': 690352, 'g-eazy': 292073, 'halsey': 292073, 'him & i': 292073, 'the beautiful & damned': 292073, 'Selena Gomezes': 146812, 'Selena Gomez meets': 146812, '2 Selena Gomezes Meet for the first time': 146812, 'Selena Gomez music': 146812, 'talking with myself': 146812, 'celebrity name game': 146812, 'Chris Brown feat. AGNEZ MO': 400949, 'On Purpose': 400949, 'rep': 154695, "taylor swift's reputation stadium tour": 154695, 'event': 154695, 'worldwide': 154695, 'north america': 154695, 'end game': 154695, 'anti santa': 396307, 'protest': 396307, 'anti santa protest': 396307, 'christmas protest': 396307, 'santa claus protest': 396307, 'protesters': 396307, 'christmas protesters': 396307, 'protesting': 396307, 'olivia sui': 396307, 'shayne topp': 396307, 'ianh': 396307, 'smosh christmas': 396307, 'smosh santa': 396307, 'smosh santa claus': 396307, 'santa protest': 396307, 'santa boycott': 396307, 'boycotting santa': 396307, 'courntey miller': 396307, 'noah grossman': 396307, 'keith leak jr': 396307, 'alabama election': 163261, 'T-Mobile': 51769, 'T-Mobile Television': 51769, 'Cable': 51769, 'T-Mobile streaming': 51769, 'Streaming service': 51769, 'John Legere': 51769, 'Layer3': 51769, 'cord cutting': 51769, 'cord cutters': 51769, 'get out of cable': 51769, 'T-Mobile TV service': 51769, 'Shows to watch': 51769, 'best streaming service': 51769, 'ThePianoGuys': 150941, 'Secrets': 150941, 'David Archuleta': 150941, 'Peter Hollens': 150941, 'Evynne Hollens': 150941, 'Nathan Pacheco': 150941, 'Tiffany Alvord': 150941, 'Taylor Davis': 150941, 'Claire Crosby': 150941, 'Dave Crosby': 150941, 'Lexi Walker': 150941, 'Angels': 150941, 'Realms of Glory': 150941, 'Peace on Earth': 150941, 'I Saw Three Ships': 150941, 'YouTube New York': 150941, 'O Holy Night': 150941, 'Ave Maria': 150941, 'Mary Did You Know': 150941, 'O Come O Come Emmanuel': 150941, 'When Love Was Born': 150941, 'Carol of the Bells': 150941, 'Pirates of the Caribbean': 150941, 'The Prayer': 150941, 'Jon Schmidt': 150941, 'Steven Sharp Nelson': 150941, 'Al van der Beek': 150941, 'Paul Anderson': 150941, 'Peanuts': 895766, 'Jughead': 895766, 'Charlie Brown': 895766, 'Lucy': 895766, 'Snoopy': 895766, 'Instagram': 895766, 'Peppermint Patty': 895766, 'Linus': 895766, 'Marcy': 895766, 'Linus Van Pelt': 895766, 'Ferdinand': 172994, 'Loreta Frankonyte': 314731, 'Eldad Hagar': 314731, 'Hope For Paws': 314731, 'Have Yourself A Merry Little Christmas': 37058, 'The Thrill Of It All': 37058, 'the 15:17 to paris': 162967, 'the 15:17 to paris movie': 162967, 'clint eastwood': 162967, 'anthony sadler': 162967, 'alek skarlatos': 162967, 'spencer stone': 162967, 'judy greer': 162967, 'ray corasani': 162967, 'pj byrne': 162967, 'tony hale': 162967, 'thomas lennon': 162967, 'wb pics': 162967, 'wb trailers': 162967, '15:17 to paris': 162967, 'the 15:17 to paris trailer': 162967, '15:17 to paris trailer': 162967, '15:17 paris movie': 162967, 'carly and erin': 79099, 'erin and carly': 79099, 'carly incontro': 79099, 'erin gilfoy': 79099, 'seat geek': 79099, 'rams': 79099, 'carly incontro vlogs': 79099, 'erin gilfoy vlogs': 79099, 'carly and erin vlogs': 79099, 'Gwen Stefani Blake Shelton You Make It Feel Like Christmas NBC': 94643, 'election night': 35774, 'vision': 961346, 'vision boards': 961346, 'jenna dewan': 961346, 'jenna dewan tatum': 961346, 'superwoman jenna': 961346, 'lilly singh jenna dewan': 961346, 'past': 961346, 'dewan': 961346, 'boards': 961346, 'embarrasing vid': 961346, 'embarassing past': 961346, 'channing and jenna': 961346, 'jenna channing tatum': 961346, 'Collabs': 961346, '12CollabsofXmas': 961346, 'Xmas Collabs': 961346, 'superwoman collabs': 961346, 'embarrassing past': 961346, 'entropy': 820969, 'cp violation': 820969, 'symmetry': 820969, 'reversal': 820969, 'reversibility': 820969, 'reversible': 820969, 'irreversible': 820969, 'particle': 820969, 'breaks': 820969, 'backwards': 820969, 'second law of thermodynamics': 820969, 'increasing entropy': 820969, 'reverse': 820969, 'meson': 820969, 'kaon': 820969, 'oscillation': 820969, 'chit chat': 24127, 'maya washington': 24127, 'shameless maya': 24127, 'GRWM': 43344, 'Chit chat grwm': 24127, 'pony tail': 24127, 'fenty beauty': 24127, 'life update': 24127, "where i've been": 24127, 'getting ready': 24127, 'chatty grwm': 24127, 'how to deal with failure': 24127, 'going beyond disappointment': 24127, 'girl boss': 24127, 'girl bawse': 24127, 'Christmas Inheritance': 31939, 'Netflix Holiday Original': 31939, 'Netflix Holidays': 31939, 'Romantic Comedy': 31939, 'Rom Com': 31939, 'Holiday Favorites': 31939, 'Romantic Drama': 31939, 'Eliza Taylor': 31939, 'Jake Lacey': 31939, 'Andie MacDowell': 31939, 'PLvahqwMqN4M0_eOsCRg4rInOe-yxm0uAr': 31939, 'k9': 98042, 'dogs who work': 98042, 'border collie': 98042, 'Nature & Animals': 98042, 'K-9': 98042, 'Planet Earth': 98042, "this one's for you": 20956, 'the ones for you': 20956, 'when it rains it pours': 20956, 'hurricane': 20956, 'honky tonk highway': 20956, 'she got the best of me': 20956, 'can I get an outlaw': 20956, 'beer can': 20956, 'used to you': 20956, 'memories are made of': 20956, 'out there': 20956, 'One Number Away': 20956, 'River House Artists/Columbia Nashville': 20956, 'Sci-Fi': 496454, 'ign movie reviews': 496454, 'movie reviews': 496454, 'top videos': 496454, 'last jedi review': 496454, 'star wars last jedi': 496454, 'the jump': 193304, 'rachel nichols': 193304, 'thunder': 193304, 'okc': 193304, 'oklahoma city': 193304, 'oklahoma city thunder': 193304, 'paul george': 193304, 'paul george thunder': 193304, 'paul george trade': 193304, 'Divine Beast Tamer’s Trial': 83487, 'Tamer’s Trial': 83487, 'easy money': 6745, 'how to make money': 6745, 'inflation': 6745, 'monetary policy': 6745, 'stock market': 6745, 'stocks': 6745, 'precious metals': 6745, 'make money online': 6745, 'make money online 2017': 6745, 'make money': 6745, 'how to be rich': 6745, 'money printer': 6745, 'how to make fast money': 6745, 'make money on youtube': 6745, 'american dollar': 6745, 'made': 6745, 'keaton jones bullying video': 1162843, 'usa news': 1162843, 'tennessee news': 1162843, 'nose': 1162843, 'keaton jones facebook': 1162843, 'playground bullying': 1162843, 'wife wakes up': 35244, 'wife sleeping': 35244, 'twelve house': 35244, 'husband and wife': 35244, 'mcconnells': 35244, 'the mcconnells': 35244, 'my wife telling me her weird dream': 35244, 'Sometimes my wife wakes up to tell me her dreams': 35244, 'Drone': 74184, 'best drone': 74184, 'Drone review': 74184, 'DJI': 74184, 'Mavic': 74184, 'DJI Mavic': 74184, 'Platinum': 74184, 'Mavic Pro Platinum': 74184, 'DJI Inspire': 74184, 'DJI Phantom 4': 74184, 'DJI Phantom 4 Pro': 74184, 'Phantom 5': 74184, 'Drone Loudness': 74184, 'How Quiet is drone': 74184, 'dJI Inspire 2': 74184, 'Devin Supertramp': 74184, 'Supertramp': 74184, 'Blade Vortext Interaction': 74184, 'BVI': 74184, 'Devin Graham': 74184, 'travel smarter': 23737, 'bag': 23737, 'patty jenkins': 13941, 'patty jenkins interview': 13941, 'patty': 13941, 'jenkins': 13941, 'patty jenkins wonder woman': 13941, 'wonder woman 2017': 13941, 'wonder woman director': 13941, 'directors roundtable': 13941, '2018 oscars': 13941, 'Glambag': 19217, 'glam bag': 19217, 'ipsy': 19217, 'ipsy glam bag': 19217, 'lamadelynn': 19217, 'madelynn': 19217, 'curly hair': 19217, 'vintage': 19217, 'vegan grwm': 19217, 'cf grwm': 19217, 'cruelty free grwm': 19217, 'lipgloss': 19217, 'monochrome makeup': 19217, 'monochrome': 19217, 'vegan and cruelty free': 19217, 'v and cf': 19217, 'hourglass': 19217, 'hourglass stick foundation': 19217, 'hourglass veil primer': 19217, 'hourglass primer': 19217, 'the atlantic': 15275, 'LA fire': 15275, 'fire chasers': 15275, 'fighting a fire': 15275, 'Stranger things': 85116, 'Stranger things season 3': 85116, 'hugh jackman': 53703, 'disney fox': 53703, 'creed 2': 53703, 'newsflash': 53703, 'Mark elllis': 53703, 'Jared Haibon': 53703, 'kristian harloff': 53703, 'schoolshooting': 26282, 'guncontrol': 26282, 'npr': 26282, 'Ellie': 90378, 'Goulding': 90378, 'Movie review': 63660, 'Lucasfilm': 167857, 'Rob Scallon': 246607, 'Song Challenge': 246607, 'tritone': 246607, 'all tritones': 246607, "the devil's interval": 246607, 'guitar challenge': 246607, 'tritones only': 246607, 'Scallon': 246607, 'Rob Scallon guitar': 246607, 'one fret song': 246607, 'fret song': 246607, 'rob scallon signature guitar': 246607, 'signature guitar': 246607, 'chapman guitars': 246607, 'intervals': 246607, 'musical interval': 246607, 'bass guitar': 246607, 'metal song': 246607, 'deathcore': 246607, 'hardcore': 246607, "devil's interval": 246607, "devil's chord": 246607, 'chord': 246607, 'only one chord': 246607, 'music theory': 246607, 'theory': 246607, 'musical theory': 246607, 'octave': 246607, 'major third': 246607, 'minor second': 246607, 'special election': 5575, 'republican party': 5575, 'roy s. moore': 5575, 'G.O.P.': 5575, 'Penguin Books UK': 51474, 'United Kingdom (Country)': 51474, 'English Language (Human Language)': 51474, 'English': 51474, 'Clarice Lispector (Author)': 51474, 'Lispector': 51474, 'Clarice': 51474, 'Clarice Lispector English': 51474, 'Brazil (Country)': 51474, 'Sao Paulo': 51474, 'author': 51474, 'author interview': 51474, 'Penguin Classics': 51474, 'Penguin Books (Organization)': 51474, 'dr pimple popper': 60120, 'drpimplepopper': 60120, 'drsandralee': 60120, 'sandra lee': 60120, 'pimple': 60120, 'popper': 60120, 'extraction': 60120, 'blackhead': 60120, 'whitehead': 60120, 'milia': 60120, 'ASMR': 60120, 'cyst': 60120, 'facial treatment': 60120, 'pimple popping': 60120, 'how to pop a pimple': 60120, 'dermatology': 60120, 'dermatologist': 60120, 'popping': 60120, 'popaholic': 60120, 'zit': 60120, 'medical school': 60120, 'educational videos for students': 60120, 'medical school videos': 60120, 'Culture 2': 20136000, 'Motor': 20136000, 'Sport': 20136000, 'machine': 159370, 'beginner': 159370, 'beginners': 159370, 'pump': 159370, 'peristaltic': 159370, 'precise': 159370, 'load': 159370, 'strain': 159370, 'gauge': 159370, 'HX711': 159370, 'beverage': 159370, 'crude': 159370, 'nano': 159370, 'L298': 159370, 'l298': 159370, 'lcd': 159370, 'i2c': 159370, 'encoder': 159370, 'tube': 159370, 'wood': 219293, 'enclosure': 159370, 'construction': 159370, 'measure': 159370, 'ml': 159370, 'scale': 159370, 'greatscott': 159370, 'greatscott!': 159370, 'tana mongeau': 1881309, 'havana ft. young thug': 1881309, 'tana': 1881309, 'invisible step': 737557, 'YOUTUBERS REACT TO INVISIBLE BOX CHALLENGE': 737557, 'fcc net neutrality': 985179, 'fcc live': 985179, 'fcc vote': 985179, 'Verizon': 985179, 'ajit pai fcc': 985179, 'irresponsible': 897442, 'hot questions': 1623081, 'explain that gram': 1623081, 'Khalid': 405193, 'Right Hand Music Group': 405193, 'LLC/RCA Records': 405193, 'Saved': 405193, 'Omarosa Manigault Newman': 119715, 'Apprentice': 119715, "you're fired": 119715, 'adviser': 119715, 'communications': 119715, 'anitta j balvin downtown official lyric video ft lele pons juanpa zurita': 4190444, 'downtown': 4190444, 'lyric': 4190444, 'ft': 4190444, 'juanpa': 4190444, 'zurita': 4190444, 'whose dog is it': 4190444, 'Anitta & J Balvin - Downtown (Official Lyric Video) ft. Lele Pons & Juanpa Zurita': 4190444, 'snow globe': 433341, 'giant snow globe': 433341, 'human snow globe': 433341, 'man vs madness': 433341, 'Will': 577476, 'scares': 577476, 'scares on ellen': 577476, 'selfie': 577476, 'fresh prince of bel air': 577476, 'bel': 577476, 'jaden': 577476, 'wax': 577476, 'figure': 577476, 'jada': 874656, 'pinket': 577476, 'scare': 577476, 'tbt': 577476, "rhett link we demand $1 fries on the new mcdonald's dollar menu": 276511, "gmm we demand $1 fries on the new mcdonald's dollar menu": 276511, "rhett link mcdonald's": 276511, "rhett link mcdonald's dollar menu": 276511, "mcdonald's dollar menu": 276511, 'dollar menu': 276511, 'value meal': 276511, 'dollar menu challenge': 276511, "dollar menu mcdonald's": 276511, 'dollar menu mcdonalds list': 276511, 'Omarosa Manigault': 189762, '…Ready For It?': 2608795, 'BloodPop®': 2608795, 'Remix': 2608795, 'dope tech': 635190, 'amazon reviews': 635190, 'hoverboard': 635190, 'new hoverboard': 635190, 'amazon shopping': 635190, 'weird things on amazon': 635190, 'retro arcade': 635190, 'strange amazon': 635190, 'arcade game': 635190, 'amazon tech': 635190, 'hilarious products': 635190, 'racing tech': 635190, '#M772': 635190, 'ridable': 635190, 'mini bike': 635190, 'pies': 1055809, 'desert': 1055809, 'drowning puppy': 329931, 'drowning dog': 329931, 'dog cpr': 329931, 'man gives dog cpr': 329931, 'dog rescue': 329931, 'puppy rescue video': 329931, 'puppy sounds': 329931, 'drowning animal rescue': 329931, 'how to give cpr': 329931, 'how to give dog cpr': 329931, 'puppy saved': 329931, 'puppy rescue 2017': 329931, 'puppy rescue patrol': 329931, 'puppy rescue compilation': 329931, 'puppy rescue stories': 329931, 'cute puppy': 329931, 'Keith': 55467, 'Urban': 55467, 'LP10': 55467, 'romantic comedy': 105229, 'teen drama': 105229, 'adaptation': 105229, 'every day book': 105229, 'david levithan': 105229, 'debby ryan': 105229, 'angourie rice': 105229, 'justice smith': 105229, 'owen teague': 105229, 'mgm': 105229, 'orion pictures': 105229, 'everything everything': 105229, 'if i stay': 105229, 'before i fall': 105229, 'me before you': 105229, 'the vow': 105229, 'single player': 109028, 'callie': 109028, 'great zapfish': 109028, 'octarian': 109028, 'roller': 109028, 'slosher': 109028, 'spatling': 109028, 'dualies': 109028, 'splatoon': 109028, 'handheld': 109028, 'console': 109028, 'Splatoon 2': 109028, 'squid': 109028, 'splat': 109028, 'splat dualies': 109028, 'splat roller': 109028, 'splat charger': 109028, 'cephalopod': 109028, 'shooter': 109028, '3rd person': 109028, 'battlefield': 109028, 'salmonid': 109028, 'squid research lab': 109028, 'power eggs': 109028, 'grizzco': 109028, 'chum': 109028, 'steelhead': 109028, 'amiibo': 109028, 'marie': 109028, 'story mode': 109028, 'squid sisters': 109028, 'multiplayer': 109028, 'salmon run': 109028, 'hoarding': 102110, 'throwing stuff away': 102110, 'life-changing magic': 102110, 'tidying': 102110, 'tidy': 102110, 'declutter': 102110, 'controlling clutter': 102110, 'darth vader': 245957, 'Gwendoline Christie reveal': 171804, 'Alison Brie': 106288, 'GLOW': 106288, 'Co-Stars': 106288, 'Freaked Out': 106288, 'Over': 106288, 'Golden Globe': 106288, 'Nomination': 106288, 'Community': 106288, 'Mad Men': 106288, 'Sleeping With Other People': 106288, 'BoJack Horseman': 106288, 'The Post': 106288, 'The Five Year Engagement': 106288, 'I made him fabulous! | Best Friend Makeover Challenge with boyinaband': 386091, 'Best Friend Makeover Challenge': 386091, 'boyinaband': 386091, 'makeup makeover': 386091, 'glam makeover': 386091, 'best friend makeup challenge': 386091, 'dave boyinaband': 386091, 'makeover challenge': 386091, 'best friend challenge': 386091, 'before and after makeover': 386091, 'makeover reveal': 386091, 'tea spill': 740020, 'spill the tea': 740020, 'simply not logical': 740020, 'simplynailogical tea': 740020, 'tea mix': 740020, 'loose leaf tea': 740020, 'diy tea': 740020, 'tea mugs': 740020, 'tea leaves': 740020, "david's tea": 740020, 'teavana': 740020, 'starbucks': 740020, 'tea time': 740020, 'high tea': 740020, 'tamar & vince': 17002, 'parenthood': 17002, 'relationship problems': 17002, 'DWTS': 17002, 'dancing with the stars': 17002, 'Tamar Braxton': 17002, 'Braxton Family Values': 17002, 'Vincent Herbert': 17002, 'reality show': 17002, 'WEtv': 17002, 'we tv': 17002, 'argue': 17002, 'new staff': 17002, 'new manager': 17002, 'The Miracle Season': 131430, 'The Miracle Season trailer': 131430, 'Helen Hunt': 131430, 'William Hurt': 131430, 'Tiera Skovbye': 131430, 'Danika Yarosh': 131430, 'Erin Moriarty': 131430, 'Burkely Duffield': 131430, 'Nesta Cooper': 131430, 'Jason Gray-Stanford': 131430, 'volleyball': 131430, 'tragic': 131430, 'championship': 131430, 'hope': 131430, 'April 13': 131430, 'LD Entertainment': 131430, 'LD': 131430, 'condition': 292107, 'derealisation': 292107, 'depersonalisation': 292107, 'dissociation': 292107, 'DP/DR': 292107, 'lyme': 292107, 'troll': 44544, 'netflix disney': 44544, 'netflix dreamworks': 44544, 'dreamworks': 44544, 'star wars 2017': 44544, 'The Star Wars Show': 104197, 'Star Wars Show': 104197, 'Andi Gutierrez': 104197, 'Anthony Carboni': 104197, 'Sphero BB-8': 104197, 'Sphero': 104197, 'BB-8': 104197, 'Star Wars Battlefront II': 104197, 'Galaxy of Heroes': 104197, 'lance': 180677, 'stephenson': 180677, 'wall': 180677, 'myles': 180677, 'turner': 180677, 'gary': 180677, 'damian': 180677, 'lillard': 180677, 'dwight': 180677, 'howard': 180677, 'demarcus': 180677, 'cousins': 180677, 'trey': 180677, 'lyles': 180677, 'Ryan Williams': 74609, 'rwilly': 74609, 'r willy': 74609, 'scooter videos': 74609, 'scooter': 74609, 'bmx': 74609, 'skatepark': 74609, 'scooter tricks': 74609, 'ryan williams nitro circus': 74609, 'rwilly vs tanner fox': 74609, 'ryan williams game of scoot': 74609, 'web edit 3': 74609, 'ryan williams 2017': 74609, 'travis pastrana': 74609, 'tanner fox': 74609, 'funk bros': 74609, 'best scooter trick ever': 74609, 'hardest trick ever': 74609, "THE WORLD'S HARDEST SCOOTER TRICK!": 74609, 'Babies': 42445, 'neonatal': 42445, 'intensive': 42445, 'care': 42445, 'unit': 42445, 'premature': 42445, 'medical': 42445, 'needs': 42445, 'mothers': 42445, 'fathers': 42445, 'ICU': 42445, 'volunteer': 42445, 'giving': 42445, 'holding': 42445, 'kangaroo care': 42445, 'hospitals': 42445, 'Feature': 13253, 'companies': 13253, 'micd': 79675, "mic'd": 79675, 'miked': 79675, 'sound': 79675, 'fx': 79675, 'celebrate': 79675, 'celebration': 79675, 'celebrations': 79675, 'jaguars': 79675, 'sideline': 79675, 'field': 79675, 'really b really': 23187, 'kingsley really b really': 23187, 'kingsley thirsty thursday': 23187, 'ask kingsley': 23187, 'kingsley spoils': 23187, 'shut up and go': 23187, '2017 songs': 23187, '2017 rap songs': 23187, 'best songs 2017': 23187, 'best music 2017': 23187, 'best tv 2017': 23187, '2017 year in review': 23187, '2017 pop songs': 23187, 'nighttime routine': 172224, '12 days of christmas': 263615, 'Avocado Slicer': 35753, 'Pineapple Corer': 35753, 'kiwi peeler': 35753, 'unitaskers': 35753, 'best unitasker': 35753, 'how to cut a pineapple': 35753, 'peel a kiwi': 35753, 'how to slice an avocado': 35753, 'best avocado method': 35753, 'best pineapple': 35753, 'knife techniques': 35753, 'serious eats': 35753, 'top chef': 35753, 'best chef': 35753, 'michelin': 35753, 'james beard': 35753, 'avocado': 35753, 'oxo': 35753, 'Anne-Marie': 1092539, 'The Pogues': 1092539, 'Kirsty McColl': 1092539, 'Fairytale Of New York': 1092539, 'Beoga': 1092539, 'beautycrush': 52587, 'three': 52587, 'collabmas': 19968, 'twelve collabs of christmas': 19968, '2017 pop culture': 19968, '2017 moments': 19968, 'best pop culture moments': 19968, 'worst moments': 19968, '2017 rant and rave': 19968, 'end of year rant': 19968, 'golfing vlog': 19968, 'kingsleyyy': 19968, 'holiday vlog': 19968, 'holiday collab': 19968, '12 days of collabs': 19968, 'Kinglsey': 19968, 'extensions': 444366, 'hairstylist': 444366, 'anders nilsson': 11148, 'hockey': 11148, 'nashville predators': 11148, 'predators': 73125, 'vancouver news': 11148, 'goal': 11148, 'nhl': 11148, 'vancouver canucks': 11148, 'p.k. subban': 11148, 'et online': 39399, 'cole': 39399, 'leanne aguilera': 39399, 'barchie': 39399, 'sprouse': 39399, 'apa': 39399, 'chosie': 39399, 'ship': 39399, 'otp': 39399, 'cast': 39399, 'reinhart': 39399, 'lili': 39399, 'beronica': 39399, 'chris hemsworth': 11325, 'matt damon': 11325, 'live with kelly & ryan': 11325, 'look book': 25780, 'new year': 25780, 'new years eve': 25780, 'nye': 25780, 'hannah witton': 25780, 'witton': 25780, 'charity shop': 25780, 'pyjamas': 25780, 'going out': 25780, 'chilling out': 25780, 'casual': 25780, 'vlognukah': 25780, 'former federal prosecutor doug jones': 1087, 'former judge roy moore': 1087, 'Alabama senate seate': 1087, 'U.S. news': 1087, 'elections news': 1087, 'Congress news': 1087, 'south': 1087, 'african americans': 1087, 'jones alabama': 1087, 'doug jones alabama': 1087, 'Big Cat Week': 61977, 'Jaguar': 61977, 'Caiman': 61977, 'Hunting': 61977, 'Predator': 61977, 'Scarface': 61977, 'the top predator': 61977, 'Scarface the jaguar': 61977, 'takes': 61977, 'cousin': 61977, 'the crocodile': 61977, 'AIRS': 61977, 'SUNDAY': 61977, 'DECEMBER': 61977, 'top predator': 61977, 'land of predators': 61977, 'takes down': 61977, 'a caiman': 61977, 'SUNDAY DECEMBER': 61977, 'CROC AIRS': 61977, 'Cat Week': 61977, 'Week': 61977, 'Big Cat': 61977, 'vs. Caiman': 61977, 'predator in a land': 61977, 'the jaguar': 61977, 'the top': 61977, 'cousin of the crocodile': 61977, 'PLNxd9fYeqXeYQaV1z9-tWQXJBeq_s8hX9': 61977, 'PLNxd9fYeqXeZULIsOZhcXhvmrfoPA65e3': 61977, 'Omarosa': 1684275, 'FCC Commissioner': 1684275, 'Obama': 1684275, 'giant coffee cup': 1684275, 'John Kelly': 1684275, 'Chief of Staff': 1684275, 'popcorn': 493279, 'cuisine': 493279, 'ポップコーン': 493279, '米': 493279, '猫': 493279, 'クッキング': 493279, 'puffed': 493279, 'junskitchen': 493279, "Jun's Kitchen": 493279, 'juns Kitchen': 493279, 'espn first': 423760, 'stephen a.': 423760, 'stephen a': 423760, 'max kellerman': 423760, 'max': 423760, 'speculates': 423760, 'told': 423760, 'lonzo': 423760, 'ball': 2822931, 'after': 423760, 'lakers': 423760, 'pinkett-smith': 297180, 'bel-air': 297180, 'fresh prince of bel-air': 297180, 'jada pinkett-smith': 297180, 'Florida Man': 279522, 'Stole': 279522, 'Kevin Hart': 279522, 'NYC Marathon': 279522, 'Central Intelligence': 279522, 'Grudge Match': 279522, 'wrecking': 2399171, 'grand': 2399171, 'thegrandtour': 2399171, 'sponsored': 2399171, 'spon': 2399171, '4 ton': 2399171, '9000lbs': 2399171, 'hot97': 757804, 'hot97app': 757804, 'US': 757804, 'United States': 757804, 'HOT97.com': 757804, "Conan O'Brien Conan Conan (TV Series) TBS (TV Channel) Team Coco Celebrity Interviews Kate Hudson": 53818, 'doll': 74186, 'jesus': 74186, 'cat-animals': 74186, 'offbeat': 74186, 'Nativity': 74186, 'baby jesus': 74186, 'steal': 74186, 'stage': 74186, 'church': 74186, 'sheep': 74186, 'preschool': 74186, 'ie trending': 74186, 'losing sleep': 62212, 'found you': 62212, 'Kane Brown Duet with Chris Young': 62212, 'Setting the Night On Fire': 62212, 'john cena interview': 1128702, 'john cena funny': 1128702, 'john cena autocomplete interview': 1128702, 'ferdinand movie': 1128702, 'ferdinand the bull': 1128702, 'john cena ferdinand': 1128702, 'wwe john cena': 1128702, 'pro wrestling': 1128702, 'professional wrestling john cena': 1128702, 'john cena autocomplete': 1128702, 'john cena wired': 1128702, 'john cena funny moments': 1128702, "you can't see me": 1128702, "john cena you can't see me": 1128702, 'john cena minecraft': 1128702, 'Polka': 88236, 'polka king': 88236, 'polka': 88236, 'sundance film festival': 88236, 'jan lewan': 88236, 'sundance 2017': 88236, 'the polka king sundance': 88236, 'polkamain': 88236, "Giving my 13 year old niece the 'emo phase'": 71264, 'emo make up': 71264, 'make up transformation': 71264, 'tom harlock': 71264, 'vine 2': 71264, 'tom harlock vine': 71264, 'tom harlock niece': 71264, 'tom harlock hair': 71264, 'niece': 71264, 'family vlog': 71264, 'paramore': 71264, 'mcr': 71264, 'g note': 71264, 'idk what to tag': 71264, 'i love cats so much': 71264, 'long vlog': 71264, 'weekend': 71264, 'letting a 12 year old tattoo me': 71264, 'emo cringe': 71264, '207': 71264, 'Middle': 236457, 'Ditto': 236457, 'Singer/Songwriter': 236457, '5 days of': 59923, 'millenial': 59923, 'social experiements': 59923, 'what i learned': 59923, 'cozy': 59923, 'hygge': 59923, 'minimalism': 59923, 'minimalist living': 59923, 'scandinavia': 59923, 'lifestyle design': 59923, 'danes': 59923, 'nordic': 59923, 'self-care': 59923, 'mindfulness': 59923, 'finland': 59923, 'copenhagen': 59923, 'blizzard': 59923, 'comfy': 59923, 'winter clothing haul': 59923, 'cold weather': 59923, 'christmas season': 59923, 'r29 lucie': 59923, 'amy farrah fowler': 14373, 'tofu': 92578, 'fried': 92578, 'icecream': 92578, 'pudding': 92578, '5 ingredient': 92578, 'fried tofu': 92578, '99 CENT': 224511, 'bargain': 224511, 'teacups': 71423, 'Dirty Sexy Money': 553971, 'Guetta': 553971, 'Afrojack': 553971, 'Charli XCX': 600633, 'XCX': 553971, 'Charli': 553971, 'Montana': 553971, 'Franch': 553971, 'Dirty': 553971, 'Sexy': 553971, 'Hit': 553971, 'I trained like Rey from star wars': 29609, 'training like a jedi': 29609, 'light saber': 29609, 'star wars: battlefront 2': 29609, 'battlefront': 29609, 'battlefront video game': 29609, 'jj abrams': 29609, 'michelle khare trained like rey': 29609, 'michelle khare star wars': 29609, 'mk ultra star wars': 29609, 'star wars work out': 29609, 'daisy ridley workout': 29609, 'rey star wars': 29609, 'backseat': 46662, 'back': 46662, 'seat': 46662, 'Carly Rae Jepsen': 46662, 'pop2': 46662}


Funkcja przygotowująca słownik tag_views dla danej kolumny


```python
def get_tag_counts(dataframe, collumn):
    global tag_views
    tag_views = {}
    all_tags = dataframe['tags']
    first = dataframe[collumn]
    for j in range(0,len(all_tags)):
      try:
        tags = all_tags[j].split("|\"")
      except(Exception):
        continue
      for i in range(1,len(tags)):
        tags[i] = tags[i][:-1]
      for i in range(0,len(tags)):
        try:
          insert_to_tag_view(tags[i], first[i])
        except(Exception):
          continue
    return tag_views
```


```python
print(get_tag_counts(cleared, 'views'))
```



Funkcja wypisująca rezultat wywołania powyższej funkcji ( top 50 )


```python
def print_tag_counts(dataframe, collumn):
    view = get_tag_counts(dataframe, collumn)
    view = sorted(view.items(), key = lambda kv : kv[1])
    view = view[-1::-1]
    print()
    for i in range(0,50):
        print(str(view[i][0]) + ":\t" + str(view[i][1]))
    print()
```

Funkcja wypisuja średnie stosunki wartości pierwszej i drugiej kolumny


```python
def get_tag_probabilities(dataframe, collumn_upper, collumn_lower):
    global tag_views
    tag_views = {}
    all_tags = dataframe['tags']
    upper = dataframe[collumn_upper]
    lower = dataframe[collumn_lower]
    for i in range(0, len(all_tags)):
        try:
          tags = all_tags[i].split("|\"")
        except(Exception):
          continue
        for i in range(1, len(tags)):
            tags[i] = tags[i][:-1]
        for i in range(0, len(tags)):
            try:
              insert_to_tag_view(tags[i], [ upper[i] / lower[i] ])
            except:
              continue

    for key, val in tag_views.items():
        tag_views[key] = sum(val) / len(val)
    return tag_views
```

Funkjca wypisująca rezultat wywołania powyższej funkcji


```python
def print_tag_probabilities(dataframe, collumn_upper, collumn_lower):
    view = get_tag_probabilities(dataframe, collumn_upper, collumn_lower)
    view = sorted(view.items(), key = lambda kv : kv[1])
    view = view[-1::-1]
    print()
    for i in range(0,50):
        print(str(view[i][0]) + ":\t" + str(view[i][1]))
    print()
```

Wyświetlenie top 50 wyświetlań dla tagów


```python
print_tag_counts(cleared, 'views')
```

    
    funny:	168212299
    comedy:	122727900
    Colbert:	89360152
    news:	72765935
    Pop:	64332181
    talk show:	59565260
    Comedy:	56351459
    music:	53974578
    live:	52987668
    explain:	51319370
    basketball:	50439569
    [none]:	49392684
    r29:	47871510
    television:	46577101
    beauty:	44948169
    pop:	44526841
    interview:	42222591
    Jimmy Fallon:	41867685
    christmas:	40780639
    video:	40507586
    comedian:	40137211
    Trailer:	39681432
    lol:	39248170
    official:	39168075
    celebrities:	38959152
    highlights:	38724200
    vox:	38700528
    Stephen Colbert:	38174704
    show:	37000356
    improv:	36688134
    NBC:	36518229
    refinery 29:	36281745
    trailer:	36090658
    offense:	35362200
    liza:	35146813
    The Late Late Show:	35002240
    movie:	34948281
    makeup routine:	34742328
    Sausage Party:	34317062
    basejump:	34317062
    Hairspray:	34317062
    The Voice:	34317062
    season 8:	34317062
    politics:	34287971
    Football:	33815540
    2017:	32780651
    sketch:	32034672
    cooking:	31580544
    entertainment:	31177041
    celebrity:	30892172
    


Wyświetlenie top 50 likes dla tagów


```python
print_tag_counts(cleared, 'likes')
```

    
    funny:	5919911
    comedy:	5109900
    Colbert:	4088924
    [none]:	3796782
    Pop:	2938698
    news:	2909076
    live:	2715013
    explain:	2349182
    celebrities:	2333659
    television:	2297763
    music:	2295028
    beauty:	2288034
    r29:	2190495
    basketball:	2163640
    pop:	2162295
    talk show:	2155829
    video:	2065454
    interview:	2002275
    late night show:	1851290
    lol:	1807688
    celebrity:	1782163
    technology:	1777043
    2017:	1733003
    politics:	1711957
    Jimmy Fallon:	1709672
    official:	1658771
    improv:	1653121
    liza:	1633020
    Trailer:	1630337
    offense:	1619017
    humor:	1618249
    Stephen Colbert:	1608886
    karaoke:	1608530
    sports:	1604645
    letterman:	1604030
    makeup routine:	1603307
    Sausage Party:	1574838
    basejump:	1574838
    Hairspray:	1574838
    The Voice:	1574838
    season 8:	1574838
    christmas:	1562544
    vox:	1554960
    sketch:	1525400
    night:	1499845
    refinery 29:	1457775
    afc:	1456161
    wrestling:	1428485
    Comedy:	1423969
    family:	1383881
    


Wyświetlenie top 50 dislike dla tagów


```python
print_tag_counts(cleared, 'dislikes')
```

    
    funny:	256868
    comedy:	214220
    [none]:	195756
    Colbert:	149492
    news:	117797
    Jimmy Fallon:	107448
    Pop:	107114
    basketball:	100742
    pop:	100019
    Stephen Colbert:	99496
    vox:	98336
    music:	93897
    lol:	93219
    Comedy:	92541
    refinery 29:	92190
    Trailer:	90673
    improv:	89872
    liza:	89831
    beauty:	88866
    makeup routine:	87346
    Sausage Party:	86840
    basejump:	86840
    Hairspray:	86840
    The Voice:	86840
    season 8:	86840
    explain:	86787
    The Late Late Show:	86394
    live:	84931
    r29:	80085
    Football:	78403
    talk show:	77478
    highlights:	76973
    interview:	76959
    love:	71553
    politics:	71239
    official:	70138
    kimmel:	67606
    recipe:	67012
    Seth Meyers:	66799
    trump:	66461
    2017:	66444
    comedian:	66372
    The Last Jedi:	66317
    vlog:	65423
    News:	65201
    entertainment:	63408
    Christmas:	63018
    technology:	62650
    family:	62537
    christmas:	61821
    


Wyświetlenie top 50 komentarzy dla tagów


```python
print_tag_counts(cleared, 'comment_count')
```

    
    [none]:	1052964
    funny:	762304
    comedy:	500588
    pop:	320213
    celebrities:	312953
    music:	310731
    beauty:	302634
    video:	294512
    jimmy:	287172
    lol:	286063
    The Tonight Show:	279399
    2017:	272153
    television:	268003
    liza:	267895
    Pop:	266842
    improv:	262401
    makeup routine:	257441
    vox.com:	255264
    nba:	254133
    news:	252210
    Sausage Party:	251764
    basejump:	251764
    Hairspray:	251764
    The Voice:	251764
    season 8:	251764
    late night show:	245252
    James Corden:	243634
    Netflix:	240272
    refinery29:	239310
    live:	233585
    Jimmy Fallon:	231905
    celebrity:	231448
    talk show:	231095
    Colbert:	229068
    Trailer:	228054
    The Late Show:	223356
    christmas:	218534
    technology:	218260
    how to:	217455
    Stephen Colbert:	213941
    basketball:	211973
    politics:	211616
    night:	205673
    family:	205022
    comedian:	203320
    vox:	203248
    highlights:	201621
    Comedy:	201074
    afc:	193977
    refinery 29:	190545
    


Wyświetlenie top 50 prawdopodobieństwa wciśnięcia przycisku like przez odwiedzającego dla tagu


```python
print_tag_probabilities(cleared,'likes','views')
```

    
    star wars: battlefront 2:	0.16607182852372518
    icecream:	0.16607182852372518
    social experiements:	0.16607182852372518
    stage:	0.16607182852372518
    HOT97.com:	0.16607182852372518
    max:	0.16607182852372518
    米:	0.16607182852372518
    doug jones alabama:	0.16607182852372518
    serious eats:	0.16607182852372518
    2017 rap songs:	0.16607182852372518
    micd:	0.16607182852372518
    care:	0.16607182852372518
    ryan williams 2017:	0.16607182852372518
    Sphero:	0.16607182852372518
    dreamworks:	0.16607182852372518
    volleyball:	0.16607182852372518
    simplynailogical tea:	0.16607182852372518
    makeover challenge:	0.16607182852372518
    justice smith:	0.16607182852372518
    puppy rescue video:	0.16607182852372518
    rhett link mcdonald's:	0.16607182852372518
    whose dog is it:	0.16607182852372518
    fcc vote:	0.16607182852372518
    tana mongeau:	0.16607182852372518
    pump:	0.16607182852372518
    author:	0.16607182852372518
    Scallon:	0.16607182852372518
    Jared Haibon:	0.16607182852372518
    cf grwm:	0.16607182852372518
    Drone Loudness:	0.16607182852372518
    how to make money:	0.16607182852372518
    paul george trade:	0.16607182852372518
    last jedi review:	0.16607182852372518
    breaks:	0.16607182852372518
    Angels:	0.16607182852372518
    Shows to watch:	0.16607182852372518
    ianh:	0.16607182852372518
    halsey:	0.16607182852372518
    compliment battle:	0.16607182852372518
    musical artist:	0.16607182852372518
    john boyega interview:	0.16607182852372518
    jake Paul:	0.16607182852372518
    blonde hair:	0.16607182852372518
    cleveland:	0.16607182852372518
    Rudy Mancuso & Maia Mitchell - Sirens (Official Music Video):	0.16607182852372518
    Youth:	0.16607182852372518
    americasnews:	0.16607182852372518
    Empire Strikes Back:	0.16607182852372518
    ham down clothes:	0.16607182852372518
    Mac Pro 2017:	0.16607182852372518
    


Wyświetlenie top 50 prawdopodobieństwa wciśnięcia przycisku dislike przez odwiedzającego dla tagu


```python
print_tag_probabilities(cleared,'dislikes','views')
```

    
    207:	0.04705022386014071
    amiibo:	0.04705022386014071
    sp:ti:home=Mia:	0.04705022386014071
    floor is lava:	0.04705022386014071
    file:	0.04705022386014071
    how to get a six pack:	0.04705022386014071
    motoring:	0.04705022386014071
    chewbacca:	0.04705022386014071
    lauder:	0.04705022386014071
    making beats:	0.04705022386014071
    sp:dt=2017-11-27T20:30:00-05:00:	0.04705022386014071
    toadstool:	0.04705022386014071
    switzerland:	0.04705022386014071
    source:	0.04705022386014071
    sp:ti:away=GB:	0.04705022386014071
    s’awesome sauce:	0.04705022386014071
    super powers:	0.04705022386014071
    darkwa:	0.04705022386014071
    riding:	0.04705022386014071
    Cat Birthday:	0.04705022386014071
    youtuber facts:	0.04705022386014071
    sp:dt=2017-11-12T16:25:00-05:00:	0.026170614575573
    living:	0.025864546140855343
    sp:ti:away=Phi:	0.02566892800659326
    Look What You Made Me Do:	0.0249332177216707
    dez:	0.024387366726362658
    pack:	0.023864663977633267
    school of life:	0.023779123453519957
    sp:ti:away=OhioSt:	0.019006699190905325
    indie:	0.016316122743589477
    fast food:	0.01296260166933944
    compilation:	0.012474360104020572
    sp:vl=en-US:	0.01214087276300851
    clean:	0.010278490748861583
    driving:	0.009920348582982831
    electronics:	0.007753226638696114
    sp:li=nfl:	0.005697928510421931
    marie:	0.005519337928628606
    greatscott:	0.005519337928628606
    jre:	0.005519337928628606
    floor:	0.005519337928628606
    layout:	0.005519337928628606
    expansion:	0.005519337928628606
    how to get abs:	0.005519337928628606
    convertible:	0.005519337928628606
    cleaning:	0.005519337928628606
    emma:	0.005519337928628606
    luigi:	0.005519337928628606
    action camera:	0.005519337928628606
    beasley:	0.005519337928628606
    


Wyświetlenie top 50 prawdopodobieństwa napisania komentarza przez odwiedzającego dla tagu



```python
print_tag_probabilities(cleared,'comment_count','views')
```

    
    michelle khare star wars:	0.02418149942568939
    scandinavia:	0.02418149942568939
    sundance film festival:	0.02418149942568939
    john cena minecraft:	0.02418149942568939
    after:	0.02418149942568939
    top predator:	0.02418149942568939
    otp:	0.02418149942568939
    kingsleyyy:	0.02418149942568939
    2017 year in review:	0.02418149942568939
    turner:	0.02418149942568939
    argue:	0.02418149942568939
    starbucks:	0.02418149942568939
    the vow:	0.02418149942568939
    puppy rescue compilation:	0.02418149942568939
    dollar menu mcdonalds list:	0.02418149942568939
    HX711:	0.02418149942568939
    popping:	0.02418149942568939
    hourglass:	0.02418149942568939
    Devin Graham:	0.02418149942568939
    Rom Com:	0.02418149942568939
    how to deal with failure:	0.02418149942568939
    kaon:	0.02418149942568939
    vision boards:	0.02418149942568939
    O Come O Come Emmanuel:	0.02418149942568939
    boycotting santa:	0.02418149942568939
    star wars cast funny:	0.02418149942568939
    Action Movies:	0.02418149942568939
    pink hair dye:	0.02418149942568939
    sporcle:	0.02418149942568939
    highschool:	0.02418149942568939
    santa's elves:	0.02418149942568939
    rooster teeth:	0.02418149942568939
    poultry:	0.02418149942568939
    countries in the world:	0.02418149942568939
    Jose Padilha:	0.02418149942568939
    Kylo:	0.02418149942568939
    music information retrieval:	0.02418149942568939
    best videos compilation:	0.02418149942568939
    diets:	0.02418149942568939
    OUTFITS:	0.02418149942568939
    polar bear canada:	0.02418149942568939
    be a hero:	0.02418149942568939
    anthonypadilla:	0.02418149942568939
    #RHOA:	0.02418149942568939
    MSNBC LIVE:	0.02418149942568939
    kimmy gibbler:	0.02418149942568939
    heart surgery:	0.02418149942568939
    joker voice:	0.02418149942568939
    eleanor mcmanus:	0.02418149942568939
    in real life:	0.02418149942568939
    
