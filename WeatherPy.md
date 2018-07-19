
# WeatherPy

Utilizing the Open Weather API on July 18, 2018, data surverying 557 random cities was compiled on humidity%, temperature (F), cloud%, and wind speed (mph). In surveying the data, there (unsurprisingly) appeared to be a correlation between latitude and temperature. When visualized in as scatter plot, the points formed a curve similar to a standard distribution with the Tropic of Cancer (23°26ʹ Latitude) at the center. Temperature tended to decrease the further latitude position the city fell from this point. This makes sense considering that the data was compiled during the Northern Hemisphere summer.

Cloud cover based on Latitude was a striking visualization. Latitude did not appear to have a strong influence on cloudiness, rather it raised questions about how cloud cover it measured. When compared to the visuals of humidity and wind speed, which both are rather nebulous, cloud levels seemed to bucket in particular % levels. The resulting image appeared to be a series of lines with scattered points between them. These "lines" of data populated around 0%, 20%, 40%, 75%, and 90%. Since cloud cover is generally measured in oktas, which is on a scale of 0-8, it follows that visualzations would populate along particular lines.

Though the data was compiled randomly, it was striking to see a preference for cities in the Northern Hemisphere. Latitude values ranged from -60 to 80 with what looked like 2/3 of the data points populating >0. In a way, the data visualized the landmass distibution of Earth itself: ~68% Northern Hemisphere, ~32% Southern Hemisphere. This indicates that we would see similar patterns when surveying Longitude data, as the landmass distribution is roughly the same East vs West.


```python
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import requests
import time
from pprint import pprint

# Import API key
import api_keys

# Incorporated citipy to determine city based on latitude and longitude
from citipy import citipy
```

## Generate Cities List


```python
# List for holding lat_lngs and cities
lat_lngs = []
cities = []

# Create a set of random lat and lng combinations
lats = np.random.uniform(low=-90.000, high=90.000, size=1500)
lngs = np.random.uniform(low=-180.000, high=180.000, size=1500)
lat_lngs = zip(lats, lngs)

# Identify nearest city for each lat, lng combination
for lat_lng in lat_lngs:
    city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name
    
    # If the city is unique, then add it to a our cities list
    if city not in cities:
        cities.append(city)

# Print the city count to confirm sufficient count
len(cities)
```




    620



## Perform API Calls


```python
#create an empty list to store dictionaries in order to build DataFrame
w_list = []

# OpenWeatherMap API Key
api_key = api_keys.api_key

#establish base url
url = "http://api.openweathermap.org/data/2.5/weather?"

#outline parameters
params = {"APPID": api_key,
         "units": "Imperial"}

print("Initializing Data Retrieval")
print("-" * len("Initializing Data Retrieval"))

for index, city in enumerate(cities):
    while True:
        try:
            params["q"] = city
            response = requests.get(url, params=params).json()
            if response['cod'] == '404':
                print('City not found. Moving on.')
                time.sleep(1)
            else:
                #print(f'Retrieving record {index} of {len(cities)} | {city}, id:{test["id"]}')
                print(f'Retrieving record {index + 1} of {len(cities)} | {city}, id:{response["id"]}')
                weather = {'City Name': response['name'],
                           'Cloud':response['clouds']['all'],
                           'Country': response['sys']['country'],
                           'Date': response['dt'],
                           'Humidity': response['main']['humidity'],
                           'Latitude': response['coord']['lat'],
                           'Longitude': response['coord']['lon'],
                           'Maximum Temperature': response['main']['temp_max'], 'Wind Speed': response['wind']['speed']}
                w_list.append(weather)
                time.sleep(1)
        except TimeoutError:
                sleep(10)
                continue
        break
                
            
        
print("-" * len("Data retrieval complete"))
print("Data retrieval complete")

weather_raw = pd.DataFrame(w_list)
weather_raw.head()
    
```

    Initializing Data Retrieval
    ---------------------------
    Retrieving record 1 of 620 | khatanga, id:2022572
    Retrieving record 2 of 620 | tuktoyaktuk, id:6170031
    Retrieving record 3 of 620 | victoria, id:1733782
    Retrieving record 4 of 620 | bodden town, id:3580733
    Retrieving record 5 of 620 | cermik, id:318766
    Retrieving record 6 of 620 | naze, id:2337542
    City not found. Moving on.
    Retrieving record 8 of 620 | alofi, id:4036284
    Retrieving record 9 of 620 | thompson, id:6165406
    Retrieving record 10 of 620 | carazinho, id:3466978
    Retrieving record 11 of 620 | cumberland, id:5221703
    Retrieving record 12 of 620 | hilo, id:5855927
    City not found. Moving on.
    Retrieving record 14 of 620 | saint-philippe, id:6138908
    Retrieving record 15 of 620 | saint-ambroise, id:3025602
    Retrieving record 16 of 620 | longyearbyen, id:2729907
    Retrieving record 17 of 620 | vanavara, id:2013727
    Retrieving record 18 of 620 | bluff, id:2175403
    Retrieving record 19 of 620 | paracuru, id:3393115
    Retrieving record 20 of 620 | rio grande, id:3451138
    Retrieving record 21 of 620 | wyndham, id:2639447
    Retrieving record 22 of 620 | vaini, id:1273574
    Retrieving record 23 of 620 | bredasdorp, id:1015776
    Retrieving record 24 of 620 | prince rupert, id:6113406
    Retrieving record 25 of 620 | salalah, id:286621
    Retrieving record 26 of 620 | sayyan, id:70979
    Retrieving record 27 of 620 | chokurdakh, id:2126123
    Retrieving record 28 of 620 | nemuro, id:2128975
    City not found. Moving on.
    Retrieving record 30 of 620 | butaritari, id:2110227
    Retrieving record 31 of 620 | carnarvon, id:1014034
    Retrieving record 32 of 620 | nantucket, id:4944903
    Retrieving record 33 of 620 | esperance, id:3573739
    Retrieving record 34 of 620 | avarua, id:4035715
    Retrieving record 35 of 620 | orangeburg, id:4590184
    Retrieving record 36 of 620 | arona, id:3182812
    Retrieving record 37 of 620 | pucallpa, id:3693345
    Retrieving record 38 of 620 | honningsvag, id:779554
    Retrieving record 39 of 620 | rikitea, id:4030556
    Retrieving record 40 of 620 | tasiilaq, id:3424607
    Retrieving record 41 of 620 | sidi ali, id:2487130
    Retrieving record 42 of 620 | kapaa, id:5848280
    Retrieving record 43 of 620 | mutoko, id:884927
    Retrieving record 44 of 620 | ushuaia, id:3833367
    Retrieving record 45 of 620 | kruisfontein, id:986717
    Retrieving record 46 of 620 | mataura, id:6201424
    Retrieving record 47 of 620 | hithadhoo, id:1282256
    Retrieving record 48 of 620 | mahebourg, id:934322
    Retrieving record 49 of 620 | iqaluit, id:5983720
    Retrieving record 50 of 620 | puerto ayora, id:3652764
    Retrieving record 51 of 620 | punta arenas, id:3874787
    Retrieving record 52 of 620 | svetlaya, id:2015852
    Retrieving record 53 of 620 | walvis bay, id:3359638
    Retrieving record 54 of 620 | tautira, id:4033557
    City not found. Moving on.
    Retrieving record 56 of 620 | cidreira, id:3466165
    Retrieving record 57 of 620 | cadillac, id:4987617
    Retrieving record 58 of 620 | lagoa, id:2267254
    Retrieving record 59 of 620 | ishigaki, id:1861416
    City not found. Moving on.
    City not found. Moving on.
    Retrieving record 62 of 620 | cockburn town, id:3576994
    Retrieving record 63 of 620 | crotone, id:2524881
    Retrieving record 64 of 620 | caraballeda, id:3646767
    Retrieving record 65 of 620 | kirkwall, id:2645198
    Retrieving record 66 of 620 | kailua, id:5847486
    Retrieving record 67 of 620 | wembley, id:2634549
    Retrieving record 68 of 620 | torbay, id:6167817
    Retrieving record 69 of 620 | isangel, id:2136825
    Retrieving record 70 of 620 | itoman, id:1861280
    City not found. Moving on.
    City not found. Moving on.
    Retrieving record 73 of 620 | buchanan, id:2278158
    Retrieving record 74 of 620 | lucapa, id:145724
    Retrieving record 75 of 620 | cape town, id:3369157
    Retrieving record 76 of 620 | wer, id:1252885
    Retrieving record 77 of 620 | mount isa, id:2065594
    Retrieving record 78 of 620 | port alfred, id:964432
    Retrieving record 79 of 620 | ponta do sol, id:3453439
    Retrieving record 80 of 620 | springbok, id:3361142
    Retrieving record 81 of 620 | east london, id:1006984
    Retrieving record 82 of 620 | arrifes, id:3373329
    Retrieving record 83 of 620 | lebu, id:344979
    City not found. Moving on.
    Retrieving record 85 of 620 | komsomolskiy, id:1486910
    Retrieving record 86 of 620 | yellowknife, id:6185377
    Retrieving record 87 of 620 | havelock, id:4470244
    Retrieving record 88 of 620 | hermanus, id:3366880
    Retrieving record 89 of 620 | nikolskoye, id:546105
    Retrieving record 90 of 620 | kaitangata, id:2208248
    Retrieving record 91 of 620 | mehamn, id:778707
    Retrieving record 92 of 620 | kavieng, id:2094342
    Retrieving record 93 of 620 | shaoguan, id:1795874
    Retrieving record 94 of 620 | escanaba, id:4992232
    Retrieving record 95 of 620 | bambous virieux, id:1106677
    Retrieving record 96 of 620 | tullow, id:2961079
    City not found. Moving on.
    City not found. Moving on.
    Retrieving record 99 of 620 | klaksvik, id:2618795
    Retrieving record 100 of 620 | nuuk, id:3421319
    Retrieving record 101 of 620 | faanui, id:4034551
    Retrieving record 102 of 620 | castro, id:3896218
    City not found. Moving on.
    Retrieving record 104 of 620 | abeche, id:245785
    Retrieving record 105 of 620 | kroya, id:7870153
    Retrieving record 106 of 620 | kavaratti, id:1267390
    City not found. Moving on.
    Retrieving record 108 of 620 | albany, id:5106834
    Retrieving record 109 of 620 | kodiak, id:4407665
    Retrieving record 110 of 620 | atuona, id:4020109
    Retrieving record 111 of 620 | mazatlan, id:3996322
    City not found. Moving on.
    Retrieving record 113 of 620 | zilupe, id:453850
    Retrieving record 114 of 620 | wencheng, id:6972233
    Retrieving record 115 of 620 | puerto rico, id:3429732
    Retrieving record 116 of 620 | khani, id:610864
    Retrieving record 117 of 620 | la ronge, id:6050066
    Retrieving record 118 of 620 | siniscola, id:3166509
    Retrieving record 119 of 620 | bethel, id:5880568
    Retrieving record 120 of 620 | clyde river, id:5924351
    City not found. Moving on.
    Retrieving record 122 of 620 | provideniya, id:4031574
    Retrieving record 123 of 620 | aitape, id:2101169
    Retrieving record 124 of 620 | airai, id:1651810
    Retrieving record 125 of 620 | jamestown, id:2069194
    Retrieving record 126 of 620 | henties bay, id:3356832
    Retrieving record 127 of 620 | ushtobe, id:1517637
    Retrieving record 128 of 620 | narsaq, id:3421719
    Retrieving record 129 of 620 | sydney, id:2147714
    Retrieving record 130 of 620 | norman wells, id:6089245
    Retrieving record 131 of 620 | thinadhoo, id:1337610
    Retrieving record 132 of 620 | lata, id:1253628
    Retrieving record 133 of 620 | hit, id:95788
    Retrieving record 134 of 620 | coquimbo, id:3893629
    Retrieving record 135 of 620 | uglich, id:479532
    Retrieving record 136 of 620 | lembeni, id:156025
    Retrieving record 137 of 620 | hopelchen, id:3527065
    Retrieving record 138 of 620 | jodhpur, id:1268865
    City not found. Moving on.
    Retrieving record 140 of 620 | huarmey, id:3939168
    Retrieving record 141 of 620 | boende, id:218680
    Retrieving record 142 of 620 | dalbandin, id:1180729
    City not found. Moving on.
    City not found. Moving on.
    Retrieving record 145 of 620 | betanzos, id:3128071
    Retrieving record 146 of 620 | bara, id:2347758
    Retrieving record 147 of 620 | kalmunai, id:1242110
    Retrieving record 148 of 620 | constitucion, id:4011743
    Retrieving record 149 of 620 | souillac, id:3026644
    Retrieving record 150 of 620 | mikhaylovka, id:2015842
    Retrieving record 151 of 620 | sao filipe, id:3374210
    Retrieving record 152 of 620 | arraial do cabo, id:3471451
    Retrieving record 153 of 620 | pombas, id:3448350
    Retrieving record 154 of 620 | gibara, id:3558315
    Retrieving record 155 of 620 | barra bonita, id:3470776
    City not found. Moving on.
    Retrieving record 157 of 620 | danane, id:2290462
    Retrieving record 158 of 620 | lavrentiya, id:4031637
    City not found. Moving on.
    Retrieving record 160 of 620 | pasca, id:3672813
    Retrieving record 161 of 620 | mount gambier, id:2156643
    Retrieving record 162 of 620 | talnakh, id:1490256
    Retrieving record 163 of 620 | sandwick, id:2640416
    Retrieving record 164 of 620 | molokovo, id:525509
    Retrieving record 165 of 620 | tankhoy, id:2015576
    Retrieving record 166 of 620 | chase, id:5006946
    Retrieving record 167 of 620 | kyabram, id:2160910
    Retrieving record 168 of 620 | barrow, id:3833859
    Retrieving record 169 of 620 | stykkisholmur, id:3412888
    Retrieving record 170 of 620 | tual, id:1623197
    City not found. Moving on.
    Retrieving record 172 of 620 | fairbanks, id:5861897
    Retrieving record 173 of 620 | ziro, id:1252668
    City not found. Moving on.
    Retrieving record 175 of 620 | vostok, id:2013279
    City not found. Moving on.
    Retrieving record 177 of 620 | dunedin, id:2191562
    Retrieving record 178 of 620 | mar del plata, id:3863379
    Retrieving record 179 of 620 | mastung, id:1170706
    Retrieving record 180 of 620 | nizhniy kuranakh, id:2019135
    Retrieving record 181 of 620 | whitehorse, id:6180550
    Retrieving record 182 of 620 | sturgeon bay, id:5274867
    Retrieving record 183 of 620 | sao felix do xingu, id:3388847
    Retrieving record 184 of 620 | jiuquan, id:1280957
    Retrieving record 185 of 620 | palmer, id:2067070
    Retrieving record 186 of 620 | samarai, id:2132606
    Retrieving record 187 of 620 | vila franca do campo, id:3372472
    Retrieving record 188 of 620 | khorixas, id:3356213
    Retrieving record 189 of 620 | road town, id:3577430
    Retrieving record 190 of 620 | camacha, id:2270385
    City not found. Moving on.
    Retrieving record 192 of 620 | paraiso, id:4011743
    Retrieving record 193 of 620 | severo-kurilsk, id:2121385
    Retrieving record 194 of 620 | ilulissat, id:3423146
    Retrieving record 195 of 620 | leningradskiy, id:2123814
    Retrieving record 196 of 620 | port elizabeth, id:4501427
    Retrieving record 197 of 620 | poya, id:2138522
    Retrieving record 198 of 620 | new norfolk, id:2155415
    Retrieving record 199 of 620 | lompoc, id:5367788
    City not found. Moving on.
    Retrieving record 201 of 620 | vardo, id:4372777
    Retrieving record 202 of 620 | busselton, id:2075265
    Retrieving record 203 of 620 | tubuala, id:3700393
    Retrieving record 204 of 620 | kuloy, id:539385
    City not found. Moving on.
    Retrieving record 206 of 620 | dalkola, id:1273642
    Retrieving record 207 of 620 | la orilla, id:4001056
    Retrieving record 208 of 620 | tiksi, id:2015306
    Retrieving record 209 of 620 | blind river, id:5903851
    Retrieving record 210 of 620 | pacific grove, id:5380437
    Retrieving record 211 of 620 | sibolga, id:1213855
    Retrieving record 212 of 620 | jalu, id:86049
    Retrieving record 213 of 620 | saint-georges, id:6295855
    Retrieving record 214 of 620 | namibe, id:3347019
    Retrieving record 215 of 620 | sirnak, id:300640
    Retrieving record 216 of 620 | lannion, id:3007609
    Retrieving record 217 of 620 | palestine, id:4717232
    City not found. Moving on.
    Retrieving record 219 of 620 | buin, id:3897774
    City not found. Moving on.
    Retrieving record 221 of 620 | san patricio, id:3437029
    City not found. Moving on.
    Retrieving record 223 of 620 | ucluelet, id:6171633
    Retrieving record 224 of 620 | rocky mountain house, id:6126749
    Retrieving record 225 of 620 | los chiles, id:3623008
    Retrieving record 226 of 620 | zabid, id:69559
    Retrieving record 227 of 620 | ancud, id:3899695
    City not found. Moving on.
    Retrieving record 229 of 620 | rocha, id:3440777
    City not found. Moving on.
    Retrieving record 231 of 620 | umm lajj, id:100926
    Retrieving record 232 of 620 | siderno, id:6540819
    Retrieving record 233 of 620 | imeni poliny osipenko, id:2023584
    Retrieving record 234 of 620 | priargunsk, id:2017646
    Retrieving record 235 of 620 | sosnovo-ozerskoye, id:2016216
    Retrieving record 236 of 620 | beyneu, id:610298
    City not found. Moving on.
    Retrieving record 238 of 620 | aklavik, id:5882953
    Retrieving record 239 of 620 | ostrovnoy, id:556268
    Retrieving record 240 of 620 | oranjemund, id:3354071
    Retrieving record 241 of 620 | sangamner, id:1257436
    Retrieving record 242 of 620 | san francisco del mar, id:3524965
    City not found. Moving on.
    Retrieving record 244 of 620 | zyryanka, id:2119283
    Retrieving record 245 of 620 | imbituba, id:3461370
    Retrieving record 246 of 620 | te anau, id:2181625
    Retrieving record 247 of 620 | luanda, id:2240449
    City not found. Moving on.
    Retrieving record 249 of 620 | saskylakh, id:2017155
    Retrieving record 250 of 620 | fengcheng, id:2037411
    Retrieving record 251 of 620 | yangjiang, id:1806408
    Retrieving record 252 of 620 | quincy, id:4169510
    Retrieving record 253 of 620 | lamar, id:5427957
    Retrieving record 254 of 620 | taoudenni, id:2450173
    Retrieving record 255 of 620 | jacareacanga, id:3397763
    Retrieving record 256 of 620 | hofn, id:2630299
    Retrieving record 257 of 620 | hobart, id:2163355
    Retrieving record 258 of 620 | puerto maldonado, id:3931470
    Retrieving record 259 of 620 | glamoc, id:3200651
    Retrieving record 260 of 620 | rabaul, id:2087894
    Retrieving record 261 of 620 | balakovo, id:579492
    Retrieving record 262 of 620 | port macquarie, id:2152659
    Retrieving record 263 of 620 | yenagoa, id:2318123
    Retrieving record 264 of 620 | paamiut, id:3421193
    City not found. Moving on.
    Retrieving record 266 of 620 | qaanaaq, id:3831208
    Retrieving record 267 of 620 | ballina, id:2966778
    City not found. Moving on.
    Retrieving record 269 of 620 | lima, id:3936456
    Retrieving record 270 of 620 | kerema, id:2094144
    Retrieving record 271 of 620 | revelstoke, id:6121621
    Retrieving record 272 of 620 | gravelbourg, id:5965462
    Retrieving record 273 of 620 | tsaratanana, id:1055059
    Retrieving record 274 of 620 | atikokan, id:5888001
    Retrieving record 275 of 620 | longkou, id:1802550
    Retrieving record 276 of 620 | sinnamary, id:3380290
    City not found. Moving on.
    Retrieving record 278 of 620 | georgetown, id:3378644
    Retrieving record 279 of 620 | olinda, id:3650121
    Retrieving record 280 of 620 | ribeira grande, id:3372707
    City not found. Moving on.
    Retrieving record 282 of 620 | kahului, id:5847411
    Retrieving record 283 of 620 | santa maria, id:3450083
    Retrieving record 284 of 620 | neiafu, id:4032420
    Retrieving record 285 of 620 | alpena, id:4984075
    Retrieving record 286 of 620 | portland, id:5746545
    Retrieving record 287 of 620 | atbasar, id:1526038
    Retrieving record 288 of 620 | bud, id:7626370
    Retrieving record 289 of 620 | seoul, id:1835848
    Retrieving record 290 of 620 | koslan, id:544084
    Retrieving record 291 of 620 | filadelfia, id:3867291
    Retrieving record 292 of 620 | teya, id:3526662
    Retrieving record 293 of 620 | nanortalik, id:3421765
    Retrieving record 294 of 620 | agutaya, id:1731798
    Retrieving record 295 of 620 | saint albans, id:5240569
    Retrieving record 296 of 620 | hervey bay, id:2146219
    Retrieving record 297 of 620 | karlshus, id:3150437
    Retrieving record 298 of 620 | ngunguru, id:2186111
    City not found. Moving on.
    Retrieving record 300 of 620 | college, id:5859699
    Retrieving record 301 of 620 | karratha, id:6620339
    Retrieving record 302 of 620 | sisimiut, id:3419842
    Retrieving record 303 of 620 | moree, id:2156927
    Retrieving record 304 of 620 | chuy, id:3443061
    Retrieving record 305 of 620 | fairview, id:5097801
    Retrieving record 306 of 620 | launceston, id:2160517
    Retrieving record 307 of 620 | padang, id:1633419
    Retrieving record 308 of 620 | kabanjahe, id:1214965
    Retrieving record 309 of 620 | torres, id:3446295
    Retrieving record 310 of 620 | urengoy, id:1488414
    Retrieving record 311 of 620 | malabo, id:217695
    Retrieving record 312 of 620 | upernavik, id:3418910
    Retrieving record 313 of 620 | fare, id:4034496
    Retrieving record 314 of 620 | port lincoln, id:2063036
    Retrieving record 315 of 620 | hasaki, id:2112802
    Retrieving record 316 of 620 | jinchang, id:1805733
    Retrieving record 317 of 620 | muravlenko, id:1540711
    Retrieving record 318 of 620 | goulburn, id:2164837
    Retrieving record 319 of 620 | valleyview, id:6173220
    Retrieving record 320 of 620 | moindou, id:2140466
    Retrieving record 321 of 620 | marsabit, id:187585
    Retrieving record 322 of 620 | usinsk, id:863061
    Retrieving record 323 of 620 | sambava, id:1056899
    Retrieving record 324 of 620 | tuatapere, id:2180815
    Retrieving record 325 of 620 | cascais, id:2269594
    Retrieving record 326 of 620 | keningau, id:1734098
    Retrieving record 327 of 620 | pampa, id:5527953
    Retrieving record 328 of 620 | baghdad, id:98182
    Retrieving record 329 of 620 | majene, id:1636806
    Retrieving record 330 of 620 | olhao, id:2265447
    Retrieving record 331 of 620 | diofior, id:2252473
    Retrieving record 332 of 620 | sungaipenuh, id:1625929
    Retrieving record 333 of 620 | jiblah, id:74185
    Retrieving record 334 of 620 | talcahuano, id:3870282
    Retrieving record 335 of 620 | berdigestyakh, id:2026861
    Retrieving record 336 of 620 | guerrero negro, id:4021858
    Retrieving record 337 of 620 | starosubkhangulovo, id:488618
    Retrieving record 338 of 620 | healesville, id:2163701
    Retrieving record 339 of 620 | sao fidelis, id:3449195
    City not found. Moving on.
    Retrieving record 341 of 620 | quatre cocos, id:1106643
    Retrieving record 342 of 620 | illela, id:2336237
    Retrieving record 343 of 620 | bonavista, id:5905393
    Retrieving record 344 of 620 | namanga, id:184570
    Retrieving record 345 of 620 | sao joao da barra, id:3448903
    Retrieving record 346 of 620 | kieta, id:2094027
    Retrieving record 347 of 620 | nurota, id:1513087
    Retrieving record 348 of 620 | egvekinot, id:4031742
    Retrieving record 349 of 620 | requena, id:2511930
    Retrieving record 350 of 620 | geraldton, id:5960603
    Retrieving record 351 of 620 | dingle, id:1714733
    City not found. Moving on.
    Retrieving record 353 of 620 | qeshm, id:119374
    Retrieving record 354 of 620 | sorland, id:3137469
    Retrieving record 355 of 620 | ahipara, id:2194098
    Retrieving record 356 of 620 | tadine, id:2138285
    Retrieving record 357 of 620 | ekhabi, id:2122614
    City not found. Moving on.
    City not found. Moving on.
    Retrieving record 360 of 620 | lahij, id:73560
    Retrieving record 361 of 620 | fiumicino, id:3176923
    Retrieving record 362 of 620 | richards bay, id:962367
    Retrieving record 363 of 620 | batemans bay, id:2176639
    Retrieving record 364 of 620 | gat, id:2249901
    City not found. Moving on.
    City not found. Moving on.
    Retrieving record 367 of 620 | gamba, id:1281256
    Retrieving record 368 of 620 | kadom, id:554549
    Retrieving record 369 of 620 | pangnirtung, id:6096551
    Retrieving record 370 of 620 | najran, id:103630
    Retrieving record 371 of 620 | saint george, id:262462
    Retrieving record 372 of 620 | beira, id:3119841
    Retrieving record 373 of 620 | westport, id:2960970
    Retrieving record 374 of 620 | bilma, id:2446796
    Retrieving record 375 of 620 | flagstaff, id:5294810
    Retrieving record 376 of 620 | fortuna, id:2517679
    Retrieving record 377 of 620 | bukama, id:217834
    Retrieving record 378 of 620 | camalu, id:3979216
    Retrieving record 379 of 620 | nome, id:4732862
    Retrieving record 380 of 620 | chowchilla, id:5336667
    Retrieving record 381 of 620 | mora, id:2691400
    City not found. Moving on.
    Retrieving record 383 of 620 | pokhara, id:1282898
    Retrieving record 384 of 620 | fukue, id:1848373
    Retrieving record 385 of 620 | miri, id:2347470
    Retrieving record 386 of 620 | bilibino, id:2126682
    Retrieving record 387 of 620 | rusape, id:882100
    Retrieving record 388 of 620 | comodoro rivadavia, id:3860443
    Retrieving record 389 of 620 | bontang, id:1648186
    Retrieving record 390 of 620 | kantunilkin, id:3822702
    Retrieving record 391 of 620 | beringovskiy, id:2126710
    Retrieving record 392 of 620 | awjilah, id:88533
    Retrieving record 393 of 620 | ulcinj, id:3188516
    Retrieving record 394 of 620 | uvalde, id:4738721
    Retrieving record 395 of 620 | abu samrah, id:172515
    Retrieving record 396 of 620 | cherskiy, id:2126199
    Retrieving record 397 of 620 | vestmannaeyjar, id:3412093
    Retrieving record 398 of 620 | antofagasta, id:3899539
    Retrieving record 399 of 620 | poum, id:787487
    Retrieving record 400 of 620 | senneterre, id:6144195
    Retrieving record 401 of 620 | pisco, id:3932145
    Retrieving record 402 of 620 | szczytno, id:757357
    Retrieving record 403 of 620 | general roca, id:3855065
    Retrieving record 404 of 620 | paranavai, id:3455051
    Retrieving record 405 of 620 | husavik, id:5961417
    Retrieving record 406 of 620 | north bend, id:5742974
    Retrieving record 407 of 620 | vryburg, id:942511
    City not found. Moving on.
    Retrieving record 409 of 620 | katsuura, id:1865309
    Retrieving record 410 of 620 | liku, id:1633034
    Retrieving record 411 of 620 | saldanha, id:2737599
    Retrieving record 412 of 620 | haines junction, id:5969025
    Retrieving record 413 of 620 | touros, id:3386213
    Retrieving record 414 of 620 | payson, id:5779548
    City not found. Moving on.
    Retrieving record 416 of 620 | simao, id:1794209
    Retrieving record 417 of 620 | ayan, id:749747
    Retrieving record 418 of 620 | gualeguaychu, id:3433658
    Retrieving record 419 of 620 | leshukonskoye, id:535839
    Retrieving record 420 of 620 | dikson, id:1507390
    Retrieving record 421 of 620 | pevek, id:2122090
    Retrieving record 422 of 620 | deutschlandsberg, id:2781023
    Retrieving record 423 of 620 | sorong, id:1626542
    Retrieving record 424 of 620 | la joya, id:3526617
    Retrieving record 425 of 620 | mahon, id:2965139
    Retrieving record 426 of 620 | kaihua, id:1789001
    Retrieving record 427 of 620 | cabo san lucas, id:3985710
    Retrieving record 428 of 620 | mitchell, id:5229794
    Retrieving record 429 of 620 | serdobsk, id:496934
    Retrieving record 430 of 620 | port-gentil, id:2396518
    Retrieving record 431 of 620 | ndola, id:901344
    Retrieving record 432 of 620 | lingao, id:1803560
    Retrieving record 433 of 620 | ratnagiri, id:1258338
    Retrieving record 434 of 620 | maniitsoq, id:3421982
    City not found. Moving on.
    Retrieving record 436 of 620 | abay, id:1526970
    Retrieving record 437 of 620 | bermejo, id:3923154
    Retrieving record 438 of 620 | chifeng, id:2038067
    City not found. Moving on.
    Retrieving record 440 of 620 | acapulco, id:3533462
    Retrieving record 441 of 620 | boguchany, id:1509844
    Retrieving record 442 of 620 | lashio, id:1314759
    Retrieving record 443 of 620 | maputo, id:1040652
    Retrieving record 444 of 620 | kolosovka, id:1502879
    Retrieving record 445 of 620 | quelimane, id:1028434
    Retrieving record 446 of 620 | codrington, id:2160063
    Retrieving record 447 of 620 | oxford, id:4081914
    Retrieving record 448 of 620 | nelson bay, id:2155562
    Retrieving record 449 of 620 | cabra, id:2520645
    Retrieving record 450 of 620 | alta floresta, id:6316343
    City not found. Moving on.
    Retrieving record 452 of 620 | viedma, id:3832899
    Retrieving record 453 of 620 | ulaangom, id:1515029
    Retrieving record 454 of 620 | maceio, id:3395981
    City not found. Moving on.
    Retrieving record 456 of 620 | birecik, id:321062
    City not found. Moving on.
    Retrieving record 458 of 620 | calderitas, id:3531816
    Retrieving record 459 of 620 | beloha, id:1067565
    Retrieving record 460 of 620 | sao jose da coroa grande, id:3388456
    Retrieving record 461 of 620 | zunyi, id:1783621
    Retrieving record 462 of 620 | baie-comeau, id:5889745
    Retrieving record 463 of 620 | porbandar, id:1259395
    Retrieving record 464 of 620 | nuevo progreso, id:3520271
    Retrieving record 465 of 620 | marzuq, id:72181
    Retrieving record 466 of 620 | plettenberg bay, id:964712
    Retrieving record 467 of 620 | adrar, id:2508813
    Retrieving record 468 of 620 | thai binh, id:1566346
    Retrieving record 469 of 620 | amazar, id:2027806
    Retrieving record 470 of 620 | paramonga, id:3933024
    Retrieving record 471 of 620 | onguday, id:1496130
    Retrieving record 472 of 620 | shingu, id:1847947
    Retrieving record 473 of 620 | edd, id:338345
    Retrieving record 474 of 620 | guanare, id:3640226
    Retrieving record 475 of 620 | manjeshwar, id:1263780
    Retrieving record 476 of 620 | qena, id:350550
    Retrieving record 477 of 620 | vigrestad, id:3131824
    Retrieving record 478 of 620 | salinas, id:5391295
    Retrieving record 479 of 620 | kuzhener, id:537939
    Retrieving record 480 of 620 | delbruck, id:2938389
    Retrieving record 481 of 620 | hamilton, id:5969785
    Retrieving record 482 of 620 | sabha, id:2212775
    City not found. Moving on.
    Retrieving record 484 of 620 | cariati, id:2525352
    Retrieving record 485 of 620 | dhidhdhoo, id:1337612
    Retrieving record 486 of 620 | saravena, id:3668257
    Retrieving record 487 of 620 | altay, id:1529651
    Retrieving record 488 of 620 | kulhudhuffushi, id:1337613
    Retrieving record 489 of 620 | ha giang, id:1581349
    Retrieving record 490 of 620 | surskoye, id:486043
    Retrieving record 491 of 620 | teahupoo, id:4033543
    Retrieving record 492 of 620 | duku, id:2344415
    Retrieving record 493 of 620 | bloemfontein, id:1018725
    Retrieving record 494 of 620 | lufilufi, id:4035249
    City not found. Moving on.
    Retrieving record 496 of 620 | flinders, id:6255012
    Retrieving record 497 of 620 | talpa, id:3991622
    Retrieving record 498 of 620 | musoma, id:152451
    Retrieving record 499 of 620 | vao, id:588365
    Retrieving record 500 of 620 | necochea, id:3430443
    City not found. Moving on.
    City not found. Moving on.
    Retrieving record 503 of 620 | kermanshah, id:128226
    Retrieving record 504 of 620 | sooke, id:6151264
    Retrieving record 505 of 620 | orcopampa, id:3933986
    Retrieving record 506 of 620 | hobyo, id:57000
    Retrieving record 507 of 620 | ocampo, id:1697332
    Retrieving record 508 of 620 | saint anthony, id:5606187
    Retrieving record 509 of 620 | santa rosa, id:3835994
    Retrieving record 510 of 620 | diego de almagro, id:3892454
    Retrieving record 511 of 620 | praia da vitoria, id:3372760
    Retrieving record 512 of 620 | reconquista, id:3429594
    Retrieving record 513 of 620 | matagami, id:2335713
    Retrieving record 514 of 620 | saint-leu, id:6690297
    Retrieving record 515 of 620 | carroll, id:2172210
    Retrieving record 516 of 620 | westerland, id:2757220
    Retrieving record 517 of 620 | quimper, id:2984701
    Retrieving record 518 of 620 | dunmore east, id:2964492
    Retrieving record 519 of 620 | bucerias, id:4016734
    Retrieving record 520 of 620 | laizhou, id:1804578
    Retrieving record 521 of 620 | bundaberg, id:2173323
    Retrieving record 522 of 620 | nishihara, id:1850144
    Retrieving record 523 of 620 | laguna, id:4013704
    Retrieving record 524 of 620 | kahramanmaras, id:310859
    Retrieving record 525 of 620 | mityana, id:229139
    Retrieving record 526 of 620 | bedford, id:2656046
    Retrieving record 527 of 620 | bandipur, id:1270686
    Retrieving record 528 of 620 | hearst, id:5973108
    Retrieving record 529 of 620 | raudeberg, id:3146487
    Retrieving record 530 of 620 | hailey, id:5594956
    Retrieving record 531 of 620 | businga, id:217637
    Retrieving record 532 of 620 | waipawa, id:2185329
    Retrieving record 533 of 620 | eyl, id:60019
    Retrieving record 534 of 620 | seymchan, id:2121373
    Retrieving record 535 of 620 | politika, id:254898
    Retrieving record 536 of 620 | urambo, id:149172
    Retrieving record 537 of 620 | parakai, id:2184927
    Retrieving record 538 of 620 | nioki, id:2311968
    Retrieving record 539 of 620 | west odessa, id:5533366
    Retrieving record 540 of 620 | felipe carrillo puerto, id:3527639
    Retrieving record 541 of 620 | borogontsy, id:2026160
    Retrieving record 542 of 620 | margate, id:2158744
    Retrieving record 543 of 620 | kamaishi, id:2112444
    Retrieving record 544 of 620 | hambantota, id:1244926
    Retrieving record 545 of 620 | ampanihy, id:1078553
    Retrieving record 546 of 620 | erzin, id:296852
    Retrieving record 547 of 620 | la primavera, id:3844421
    Retrieving record 548 of 620 | mareeba, id:2158767
    Retrieving record 549 of 620 | taltal, id:3870243
    Retrieving record 550 of 620 | san cristobal, id:3652462
    Retrieving record 551 of 620 | zawiercie, id:3080526
    Retrieving record 552 of 620 | kharp, id:1503726
    Retrieving record 553 of 620 | labuhan, id:1641899
    Retrieving record 554 of 620 | fomboni, id:921889
    Retrieving record 555 of 620 | trinidad, id:3439749
    Retrieving record 556 of 620 | mochudi, id:933340
    Retrieving record 557 of 620 | jining, id:1805518
    Retrieving record 558 of 620 | buraydah, id:107304
    Retrieving record 559 of 620 | ambilobe, id:1082243
    Retrieving record 560 of 620 | hyeres, id:3012937
    Retrieving record 561 of 620 | puerto del rosario, id:2512186
    City not found. Moving on.
    Retrieving record 563 of 620 | el dorado, id:3644689
    City not found. Moving on.
    Retrieving record 565 of 620 | alindao, id:240999
    Retrieving record 566 of 620 | chambery, id:3027422
    Retrieving record 567 of 620 | snezhnogorsk, id:795243
    Retrieving record 568 of 620 | umea, id:602150
    City not found. Moving on.
    Retrieving record 570 of 620 | ndjole, id:2397674
    Retrieving record 571 of 620 | gazanjyk, id:161974
    Retrieving record 572 of 620 | kapuskasing, id:5989403
    Retrieving record 573 of 620 | baruun-urt, id:2032614
    Retrieving record 574 of 620 | ibra, id:287832
    Retrieving record 575 of 620 | myitkyina, id:1307741
    Retrieving record 576 of 620 | saint-augustin, id:3031582
    Retrieving record 577 of 620 | namatanai, id:2090021
    Retrieving record 578 of 620 | sand, id:3140452
    Retrieving record 579 of 620 | chara, id:262462
    Retrieving record 580 of 620 | djambala, id:2260401
    Retrieving record 581 of 620 | uarini, id:3661756
    Retrieving record 582 of 620 | yaan, id:2338660
    Retrieving record 583 of 620 | tutoia, id:3385899
    Retrieving record 584 of 620 | bathsheba, id:3374083
    Retrieving record 585 of 620 | miramar, id:3622854
    Retrieving record 586 of 620 | tongliao, id:2034400
    Retrieving record 587 of 620 | fort nelson, id:5955902
    Retrieving record 588 of 620 | avera, id:4231997
    Retrieving record 589 of 620 | sioux lookout, id:6148373
    Retrieving record 590 of 620 | klyavlino, id:547383
    Retrieving record 591 of 620 | iberia, id:3938531
    Retrieving record 592 of 620 | dudinka, id:1507116
    Retrieving record 593 of 620 | udachnyy, id:2014624
    Retrieving record 594 of 620 | grand gaube, id:934479
    Retrieving record 595 of 620 | makushino, id:1500036
    Retrieving record 596 of 620 | khudumelapye, id:933562
    Retrieving record 597 of 620 | moron, id:3631878
    City not found. Moving on.
    Retrieving record 599 of 620 | tricase, id:2522857
    Retrieving record 600 of 620 | coos bay, id:5720495
    Retrieving record 601 of 620 | gigmoto, id:1712961
    Retrieving record 602 of 620 | vila velha, id:6320062
    Retrieving record 603 of 620 | kraskino, id:2021644
    Retrieving record 604 of 620 | kadiri, id:1268673
    City not found. Moving on.
    Retrieving record 606 of 620 | lethem, id:3379316
    Retrieving record 607 of 620 | bulgan, id:2032201
    Retrieving record 608 of 620 | kidal, id:2455290
    Retrieving record 609 of 620 | ulladulla, id:2145554
    City not found. Moving on.
    Retrieving record 611 of 620 | rawson, id:3839307
    Retrieving record 612 of 620 | red bluff, id:5570065
    Retrieving record 613 of 620 | visnes, id:3131500
    Retrieving record 614 of 620 | berlevag, id:780687
    Retrieving record 615 of 620 | nouakchott, id:2377450
    Retrieving record 616 of 620 | kosh-agach, id:1502422
    Retrieving record 617 of 620 | staicele, id:455298
    Retrieving record 618 of 620 | fort thomas, id:4292071
    Retrieving record 619 of 620 | quezon, id:1692197
    Retrieving record 620 of 620 | matsumoto, id:1857519
    -----------------------
    Data retrieval complete
    




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City Name</th>
      <th>Cloud</th>
      <th>Country</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Maximum Temperature</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Khatanga</td>
      <td>48</td>
      <td>RU</td>
      <td>1531977064</td>
      <td>78</td>
      <td>71.98</td>
      <td>102.47</td>
      <td>42.33</td>
      <td>17.38</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Tuktoyaktuk</td>
      <td>75</td>
      <td>CA</td>
      <td>1531972800</td>
      <td>48</td>
      <td>69.44</td>
      <td>-133.03</td>
      <td>64.40</td>
      <td>12.75</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Victoria</td>
      <td>75</td>
      <td>BN</td>
      <td>1531974600</td>
      <td>75</td>
      <td>5.28</td>
      <td>115.24</td>
      <td>91.40</td>
      <td>3.36</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Bodden Town</td>
      <td>0</td>
      <td>KY</td>
      <td>1531976784</td>
      <td>99</td>
      <td>19.28</td>
      <td>-81.25</td>
      <td>84.09</td>
      <td>8.43</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Cermik</td>
      <td>0</td>
      <td>TR</td>
      <td>1531975800</td>
      <td>36</td>
      <td>38.14</td>
      <td>39.45</td>
      <td>77.00</td>
      <td>3.36</td>
    </tr>
  </tbody>
</table>
</div>




```python
#save DataFrame to csv file so the calls do not need to be repeated
weather_raw.to_csv("weather_by_city.csv")
weather_raw.count()
```




    City Name              557
    Cloud                  557
    Country                557
    Date                   557
    Humidity               557
    Latitude               557
    Longitude              557
    Maximum Temperature    557
    Wind Speed             557
    dtype: int64




```python
#build a scatter plot based on the columns Latitude and temperature
plt.figure(figsize = (15, 10))
plt.scatter(weather_raw['Latitude'], weather_raw['Maximum Temperature'], s=75, c="lightskyblue", edgecolors='k', alpha=0.7)
plt.grid(color="0.75", linestyle="-")
plt.title("City Latitude vs Temperature(F)")
plt.xlabel("Latitude")
plt.ylabel("Temperature")

#save the plot
plt.savefig('lat_v_temp.png')
plt.show()
```


![png](output_8_0.png)



```python
#build a scatter plot based on the columns Latitude and humidity
plt.figure(figsize = (15, 10))
plt.scatter(weather_raw['Latitude'], weather_raw['Humidity'], s=75, c="lightskyblue", edgecolors='k', alpha=0.7)
plt.grid(color="0.75", linestyle="-")
plt.title("City Latitude vs Humidity")
plt.xlabel("Latitude")
plt.ylabel("Humidity")
plt.gca().set_yticklabels(['{:.0f}%'.format(x) for x in plt.gca().get_yticks()]) 

#save the plot
plt.savefig('lat_v_hum.png')
plt.show()
```


![png](output_9_0.png)



```python
#build a scatter plot based on the columns Latitude and cloudiness
plt.figure(figsize = (15, 10))
plt.scatter(weather_raw['Latitude'], weather_raw['Cloud'], s=75, c="lightskyblue", edgecolors='k', alpha=0.7)
plt.grid(color="0.75", linestyle="-")
plt.title("City Latitude vs Cloudiness")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness")
plt.gca().set_yticklabels(['{:.0f}%'.format(x) for x in plt.gca().get_yticks()])

#save the plot
plt.savefig('lat_v_cloud.png')
plt.show()
```


![png](output_10_0.png)



```python
#build a scatter plot based on the columns Latitude and wind speed
plt.figure(figsize = (15, 10))
plt.scatter(weather_raw['Latitude'], weather_raw['Wind Speed'], s=75, c="lightskyblue", edgecolors='k', alpha=0.7)
plt.grid(color="0.75", linestyle="-")
plt.title("City Latitude vs Wind Speed")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed")
plt.gca().set_yticklabels(['{:.0f}%'.format(x) for x in plt.gca().get_yticks()])

#save the plot
plt.savefig('lat_v_wind.png')
plt.show()
```


![png](output_11_0.png)

