
### Importing modules, downloading data and applying regex on it


```python
import requests, re
import pandas as pd

response = requests.get("https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M").text
save = re.findall("<td>(.*?)\n*</td>", response)
```

### Colecting the data from regex above and saving it (in a dict and in a list)


```python
i = 0
postcodes = {}
post_lines = []
p = re.compile("^\w\d\w$")
for line in save:
    if p.search (line) != None:
        postcodes[i] = []
        i_old = i
        i+=1
        post_lines.append(line)
    elif "title" in line:
        postcodes[i_old].append(re.findall('>(.*?)<', line)[0])
    elif i != i_old:
        postcodes[i_old].append(line)

```

### Deleting rows with "Not assigned" on it.


```python
borough_neigh = []
for i in range(len(postcodes)):
    borough_neigh.append(postcodes[i])

PostalCode = []
Borough = []
Neigh = []

for i in range(len(borough_neigh)):
    if "Not assigned" not in borough_neigh[i]:
        Borough.append(borough_neigh[i][0])
        Neigh.append(borough_neigh[i][1:])
        PostalCode.append(post_lines[i])
```

### Creating a dictionary that will be used for the final creation of the three columns of the dataframe


```python
dict_p = {}
for i in range(len(PostalCode)):
    dict_p[PostalCode[i]] = []

for i in range(len(PostalCode)):
    if Borough[i] not in dict_p[PostalCode[i]]:
        dict_p[PostalCode[i]].append(Borough[i])
    dict_p[PostalCode[i]].append(Neigh[i])
```

### Creating the three columns


```python
Final_Postal_Code = []
Final_Borough = []
Neigh = []

for i in dict_p:
    Final_Postal_Code.append(i)
    Final_Borough.append(dict_p[i][0])
    Neigh.append(dict_p[i][1:])
```

### Converting data in column Neighborhood to be separated with commas


```python
Final_Neighborhood = []

final_str = ""
for i in range(len(Neigh)):
    for j in Neigh[i]:
        if j != []:
            final_str += j[0]+", "

    Final_Neighborhood.append(final_str[:-2])
    final_str = ""
```

### Create the dataframe as required in the lab


```python
dict_to_dataframe = {'PostalCode': Final_Postal_Code, 'Borough': Final_Borough, 'Neighborhood': Final_Neighborhood}
df = pd.DataFrame(dict_to_dataframe)
df.head(10)
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park / Harbourfront</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor / Lawrence Heights</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park / Ontario Provincial Government</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern / Rouge</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill / Woodbine Gardens</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
    </tr>
  </tbody>
</table>
</div>



### Showing the shape of the dataframe


```python
df.shape
```




    (103, 3)



### Downloading geospatial data


```python
geo = pd.read_csv("https://cocl.us/Geospatial_data")
```

### Confirming its shape


```python
geo.shape
```




    (103, 3)



### Changing column name in order to match with column in df


```python
geo = geo.rename(columns={"Postal Code": "PostalCode"})
```

### Verifying...


```python
geo.head()
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
      <th>PostalCode</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
  </tbody>
</table>
</div>



### Merging the table df and geo using the column "PostalCode" as guide


```python
df_merged = pd.merge(df, geo, on="PostalCode")
```

### Showing the results


```python
df_merged
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
      <td>43.725882</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park / Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor / Lawrence Heights</td>
      <td>43.718518</td>
      <td>-79.464763</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park / Ontario Provincial Government</td>
      <td>43.662301</td>
      <td>-79.389494</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
      <td>43.667856</td>
      <td>-79.532242</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern / Rouge</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
      <td>43.745906</td>
      <td>-79.352188</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill / Woodbine Gardens</td>
      <td>43.706397</td>
      <td>-79.309937</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
      <td>43.657162</td>
      <td>-79.378937</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M6B</td>
      <td>North York</td>
      <td>Glencairn</td>
      <td>43.709577</td>
      <td>-79.445073</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>West Deane Park / Princess Gardens / Martin Gr...</td>
      <td>43.650943</td>
      <td>-79.554724</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Rouge Hill / Port Union / Highland Creek</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M3C</td>
      <td>North York</td>
      <td>Don Mills</td>
      <td>43.725900</td>
      <td>-79.340923</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M4C</td>
      <td>East York</td>
      <td>Woodbine Heights</td>
      <td>43.695344</td>
      <td>-79.318389</td>
    </tr>
    <tr>
      <th>15</th>
      <td>M5C</td>
      <td>Downtown Toronto</td>
      <td>St. James Town</td>
      <td>43.651494</td>
      <td>-79.375418</td>
    </tr>
    <tr>
      <th>16</th>
      <td>M6C</td>
      <td>York</td>
      <td>Humewood-Cedarvale</td>
      <td>43.693781</td>
      <td>-79.428191</td>
    </tr>
    <tr>
      <th>17</th>
      <td>M9C</td>
      <td>Etobicoke</td>
      <td>Eringate / Bloordale Gardens / Old Burnhamthor...</td>
      <td>43.643515</td>
      <td>-79.577201</td>
    </tr>
    <tr>
      <th>18</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood / Morningside / West Hill</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>19</th>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
    </tr>
    <tr>
      <th>20</th>
      <td>M5E</td>
      <td>Downtown Toronto</td>
      <td>Berczy Park</td>
      <td>43.644771</td>
      <td>-79.373306</td>
    </tr>
    <tr>
      <th>21</th>
      <td>M6E</td>
      <td>York</td>
      <td>Caledonia-Fairbanks</td>
      <td>43.689026</td>
      <td>-79.453512</td>
    </tr>
    <tr>
      <th>22</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>23</th>
      <td>M4G</td>
      <td>East York</td>
      <td>Leaside</td>
      <td>43.709060</td>
      <td>-79.363452</td>
    </tr>
    <tr>
      <th>24</th>
      <td>M5G</td>
      <td>Downtown Toronto</td>
      <td>Central Bay Street</td>
      <td>43.657952</td>
      <td>-79.387383</td>
    </tr>
    <tr>
      <th>25</th>
      <td>M6G</td>
      <td>Downtown Toronto</td>
      <td>Christie</td>
      <td>43.669542</td>
      <td>-79.422564</td>
    </tr>
    <tr>
      <th>26</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <th>27</th>
      <td>M2H</td>
      <td>North York</td>
      <td>Hillcrest Village</td>
      <td>43.803762</td>
      <td>-79.363452</td>
    </tr>
    <tr>
      <th>28</th>
      <td>M3H</td>
      <td>North York</td>
      <td>Bathurst Manor / Wilson Heights / Downsview North</td>
      <td>43.754328</td>
      <td>-79.442259</td>
    </tr>
    <tr>
      <th>29</th>
      <td>M4H</td>
      <td>East York</td>
      <td>Thorncliffe Park</td>
      <td>43.705369</td>
      <td>-79.349372</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>73</th>
      <td>M4R</td>
      <td>Central Toronto</td>
      <td>North Toronto West</td>
      <td>43.715383</td>
      <td>-79.405678</td>
    </tr>
    <tr>
      <th>74</th>
      <td>M5R</td>
      <td>Central Toronto</td>
      <td>The Annex / North Midtown / Yorkville</td>
      <td>43.672710</td>
      <td>-79.405678</td>
    </tr>
    <tr>
      <th>75</th>
      <td>M6R</td>
      <td>West Toronto</td>
      <td>Parkdale / Roncesvalles</td>
      <td>43.648960</td>
      <td>-79.456325</td>
    </tr>
    <tr>
      <th>76</th>
      <td>M7R</td>
      <td>Mississauga</td>
      <td>Canada Post Gateway Processing Centre</td>
      <td>43.636966</td>
      <td>-79.615819</td>
    </tr>
    <tr>
      <th>77</th>
      <td>M9R</td>
      <td>Etobicoke</td>
      <td>Kingsview Village / St. Phillips / Martin Grov...</td>
      <td>43.688905</td>
      <td>-79.554724</td>
    </tr>
    <tr>
      <th>78</th>
      <td>M1S</td>
      <td>Scarborough</td>
      <td>Agincourt</td>
      <td>43.794200</td>
      <td>-79.262029</td>
    </tr>
    <tr>
      <th>79</th>
      <td>M4S</td>
      <td>Central Toronto</td>
      <td>Davisville</td>
      <td>43.704324</td>
      <td>-79.388790</td>
    </tr>
    <tr>
      <th>80</th>
      <td>M5S</td>
      <td>Downtown Toronto</td>
      <td>University of Toronto / Harbord</td>
      <td>43.662696</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>81</th>
      <td>M6S</td>
      <td>West Toronto</td>
      <td>Runnymede / Swansea</td>
      <td>43.651571</td>
      <td>-79.484450</td>
    </tr>
    <tr>
      <th>82</th>
      <td>M1T</td>
      <td>Scarborough</td>
      <td>Clarks Corners / Tam O'Shanter / Sullivan</td>
      <td>43.781638</td>
      <td>-79.304302</td>
    </tr>
    <tr>
      <th>83</th>
      <td>M4T</td>
      <td>Central Toronto</td>
      <td>Moore Park / Summerhill East</td>
      <td>43.689574</td>
      <td>-79.383160</td>
    </tr>
    <tr>
      <th>84</th>
      <td>M5T</td>
      <td>Downtown Toronto</td>
      <td>Kensington Market / Chinatown / Grange Park</td>
      <td>43.653206</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>85</th>
      <td>M1V</td>
      <td>Scarborough</td>
      <td>Milliken / Agincourt North / Steeles East / L'...</td>
      <td>43.815252</td>
      <td>-79.284577</td>
    </tr>
    <tr>
      <th>86</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>Summerhill West / Rathnelly / South Hill / For...</td>
      <td>43.686412</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>87</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td></td>
      <td>43.628947</td>
      <td>-79.394420</td>
    </tr>
    <tr>
      <th>88</th>
      <td>M8V</td>
      <td>Etobicoke</td>
      <td>New Toronto / Mimico South / Humber Bay Shores</td>
      <td>43.605647</td>
      <td>-79.501321</td>
    </tr>
    <tr>
      <th>89</th>
      <td>M9V</td>
      <td>Etobicoke</td>
      <td>South Steeles / Silverstone / Humbergate / Jam...</td>
      <td>43.739416</td>
      <td>-79.588437</td>
    </tr>
    <tr>
      <th>90</th>
      <td>M1W</td>
      <td>Scarborough</td>
      <td>Steeles West / L'Amoreaux West</td>
      <td>43.799525</td>
      <td>-79.318389</td>
    </tr>
    <tr>
      <th>91</th>
      <td>M4W</td>
      <td>Downtown Toronto</td>
      <td>Rosedale</td>
      <td>43.679563</td>
      <td>-79.377529</td>
    </tr>
    <tr>
      <th>92</th>
      <td>M5W</td>
      <td>Downtown Toronto</td>
      <td>Stn A PO Boxes</td>
      <td>43.646435</td>
      <td>-79.374846</td>
    </tr>
    <tr>
      <th>93</th>
      <td>M8W</td>
      <td>Etobicoke</td>
      <td>Alderwood / Long Branch</td>
      <td>43.602414</td>
      <td>-79.543484</td>
    </tr>
    <tr>
      <th>94</th>
      <td>M9W</td>
      <td>Etobicoke</td>
      <td>Northwest</td>
      <td>43.706748</td>
      <td>-79.594054</td>
    </tr>
    <tr>
      <th>95</th>
      <td>M1X</td>
      <td>Scarborough</td>
      <td>Upper Rouge</td>
      <td>43.836125</td>
      <td>-79.205636</td>
    </tr>
    <tr>
      <th>96</th>
      <td>M4X</td>
      <td>Downtown Toronto</td>
      <td>St. James Town / Cabbagetown</td>
      <td>43.667967</td>
      <td>-79.367675</td>
    </tr>
    <tr>
      <th>97</th>
      <td>M5X</td>
      <td>Downtown Toronto</td>
      <td>First Canadian Place / Underground city</td>
      <td>43.648429</td>
      <td>-79.382280</td>
    </tr>
    <tr>
      <th>98</th>
      <td>M8X</td>
      <td>Etobicoke</td>
      <td>The Kingsway / Montgomery Road  / Old Mill North</td>
      <td>43.653654</td>
      <td>-79.506944</td>
    </tr>
    <tr>
      <th>99</th>
      <td>M4Y</td>
      <td>Downtown Toronto</td>
      <td>Church and Wellesley</td>
      <td>43.665860</td>
      <td>-79.383160</td>
    </tr>
    <tr>
      <th>100</th>
      <td>M7Y</td>
      <td>East Toronto</td>
      <td>Business reply mail Processing CentrE</td>
      <td>43.662744</td>
      <td>-79.321558</td>
    </tr>
    <tr>
      <th>101</th>
      <td>M8Y</td>
      <td>Etobicoke</td>
      <td>Old Mill South / King's Mill Park / Sunnylea /...</td>
      <td>43.636258</td>
      <td>-79.498509</td>
    </tr>
    <tr>
      <th>102</th>
      <td>M8Z</td>
      <td>Etobicoke</td>
      <td>Mimico NW / The Queensway West / South of Bloo...</td>
      <td>43.628841</td>
      <td>-79.520999</td>
    </tr>
  </tbody>
</table>
<p>103 rows × 5 columns</p>
</div>



# Segmentation and Clustering below


```python
!conda install -c conda-forge geopy --yes # uncomment this line if you haven't completed the Foursquare API lab
from geopy.geocoders import Nominatim # convert an address into latitude and longitude values
!conda install -c conda-forge folium=0.5.0 --yes # uncomment this line if you haven't completed the Foursquare API lab
import folium # map rendering library
```

    Solving environment: done
    
    ## Package Plan ##
    
      environment location: /opt/conda/envs/Python36
    
      added / updated specs: 
        - geopy
    
    
    The following packages will be downloaded:
    
        package                    |            build
        ---------------------------|-----------------
        openssl-1.1.1f             |       h516909a_0         2.1 MB  conda-forge
        python_abi-3.6             |          1_cp36m           4 KB  conda-forge
        geopy-1.21.0               |             py_0          58 KB  conda-forge
        ca-certificates-2020.4.5.1 |       hecc5488_0         146 KB  conda-forge
        certifi-2020.4.5.1         |   py36h9f0ad1d_0         151 KB  conda-forge
        geographiclib-1.50         |             py_0          34 KB  conda-forge
        ------------------------------------------------------------
                                               Total:         2.5 MB
    
    The following NEW packages will be INSTALLED:
    
        geographiclib:   1.50-py_0         conda-forge
        geopy:           1.21.0-py_0       conda-forge
        python_abi:      3.6-1_cp36m       conda-forge
    
    The following packages will be UPDATED:
    
        ca-certificates: 2020.1.1-0                    --> 2020.4.5.1-hecc5488_0     conda-forge
        certifi:         2019.11.28-py36_0             --> 2020.4.5.1-py36h9f0ad1d_0 conda-forge
        openssl:         1.1.1e-h7b6447c_0             --> 1.1.1f-h516909a_0         conda-forge
    
    
    Downloading and Extracting Packages
    openssl-1.1.1f       | 2.1 MB    | ##################################### | 100% 
    python_abi-3.6       | 4 KB      | ##################################### | 100% 
    geopy-1.21.0         | 58 KB     | ##################################### | 100% 
    ca-certificates-2020 | 146 KB    | ##################################### | 100% 
    certifi-2020.4.5.1   | 151 KB    | ##################################### | 100% 
    geographiclib-1.50   | 34 KB     | ##################################### | 100% 
    Preparing transaction: done
    Verifying transaction: done
    Executing transaction: done
    Solving environment: done
    
    ## Package Plan ##
    
      environment location: /opt/conda/envs/Python36
    
      added / updated specs: 
        - folium=0.5.0
    
    
    The following packages will be downloaded:
    
        package                    |            build
        ---------------------------|-----------------
        folium-0.5.0               |             py_0          45 KB  conda-forge
        vincent-0.4.4              |             py_1          28 KB  conda-forge
        altair-4.1.0               |             py_1         614 KB  conda-forge
        branca-0.4.0               |             py_0          26 KB  conda-forge
        ------------------------------------------------------------
                                               Total:         713 KB
    
    The following NEW packages will be INSTALLED:
    
        altair:  4.1.0-py_1 conda-forge
        branca:  0.4.0-py_0 conda-forge
        folium:  0.5.0-py_0 conda-forge
        vincent: 0.4.4-py_1 conda-forge
    
    
    Downloading and Extracting Packages
    folium-0.5.0         | 45 KB     | ##################################### | 100% 
    vincent-0.4.4        | 28 KB     | ##################################### | 100% 
    altair-4.1.0         | 614 KB    | ##################################### | 100% 
    branca-0.4.0         | 26 KB     | ##################################### | 100% 
    Preparing transaction: done
    Verifying transaction: done
    Executing transaction: done


### Get the latitude and longitude of Toronto-Canada


```python
address = 'Toronto, CA'

geolocator = Nominatim(user_agent="ny_explorer")
location = geolocator.geocode(address)
latitude = location.latitude
longitude = location.longitude
print('The geograpical coordinate of Toronto are {}, {}.'.format(latitude, longitude))                 
```

    The geograpical coordinate of Toronto are 43.6534817, -79.3839347.


### Map of Toronto with neighboorhods


```python
map_toronto = folium.Map(location=[latitude, longitude], zoom_start=10)
# add markers to map
for lat, lng, borough, neighborhood in zip(df_merged['Latitude'], df_merged['Longitude'], df_merged['Borough'], df_merged['Neighborhood']):
    label = '{}, {}'.format(neighborhood, borough)
    label = folium.Popup(label, parse_html=True)
    folium.CircleMarker(
        [lat, lng],
        radius=5,
        popup=label,
        color='blue',
        fill=True,
        fill_color='#3186cc',
        fill_opacity=0.7,
        parse_html=False).add_to(map_toronto)  
    
```


```python
map_toronto
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYiA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYicsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjUzNDgxNywtNzkuMzgzOTM0N10sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB6b29tOiAxMCwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIG1heEJvdW5kczogYm91bmRzLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgbGF5ZXJzOiBbXSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHdvcmxkQ29weUp1bXA6IGZhbHNlLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgY3JzOiBMLkNSUy5FUFNHMzg1NwogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfZWUxYWM5YjRjYzE3NGI1YWE0OWY1ODYxNDg4NjgxMWIgPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICdodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZycsCiAgICAgICAgICAgICAgICB7CiAgImF0dHJpYnV0aW9uIjogbnVsbCwKICAiZGV0ZWN0UmV0aW5hIjogZmFsc2UsCiAgIm1heFpvb20iOiAxOCwKICAibWluWm9vbSI6IDEsCiAgIm5vV3JhcCI6IGZhbHNlLAogICJzdWJkb21haW5zIjogImFiYyIKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzE1NTc4MTU1MjY3OTRiOWVhMWRmZTFmYTgwNjRhOGU0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzUzMjU4NiwtNzkuMzI5NjU2NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yZWNmYTdiYzg5ZGQ0NDk4YWJiNDI2MWI1ZGMzMWZmYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85ZjZjMDAwNTJlYTc0YzNmYjZhODUyNDRjMDc5YTcwOSA9ICQoJzxkaXYgaWQ9Imh0bWxfOWY2YzAwMDUyZWE3NGMzZmI2YTg1MjQ0YzA3OWE3MDkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBhcmt3b29kcywgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMmVjZmE3YmM4OWRkNDQ5OGFiYjQyNjFiNWRjMzFmZmEuc2V0Q29udGVudChodG1sXzlmNmMwMDA1MmVhNzRjM2ZiNmE4NTI0NGMwNzlhNzA5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzE1NTc4MTU1MjY3OTRiOWVhMWRmZTFmYTgwNjRhOGU0LmJpbmRQb3B1cChwb3B1cF8yZWNmYTdiYzg5ZGQ0NDk4YWJiNDI2MWI1ZGMzMWZmYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82MGY0MDk4Y2I4OWI0Yzc3OWFhZGIyMGUxOWM1YTg5OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyNTg4MjI5OTk5OTk5NSwtNzkuMzE1NTcxNTk5OTk5OThdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzdhOGYzY2ZkZDdmNGU0M2E4NDhiZmQ2Y2M1OTA4ZTkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzBjZTVmYTQ5NDM4NGU4ZmFhODA2MDk1NjA0ZGZkNjEgPSAkKCc8ZGl2IGlkPSJodG1sX2MwY2U1ZmE0OTQzODRlOGZhYTgwNjA5NTYwNGRmZDYxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5WaWN0b3JpYSBWaWxsYWdlLCBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jN2E4ZjNjZmRkN2Y0ZTQzYTg0OGJmZDZjYzU5MDhlOS5zZXRDb250ZW50KGh0bWxfYzBjZTVmYTQ5NDM4NGU4ZmFhODA2MDk1NjA0ZGZkNjEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjBmNDA5OGNiODliNGM3NzlhYWRiMjBlMTljNWE4OTkuYmluZFBvcHVwKHBvcHVwX2M3YThmM2NmZGQ3ZjRlNDNhODQ4YmZkNmNjNTkwOGU5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJiOTVlNjVlZGUzYTRmOGViNWIxODAxODQ1ZWI2NTZkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU0MjU5OSwtNzkuMzYwNjM1OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iYTNjYzBkMmI4ODQ0MTM5OGE3MzA3YjBhMTA4NDE2YSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zZGIyMmZhMTkxNTQ0NWVmYmMxOTFkMjcyNWY5OTAxYiA9ICQoJzxkaXYgaWQ9Imh0bWxfM2RiMjJmYTE5MTU0NDVlZmJjMTkxZDI3MjVmOTkwMWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJlZ2VudCBQYXJrIC8gSGFyYm91cmZyb250LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iYTNjYzBkMmI4ODQ0MTM5OGE3MzA3YjBhMTA4NDE2YS5zZXRDb250ZW50KGh0bWxfM2RiMjJmYTE5MTU0NDVlZmJjMTkxZDI3MjVmOTkwMWIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmI5NWU2NWVkZTNhNGY4ZWI1YjE4MDE4NDVlYjY1NmQuYmluZFBvcHVwKHBvcHVwX2JhM2NjMGQyYjg4NDQxMzk4YTczMDdiMGExMDg0MTZhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzYwZDg3MzlkOWFkNzQzYWE4ZTA3ZmY3MTcwNjgyOTAxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzE4NTE3OTk5OTk5OTk2LC03OS40NjQ3NjMyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81NTQ3OGI4ZTUxYjI0OGNlYTAzMjdlMTAwZTRmMTc5OCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iNzEzOGIzNjk5Yjg0YmYzOTFlNjVjYzE4ZDI2ODM1YiA9ICQoJzxkaXYgaWQ9Imh0bWxfYjcxMzhiMzY5OWI4NGJmMzkxZTY1Y2MxOGQyNjgzNWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxhd3JlbmNlIE1hbm9yIC8gTGF3cmVuY2UgSGVpZ2h0cywgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTU0NzhiOGU1MWIyNDhjZWEwMzI3ZTEwMGU0ZjE3OTguc2V0Q29udGVudChodG1sX2I3MTM4YjM2OTliODRiZjM5MWU2NWNjMThkMjY4MzViKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzYwZDg3MzlkOWFkNzQzYWE4ZTA3ZmY3MTcwNjgyOTAxLmJpbmRQb3B1cChwb3B1cF81NTQ3OGI4ZTUxYjI0OGNlYTAzMjdlMTAwZTRmMTc5OCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85OGIxODFmMWIyYTQ0Y2RkOGIwOGM3NDIyOTQ2MDJlMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjMwMTUsLTc5LjM4OTQ5MzhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNWM5ZDY2MTE2YWM5NGZhMmI2YjUyYTc3MjgyZTU5OGQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmQ3Njk1OGJlOTY4NGQ0OTlhOGE5YzkyNjc2OGFjNWMgPSAkKCc8ZGl2IGlkPSJodG1sX2ZkNzY5NThiZTk2ODRkNDk5YThhOWM5MjY3NjhhYzVjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5RdWVlbiYjMzk7cyBQYXJrIC8gT250YXJpbyBQcm92aW5jaWFsIEdvdmVybm1lbnQsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzVjOWQ2NjExNmFjOTRmYTJiNmI1MmE3NzI4MmU1OThkLnNldENvbnRlbnQoaHRtbF9mZDc2OTU4YmU5Njg0ZDQ5OWE4YTljOTI2NzY4YWM1Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85OGIxODFmMWIyYTQ0Y2RkOGIwOGM3NDIyOTQ2MDJlMi5iaW5kUG9wdXAocG9wdXBfNWM5ZDY2MTE2YWM5NGZhMmI2YjUyYTc3MjgyZTU5OGQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMTdlNDU2Y2ViY2E5NDg3NDg4NWQxYzMzNzViMjkwODUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njc4NTU2LC03OS41MzIyNDI0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wMzdkOGIyYTI1MTk0OGU5OTI5ZGU3MzkzYzYyM2MwMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80ZDIyMTAwMDc0NTE0MDZlYTZhNDRjZTQ2ZDA1YjUxMiA9ICQoJzxkaXYgaWQ9Imh0bWxfNGQyMjEwMDA3NDUxNDA2ZWE2YTQ0Y2U0NmQwNWI1MTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPklzbGluZ3RvbiBBdmVudWUsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDM3ZDhiMmEyNTE5NDhlOTkyOWRlNzM5M2M2MjNjMDAuc2V0Q29udGVudChodG1sXzRkMjIxMDAwNzQ1MTQwNmVhNmE0NGNlNDZkMDViNTEyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzE3ZTQ1NmNlYmNhOTQ4NzQ4ODVkMWMzMzc1YjI5MDg1LmJpbmRQb3B1cChwb3B1cF8wMzdkOGIyYTI1MTk0OGU5OTI5ZGU3MzkzYzYyM2MwMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80ZTA5ZTY3ZTA2NjQ0YjhlODU4MzcyZThhY2M4OWRkYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjgwNjY4NjI5OTk5OTk5NiwtNzkuMTk0MzUzNDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMGY0NWQ1MzYwMDBhNDZjOThiYmFkNmFiN2U4OWUxN2EgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWUyMmE4N2ExMDU4NGVlMmJjNDU4MTkzMDk1YzgxOWUgPSAkKCc8ZGl2IGlkPSJodG1sXzVlMjJhODdhMTA1ODRlZTJiYzQ1ODE5MzA5NWM4MTllIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5NYWx2ZXJuIC8gUm91Z2UsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wZjQ1ZDUzNjAwMGE0NmM5OGJiYWQ2YWI3ZTg5ZTE3YS5zZXRDb250ZW50KGh0bWxfNWUyMmE4N2ExMDU4NGVlMmJjNDU4MTkzMDk1YzgxOWUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNGUwOWU2N2UwNjY0NGI4ZTg1ODM3MmU4YWNjODlkZGIuYmluZFBvcHVwKHBvcHVwXzBmNDVkNTM2MDAwYTQ2Yzk4YmJhZDZhYjdlODllMTdhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzA5MDI3MWE1NmZjZTQ3NDg4ZjMwNTNjNzY5OWYzYjZjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzQ1OTA1Nzk5OTk5OTk2LC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMGVhOWExZGNiOTQyNDMxMmFkZGE2N2I1NDNkMDcxMTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTQ1NDIyZWJiMjZhNDBhZWE2ZWQwNWYzN2VlZmJkMmUgPSAkKCc8ZGl2IGlkPSJodG1sX2E0NTQyMmViYjI2YTQwYWVhNmVkMDVmMzdlZWZiZDJlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Eb24gTWlsbHMsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBlYTlhMWRjYjk0MjQzMTJhZGRhNjdiNTQzZDA3MTEzLnNldENvbnRlbnQoaHRtbF9hNDU0MjJlYmIyNmE0MGFlYTZlZDA1ZjM3ZWVmYmQyZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wOTAyNzFhNTZmY2U0NzQ4OGYzMDUzYzc2OTlmM2I2Yy5iaW5kUG9wdXAocG9wdXBfMGVhOWExZGNiOTQyNDMxMmFkZGE2N2I1NDNkMDcxMTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOGY3ZWRhNWUzODRmNDFmZmI4ZDA1OTIzMGFlMWQyYjUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDYzOTcyLC03OS4zMDk5MzddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjBmMDQyYjE0NWI1NGNlZmE4NWE3NWZkMDA1NjZiY2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGUwMzg3ZTg5MDc2NDZiMDgwNzUyNDYxM2Y5MDllNTIgPSAkKCc8ZGl2IGlkPSJodG1sXzRlMDM4N2U4OTA3NjQ2YjA4MDc1MjQ2MTNmOTA5ZTUyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QYXJrdmlldyBIaWxsIC8gV29vZGJpbmUgR2FyZGVucywgRWFzdCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mMGYwNDJiMTQ1YjU0Y2VmYTg1YTc1ZmQwMDU2NmJjYy5zZXRDb250ZW50KGh0bWxfNGUwMzg3ZTg5MDc2NDZiMDgwNzUyNDYxM2Y5MDllNTIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOGY3ZWRhNWUzODRmNDFmZmI4ZDA1OTIzMGFlMWQyYjUuYmluZFBvcHVwKHBvcHVwX2YwZjA0MmIxNDViNTRjZWZhODVhNzVmZDAwNTY2YmNjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzI5MGQxOTYwODZkYTRkMjM5ZWMzZjc2MzM3ZDExOTczID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU3MTYxOCwtNzkuMzc4OTM3MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDA3OGM5ODQyZTQ5NDljZWJiZDYyOTMzMWFkZTcwN2QgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjEwY2ViZjk4NzAyNGFlMjk1ZWM0YTZiMGZiMjJjZDQgPSAkKCc8ZGl2IGlkPSJodG1sXzYxMGNlYmY5ODcwMjRhZTI5NWVjNGE2YjBmYjIyY2Q0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HYXJkZW4gRGlzdHJpY3QsIFJ5ZXJzb24sIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2QwNzhjOTg0MmU0OTQ5Y2ViYmQ2MjkzMzFhZGU3MDdkLnNldENvbnRlbnQoaHRtbF82MTBjZWJmOTg3MDI0YWUyOTVlYzRhNmIwZmIyMmNkNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yOTBkMTk2MDg2ZGE0ZDIzOWVjM2Y3NjMzN2QxMTk3My5iaW5kUG9wdXAocG9wdXBfZDA3OGM5ODQyZTQ5NDljZWJiZDYyOTMzMWFkZTcwN2QpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOGJjZmY1NWI1NmZlNDhmNTgzNjQ3MTk2MWYyOTE1ZmUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDk1NzcsLTc5LjQ0NTA3MjU5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzdhZWQ5MDk5NjM4YjRiNWI4YTVjZjQ1ZTA0ZjdlY2FjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2UzMzUxYTVhMzc2MDQ0MjFiNjRiNWI4MmE0NGQ5OGE1ID0gJCgnPGRpdiBpZD0iaHRtbF9lMzM1MWE1YTM3NjA0NDIxYjY0YjViODJhNDRkOThhNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R2xlbmNhaXJuLCBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83YWVkOTA5OTYzOGI0YjViOGE1Y2Y0NWUwNGY3ZWNhYy5zZXRDb250ZW50KGh0bWxfZTMzNTFhNWEzNzYwNDQyMWI2NGI1YjgyYTQ0ZDk4YTUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOGJjZmY1NWI1NmZlNDhmNTgzNjQ3MTk2MWYyOTE1ZmUuYmluZFBvcHVwKHBvcHVwXzdhZWQ5MDk5NjM4YjRiNWI4YTVjZjQ1ZTA0ZjdlY2FjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzdjZDRlMGU0OTEwNDRiMzQ4MzMwZjI2YjE2YTM1NDgyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUwOTQzMiwtNzkuNTU0NzI0NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDcwNTg3NTdiYmYzNDYwZDhjYzhiZDc4YjQyYzU3N2EgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWJiNjczYzVkNTIyNDFhMWEwZDAwZmViMGIwOGQxYWMgPSAkKCc8ZGl2IGlkPSJodG1sXzFiYjY3M2M1ZDUyMjQxYTFhMGQwMGZlYjBiMDhkMWFjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XZXN0IERlYW5lIFBhcmsgLyBQcmluY2VzcyBHYXJkZW5zIC8gTWFydGluIEdyb3ZlIC8gSXNsaW5ndG9uIC8gQ2xvdmVyZGFsZSwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kNzA1ODc1N2JiZjM0NjBkOGNjOGJkNzhiNDJjNTc3YS5zZXRDb250ZW50KGh0bWxfMWJiNjczYzVkNTIyNDFhMWEwZDAwZmViMGIwOGQxYWMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfN2NkNGUwZTQ5MTA0NGIzNDgzMzBmMjZiMTZhMzU0ODIuYmluZFBvcHVwKHBvcHVwX2Q3MDU4NzU3YmJmMzQ2MGQ4Y2M4YmQ3OGI0MmM1NzdhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRlNzAxYjU0YTI3ZjQ4MTg5ZjBlZWFlZDM3Mzg1NGRmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzg0NTM1MSwtNzkuMTYwNDk3MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzEyYzFkN2Y3NmVlNGI4Yjk4ZGMwZWE2NDQ3ZDYzNGMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzViYWJhMGYwM2VjNDFmZjlmYWIzODk2ZWI5MTZmNGIgPSAkKCc8ZGl2IGlkPSJodG1sXzM1YmFiYTBmMDNlYzQxZmY5ZmFiMzg5NmViOTE2ZjRiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Sb3VnZSBIaWxsIC8gUG9ydCBVbmlvbiAvIEhpZ2hsYW5kIENyZWVrLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzEyYzFkN2Y3NmVlNGI4Yjk4ZGMwZWE2NDQ3ZDYzNGMuc2V0Q29udGVudChodG1sXzM1YmFiYTBmMDNlYzQxZmY5ZmFiMzg5NmViOTE2ZjRiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzRlNzAxYjU0YTI3ZjQ4MTg5ZjBlZWFlZDM3Mzg1NGRmLmJpbmRQb3B1cChwb3B1cF9jMTJjMWQ3Zjc2ZWU0YjhiOThkYzBlYTY0NDdkNjM0Yyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kMjM3NzFhNDE1MDk0MDA2YWIwYjVjMTdiODNiMDBmYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyNTg5OTcwMDAwMDAxLC03OS4zNDA5MjNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTVkOTQyZTE0ZDI4NDFjMGE3ZTJmZDk4ZmYyZjFlOWMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjQyYTQzN2RlODZhNDg3NGE4MjJmMTgxMGU0YWFiNmMgPSAkKCc8ZGl2IGlkPSJodG1sX2Y0MmE0MzdkZTg2YTQ4NzRhODIyZjE4MTBlNGFhYjZjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Eb24gTWlsbHMsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2U1ZDk0MmUxNGQyODQxYzBhN2UyZmQ5OGZmMmYxZTljLnNldENvbnRlbnQoaHRtbF9mNDJhNDM3ZGU4NmE0ODc0YTgyMmYxODEwZTRhYWI2Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kMjM3NzFhNDE1MDk0MDA2YWIwYjVjMTdiODNiMDBmYy5iaW5kUG9wdXAocG9wdXBfZTVkOTQyZTE0ZDI4NDFjMGE3ZTJmZDk4ZmYyZjFlOWMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmQxMDQyN2UyZWEyNDcxN2EyOThkNDUwMjY3NDExNGYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTUzNDM5MDAwMDAwMDUsLTc5LjMxODM4ODddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjBiZjA5ZmE0YmQ1NDBjYzk4YmI3YjAzZmM2ZGEyMGYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzY1NmY3MjBmODdmNDZjMjg2YWVkZTRkMjQwN2Q0YWQgPSAkKCc8ZGl2IGlkPSJodG1sXzM2NTZmNzIwZjg3ZjQ2YzI4NmFlZGU0ZDI0MDdkNGFkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Xb29kYmluZSBIZWlnaHRzLCBFYXN0IFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2IwYmYwOWZhNGJkNTQwY2M5OGJiN2IwM2ZjNmRhMjBmLnNldENvbnRlbnQoaHRtbF8zNjU2ZjcyMGY4N2Y0NmMyODZhZWRlNGQyNDA3ZDRhZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mZDEwNDI3ZTJlYTI0NzE3YTI5OGQ0NTAyNjc0MTE0Zi5iaW5kUG9wdXAocG9wdXBfYjBiZjA5ZmE0YmQ1NDBjYzk4YmI3YjAzZmM2ZGEyMGYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjJmNDRkMjQ5NDA1NDBkZWFlNTI3OTI5NjExYTZkYmYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTE0OTM5LC03OS4zNzU0MTc5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlmYTZjZWJjYmE2ZDRmM2M5OTZhOGJkYmFhNjM3MzE5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzg3Y2FkNDczOGFhMDQ0OWM4YTNmZjg3OTk3MGQ3MThkID0gJCgnPGRpdiBpZD0iaHRtbF84N2NhZDQ3MzhhYTA0NDljOGEzZmY4Nzk5NzBkNzE4ZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3QuIEphbWVzIFRvd24sIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzlmYTZjZWJjYmE2ZDRmM2M5OTZhOGJkYmFhNjM3MzE5LnNldENvbnRlbnQoaHRtbF84N2NhZDQ3MzhhYTA0NDljOGEzZmY4Nzk5NzBkNzE4ZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82MmY0NGQyNDk0MDU0MGRlYWU1Mjc5Mjk2MTFhNmRiZi5iaW5kUG9wdXAocG9wdXBfOWZhNmNlYmNiYTZkNGYzYzk5NmE4YmRiYWE2MzczMTkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTdkNWNjODkyZDAzNDAwODk3ZDljYThhZmJmZjM3MTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTM3ODEzLC03OS40MjgxOTE0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wYjRhODMxYThjMWM0NGZiODMwMWQwZThjOGMxM2Q1ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82NjViZWU5OGU2OTk0MzAxOGU2NzYyMDk5NzY1NGZmZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNjY1YmVlOThlNjk5NDMwMThlNjc2MjA5OTc2NTRmZmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkh1bWV3b29kLUNlZGFydmFsZSwgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMGI0YTgzMWE4YzFjNDRmYjgzMDFkMGU4YzhjMTNkNWUuc2V0Q29udGVudChodG1sXzY2NWJlZTk4ZTY5OTQzMDE4ZTY3NjIwOTk3NjU0ZmZlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2U3ZDVjYzg5MmQwMzQwMDg5N2Q5Y2E4YWZiZmYzNzE0LmJpbmRQb3B1cChwb3B1cF8wYjRhODMxYThjMWM0NGZiODMwMWQwZThjOGMxM2Q1ZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84YzIwZDk2YzYwMzE0YjA4ODFkMzRiYTJlYjlkMjc1ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0MzUxNTIsLTc5LjU3NzIwMDc5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2YxMDk4NjViOGFhYTQ0MWM5Y2VmZWNiYTFkOGMwNDFmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzAzMDQ1NGI3MTg0YjQxMzQ4YTAwOTNhYTUxOTRlN2E5ID0gJCgnPGRpdiBpZD0iaHRtbF8wMzA0NTRiNzE4NGI0MTM0OGEwMDkzYWE1MTk0ZTdhOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RXJpbmdhdGUgLyBCbG9vcmRhbGUgR2FyZGVucyAvIE9sZCBCdXJuaGFtdGhvcnBlIC8gTWFya2xhbmQgV29vZCwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mMTA5ODY1YjhhYWE0NDFjOWNlZmVjYmExZDhjMDQxZi5zZXRDb250ZW50KGh0bWxfMDMwNDU0YjcxODRiNDEzNDhhMDA5M2FhNTE5NGU3YTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOGMyMGQ5NmM2MDMxNGIwODgxZDM0YmEyZWI5ZDI3NWUuYmluZFBvcHVwKHBvcHVwX2YxMDk4NjViOGFhYTQ0MWM5Y2VmZWNiYTFkOGMwNDFmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzQ1YzljZmUwNDZjZjRjMzRhYTg1NDE3Zjc4MGY2NmEzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzYzNTcyNiwtNzkuMTg4NzExNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMGQ2NWI1ZmRiMDQ0OWVmOTUxOTliZjgzODUxNmY3NCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85Y2NlMTNiYjgyYWU0ODFiODkwNmIzMjhhYjFkZTk3NCA9ICQoJzxkaXYgaWQ9Imh0bWxfOWNjZTEzYmI4MmFlNDgxYjg5MDZiMzI4YWIxZGU5NzQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkd1aWxkd29vZCAvIE1vcm5pbmdzaWRlIC8gV2VzdCBIaWxsLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjBkNjViNWZkYjA0NDllZjk1MTk5YmY4Mzg1MTZmNzQuc2V0Q29udGVudChodG1sXzljY2UxM2JiODJhZTQ4MWI4OTA2YjMyOGFiMWRlOTc0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQ1YzljZmUwNDZjZjRjMzRhYTg1NDE3Zjc4MGY2NmEzLmJpbmRQb3B1cChwb3B1cF8yMGQ2NWI1ZmRiMDQ0OWVmOTUxOTliZjgzODUxNmY3NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82OGFhMGRjMzFhOTA0ZjEzYjUxZTIxNWFmMmU2ZTgxYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3NjM1NzM5OTk5OTk5LC03OS4yOTMwMzEyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2IyODQxZmY1Mjk2MzRjZmJhMzI0NGQ5NjI5Y2RjMGQxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQwOTk1Mzc1MzljMzQ5NzNiNzNiOTBkYTYzNWZiNGUyID0gJCgnPGRpdiBpZD0iaHRtbF80MDk5NTM3NTM5YzM0OTczYjczYjkwZGE2MzVmYjRlMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VGhlIEJlYWNoZXMsIEVhc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjI4NDFmZjUyOTYzNGNmYmEzMjQ0ZDk2MjljZGMwZDEuc2V0Q29udGVudChodG1sXzQwOTk1Mzc1MzljMzQ5NzNiNzNiOTBkYTYzNWZiNGUyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY4YWEwZGMzMWE5MDRmMTNiNTFlMjE1YWYyZTZlODFiLmJpbmRQb3B1cChwb3B1cF9iMjg0MWZmNTI5NjM0Y2ZiYTMyNDRkOTYyOWNkYzBkMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83NTczZDc0ZTdiZGU0MGIwOGExZDkyZDk0MmU4MGJiMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NDc3MDc5OTk5OTk5NiwtNzkuMzczMzA2NF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hOGIyY2U4ZDEwZDU0YjE4OTJiMWZiNjk3NzM2YjYwOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lOGUwZTYwMWEwNTM0OGRhYmQ0YzQ4ZDIyMGNhYTZlYiA9ICQoJzxkaXYgaWQ9Imh0bWxfZThlMGU2MDFhMDUzNDhkYWJkNGM0OGQyMjBjYWE2ZWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJlcmN6eSBQYXJrLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hOGIyY2U4ZDEwZDU0YjE4OTJiMWZiNjk3NzM2YjYwOS5zZXRDb250ZW50KGh0bWxfZThlMGU2MDFhMDUzNDhkYWJkNGM0OGQyMjBjYWE2ZWIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzU3M2Q3NGU3YmRlNDBiMDhhMWQ5MmQ5NDJlODBiYjEuYmluZFBvcHVwKHBvcHVwX2E4YjJjZThkMTBkNTRiMTg5MmIxZmI2OTc3MzZiNjA5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzE4MzBmNDQ4MDc0ZjQxZmQ5ZGE2ZDk2ZTVkNmI2MGRkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg5MDI1NiwtNzkuNDUzNTEyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzE5YzA4MzQ5NjFiNTQ2YTRhYWMwODA2MGQzZDVjNTk0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzBiNjIxMmQ5ZmQ2NDRkZGJhNzVlNDA4NzU5Y2FkNDgzID0gJCgnPGRpdiBpZD0iaHRtbF8wYjYyMTJkOWZkNjQ0ZGRiYTc1ZTQwODc1OWNhZDQ4MyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2FsZWRvbmlhLUZhaXJiYW5rcywgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTljMDgzNDk2MWI1NDZhNGFhYzA4MDYwZDNkNWM1OTQuc2V0Q29udGVudChodG1sXzBiNjIxMmQ5ZmQ2NDRkZGJhNzVlNDA4NzU5Y2FkNDgzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzE4MzBmNDQ4MDc0ZjQxZmQ5ZGE2ZDk2ZTVkNmI2MGRkLmJpbmRQb3B1cChwb3B1cF8xOWMwODM0OTYxYjU0NmE0YWFjMDgwNjBkM2Q1YzU5NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iNDBiNjM2OTM3MDY0ODBmYmZiYThjYTg1YTg3ODRhNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc3MDk5MjEsLTc5LjIxNjkxNzQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzc0MDgyOTZmNTQ2OTQ2OGQ4Yzc4MTcwNzc0N2E4N2YxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzgzZWYwMWI2Mzk4MTQxMjU5MTFjOTQ3NGQ5ZmRlNjMxID0gJCgnPGRpdiBpZD0iaHRtbF84M2VmMDFiNjM5ODE0MTI1OTExYzk0NzRkOWZkZTYzMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V29idXJuLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzQwODI5NmY1NDY5NDY4ZDhjNzgxNzA3NzQ3YTg3ZjEuc2V0Q29udGVudChodG1sXzgzZWYwMWI2Mzk4MTQxMjU5MTFjOTQ3NGQ5ZmRlNjMxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2I0MGI2MzY5MzcwNjQ4MGZiZmJhOGNhODVhODc4NGE0LmJpbmRQb3B1cChwb3B1cF83NDA4Mjk2ZjU0Njk0NjhkOGM3ODE3MDc3NDdhODdmMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lM2Y3OGM2MDhiMDE0YzE1ODg5OWZhMDgzY2MwMjcyMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcwOTA2MDQsLTc5LjM2MzQ1MTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDU5NzM4MGZjNjhhNDExYmJiZGIzZGZkNDVhZmFhZWIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmEzZTkzMWMyZTUxNDkxYjk5NGM2NjJjYmZjYjY4NzYgPSAkKCc8ZGl2IGlkPSJodG1sXzZhM2U5MzFjMmU1MTQ5MWI5OTRjNjYyY2JmY2I2ODc2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MZWFzaWRlLCBFYXN0IFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Q1OTczODBmYzY4YTQxMWJiYmRiM2RmZDQ1YWZhYWViLnNldENvbnRlbnQoaHRtbF82YTNlOTMxYzJlNTE0OTFiOTk0YzY2MmNiZmNiNjg3Nik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lM2Y3OGM2MDhiMDE0YzE1ODg5OWZhMDgzY2MwMjcyMy5iaW5kUG9wdXAocG9wdXBfZDU5NzM4MGZjNjhhNDExYmJiZGIzZGZkNDVhZmFhZWIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmI1NWE4MzUyZjZiNGMxN2JiZDQ5Y2Y5NGEyMzhmYTAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTc5NTI0LC03OS4zODczODI2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2I0MmM3ZmVmMTQ1YTRmNTY4NzVkZWZiMTNiMmU1MzUxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2MzZDgyYTQ1OWU1MzQyZmE4ZmUxMGRjYTkxZWE1MjhhID0gJCgnPGRpdiBpZD0iaHRtbF9jM2Q4MmE0NTllNTM0MmZhOGZlMTBkY2E5MWVhNTI4YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2VudHJhbCBCYXkgU3RyZWV0LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iNDJjN2ZlZjE0NWE0ZjU2ODc1ZGVmYjEzYjJlNTM1MS5zZXRDb250ZW50KGh0bWxfYzNkODJhNDU5ZTUzNDJmYThmZTEwZGNhOTFlYTUyOGEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZmI1NWE4MzUyZjZiNGMxN2JiZDQ5Y2Y5NGEyMzhmYTAuYmluZFBvcHVwKHBvcHVwX2I0MmM3ZmVmMTQ1YTRmNTY4NzVkZWZiMTNiMmU1MzUxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2VjY2M4ODBjZDc5YzQ1ZDZiNmVmMWI5ODdjMGRiZjBiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY5NTQyLC03OS40MjI1NjM3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2FmZDJiMjc1MmEyNzQ5ZTE4YWU0N2NlZjZlMzJkNjBlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2M3OGE1NjNiZmM4MTRmMmU5MTMxYThlMzQzYzhjOTM1ID0gJCgnPGRpdiBpZD0iaHRtbF9jNzhhNTYzYmZjODE0ZjJlOTEzMWE4ZTM0M2M4YzkzNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2hyaXN0aWUsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2FmZDJiMjc1MmEyNzQ5ZTE4YWU0N2NlZjZlMzJkNjBlLnNldENvbnRlbnQoaHRtbF9jNzhhNTYzYmZjODE0ZjJlOTEzMWE4ZTM0M2M4YzkzNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lY2NjODgwY2Q3OWM0NWQ2YjZlZjFiOTg3YzBkYmYwYi5iaW5kUG9wdXAocG9wdXBfYWZkMmIyNzUyYTI3NDllMThhZTQ3Y2VmNmUzMmQ2MGUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2Q1YTFiMDE3MWJiNGM0MWIzYjk3MDhjOGUyMTViMzIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43NzMxMzYsLTc5LjIzOTQ3NjA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzUxMzZjM2E2Y2MxMjQ2MzM5M2FkZWE5ZmFhZjNmMGRiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2FiY2NlZDlhNTRlYjRjNjM4YzY4YmVkMjRmNjU2MWRkID0gJCgnPGRpdiBpZD0iaHRtbF9hYmNjZWQ5YTU0ZWI0YzYzOGM2OGJlZDI0ZjY1NjFkZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2VkYXJicmFlLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTEzNmMzYTZjYzEyNDYzMzkzYWRlYTlmYWFmM2YwZGIuc2V0Q29udGVudChodG1sX2FiY2NlZDlhNTRlYjRjNjM4YzY4YmVkMjRmNjU2MWRkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzNkNWExYjAxNzFiYjRjNDFiM2I5NzA4YzhlMjE1YjMyLmJpbmRQb3B1cChwb3B1cF81MTM2YzNhNmNjMTI0NjMzOTNhZGVhOWZhYWYzZjBkYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hY2ZmOTEyNjljYzQ0Nzc2OTU4ZGE5ZjhhMDI4ODgxOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjgwMzc2MjIsLTc5LjM2MzQ1MTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTRkYzgyN2Y5MzJmNDc4NzgzZGJjYmFkNjg3NWE3MWIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjA3ZjgyN2Q1MGRlNDlkNDliODcyM2FhOTFhMTU2MTAgPSAkKCc8ZGl2IGlkPSJodG1sXzYwN2Y4MjdkNTBkZTQ5ZDQ5Yjg3MjNhYTkxYTE1NjEwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IaWxsY3Jlc3QgVmlsbGFnZSwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTRkYzgyN2Y5MzJmNDc4NzgzZGJjYmFkNjg3NWE3MWIuc2V0Q29udGVudChodG1sXzYwN2Y4MjdkNTBkZTQ5ZDQ5Yjg3MjNhYTkxYTE1NjEwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2FjZmY5MTI2OWNjNDQ3NzY5NThkYTlmOGEwMjg4ODE4LmJpbmRQb3B1cChwb3B1cF8xNGRjODI3ZjkzMmY0Nzg3ODNkYmNiYWQ2ODc1YTcxYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wYzg3M2Q1YmI5M2I0ODBjYWEzMTUwMGFhZTYyYzk2MCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1NDMyODMsLTc5LjQ0MjI1OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWM4ZmRmN2Y4NTlhNDU2NzlkNWVkMzdlZjc4YTNkM2EgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDcwYzdhMTc5NTgzNDVhZDlhN2JjMjNkMTk2OThiNDkgPSAkKCc8ZGl2IGlkPSJodG1sXzQ3MGM3YTE3OTU4MzQ1YWQ5YTdiYzIzZDE5Njk4YjQ5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CYXRodXJzdCBNYW5vciAvIFdpbHNvbiBIZWlnaHRzIC8gRG93bnN2aWV3IE5vcnRoLCBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85YzhmZGY3Zjg1OWE0NTY3OWQ1ZWQzN2VmNzhhM2QzYS5zZXRDb250ZW50KGh0bWxfNDcwYzdhMTc5NTgzNDVhZDlhN2JjMjNkMTk2OThiNDkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMGM4NzNkNWJiOTNiNDgwY2FhMzE1MDBhYWU2MmM5NjAuYmluZFBvcHVwKHBvcHVwXzljOGZkZjdmODU5YTQ1Njc5ZDVlZDM3ZWY3OGEzZDNhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2NjZTI3ZmZhOGIwNDQ1ZTFhZWRhZWIzNTYzM2EyNmJkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzA1MzY4OSwtNzkuMzQ5MzcxOTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODBlNzE0NzUyMjYyNGNlYmEyMTI0Y2MwOGJmMGIxNGYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYmFmOGM4YjBkZjUyNGNkNjk5MTA2NmUwOTEzNTJlODAgPSAkKCc8ZGl2IGlkPSJodG1sX2JhZjhjOGIwZGY1MjRjZDY5OTEwNjZlMDkxMzUyZTgwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaG9ybmNsaWZmZSBQYXJrLCBFYXN0IFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgwZTcxNDc1MjI2MjRjZWJhMjEyNGNjMDhiZjBiMTRmLnNldENvbnRlbnQoaHRtbF9iYWY4YzhiMGRmNTI0Y2Q2OTkxMDY2ZTA5MTM1MmU4MCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jY2UyN2ZmYThiMDQ0NWUxYWVkYWViMzU2MzNhMjZiZC5iaW5kUG9wdXAocG9wdXBfODBlNzE0NzUyMjYyNGNlYmEyMTI0Y2MwOGJmMGIxNGYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjg1ZDE3MTAwOTU4NDIwNTkzODI1ZTQxMTliMzU5ZGIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTA1NzEyMDAwMDAwMSwtNzkuMzg0NTY3NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jOTVkOGFkNDhiNjY0NmZlYmI5ZGZkMTU5YTU5NDdlMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81ODY3OTFkZjE3ODQ0ZDg0YjhjOWE0MDRhMTgxNWZjNCA9ICQoJzxkaXYgaWQ9Imh0bWxfNTg2NzkxZGYxNzg0NGQ4NGI4YzlhNDA0YTE4MTVmYzQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJpY2htb25kIC8gQWRlbGFpZGUgLyBLaW5nLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jOTVkOGFkNDhiNjY0NmZlYmI5ZGZkMTU5YTU5NDdlMC5zZXRDb250ZW50KGh0bWxfNTg2NzkxZGYxNzg0NGQ4NGI4YzlhNDA0YTE4MTVmYzQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjg1ZDE3MTAwOTU4NDIwNTkzODI1ZTQxMTliMzU5ZGIuYmluZFBvcHVwKHBvcHVwX2M5NWQ4YWQ0OGI2NjQ2ZmViYjlkZmQxNTlhNTk0N2UwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzI4M2UxMmUwMzYyZTQwNjg4N2ExYWQzMWY0MjJkZjdlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY5MDA1MTAwMDAwMDEsLTc5LjQ0MjI1OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfY2ZjYmJjZDNjYWM1NDAyYWFmYWRjOWEwM2FlZWFkMzAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZGY4ZThhYTRlZTdiNGFmYTgyNGIyMDRjOWY5OWI5MTcgPSAkKCc8ZGl2IGlkPSJodG1sX2RmOGU4YWE0ZWU3YjRhZmE4MjRiMjA0YzlmOTliOTE3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5EdWZmZXJpbiAvIERvdmVyY291cnQgVmlsbGFnZSwgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jZmNiYmNkM2NhYzU0MDJhYWZhZGM5YTAzYWVlYWQzMC5zZXRDb250ZW50KGh0bWxfZGY4ZThhYTRlZTdiNGFmYTgyNGIyMDRjOWY5OWI5MTcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjgzZTEyZTAzNjJlNDA2ODg3YTFhZDMxZjQyMmRmN2UuYmluZFBvcHVwKHBvcHVwX2NmY2JiY2QzY2FjNTQwMmFhZmFkYzlhMDNhZWVhZDMwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzgzNzBlOTI3ZjA2NjQ2ZTk5OGMyNmM1N2MyNDFjMDM1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzQ0NzM0MiwtNzkuMjM5NDc2MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWZmMTVjZDI1NGY3NDY0MWE5MTg3MTBlM2YyZjAxNjkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOWVmMDI1ZjQ1MTNjNDhhMGFjZDdhYmVkZWYzYTZkN2MgPSAkKCc8ZGl2IGlkPSJodG1sXzllZjAyNWY0NTEzYzQ4YTBhY2Q3YWJlZGVmM2E2ZDdjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TY2FyYm9yb3VnaCBWaWxsYWdlLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWZmMTVjZDI1NGY3NDY0MWE5MTg3MTBlM2YyZjAxNjkuc2V0Q29udGVudChodG1sXzllZjAyNWY0NTEzYzQ4YTBhY2Q3YWJlZGVmM2E2ZDdjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzgzNzBlOTI3ZjA2NjQ2ZTk5OGMyNmM1N2MyNDFjMDM1LmJpbmRQb3B1cChwb3B1cF85ZmYxNWNkMjU0Zjc0NjQxYTkxODcxMGUzZjJmMDE2OSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yYmFhNjk3ZjljOTk0ZGVhYWNhYzJmNWI3OTc4MGYzNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc3ODUxNzUsLTc5LjM0NjU1NTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzRmYmYxOTc5YzBhNDZmNzg1MTAxYjhkMjE3NTFhZDIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOWI3YzZjOWJiY2UyNDkyNzk4MTY3MzBlYmU1MzVlOGMgPSAkKCc8ZGl2IGlkPSJodG1sXzliN2M2YzliYmNlMjQ5Mjc5ODE2NzMwZWJlNTM1ZThjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5GYWlydmlldyAvIEhlbnJ5IEZhcm0gLyBPcmlvbGUsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzM0ZmJmMTk3OWMwYTQ2Zjc4NTEwMWI4ZDIxNzUxYWQyLnNldENvbnRlbnQoaHRtbF85YjdjNmM5YmJjZTI0OTI3OTgxNjczMGViZTUzNWU4Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yYmFhNjk3ZjljOTk0ZGVhYWNhYzJmNWI3OTc4MGYzNS5iaW5kUG9wdXAocG9wdXBfMzRmYmYxOTc5YzBhNDZmNzg1MTAxYjhkMjE3NTFhZDIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzNlNjkxZjc1N2ZhNGExNTkyMmIyY2E2MzM1Mzc5YTEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Njc5ODAzLC03OS40ODcyNjE5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMDUxYWEwMTkwYWU0MTRhYjk0OWUwNWExZTY2Nzk0ZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83MWU2YmM2MDg5NDA0MGM4YmYxYzZjNDgxYmI3MTQxMiA9ICQoJzxkaXYgaWQ9Imh0bWxfNzFlNmJjNjA4OTQwNDBjOGJmMWM2YzQ4MWJiNzE0MTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRod29vZCBQYXJrIC8gWW9yayBVbml2ZXJzaXR5LCBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yMDUxYWEwMTkwYWU0MTRhYjk0OWUwNWExZTY2Nzk0ZC5zZXRDb250ZW50KGh0bWxfNzFlNmJjNjA4OTQwNDBjOGJmMWM2YzQ4MWJiNzE0MTIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzNlNjkxZjc1N2ZhNGExNTkyMmIyY2E2MzM1Mzc5YTEuYmluZFBvcHVwKHBvcHVwXzIwNTFhYTAxOTBhZTQxNGFiOTQ5ZTA1YTFlNjY3OTRkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRjNDYyNjk2MDgzYjRkODZiNDMzM2JkNGNmOTMwYzMzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg1MzQ3LC03OS4zMzgxMDY1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzZkNGM4NDAxNmFhZTQyZDVhNTFjNjAwM2NiOTFiMDAzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzMzMGY1MzA1MjM3NjRlZjg5ZDA0ZjdmOWI3ZDIxNjQ4ID0gJCgnPGRpdiBpZD0iaHRtbF8zMzBmNTMwNTIzNzY0ZWY4OWQwNGY3ZjliN2QyMTY0OCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RWFzdCBUb3JvbnRvLCBFYXN0IFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzZkNGM4NDAxNmFhZTQyZDVhNTFjNjAwM2NiOTFiMDAzLnNldENvbnRlbnQoaHRtbF8zMzBmNTMwNTIzNzY0ZWY4OWQwNGY3ZjliN2QyMTY0OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80YzQ2MjY5NjA4M2I0ZDg2YjQzMzNiZDRjZjkzMGMzMy5iaW5kUG9wdXAocG9wdXBfNmQ0Yzg0MDE2YWFlNDJkNWE1MWM2MDAzY2I5MWIwMDMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzY5MTMyOTZiYzhlNDM5N2IwNGYxYWEwOGMxMzQ0ZGUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDA4MTU3LC03OS4zODE3NTIyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xOThmYzdjNDI4NmM0MDMxYTBkM2JjM2RkN2QxOTdkMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lMDZkY2RjY2M0ZTQ0YzFiOWNmMWVhYTEzODQ5ZmNiMyA9ICQoJzxkaXYgaWQ9Imh0bWxfZTA2ZGNkY2NjNGU0NGMxYjljZjFlYWExMzg0OWZjYjMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhhcmJvdXJmcm9udCBFYXN0IC8gVW5pb24gU3RhdGlvbiAvIFRvcm9udG8gSXNsYW5kcywgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTk4ZmM3YzQyODZjNDAzMWEwZDNiYzNkZDdkMTk3ZDAuc2V0Q29udGVudChodG1sX2UwNmRjZGNjYzRlNDRjMWI5Y2YxZWFhMTM4NDlmY2IzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2M2OTEzMjk2YmM4ZTQzOTdiMDRmMWFhMDhjMTM0NGRlLmJpbmRQb3B1cChwb3B1cF8xOThmYzdjNDI4NmM0MDMxYTBkM2JjM2RkN2QxOTdkMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hODViN2VjZTJmYmY0MjY5OTk5OWRkMDY2OWM3MTU4YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NzkyNjcwMDAwMDAwNiwtNzkuNDE5NzQ5N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zYmVlNDcyZGM0NDU0ZDA4YjU1NDZkZTc1YjYxNTJmMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wMDNlZmVjODQ5ZDg0YTdmYTA5OTYwZDQ2YzRiOTYyMyA9ICQoJzxkaXYgaWQ9Imh0bWxfMDAzZWZlYzg0OWQ4NGE3ZmEwOTk2MGQ0NmM0Yjk2MjMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxpdHRsZSBQb3J0dWdhbCAvIFRyaW5pdHksIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2JlZTQ3MmRjNDQ1NGQwOGI1NTQ2ZGU3NWI2MTUyZjEuc2V0Q29udGVudChodG1sXzAwM2VmZWM4NDlkODRhN2ZhMDk5NjBkNDZjNGI5NjIzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E4NWI3ZWNlMmZiZjQyNjk5OTk5ZGQwNjY5YzcxNThiLmJpbmRQb3B1cChwb3B1cF8zYmVlNDcyZGM0NDU0ZDA4YjU1NDZkZTc1YjYxNTJmMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zM2U4NzIwNjFkZDA0YTA1OGI3ZjBhMzM4ZTY1ZDM4OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyNzkyOTIsLTc5LjI2MjAyOTQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzhkM2ZlNDkwZmJhNzRlZmRhM2QxZGUyZjU0ZmE2M2JkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzYxN2U4MTA4YWJkOTQ3ZmZiODRmNTc5NzA1MGVjZjA4ID0gJCgnPGRpdiBpZD0iaHRtbF82MTdlODEwOGFiZDk0N2ZmYjg0ZjU3OTcwNTBlY2YwOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S2VubmVkeSBQYXJrIC8gSW9udmlldyAvIEVhc3QgQmlyY2htb3VudCBQYXJrLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOGQzZmU0OTBmYmE3NGVmZGEzZDFkZTJmNTRmYTYzYmQuc2V0Q29udGVudChodG1sXzYxN2U4MTA4YWJkOTQ3ZmZiODRmNTc5NzA1MGVjZjA4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMzZTg3MjA2MWRkMDRhMDU4YjdmMGEzMzhlNjVkMzg5LmJpbmRQb3B1cChwb3B1cF84ZDNmZTQ5MGZiYTc0ZWZkYTNkMWRlMmY1NGZhNjNiZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mODQwZDExOTQyNGQ0YzRhODEyNDk0NjgxMDZhMWUxZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc4Njk0NzMsLTc5LjM4NTk3NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84NzkwMzg2ODVjNjI0YjI4YTljNjkwYmExYjUwOWY2YSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mYzA1YTg2MjNkZGU0ZWFkYWVhOTI5OWNjYjY3YjJjOCA9ICQoJzxkaXYgaWQ9Imh0bWxfZmMwNWE4NjIzZGRlNGVhZGFlYTkyOTljY2I2N2IyYzgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJheXZpZXcgVmlsbGFnZSwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODc5MDM4Njg1YzYyNGIyOGE5YzY5MGJhMWI1MDlmNmEuc2V0Q29udGVudChodG1sX2ZjMDVhODYyM2RkZTRlYWRhZWE5Mjk5Y2NiNjdiMmM4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Y4NDBkMTE5NDI0ZDRjNGE4MTI0OTQ2ODEwNmExZTFkLmJpbmRQb3B1cChwb3B1cF84NzkwMzg2ODVjNjI0YjI4YTljNjkwYmExYjUwOWY2YSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wMGQxZGFjMDA1Njc0YmVmYjY0MzM0MmIwNjA2Zjg2YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjczNzQ3MzIwMDAwMDAwNCwtNzkuNDY0NzYzMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzBiN2I1YTYwNzQ5NDU1YjkzY2RmYTM2NmVjZDY3MDIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODc1NWRkNGZjZWQ1NDU3MmI5MGE5MzJjNmEyNDJlOGQgPSAkKCc8ZGl2IGlkPSJodG1sXzg3NTVkZDRmY2VkNTQ1NzJiOTBhOTMyYzZhMjQyZThkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Eb3duc3ZpZXcsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzMwYjdiNWE2MDc0OTQ1NWI5M2NkZmEzNjZlY2Q2NzAyLnNldENvbnRlbnQoaHRtbF84NzU1ZGQ0ZmNlZDU0NTcyYjkwYTkzMmM2YTI0MmU4ZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wMGQxZGFjMDA1Njc0YmVmYjY0MzM0MmIwNjA2Zjg2Yy5iaW5kUG9wdXAocG9wdXBfMzBiN2I1YTYwNzQ5NDU1YjkzY2RmYTM2NmVjZDY3MDIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDFhNWY3ZjExMTExNGMxMThhNTZlNDM1ZTVlMTdmMWEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Nzk1NTcxLC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWQ2ZmYyMjUzYTVlNDA1Yjg1ODc0ZDNlZDY4ZjJlYjMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjRmYTgwM2YxODQyNDQ1ODhkNmU1OWNhMmM4ODg2NGYgPSAkKCc8ZGl2IGlkPSJodG1sXzI0ZmE4MDNmMTg0MjQ0NTg4ZDZlNTljYTJjODg4NjRmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgRGFuZm9ydGggV2VzdCAvIFJpdmVyZGFsZSwgRWFzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hZDZmZjIyNTNhNWU0MDViODU4NzRkM2VkNjhmMmViMy5zZXRDb250ZW50KGh0bWxfMjRmYTgwM2YxODQyNDQ1ODhkNmU1OWNhMmM4ODg2NGYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDFhNWY3ZjExMTExNGMxMThhNTZlNDM1ZTVlMTdmMWEuYmluZFBvcHVwKHBvcHVwX2FkNmZmMjI1M2E1ZTQwNWI4NTg3NGQzZWQ2OGYyZWIzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzMyMDFiMGE1NDM2MTQzMWFiN2QwNTU5MmIxMTFkZDNkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ3MTc2OCwtNzkuMzgxNTc2NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzhmMjEwMTQ1ZmNlNDUwYjk4YTc1Mzc2Yjc2MmE2YTcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMmIwMzdkMWRiODA2NDIzOGIwZDg2NjU4MGUxYjNkYWQgPSAkKCc8ZGl2IGlkPSJodG1sXzJiMDM3ZDFkYjgwNjQyMzhiMGQ4NjY1ODBlMWIzZGFkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ub3JvbnRvIERvbWluaW9uIENlbnRyZSAvIERlc2lnbiBFeGNoYW5nZSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzhmMjEwMTQ1ZmNlNDUwYjk4YTc1Mzc2Yjc2MmE2YTcuc2V0Q29udGVudChodG1sXzJiMDM3ZDFkYjgwNjQyMzhiMGQ4NjY1ODBlMWIzZGFkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMyMDFiMGE1NDM2MTQzMWFiN2QwNTU5MmIxMTFkZDNkLmJpbmRQb3B1cChwb3B1cF83OGYyMTAxNDVmY2U0NTBiOThhNzUzNzZiNzYyYTZhNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84N2JkYjliM2Q3OGQ0MjE3ODcwZTFlOGIyODUxZjJhYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYzNjg0NzIsLTc5LjQyODE5MTQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzUxMGUwNzcyNjA4MTQ0ZWZiN2E0MTE5NGEyNzVmNTk4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhlMWQ2NDM3N2Y4NDQ1ZmU4ZjIzMTQ5Y2IzYmZkMTQ5ID0gJCgnPGRpdiBpZD0iaHRtbF84ZTFkNjQzNzdmODQ0NWZlOGYyMzE0OWNiM2JmZDE0OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QnJvY2t0b24gLyBQYXJrZGFsZSBWaWxsYWdlIC8gRXhoaWJpdGlvbiBQbGFjZSwgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81MTBlMDc3MjYwODE0NGVmYjdhNDExOTRhMjc1ZjU5OC5zZXRDb250ZW50KGh0bWxfOGUxZDY0Mzc3Zjg0NDVmZThmMjMxNDljYjNiZmQxNDkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODdiZGI5YjNkNzhkNDIxNzg3MGUxZThiMjg1MWYyYWIuYmluZFBvcHVwKHBvcHVwXzUxMGUwNzcyNjA4MTQ0ZWZiN2E0MTE5NGEyNzVmNTk4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzE5ZmY4Njg3ZThjMTQ4ODhiZjlmYjFmY2UxOTJhOWU5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzExMTExNzAwMDAwMDA0LC03OS4yODQ1NzcyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2U0MTM3ODFkYzc0YzQzODViMzk2YzJmMDczMDZlMTI2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2JjMDQ2Mzk0MzgzMTQwMGNhZGM2Y2MwNTI0N2E3NDE0ID0gJCgnPGRpdiBpZD0iaHRtbF9iYzA0NjM5NDM4MzE0MDBjYWRjNmNjMDUyNDdhNzQxNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R29sZGVuIE1pbGUgLyBDbGFpcmxlYSAvIE9ha3JpZGdlLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTQxMzc4MWRjNzRjNDM4NWIzOTZjMmYwNzMwNmUxMjYuc2V0Q29udGVudChodG1sX2JjMDQ2Mzk0MzgzMTQwMGNhZGM2Y2MwNTI0N2E3NDE0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzE5ZmY4Njg3ZThjMTQ4ODhiZjlmYjFmY2UxOTJhOWU5LmJpbmRQb3B1cChwb3B1cF9lNDEzNzgxZGM3NGM0Mzg1YjM5NmMyZjA3MzA2ZTEyNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84M2E4MzZmNzJmZWM0NDczOWI2ZjBmN2M2ZWZmMzZmNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1NzQ5MDIsLTc5LjM3NDcxNDA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzVhMGUxYzgzOTYzNzRlODFiZjkzMWExZTExOGYxZmQ3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RiYjc2NjY2ZTI0YjRlMDlhZTBkODFhMzg3ZTAxMDc1ID0gJCgnPGRpdiBpZD0iaHRtbF9kYmI3NjY2NmUyNGI0ZTA5YWUwZDgxYTM4N2UwMTA3NSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+WW9yayBNaWxscyAvIFNpbHZlciBIaWxscywgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNWEwZTFjODM5NjM3NGU4MWJmOTMxYTFlMTE4ZjFmZDcuc2V0Q29udGVudChodG1sX2RiYjc2NjY2ZTI0YjRlMDlhZTBkODFhMzg3ZTAxMDc1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzgzYTgzNmY3MmZlYzQ0NzM5YjZmMGY3YzZlZmYzNmY0LmJpbmRQb3B1cChwb3B1cF81YTBlMWM4Mzk2Mzc0ZTgxYmY5MzFhMWUxMThmMWZkNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80YWE2ZWVmYTIzMDQ0ZTVmODIwOGE5MDRhZDdlY2U5MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjczOTAxNDYsLTc5LjUwNjk0MzZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMGRjM2QxYzBhMjhkNDY0NzhmY2ViYmQwNDhhNjNmNjAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTRmMzZjODFkZWQwNDczZGEzNzMzYWQ1NGQ5YjY2Y2IgPSAkKCc8ZGl2IGlkPSJodG1sXzU0ZjM2YzgxZGVkMDQ3M2RhMzczM2FkNTRkOWI2NmNiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Eb3duc3ZpZXcsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBkYzNkMWMwYTI4ZDQ2NDc4ZmNlYmJkMDQ4YTYzZjYwLnNldENvbnRlbnQoaHRtbF81NGYzNmM4MWRlZDA0NzNkYTM3MzNhZDU0ZDliNjZjYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80YWE2ZWVmYTIzMDQ0ZTVmODIwOGE5MDRhZDdlY2U5My5iaW5kUG9wdXAocG9wdXBfMGRjM2QxYzBhMjhkNDY0NzhmY2ViYmQwNDhhNjNmNjApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWE1MzlhYmI4YTVlNDgzOWE0YTg5MGU3ZGI4MWM0MDMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njg5OTg1LC03OS4zMTU1NzE1OTk5OTk5OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iMzMzOGQyYjZlNzM0ZDAxODMwZTk5NTE5ZGE4ZTQzYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85YTRlNzIwMDQ1ZTc0OTJlODc1ODFmMjcwMGYyODg0NSA9ICQoJzxkaXYgaWQ9Imh0bWxfOWE0ZTcyMDA0NWU3NDkyZTg3NTgxZjI3MDBmMjg4NDUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkluZGlhIEJhemFhciAvIFRoZSBCZWFjaGVzIFdlc3QsIEVhc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjMzMzhkMmI2ZTczNGQwMTgzMGU5OTUxOWRhOGU0M2Muc2V0Q29udGVudChodG1sXzlhNGU3MjAwNDVlNzQ5MmU4NzU4MWYyNzAwZjI4ODQ1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFhNTM5YWJiOGE1ZTQ4MzlhNGE4OTBlN2RiODFjNDAzLmJpbmRQb3B1cChwb3B1cF9iMzMzOGQyYjZlNzM0ZDAxODMwZTk5NTE5ZGE4ZTQzYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81MGYwMGE5NmNiZTg0ZWEwYTA5ZWFkNjM4M2E4YjI0MiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODE5ODUsLTc5LjM3OTgxNjkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzJjNzQ3NzY3YzQ4NTRlMTg4MzFhMGM1ZDY1YjUxMzMzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzAxYmY1ZWZkYzY0ODQxMmNhNjY2NTYyY2I0MGZkYjFiID0gJCgnPGRpdiBpZD0iaHRtbF8wMWJmNWVmZGM2NDg0MTJjYTY2NjU2MmNiNDBmZGIxYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q29tbWVyY2UgQ291cnQgLyBWaWN0b3JpYSBIb3RlbCwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMmM3NDc3NjdjNDg1NGUxODgzMWEwYzVkNjViNTEzMzMuc2V0Q29udGVudChodG1sXzAxYmY1ZWZkYzY0ODQxMmNhNjY2NTYyY2I0MGZkYjFiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzUwZjAwYTk2Y2JlODRlYTBhMDllYWQ2MzgzYThiMjQyLmJpbmRQb3B1cChwb3B1cF8yYzc0Nzc2N2M0ODU0ZTE4ODMxYTBjNWQ2NWI1MTMzMyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82YmY0ZWJiNDBiMTM0NDc4OGJkYTNlMDQ1MTY1N2RkOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxMzc1NjIwMDAwMDAwNiwtNzkuNDkwMDczOF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80MTA4YzEyNjQ0Y2M0ODJjOWJiNzQ0OGViN2Q1MjA2NyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xMjU4YzBmNTU1YzU0MWMyOTA1YTkyNTVmNjlmYmJiMyA9ICQoJzxkaXYgaWQ9Imh0bWxfMTI1OGMwZjU1NWM1NDFjMjkwNWE5MjU1ZjY5ZmJiYjMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRoIFBhcmsgLyBNYXBsZSBMZWFmIFBhcmsgLyBVcHdvb2QgUGFyaywgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNDEwOGMxMjY0NGNjNDgyYzliYjc0NDhlYjdkNTIwNjcuc2V0Q29udGVudChodG1sXzEyNThjMGY1NTVjNTQxYzI5MDVhOTI1NWY2OWZiYmIzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzZiZjRlYmI0MGIxMzQ0Nzg4YmRhM2UwNDUxNjU3ZGQ4LmJpbmRQb3B1cChwb3B1cF80MTA4YzEyNjQ0Y2M0ODJjOWJiNzQ0OGViN2Q1MjA2Nyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kMzJhMDViNDgwYmY0ZjlkOTUxYjk2ZjM2NzVmZGJhNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1NjMwMzMsLTc5LjU2NTk2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzA0MmU2OTFjNzRmMDRlZTFhNTdmY2EyMjg1Y2FhOTgwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzdkMTI0NDk4YjYyMjQ0ZmJhN2MxYjFkYjM1Yjg5YzM4ID0gJCgnPGRpdiBpZD0iaHRtbF83ZDEyNDQ5OGI2MjI0NGZiYTdjMWIxZGIzNWI4OWMzOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SHVtYmVyIFN1bW1pdCwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDQyZTY5MWM3NGYwNGVlMWE1N2ZjYTIyODVjYWE5ODAuc2V0Q29udGVudChodG1sXzdkMTI0NDk4YjYyMjQ0ZmJhN2MxYjFkYjM1Yjg5YzM4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2QzMmEwNWI0ODBiZjRmOWQ5NTFiOTZmMzY3NWZkYmE1LmJpbmRQb3B1cChwb3B1cF8wNDJlNjkxYzc0ZjA0ZWUxYTU3ZmNhMjI4NWNhYTk4MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84YmFlZWEzZTk5MTA0NzA0YmQzODBlY2JiMjUzZjRkZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxNjMxNiwtNzkuMjM5NDc2MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDQxNmE3OWRhNTRlNDlkYjgzYmU4ODA3Y2ZjNTVlY2IgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMGNlODI4MzIyNDlhNGM5MWJkODVhNzgxMWU5N2ZhN2QgPSAkKCc8ZGl2IGlkPSJodG1sXzBjZTgyODMyMjQ5YTRjOTFiZDg1YTc4MTFlOTdmYTdkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DbGlmZnNpZGUgLyBDbGlmZmNyZXN0IC8gU2NhcmJvcm91Z2ggVmlsbGFnZSBXZXN0LCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDQxNmE3OWRhNTRlNDlkYjgzYmU4ODA3Y2ZjNTVlY2Iuc2V0Q29udGVudChodG1sXzBjZTgyODMyMjQ5YTRjOTFiZDg1YTc4MTFlOTdmYTdkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzhiYWVlYTNlOTkxMDQ3MDRiZDM4MGVjYmIyNTNmNGRlLmJpbmRQb3B1cChwb3B1cF9kNDE2YTc5ZGE1NGU0OWRiODNiZTg4MDdjZmM1NWVjYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lNTIyMDU5N2Y0NTE0NTU3OWYyYjlhYzM4YTgyYzA0NCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc4OTA1MywtNzkuNDA4NDkyNzk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTM3MDZmZGViY2FiNDA4NWIwODRhYTdiZGRlMDE3YjkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfN2NkNjk1YzFiMjU1NDNmMTg0ODQ5ZGM1MWY1MTc3NWEgPSAkKCc8ZGl2IGlkPSJodG1sXzdjZDY5NWMxYjI1NTQzZjE4NDg0OWRjNTFmNTE3NzVhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XaWxsb3dkYWxlIC8gTmV3dG9uYnJvb2ssIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkzNzA2ZmRlYmNhYjQwODViMDg0YWE3YmRkZTAxN2I5LnNldENvbnRlbnQoaHRtbF83Y2Q2OTVjMWIyNTU0M2YxODQ4NDlkYzUxZjUxNzc1YSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lNTIyMDU5N2Y0NTE0NTU3OWYyYjlhYzM4YTgyYzA0NC5iaW5kUG9wdXAocG9wdXBfOTM3MDZmZGViY2FiNDA4NWIwODRhYTdiZGRlMDE3YjkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjNmNjdkN2RiYmExNDdiNjhiZTgyNDE3NGE1MGJlN2EgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mjg0OTY0LC03OS40OTU2OTc0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82YmEwZWYwYzRlODc0OWJkYWI5ZGU2MjQ3ZTNkZTcxZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80MDA1ZmY3N2FlYjc0Nzc4YWUyNDQzYzBiNGM3Y2MzMyA9ICQoJzxkaXYgaWQ9Imh0bWxfNDAwNWZmNzdhZWI3NDc3OGFlMjQ0M2MwYjRjN2NjMzMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRvd25zdmlldywgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNmJhMGVmMGM0ZTg3NDliZGFiOWRlNjI0N2UzZGU3MWYuc2V0Q29udGVudChodG1sXzQwMDVmZjc3YWViNzQ3NzhhZTI0NDNjMGI0YzdjYzMzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzIzZjY3ZDdkYmJhMTQ3YjY4YmU4MjQxNzRhNTBiZTdhLmJpbmRQb3B1cChwb3B1cF82YmEwZWYwYzRlODc0OWJkYWI5ZGU2MjQ3ZTNkZTcxZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xMTI0NWYzMDhkYjE0NDc5YjJhYjBkNzkxNTBhYTI4YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1OTUyNTUsLTc5LjM0MDkyM10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iNmJiYTE3Zjg2YjI0NjljOGVkYWVhODhmMmRjN2Q3MCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hYzYyOGMyZTM1ZDM0OTE4OWZlN2QyMWE3YTAyNjNkYyA9ICQoJzxkaXYgaWQ9Imh0bWxfYWM2MjhjMmUzNWQzNDkxODlmZTdkMjFhN2EwMjYzZGMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0dWRpbyBEaXN0cmljdCwgRWFzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iNmJiYTE3Zjg2YjI0NjljOGVkYWVhODhmMmRjN2Q3MC5zZXRDb250ZW50KGh0bWxfYWM2MjhjMmUzNWQzNDkxODlmZTdkMjFhN2EwMjYzZGMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTEyNDVmMzA4ZGIxNDQ3OWIyYWIwZDc5MTUwYWEyOGEuYmluZFBvcHVwKHBvcHVwX2I2YmJhMTdmODZiMjQ2OWM4ZWRhZWE4OGYyZGM3ZDcwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2U5OGU2OWU0Y2QwZjQwMjg5NTM5OWVlODVlZDQzMzAyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzMzMjgyNSwtNzkuNDE5NzQ5N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jYWQwODE2MzMxYzM0MWMyYTNiYTk1MjBlYmIzZWVkZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82NDQ0MzZmNDA2ZmU0YTNmYTg0MzhhYmQ3Yjg5MjY2ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfNjQ0NDM2ZjQwNmZlNGEzZmE4NDM4YWJkN2I4OTI2NmQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJlZGZvcmQgUGFyayAvIExhd3JlbmNlIE1hbm9yIEVhc3QsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2NhZDA4MTYzMzFjMzQxYzJhM2JhOTUyMGViYjNlZWRmLnNldENvbnRlbnQoaHRtbF82NDQ0MzZmNDA2ZmU0YTNmYTg0MzhhYmQ3Yjg5MjY2ZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lOThlNjllNGNkMGY0MDI4OTUzOTllZTg1ZWQ0MzMwMi5iaW5kUG9wdXAocG9wdXBfY2FkMDgxNjMzMWMzNDFjMmEzYmE5NTIwZWJiM2VlZGYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjY2YzhlMTljMGEyNGEwMjhhNGRiYjFiNzZjYWM1ZjEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTExMTU4LC03OS40NzYwMTMyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kNDMxZDU4NjEwNDM0N2ZmOGIxZmJiOWMxZGNkOGRmMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mMmZhMTk0YWNlNjk0MGI1ODIwZTM5MTg4ZjJmMGJkZSA9ICQoJzxkaXYgaWQ9Imh0bWxfZjJmYTE5NGFjZTY5NDBiNTgyMGUzOTE4OGYyZjBiZGUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRlbCBSYXkgLyBNb3VudCBEZW5uaXMgLyBLZWVsc2RhbGUgYW5kIFNpbHZlcnRob3JuLCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kNDMxZDU4NjEwNDM0N2ZmOGIxZmJiOWMxZGNkOGRmMS5zZXRDb250ZW50KGh0bWxfZjJmYTE5NGFjZTY5NDBiNTgyMGUzOTE4OGYyZjBiZGUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjY2YzhlMTljMGEyNGEwMjhhNGRiYjFiNzZjYWM1ZjEuYmluZFBvcHVwKHBvcHVwX2Q0MzFkNTg2MTA0MzQ3ZmY4YjFmYmI5YzFkY2Q4ZGYxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M2OTVmZTg0OTM3NDRiNDc4ZThjNTU4YTdjMzBlMzViID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzI0NzY1OSwtNzkuNTMyMjQyNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMmFlNTA1MzlhMWYwNDA5NTk3YmEwNmQyYmQ0MDUwOWEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjJkZmE1MmRiMGNiNGE2ZWI0YzEyNTNhMjU0ODc1YTYgPSAkKCc8ZGl2IGlkPSJodG1sXzYyZGZhNTJkYjBjYjRhNmViNGMxMjUzYTI1NDg3NWE2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IdW1iZXJsZWEgLyBFbWVyeSwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMmFlNTA1MzlhMWYwNDA5NTk3YmEwNmQyYmQ0MDUwOWEuc2V0Q29udGVudChodG1sXzYyZGZhNTJkYjBjYjRhNmViNGMxMjUzYTI1NDg3NWE2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2M2OTVmZTg0OTM3NDRiNDc4ZThjNTU4YTdjMzBlMzViLmJpbmRQb3B1cChwb3B1cF8yYWU1MDUzOWExZjA0MDk1OTdiYTA2ZDJiZDQwNTA5YSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83MTllYjk0MDE0OWY0YjY0YmVmMDA3MDZjNTU2Y2M0ZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY5MjY1NzAwMDAwMDAwNCwtNzkuMjY0ODQ4MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xMjljYjc0ZjM5YTY0ZTVkOWI0MGQyYWZiMWJhODg4NCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84Nzk1ZDBmY2FkN2Q0MDdiODljYWRjMzJjYTVmOTQ5YiA9ICQoJzxkaXYgaWQ9Imh0bWxfODc5NWQwZmNhZDdkNDA3Yjg5Y2FkYzMyY2E1Zjk0OWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJpcmNoIENsaWZmIC8gQ2xpZmZzaWRlIFdlc3QsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xMjljYjc0ZjM5YTY0ZTVkOWI0MGQyYWZiMWJhODg4NC5zZXRDb250ZW50KGh0bWxfODc5NWQwZmNhZDdkNDA3Yjg5Y2FkYzMyY2E1Zjk0OWIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzE5ZWI5NDAxNDlmNGI2NGJlZjAwNzA2YzU1NmNjNGQuYmluZFBvcHVwKHBvcHVwXzEyOWNiNzRmMzlhNjRlNWQ5YjQwZDJhZmIxYmE4ODg0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZkZGU3Zjg4MjM3ZjQ1OTdhNjgxNWU0NWUyZjE0Y2Y5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzcwMTE5OSwtNzkuNDA4NDkyNzk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzM5YjJjNDIyMWI2NGI0OTg2MmU1NzMxYTFlYmFhZmEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMGVjMjgyYTYzMjBmNDhkMGJiMzRkZWJhYzRjNTc3ZTkgPSAkKCc8ZGl2IGlkPSJodG1sXzBlYzI4MmE2MzIwZjQ4ZDBiYjM0ZGViYWM0YzU3N2U5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XaWxsb3dkYWxlLCBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zMzliMmM0MjIxYjY0YjQ5ODYyZTU3MzFhMWViYWFmYS5zZXRDb250ZW50KGh0bWxfMGVjMjgyYTYzMjBmNDhkMGJiMzRkZWJhYzRjNTc3ZTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNmRkZTdmODgyMzdmNDU5N2E2ODE1ZTQ1ZTJmMTRjZjkuYmluZFBvcHVwKHBvcHVwXzMzOWIyYzQyMjFiNjRiNDk4NjJlNTczMWExZWJhYWZhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZjZmZhZGFlYzE1MzQ0MzRiOWU2ZTA1ZWMwZTQyYTI0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzYxNjMxMywtNzkuNTIwOTk5NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTRkYTZiZTViNjI1NDEyYWI1MTc1MmYxNjE4NmY1YzMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWYzMjEzMDY4YzVlNDk1NjkxNzFiZDA2ODE3OWNiNGIgPSAkKCc8ZGl2IGlkPSJodG1sXzVmMzIxMzA2OGM1ZTQ5NTY5MTcxYmQwNjgxNzljYjRiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Eb3duc3ZpZXcsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2U0ZGE2YmU1YjYyNTQxMmFiNTE3NTJmMTYxODZmNWMzLnNldENvbnRlbnQoaHRtbF81ZjMyMTMwNjhjNWU0OTU2OTE3MWJkMDY4MTc5Y2I0Yik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82Y2ZmYWRhZWMxNTM0NDM0YjllNmUwNWVjMGU0MmEyNC5iaW5kUG9wdXAocG9wdXBfZTRkYTZiZTViNjI1NDEyYWI1MTc1MmYxNjE4NmY1YzMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTBmM2M1Njk3ZjNjNDk4ZmI5YWI1NjQ1OTIwNmE3NmIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MjgwMjA1LC03OS4zODg3OTAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzdjNWU5NGMzMjhlYzQ5YTZhZTEwYTRjMGE3NTkyZGU1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzRmYjdhZThmMDI1MDRkNjc5Y2UyNWE1ZWI5MWRlZjM3ID0gJCgnPGRpdiBpZD0iaHRtbF80ZmI3YWU4ZjAyNTA0ZDY3OWNlMjVhNWViOTFkZWYzNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF3cmVuY2UgUGFyaywgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83YzVlOTRjMzI4ZWM0OWE2YWUxMGE0YzBhNzU5MmRlNS5zZXRDb250ZW50KGh0bWxfNGZiN2FlOGYwMjUwNGQ2NzljZTI1YTVlYjkxZGVmMzcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZTBmM2M1Njk3ZjNjNDk4ZmI5YWI1NjQ1OTIwNmE3NmIuYmluZFBvcHVwKHBvcHVwXzdjNWU5NGMzMjhlYzQ5YTZhZTEwYTRjMGE3NTkyZGU1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Y0ZDM3MTc4MTIwODRhZWI4NGYxMDZlZjhkMmEwYjVkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzExNjk0OCwtNzkuNDE2OTM1NTk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjk5MTc4MGYwMGExNGY3NWI2MzMyNmM3NjhkNGRlODcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzMxYjA4YjIwOTU3NGExMjlhZmUyZjUyY2M4MGJmNWUgPSAkKCc8ZGl2IGlkPSJodG1sX2MzMWIwOGIyMDk1NzRhMTI5YWZlMmY1MmNjODBiZjVlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Sb3NlbGF3biwgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mOTkxNzgwZjAwYTE0Zjc1YjYzMzI2Yzc2OGQ0ZGU4Ny5zZXRDb250ZW50KGh0bWxfYzMxYjA4YjIwOTU3NGExMjlhZmUyZjUyY2M4MGJmNWUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjRkMzcxNzgxMjA4NGFlYjg0ZjEwNmVmOGQyYTBiNWQuYmluZFBvcHVwKHBvcHVwX2Y5OTE3ODBmMDBhMTRmNzViNjMzMjZjNzY4ZDRkZTg3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJkNWFhMjVlODE3OTQxYTBhYzMzY2E0NDNlZjRlMWZlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjczMTg1Mjk5OTk5OTksLTc5LjQ4NzI2MTkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E1Y2Q3ZTAyOWNiYjQyNzZhZTdlZWQ1MGViMjAyMjNjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2E5ZDhhMjk4ZTJiNDRiMDdhM2NmMDdlMmQzZTJjZWMxID0gJCgnPGRpdiBpZD0iaHRtbF9hOWQ4YTI5OGUyYjQ0YjA3YTNjZjA3ZTJkM2UyY2VjMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UnVubnltZWRlIC8gVGhlIEp1bmN0aW9uIE5vcnRoLCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hNWNkN2UwMjljYmI0Mjc2YWU3ZWVkNTBlYjIwMjIzYy5zZXRDb250ZW50KGh0bWxfYTlkOGEyOThlMmI0NGIwN2EzY2YwN2UyZDNlMmNlYzEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmQ1YWEyNWU4MTc5NDFhMGFjMzNjYTQ0M2VmNGUxZmUuYmluZFBvcHVwKHBvcHVwX2E1Y2Q3ZTAyOWNiYjQyNzZhZTdlZWQ1MGViMjAyMjNjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzVmNWM0YmIxMjcxZjQxOTNhOTllMjMxYzJlYTYxYmQyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzA2ODc2LC03OS41MTgxODg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82ODJhYjhlNGQwMDk0YjM3YjFkMGE3MDY2YmY0OTUyMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iOTJiN2YwY2E3NGQ0NWE5OTZjNWFiYTVmM2FmNGRhYiA9ICQoJzxkaXYgaWQ9Imh0bWxfYjkyYjdmMGNhNzRkNDVhOTk2YzVhYmE1ZjNhZjRkYWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldlc3RvbiwgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjgyYWI4ZTRkMDA5NGIzN2IxZDBhNzA2NmJmNDk1MjIuc2V0Q29udGVudChodG1sX2I5MmI3ZjBjYTc0ZDQ1YTk5NmM1YWJhNWYzYWY0ZGFiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzVmNWM0YmIxMjcxZjQxOTNhOTllMjMxYzJlYTYxYmQyLmJpbmRQb3B1cChwb3B1cF82ODJhYjhlNGQwMDk0YjM3YjFkMGE3MDY2YmY0OTUyMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85YjgxYjA3ODI0ZTc0MDE5ODZhYTc1YzBiNDVkNjMxYSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1NzQwOTYsLTc5LjI3MzMwNDAwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzZhYzlmOTkxMjQ4NTRhMjQ4NDc2YjI4YjhmNTYwYjdiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzczYjBhZDAxNjc5YzRhMGE4NWEwNzY1YWU5MjAwZmVhID0gJCgnPGRpdiBpZD0iaHRtbF83M2IwYWQwMTY3OWM0YTBhODVhMDc2NWFlOTIwMGZlYSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RG9yc2V0IFBhcmsgLyBXZXhmb3JkIEhlaWdodHMgLyBTY2FyYm9yb3VnaCBUb3duIENlbnRyZSwgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzZhYzlmOTkxMjQ4NTRhMjQ4NDc2YjI4YjhmNTYwYjdiLnNldENvbnRlbnQoaHRtbF83M2IwYWQwMTY3OWM0YTBhODVhMDc2NWFlOTIwMGZlYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85YjgxYjA3ODI0ZTc0MDE5ODZhYTc1YzBiNDVkNjMxYS5iaW5kUG9wdXAocG9wdXBfNmFjOWY5OTEyNDg1NGEyNDg0NzZiMjhiOGY1NjBiN2IpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjljOGNmMDE0MmE4NDIzOGJiM2UxOWU2YmUwYjhiNGYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43NTI3NTgyOTk5OTk5OTYsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTRiZWUyMWU4YTY3NDM3NmFlYmE4Zjk3OGQ4ZmI1NTQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOGY2NzAzYzBkNDExNDFhMGFiYzQzMzVlOTcwMGI5ZDQgPSAkKCc8ZGl2IGlkPSJodG1sXzhmNjcwM2MwZDQxMTQxYTBhYmM0MzM1ZTk3MDBiOWQ0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Zb3JrIE1pbGxzIFdlc3QsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE0YmVlMjFlOGE2NzQzNzZhZWJhOGY5NzhkOGZiNTU0LnNldENvbnRlbnQoaHRtbF84ZjY3MDNjMGQ0MTE0MWEwYWJjNDMzNWU5NzAwYjlkNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yOWM4Y2YwMTQyYTg0MjM4YmIzZTE5ZTZiZTBiOGI0Zi5iaW5kUG9wdXAocG9wdXBfMTRiZWUyMWU4YTY3NDM3NmFlYmE4Zjk3OGQ4ZmI1NTQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNWEzNWZmZTA2YzIyNDRiN2FlNzBlMDhiNGMyZTkwZWIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTI3NTExLC03OS4zOTAxOTc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2ExNmMyOWJjYTYzNzQyMDE4ZmRmM2E4NDhhY2UwMzMzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzU5YjQwM2Q0NGUzNTRhOGZiYzg3NzE2MjFiYjc3OTlkID0gJCgnPGRpdiBpZD0iaHRtbF81OWI0MDNkNDRlMzU0YThmYmM4NzcxNjIxYmI3Nzk5ZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGF2aXN2aWxsZSBOb3J0aCwgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hMTZjMjliY2E2Mzc0MjAxOGZkZjNhODQ4YWNlMDMzMy5zZXRDb250ZW50KGh0bWxfNTliNDAzZDQ0ZTM1NGE4ZmJjODc3MTYyMWJiNzc5OWQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNWEzNWZmZTA2YzIyNDRiN2FlNzBlMDhiNGMyZTkwZWIuYmluZFBvcHVwKHBvcHVwX2ExNmMyOWJjYTYzNzQyMDE4ZmRmM2E4NDhhY2UwMzMzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2E1YTVkYzdlMGUzYzQ1YWY4OWNkOThiMTNiNzBlZDE4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjk2OTQ3NiwtNzkuNDExMzA3MjAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWQxNDU3OTRiYTcwNDY5Yjk5MjAyOWYyZjRlZDU0NDQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODFlMTJlYjEzY2FhNGMzMjhiNzRhNTlkZTFiNDU0ZGEgPSAkKCc8ZGl2IGlkPSJodG1sXzgxZTEyZWIxM2NhYTRjMzI4Yjc0YTU5ZGUxYjQ1NGRhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Gb3Jlc3QgSGlsbCBOb3J0aCAmYW1wO2FtcDsgV2VzdCwgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ZDE0NTc5NGJhNzA0NjliOTkyMDI5ZjJmNGVkNTQ0NC5zZXRDb250ZW50KGh0bWxfODFlMTJlYjEzY2FhNGMzMjhiNzRhNTlkZTFiNDU0ZGEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYTVhNWRjN2UwZTNjNDVhZjg5Y2Q5OGIxM2I3MGVkMTguYmluZFBvcHVwKHBvcHVwXzlkMTQ1Nzk0YmE3MDQ2OWI5OTIwMjlmMmY0ZWQ1NDQ0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2E5MTZiNWI0M2MzNzQyYTViMGJiMGYwMjZmNmFmOTc5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjYxNjA4MywtNzkuNDY0NzYzMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWVlYWI0NTY0MjQ4NDMxZmE3M2QxZmZkZGY1ZmU1MDYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzQ4YmY5ZjhlMGI1NGY5MGIxYjVjZDNhN2U2NTZjMDEgPSAkKCc8ZGl2IGlkPSJodG1sXzc0OGJmOWY4ZTBiNTRmOTBiMWI1Y2QzYTdlNjU2YzAxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IaWdoIFBhcmsgLyBUaGUgSnVuY3Rpb24gU291dGgsIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMWVlYWI0NTY0MjQ4NDMxZmE3M2QxZmZkZGY1ZmU1MDYuc2V0Q29udGVudChodG1sXzc0OGJmOWY4ZTBiNTRmOTBiMWI1Y2QzYTdlNjU2YzAxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E5MTZiNWI0M2MzNzQyYTViMGJiMGYwMjZmNmFmOTc5LmJpbmRQb3B1cChwb3B1cF8xZWVhYjQ1NjQyNDg0MzFmYTczZDFmZmRkZjVmZTUwNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iMGQ0YzYwZjgyZGY0ZjkzYmU3MzlhN2IyMjkwOGJkNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY5NjMxOSwtNzkuNTMyMjQyNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNTRjYTU1MzVkMzVkNDU0MGI0ODFiMGE1YmU2OTRmMTAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOWM1ZWFjYmU3YjM0NDlmM2I2YWFlMDhkNjY3MzNmMzAgPSAkKCc8ZGl2IGlkPSJodG1sXzljNWVhY2JlN2IzNDQ5ZjNiNmFhZTA4ZDY2NzMzZjMwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XZXN0bW91bnQsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTRjYTU1MzVkMzVkNDU0MGI0ODFiMGE1YmU2OTRmMTAuc2V0Q29udGVudChodG1sXzljNWVhY2JlN2IzNDQ5ZjNiNmFhZTA4ZDY2NzMzZjMwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2IwZDRjNjBmODJkZjRmOTNiZTczOWE3YjIyOTA4YmQ3LmJpbmRQb3B1cChwb3B1cF81NGNhNTUzNWQzNWQ0NTQwYjQ4MWIwYTViZTY5NGYxMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iYTIyZmFlOWU5NDU0MTBjYTI0NWE2ZGQzYWU3NjMyNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1MDA3MTUwMDAwMDAwNCwtNzkuMjk1ODQ5MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81YzlmOGZiNDk4NDY0ZGI0ODE2YmIwYzU4M2YzNmFiMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85OGEzNTM0NWRiYjc0MTY2YWY2OWMwMDg4YmVmZjQxNCA9ICQoJzxkaXYgaWQ9Imh0bWxfOThhMzUzNDVkYmI3NDE2NmFmNjljMDA4OGJlZmY0MTQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldleGZvcmQgLyBNYXJ5dmFsZSwgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzVjOWY4ZmI0OTg0NjRkYjQ4MTZiYjBjNTgzZjM2YWIzLnNldENvbnRlbnQoaHRtbF85OGEzNTM0NWRiYjc0MTY2YWY2OWMwMDg4YmVmZjQxNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iYTIyZmFlOWU5NDU0MTBjYTI0NWE2ZGQzYWU3NjMyNC5iaW5kUG9wdXAocG9wdXBfNWM5ZjhmYjQ5ODQ2NGRiNDgxNmJiMGM1ODNmMzZhYjMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzVhOTBmYzg1OTJmNDQzMzkyNjU2N2NlZTNhZTRhODkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43ODI3MzY0LC03OS40NDIyNTkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzcyZTVjZDhmZTM0NzQ2NzdhMGI5M2JiZGQ4NjYwNzkwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzBjODJjYTYxMmFhZjRkYmRhZWNhYjQ1NzEwMTg4ZGQxID0gJCgnPGRpdiBpZD0iaHRtbF8wYzgyY2E2MTJhYWY0ZGJkYWVjYWI0NTcxMDE4OGRkMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V2lsbG93ZGFsZSwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzJlNWNkOGZlMzQ3NDY3N2EwYjkzYmJkZDg2NjA3OTAuc2V0Q29udGVudChodG1sXzBjODJjYTYxMmFhZjRkYmRhZWNhYjQ1NzEwMTg4ZGQxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM1YTkwZmM4NTkyZjQ0MzM5MjY1NjdjZWUzYWU0YTg5LmJpbmRQb3B1cChwb3B1cF83MmU1Y2Q4ZmUzNDc0Njc3YTBiOTNiYmRkODY2MDc5MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kODRjZTU2Mzk3OGU0Zjg2ODgxMGRmNDM1ZWM3YTJlNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxNTM4MzQsLTc5LjQwNTY3ODQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzkwOGI3MTdiMzM3YTRlNGRhY2VhYTE4NTBmYTQ0Y2UyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhlYjE0NzJhYTE5MzQxYjU5NjdhNDczMTZmMzg5YTkxID0gJCgnPGRpdiBpZD0iaHRtbF84ZWIxNDcyYWExOTM0MWI1OTY3YTQ3MzE2ZjM4OWE5MSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Tm9ydGggVG9yb250byBXZXN0LCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkwOGI3MTdiMzM3YTRlNGRhY2VhYTE4NTBmYTQ0Y2UyLnNldENvbnRlbnQoaHRtbF84ZWIxNDcyYWExOTM0MWI1OTY3YTQ3MzE2ZjM4OWE5MSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kODRjZTU2Mzk3OGU0Zjg2ODgxMGRmNDM1ZWM3YTJlNi5iaW5kUG9wdXAocG9wdXBfOTA4YjcxN2IzMzdhNGU0ZGFjZWFhMTg1MGZhNDRjZTIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDFkNTFmM2RiNmRjNDNiMTllNmNjMGJlNzZjNGFlZTcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzI3MDk3LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84OWMyNDA4Y2RkYzE0NzQ0OGRkNzdiZmFkMWI3NDA0OCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mNDRlMTE4MTVhMTI0YzM0YTgyMTE2YmQzNTdmODk2NiA9ICQoJzxkaXYgaWQ9Imh0bWxfZjQ0ZTExODE1YTEyNGMzNGE4MjExNmJkMzU3Zjg5NjYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBBbm5leCAvIE5vcnRoIE1pZHRvd24gLyBZb3JrdmlsbGUsIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODljMjQwOGNkZGMxNDc0NDhkZDc3YmZhZDFiNzQwNDguc2V0Q29udGVudChodG1sX2Y0NGUxMTgxNWExMjRjMzRhODIxMTZiZDM1N2Y4OTY2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQxZDUxZjNkYjZkYzQzYjE5ZTZjYzBiZTc2YzRhZWU3LmJpbmRQb3B1cChwb3B1cF84OWMyNDA4Y2RkYzE0NzQ0OGRkNzdiZmFkMWI3NDA0OCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jMzExOGMyMWZhYWU0MGIyOTZkNzYwZWM0MGEyOTVhYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODk1OTcsLTc5LjQ1NjMyNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wZTA5ZTI5NTEyMDA0NzQ5OGQxYTMyMjdhOTUzZmVjMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yM2NjYzRhNTk1MTA0NDgxODg4OGQ5NTc0ZmRhOWZkZSA9ICQoJzxkaXYgaWQ9Imh0bWxfMjNjY2M0YTU5NTEwNDQ4MTg4ODhkOTU3NGZkYTlmZGUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBhcmtkYWxlIC8gUm9uY2VzdmFsbGVzLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBlMDllMjk1MTIwMDQ3NDk4ZDFhMzIyN2E5NTNmZWMxLnNldENvbnRlbnQoaHRtbF8yM2NjYzRhNTk1MTA0NDgxODg4OGQ5NTc0ZmRhOWZkZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jMzExOGMyMWZhYWU0MGIyOTZkNzYwZWM0MGEyOTVhYi5iaW5kUG9wdXAocG9wdXBfMGUwOWUyOTUxMjAwNDc0OThkMWEzMjI3YTk1M2ZlYzEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjRjODFhMzk3YWE3NDk0M2EwNTk4ZTNkZmNiOTQ2NjEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzY5NjU2LC03OS42MTU4MTg5OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iNDFlZTljYjgxZTY0NTI5ODQyYjc5NGVlZTQyYjJhZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iNTI2ZTYyYjBjZDA0NWY0OTVlNGM1MGVkZjBkZmRlZSA9ICQoJzxkaXYgaWQ9Imh0bWxfYjUyNmU2MmIwY2QwNDVmNDk1ZTRjNTBlZGYwZGZkZWUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNhbmFkYSBQb3N0IEdhdGV3YXkgUHJvY2Vzc2luZyBDZW50cmUsIE1pc3Npc3NhdWdhPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iNDFlZTljYjgxZTY0NTI5ODQyYjc5NGVlZTQyYjJhZC5zZXRDb250ZW50KGh0bWxfYjUyNmU2MmIwY2QwNDVmNDk1ZTRjNTBlZGYwZGZkZWUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjRjODFhMzk3YWE3NDk0M2EwNTk4ZTNkZmNiOTQ2NjEuYmluZFBvcHVwKHBvcHVwX2I0MWVlOWNiODFlNjQ1Mjk4NDJiNzk0ZWVlNDJiMmFkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZkYmFmNzI5ODRlNjQ4Y2Y4MjZjODI2M2JlY2EwMWU0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg4OTA1NCwtNzkuNTU0NzI0NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMGI0MWM1OWU1NDU4NDQxMGFmMmI4ZmZhOGM5ZDc3ZjcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGJlYjk4OTZlZjYxNGRlYmIxYWE5ODAyZTY4OGJmMzQgPSAkKCc8ZGl2IGlkPSJodG1sXzRiZWI5ODk2ZWY2MTRkZWJiMWFhOTgwMmU2ODhiZjM0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5LaW5nc3ZpZXcgVmlsbGFnZSAvIFN0LiBQaGlsbGlwcyAvIE1hcnRpbiBHcm92ZSBHYXJkZW5zIC8gUmljaHZpZXcgR2FyZGVucywgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wYjQxYzU5ZTU0NTg0NDEwYWYyYjhmZmE4YzlkNzdmNy5zZXRDb250ZW50KGh0bWxfNGJlYjk4OTZlZjYxNGRlYmIxYWE5ODAyZTY4OGJmMzQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNmRiYWY3Mjk4NGU2NDhjZjgyNmM4MjYzYmVjYTAxZTQuYmluZFBvcHVwKHBvcHVwXzBiNDFjNTllNTQ1ODQ0MTBhZjJiOGZmYThjOWQ3N2Y3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2RkMTZiYjg3ZTIyYjQzYTQ4NzA3NTI3YzVlZTM2OWJlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzk0MjAwMywtNzkuMjYyMDI5NDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDMxODMwZTNjOGEwNDFkNWI2YThkNmIxMjVlZThhZjYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODY5NDlmYmJkNGMzNDEzMDgwZDgxNzI0MjkxODBlZjAgPSAkKCc8ZGl2IGlkPSJodG1sXzg2OTQ5ZmJiZDRjMzQxMzA4MGQ4MTcyNDI5MTgwZWYwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BZ2luY291cnQsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80MzE4MzBlM2M4YTA0MWQ1YjZhOGQ2YjEyNWVlOGFmNi5zZXRDb250ZW50KGh0bWxfODY5NDlmYmJkNGMzNDEzMDgwZDgxNzI0MjkxODBlZjApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZGQxNmJiODdlMjJiNDNhNDg3MDc1MjdjNWVlMzY5YmUuYmluZFBvcHVwKHBvcHVwXzQzMTgzMGUzYzhhMDQxZDViNmE4ZDZiMTI1ZWU4YWY2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzlmNDJkYjkyNzA1MTQ1YmM5N2VhNTFiZjU0ZTIwNjg4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzA0MzI0NCwtNzkuMzg4NzkwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84MGQ2OTkxOWQ4NGM0OTdiYTU1MjIxMzM3NmI1MmJhZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yODM5ZjU1MDQ1OTQ0NzhiOWQ2Y2ZhOGM4ZmVjMWFiMyA9ICQoJzxkaXYgaWQ9Imh0bWxfMjgzOWY1NTA0NTk0NDc4YjlkNmNmYThjOGZlYzFhYjMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRhdmlzdmlsbGUsIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODBkNjk5MTlkODRjNDk3YmE1NTIyMTMzNzZiNTJiYWYuc2V0Q29udGVudChodG1sXzI4MzlmNTUwNDU5NDQ3OGI5ZDZjZmE4YzhmZWMxYWIzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzlmNDJkYjkyNzA1MTQ1YmM5N2VhNTFiZjU0ZTIwNjg4LmJpbmRQb3B1cChwb3B1cF84MGQ2OTkxOWQ4NGM0OTdiYTU1MjIxMzM3NmI1MmJhZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mMTY4YzNiY2M2ZDc0NjQ1OWUwZTY0Nzc4YzZjNjU2MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjY5NTYsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmVkZjhmNzAzY2UxNDA5ZGE5YzJjZjBiMjI2YWI2MjMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYmMwODE2YWIyZmJjNDUzOGI0ZmVkZDIyMzYyOTZhNTEgPSAkKCc8ZGl2IGlkPSJodG1sX2JjMDgxNmFiMmZiYzQ1MzhiNGZlZGQyMjM2Mjk2YTUxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Vbml2ZXJzaXR5IG9mIFRvcm9udG8gLyBIYXJib3JkLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82ZWRmOGY3MDNjZTE0MDlkYTljMmNmMGIyMjZhYjYyMy5zZXRDb250ZW50KGh0bWxfYmMwODE2YWIyZmJjNDUzOGI0ZmVkZDIyMzYyOTZhNTEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjE2OGMzYmNjNmQ3NDY0NTllMGU2NDc3OGM2YzY1NjMuYmluZFBvcHVwKHBvcHVwXzZlZGY4ZjcwM2NlMTQwOWRhOWMyY2YwYjIyNmFiNjIzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzc0OTYwOTJmODUxYjRmZjM4NzNiYWMxNDU2MWIzZmI1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUxNTcwNiwtNzkuNDg0NDQ5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84OTI2NjYxZGM5YzI0YmE4YTczMmEyMWQ1NzdlNjFkNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mMzg1MDMyMjY0NTc0ZGY5ODJkOTY0NzNhNTJjMmJiYiA9ICQoJzxkaXYgaWQ9Imh0bWxfZjM4NTAzMjI2NDU3NGRmOTgyZDk2NDczYTUyYzJiYmIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJ1bm55bWVkZSAvIFN3YW5zZWEsIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODkyNjY2MWRjOWMyNGJhOGE3MzJhMjFkNTc3ZTYxZDUuc2V0Q29udGVudChodG1sX2YzODUwMzIyNjQ1NzRkZjk4MmQ5NjQ3M2E1MmMyYmJiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc0OTYwOTJmODUxYjRmZjM4NzNiYWMxNDU2MWIzZmI1LmJpbmRQb3B1cChwb3B1cF84OTI2NjYxZGM5YzI0YmE4YTczMmEyMWQ1NzdlNjFkNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iODRlZWNlMjFmOTY0YmM1YmFkMGQ5Yzc2ZGYxN2IxMCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc4MTYzNzUsLTc5LjMwNDMwMjFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjM5YTgyNTljNDNjNGU1Yzg5YWIxZGMzYmJiNmEwZTAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWViZTc0M2NjOGZiNDA5MWJhMjc5MWI0Y2UzMTI2ZTMgPSAkKCc8ZGl2IGlkPSJodG1sXzFlYmU3NDNjYzhmYjQwOTFiYTI3OTFiNGNlMzEyNmUzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DbGFya3MgQ29ybmVycyAvIFRhbSBPJiMzOTtTaGFudGVyIC8gU3VsbGl2YW4sIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yMzlhODI1OWM0M2M0ZTVjODlhYjFkYzNiYmI2YTBlMC5zZXRDb250ZW50KGh0bWxfMWViZTc0M2NjOGZiNDA5MWJhMjc5MWI0Y2UzMTI2ZTMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYjg0ZWVjZTIxZjk2NGJjNWJhZDBkOWM3NmRmMTdiMTAuYmluZFBvcHVwKHBvcHVwXzIzOWE4MjU5YzQzYzRlNWM4OWFiMWRjM2JiYjZhMGUwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Y2NTRlMDNjZTRhYjRiN2E4YWRkYjk5NzhjMzZiNTI3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg5NTc0MywtNzkuMzgzMTU5OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzgyODFhMjg1NDQyNDgxZGIyZjg1ZGJiNDg4ZWMzODIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzg1MWRjYzcxMDI3NGZjZGI0NzFkMTMzZDY2MWIxNWYgPSAkKCc8ZGl2IGlkPSJodG1sXzM4NTFkY2M3MTAyNzRmY2RiNDcxZDEzM2Q2NjFiMTVmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Nb29yZSBQYXJrIC8gU3VtbWVyaGlsbCBFYXN0LCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzM4MjgxYTI4NTQ0MjQ4MWRiMmY4NWRiYjQ4OGVjMzgyLnNldENvbnRlbnQoaHRtbF8zODUxZGNjNzEwMjc0ZmNkYjQ3MWQxMzNkNjYxYjE1Zik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mNjU0ZTAzY2U0YWI0YjdhOGFkZGI5OTc4YzM2YjUyNy5iaW5kUG9wdXAocG9wdXBfMzgyODFhMjg1NDQyNDgxZGIyZjg1ZGJiNDg4ZWMzODIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjE4MGM5ZTZmMjFiNDlkZThjMDIyNTQzNDdhNDI1M2YgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTMyMDU3LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzFhNmNkNjQwY2FhNTRiNjhiOGUxYTIxZDk0M2VlZjNjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2JhZjFkZDY4YzgwNjQ1OWI4MDNkNGY0OWQxZmZhMWE0ID0gJCgnPGRpdiBpZD0iaHRtbF9iYWYxZGQ2OGM4MDY0NTliODAzZDRmNDlkMWZmYTFhNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S2Vuc2luZ3RvbiBNYXJrZXQgLyBDaGluYXRvd24gLyBHcmFuZ2UgUGFyaywgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMWE2Y2Q2NDBjYWE1NGI2OGI4ZTFhMjFkOTQzZWVmM2Muc2V0Q29udGVudChodG1sX2JhZjFkZDY4YzgwNjQ1OWI4MDNkNGY0OWQxZmZhMWE0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzIxODBjOWU2ZjIxYjQ5ZGU4YzAyMjU0MzQ3YTQyNTNmLmJpbmRQb3B1cChwb3B1cF8xYTZjZDY0MGNhYTU0YjY4YjhlMWEyMWQ5NDNlZWYzYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wMzJkYzdlYzRhNjU0MjA0YmY3ZjA5YjUzM2Q0MGJjZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjgxNTI1MjIsLTc5LjI4NDU3NzJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjJmNGNmN2MxYmMwNGJmNGFlOTJhZTE1NGZlNzY0ZjUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjBkMzdjNjdkZDFkNGFlNWI2MjBkYmFmMjc1NTllZjQgPSAkKCc8ZGl2IGlkPSJodG1sXzIwZDM3YzY3ZGQxZDRhZTViNjIwZGJhZjI3NTU5ZWY0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5NaWxsaWtlbiAvIEFnaW5jb3VydCBOb3J0aCAvIFN0ZWVsZXMgRWFzdCAvIEwmIzM5O0Ftb3JlYXV4IEVhc3QsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mMmY0Y2Y3YzFiYzA0YmY0YWU5MmFlMTU0ZmU3NjRmNS5zZXRDb250ZW50KGh0bWxfMjBkMzdjNjdkZDFkNGFlNWI2MjBkYmFmMjc1NTllZjQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDMyZGM3ZWM0YTY1NDIwNGJmN2YwOWI1MzNkNDBiY2UuYmluZFBvcHVwKHBvcHVwX2YyZjRjZjdjMWJjMDRiZjRhZTkyYWUxNTRmZTc2NGY1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzFkZTgzMjA0ZDZhZDQ0ZmZiMmFjMjdjM2I0ODVlOWNjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg2NDEyMjk5OTk5OTksLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfY2FlY2FjNWNkNzY2NDA1MTg5MjQwYzg4NGYzZjkzYzAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTYyYjFhMmI3NTE3NDAwYTgzNmIzOTlhMDlkNTYwNWUgPSAkKCc8ZGl2IGlkPSJodG1sX2U2MmIxYTJiNzUxNzQwMGE4MzZiMzk5YTA5ZDU2MDVlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdW1tZXJoaWxsIFdlc3QgLyBSYXRobmVsbHkgLyBTb3V0aCBIaWxsIC8gRm9yZXN0IEhpbGwgU0UgLyBEZWVyIFBhcmssIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfY2FlY2FjNWNkNzY2NDA1MTg5MjQwYzg4NGYzZjkzYzAuc2V0Q29udGVudChodG1sX2U2MmIxYTJiNzUxNzQwMGE4MzZiMzk5YTA5ZDU2MDVlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFkZTgzMjA0ZDZhZDQ0ZmZiMmFjMjdjM2I0ODVlOWNjLmJpbmRQb3B1cChwb3B1cF9jYWVjYWM1Y2Q3NjY0MDUxODkyNDBjODg0ZjNmOTNjMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80MGY2MjhjZDMwZDU0Njg3OWNlZWY0YjY0M2M5YTVhYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODJlNDJkZjBjOTg2NDlmMWJjYmRmOTQ3MjkxMjliZmIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjRiOGVhYzM2NjcwNDRmMGJmZDhhNWUxYWYxNzIxMWIgPSAkKCc8ZGl2IGlkPSJodG1sX2Y0YjhlYWMzNjY3MDQ0ZjBiZmQ4YTVlMWFmMTcyMTFiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4sIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgyZTQyZGYwYzk4NjQ5ZjFiY2JkZjk0NzI5MTI5YmZiLnNldENvbnRlbnQoaHRtbF9mNGI4ZWFjMzY2NzA0NGYwYmZkOGE1ZTFhZjE3MjExYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80MGY2MjhjZDMwZDU0Njg3OWNlZWY0YjY0M2M5YTVhYi5iaW5kUG9wdXAocG9wdXBfODJlNDJkZjBjOTg2NDlmMWJjYmRmOTQ3MjkxMjliZmIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTQ4MjM5N2YyYzE1NGQ1MGI0ZmVkMzQ5ZmZkNmNiMmUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MDU2NDY2LC03OS41MDEzMjA3MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85OTMyMjAzZDMwN2I0YWRjODkzODJkOWQ5MThjYjIzMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kNDA0NGY2OTMxMjE0NDAyYjE4ZTE4MTQ0OTdlZDMzYSA9ICQoJzxkaXYgaWQ9Imh0bWxfZDQwNDRmNjkzMTIxNDQwMmIxOGUxODE0NDk3ZWQzM2EiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5ldyBUb3JvbnRvIC8gTWltaWNvIFNvdXRoIC8gSHVtYmVyIEJheSBTaG9yZXMsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTkzMjIwM2QzMDdiNGFkYzg5MzgyZDlkOTE4Y2IyMzMuc2V0Q29udGVudChodG1sX2Q0MDQ0ZjY5MzEyMTQ0MDJiMThlMTgxNDQ5N2VkMzNhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2U0ODIzOTdmMmMxNTRkNTBiNGZlZDM0OWZmZDZjYjJlLmJpbmRQb3B1cChwb3B1cF85OTMyMjAzZDMwN2I0YWRjODkzODJkOWQ5MThjYjIzMyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iMDc4ZDA5Zjk2Nzg0YWQzYjhmYWVjNTNiNTAxYjM5YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjczOTQxNjM5OTk5OTk5NiwtNzkuNTg4NDM2OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83MjRjNGI4MGQ0MGI0NDVhYWNhNTg3NjUyYWUyYzMwYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jOGQ4OGRkOTQ1OGM0MzJkYjk0NjUwNDg5ZGRiMmZjNCA9ICQoJzxkaXYgaWQ9Imh0bWxfYzhkODhkZDk0NThjNDMyZGI5NDY1MDQ4OWRkYjJmYzQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlNvdXRoIFN0ZWVsZXMgLyBTaWx2ZXJzdG9uZSAvIEh1bWJlcmdhdGUgLyBKYW1lc3Rvd24gLyBNb3VudCBPbGl2ZSAvIEJlYXVtb25kIEhlaWdodHMgLyBUaGlzdGxldG93biAvIEFsYmlvbiBHYXJkZW5zLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzcyNGM0YjgwZDQwYjQ0NWFhY2E1ODc2NTJhZTJjMzBjLnNldENvbnRlbnQoaHRtbF9jOGQ4OGRkOTQ1OGM0MzJkYjk0NjUwNDg5ZGRiMmZjNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iMDc4ZDA5Zjk2Nzg0YWQzYjhmYWVjNTNiNTAxYjM5Yy5iaW5kUG9wdXAocG9wdXBfNzI0YzRiODBkNDBiNDQ1YWFjYTU4NzY1MmFlMmMzMGMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjhlOTQ4ZDE4NzBjNDFmNTk0MjJkZTZhNjkxM2NkODkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43OTk1MjUyMDAwMDAwMDUsLTc5LjMxODM4ODddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2FhNGNhNWQ0ZGUwNDRlZDk4NzhiZTM5MzJhYmVkMzQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOWE3YTEyYWQ5ODU0NDc4OWI2Yzg1YzkzZWNiY2NiZTIgPSAkKCc8ZGl2IGlkPSJodG1sXzlhN2ExMmFkOTg1NDQ3ODliNmM4NWM5M2VjYmNjYmUyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdGVlbGVzIFdlc3QgLyBMJiMzOTtBbW9yZWF1eCBXZXN0LCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2FhNGNhNWQ0ZGUwNDRlZDk4NzhiZTM5MzJhYmVkMzQuc2V0Q29udGVudChodG1sXzlhN2ExMmFkOTg1NDQ3ODliNmM4NWM5M2VjYmNjYmUyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzI4ZTk0OGQxODcwYzQxZjU5NDIyZGU2YTY5MTNjZDg5LmJpbmRQb3B1cChwb3B1cF8zYWE0Y2E1ZDRkZTA0NGVkOTg3OGJlMzkzMmFiZWQzNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kODhmYWY1NGU0ZDg0NzJlYTcxMjdmYWI2MTY0MDdkMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3OTU2MjYsLTc5LjM3NzUyOTQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2EyZWQ4MjE0YzUxMDQ1Njc5NTA5ODZmNjdlNzUxYjYxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhkNWExNmVjNWRkYzQzNzg4YzgwZmUwZjc2MjFiNzRkID0gJCgnPGRpdiBpZD0iaHRtbF84ZDVhMTZlYzVkZGM0Mzc4OGM4MGZlMGY3NjIxYjc0ZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um9zZWRhbGUsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2EyZWQ4MjE0YzUxMDQ1Njc5NTA5ODZmNjdlNzUxYjYxLnNldENvbnRlbnQoaHRtbF84ZDVhMTZlYzVkZGM0Mzc4OGM4MGZlMGY3NjIxYjc0ZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kODhmYWY1NGU0ZDg0NzJlYTcxMjdmYWI2MTY0MDdkMi5iaW5kUG9wdXAocG9wdXBfYTJlZDgyMTRjNTEwNDU2Nzk1MDk4NmY2N2U3NTFiNjEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzk3NTk1ZjMzY2MxNDBkYWFkNWQ3Y2NmODE3ZDZmNzIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDY0MzUyLC03OS4zNzQ4NDU5OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hNThkY2U4ZDZhM2M0ZjYzYTg5N2Q2MTJlMDk4OGFkNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNzBjYmY3ZDkzYTc0ZDdmYjhiNzM4NDI5MjQ0MGMxMCA9ICQoJzxkaXYgaWQ9Imh0bWxfMzcwY2JmN2Q5M2E3NGQ3ZmI4YjczODQyOTI0NDBjMTAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0biBBIFBPIEJveGVzLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hNThkY2U4ZDZhM2M0ZjYzYTg5N2Q2MTJlMDk4OGFkNy5zZXRDb250ZW50KGh0bWxfMzcwY2JmN2Q5M2E3NGQ3ZmI4YjczODQyOTI0NDBjMTApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzk3NTk1ZjMzY2MxNDBkYWFkNWQ3Y2NmODE3ZDZmNzIuYmluZFBvcHVwKHBvcHVwX2E1OGRjZThkNmEzYzRmNjNhODk3ZDYxMmUwOTg4YWQ3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZiOTIzNTc1NDdkNTQ5ZTBiYzI4ODNhMDg1MzRlMjlkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjAyNDEzNzAwMDAwMDEsLTc5LjU0MzQ4NDA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzJiZmY1NjI2YzZhMTRkNWRhNWEzNTkxMzE4NTAzYWZhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RlMzExNjRhOGYwZjQ5YjNiMzkzODNlNDFkYmIyMWExID0gJCgnPGRpdiBpZD0iaHRtbF9kZTMxMTY0YThmMGY0OWIzYjM5MzgzZTQxZGJiMjFhMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QWxkZXJ3b29kIC8gTG9uZyBCcmFuY2gsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMmJmZjU2MjZjNmExNGQ1ZGE1YTM1OTEzMTg1MDNhZmEuc2V0Q29udGVudChodG1sX2RlMzExNjRhOGYwZjQ5YjNiMzkzODNlNDFkYmIyMWExKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzZiOTIzNTc1NDdkNTQ5ZTBiYzI4ODNhMDg1MzRlMjlkLmJpbmRQb3B1cChwb3B1cF8yYmZmNTYyNmM2YTE0ZDVkYTVhMzU5MTMxODUwM2FmYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mNjRiNTkwMDBhNGM0YTM4ODViNjBhYzY2MjcwYTI2YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcwNjc0ODI5OTk5OTk5NCwtNzkuNTk0MDU0NF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81ZWQ4M2FhODQxOGQ0NjQ5YWM1MzNlNDQ2MGZiMWZkNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85OGQ0YzlmNDVjMDQ0ZTZlYWQ1MGJiMTM0YzE2YWUwNCA9ICQoJzxkaXYgaWQ9Imh0bWxfOThkNGM5ZjQ1YzA0NGU2ZWFkNTBiYjEzNGMxNmFlMDQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRod2VzdCwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81ZWQ4M2FhODQxOGQ0NjQ5YWM1MzNlNDQ2MGZiMWZkNy5zZXRDb250ZW50KGh0bWxfOThkNGM5ZjQ1YzA0NGU2ZWFkNTBiYjEzNGMxNmFlMDQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjY0YjU5MDAwYTRjNGEzODg1YjYwYWM2NjI3MGEyNmEuYmluZFBvcHVwKHBvcHVwXzVlZDgzYWE4NDE4ZDQ2NDlhYzUzM2U0NDYwZmIxZmQ3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2FmYzE3YWFmNGY4MzRhMWU5NGQwMjA5NTdkMWVjYzA4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuODM2MTI0NzAwMDAwMDA2LC03OS4yMDU2MzYwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xMjkwYzUwNDk1Mjg0NTMzOWJkODFmMjRiZTYwYWJjMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hMjYxYmYzMmU5ZWI0YjI2YjA1ZDFiODA5ZWFmZDliZCA9ICQoJzxkaXYgaWQ9Imh0bWxfYTI2MWJmMzJlOWViNGIyNmIwNWQxYjgwOWVhZmQ5YmQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlVwcGVyIFJvdWdlLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTI5MGM1MDQ5NTI4NDUzMzliZDgxZjI0YmU2MGFiYzIuc2V0Q29udGVudChodG1sX2EyNjFiZjMyZTllYjRiMjZiMDVkMWI4MDllYWZkOWJkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2FmYzE3YWFmNGY4MzRhMWU5NGQwMjA5NTdkMWVjYzA4LmJpbmRQb3B1cChwb3B1cF8xMjkwYzUwNDk1Mjg0NTMzOWJkODFmMjRiZTYwYWJjMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85N2ZlMDk0YmMzNTg0NjQ1OTZjMTFhZjg3NGU1NjQwNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2Nzk2NywtNzkuMzY3Njc1M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wMWM2MjUxY2NkYmQ0MTVhYjI0YmY3MGU4ZjU2ZWIxNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zMWIwYjgwNThjZTI0NmY4YmY3M2UwZWVlMmJlMjY3NCA9ICQoJzxkaXYgaWQ9Imh0bWxfMzFiMGI4MDU4Y2UyNDZmOGJmNzNlMGVlZTJiZTI2NzQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0LiBKYW1lcyBUb3duIC8gQ2FiYmFnZXRvd24sIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzAxYzYyNTFjY2RiZDQxNWFiMjRiZjcwZThmNTZlYjE1LnNldENvbnRlbnQoaHRtbF8zMWIwYjgwNThjZTI0NmY4YmY3M2UwZWVlMmJlMjY3NCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85N2ZlMDk0YmMzNTg0NjQ1OTZjMTFhZjg3NGU1NjQwNy5iaW5kUG9wdXAocG9wdXBfMDFjNjI1MWNjZGJkNDE1YWIyNGJmNzBlOGY1NmViMTUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTY5ZDQ5YzNkMjExNDNiYjgzMmFmODk4NWM5YjQ2MWYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg0MjkyLC03OS4zODIyODAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzBjOWJiNzZlOTY5YzQyODViYTM5M2M5NGU5MjUxYTZmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZkZjc0MjU3ZjU0OTQyMWU5Y2RkMDc0NjkyYjY5YTJjID0gJCgnPGRpdiBpZD0iaHRtbF9mZGY3NDI1N2Y1NDk0MjFlOWNkZDA3NDY5MmI2OWEyYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Rmlyc3QgQ2FuYWRpYW4gUGxhY2UgLyBVbmRlcmdyb3VuZCBjaXR5LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wYzliYjc2ZTk2OWM0Mjg1YmEzOTNjOTRlOTI1MWE2Zi5zZXRDb250ZW50KGh0bWxfZmRmNzQyNTdmNTQ5NDIxZTljZGQwNzQ2OTJiNjlhMmMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTY5ZDQ5YzNkMjExNDNiYjgzMmFmODk4NWM5YjQ2MWYuYmluZFBvcHVwKHBvcHVwXzBjOWJiNzZlOTY5YzQyODViYTM5M2M5NGU5MjUxYTZmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzQzNDk0NWFmYzY5YjQ5ZTk4MWY0Mjc5MmNlZDBhN2MyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUzNjUzNjAwMDAwMDA1LC03OS41MDY5NDM2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk0MTdlOWQ1MDE3ZjRmMzM4MWJiYzY5MTZhYWIwMjgxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzU1OGJkNmNhNzBlNTQzMjhhZGEyMjZlMTI4YzBlNDI4ID0gJCgnPGRpdiBpZD0iaHRtbF81NThiZDZjYTcwZTU0MzI4YWRhMjI2ZTEyOGMwZTQyOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VGhlIEtpbmdzd2F5IC8gTW9udGdvbWVyeSBSb2FkICAvIE9sZCBNaWxsIE5vcnRoLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk0MTdlOWQ1MDE3ZjRmMzM4MWJiYzY5MTZhYWIwMjgxLnNldENvbnRlbnQoaHRtbF81NThiZDZjYTcwZTU0MzI4YWRhMjI2ZTEyOGMwZTQyOCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80MzQ5NDVhZmM2OWI0OWU5ODFmNDI3OTJjZWQwYTdjMi5iaW5kUG9wdXAocG9wdXBfOTQxN2U5ZDUwMTdmNGYzMzgxYmJjNjkxNmFhYjAyODEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDc1ODhiODNiYWQ4NDQxNzkzMTI1NWE5M2M3NGY2ZTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjU4NTk5LC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lZDllMWViNDQ0NDI0NjMxOThhZmY1OWRkNWI1ZGRlZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mNjViYmRjNTgyMzg0OTQzOThkZWNkNGNhMjM5MzkxZiA9ICQoJzxkaXYgaWQ9Imh0bWxfZjY1YmJkYzU4MjM4NDk0Mzk4ZGVjZDRjYTIzOTM5MWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNodXJjaCBhbmQgV2VsbGVzbGV5LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lZDllMWViNDQ0NDI0NjMxOThhZmY1OWRkNWI1ZGRlZi5zZXRDb250ZW50KGh0bWxfZjY1YmJkYzU4MjM4NDk0Mzk4ZGVjZDRjYTIzOTM5MWYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNDc1ODhiODNiYWQ4NDQxNzkzMTI1NWE5M2M3NGY2ZTMuYmluZFBvcHVwKHBvcHVwX2VkOWUxZWI0NDQ0MjQ2MzE5OGFmZjU5ZGQ1YjVkZGVmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Y0NDIzMjcyZmIwMjQyZGViZTk2YzAyZGIwYmVhMWRjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjYyNzQzOSwtNzkuMzIxNTU4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzNkOTNmYWE3NGYzMTQ0NzU4NWFmNDQxMTJiMzk5NmFiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlkMjY1NGZmNjI3NzRjMGI5Y2VmZWY4OTA0YWEzOTUwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhmMTE2ZDQ3ZTg2MTRjNTBhNDg1YjMwNmU0MDk0OWY1ID0gJCgnPGRpdiBpZD0iaHRtbF84ZjExNmQ0N2U4NjE0YzUwYTQ4NWIzMDZlNDA5NDlmNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QnVzaW5lc3MgcmVwbHkgbWFpbCBQcm9jZXNzaW5nIENlbnRyRSwgRWFzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ZDI2NTRmZjYyNzc0YzBiOWNlZmVmODkwNGFhMzk1MC5zZXRDb250ZW50KGh0bWxfOGYxMTZkNDdlODYxNGM1MGE0ODViMzA2ZTQwOTQ5ZjUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjQ0MjMyNzJmYjAyNDJkZWJlOTZjMDJkYjBiZWExZGMuYmluZFBvcHVwKHBvcHVwXzlkMjY1NGZmNjI3NzRjMGI5Y2VmZWY4OTA0YWEzOTUwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzg0MjVhNDkxMDI4NjRkYjQ4OTc3NzE3ZTZlNzkyNmViID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjM2MjU3OSwtNzkuNDk4NTA5MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfM2Q5M2ZhYTc0ZjMxNDQ3NTg1YWY0NDExMmIzOTk2YWIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDAzMDAxNDY3NWRlNGRjYWEwMTQ0ZjExOWZkNjFmNjIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTNjYmRmNTc0NzM3NGExMzkwM2FiYjA2ZjVjODM3YTEgPSAkKCc8ZGl2IGlkPSJodG1sX2UzY2JkZjU3NDczNzRhMTM5MDNhYmIwNmY1YzgzN2ExIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5PbGQgTWlsbCBTb3V0aCAvIEtpbmcmIzM5O3MgTWlsbCBQYXJrIC8gU3VubnlsZWEgLyBIdW1iZXIgQmF5IC8gTWltaWNvIE5FIC8gVGhlIFF1ZWVuc3dheSBFYXN0IC8gUm95YWwgWW9yayBTb3V0aCBFYXN0IC8gS2luZ3N3YXkgUGFyayBTb3V0aCBFYXN0LCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzAwMzAwMTQ2NzVkZTRkY2FhMDE0NGYxMTlmZDYxZjYyLnNldENvbnRlbnQoaHRtbF9lM2NiZGY1NzQ3Mzc0YTEzOTAzYWJiMDZmNWM4MzdhMSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84NDI1YTQ5MTAyODY0ZGI0ODk3NzcxN2U2ZTc5MjZlYi5iaW5kUG9wdXAocG9wdXBfMDAzMDAxNDY3NWRlNGRjYWEwMTQ0ZjExOWZkNjFmNjIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzkzOGFiYTYzNDA3NGYwMGFlZTdiY2ZkMTI2Mzk4NmYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg4NDA4LC03OS41MjA5OTk0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8zZDkzZmFhNzRmMzE0NDc1ODVhZjQ0MTEyYjM5OTZhYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83MzZlMzFmYjZmY2I0YzJlYjQ4NDhkZTU1OWNhNjBjOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80YWQ4MTQ1NTA1Zjc0MzQyYjVmMTEwYTMzMTNiYjExZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNGFkODE0NTUwNWY3NDM0MmI1ZjExMGEzMzEzYmIxMWUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1pbWljbyBOVyAvIFRoZSBRdWVlbnN3YXkgV2VzdCAvIFNvdXRoIG9mIEJsb29yIC8gS2luZ3N3YXkgUGFyayBTb3V0aCBXZXN0IC8gUm95YWwgWW9yayBTb3V0aCBXZXN0LCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzczNmUzMWZiNmZjYjRjMmViNDg0OGRlNTU5Y2E2MGM5LnNldENvbnRlbnQoaHRtbF80YWQ4MTQ1NTA1Zjc0MzQyYjVmMTEwYTMzMTNiYjExZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jOTM4YWJhNjM0MDc0ZjAwYWVlN2JjZmQxMjYzOTg2Zi5iaW5kUG9wdXAocG9wdXBfNzM2ZTMxZmI2ZmNiNGMyZWI0ODQ4ZGU1NTljYTYwYzkpOwoKICAgICAgICAgICAgCiAgICAgICAgCjwvc2NyaXB0Pg== onload="this.contentDocument.open();this.contentDocument.write(atob(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



### Credentials to access Foursquare API


```python
CLIENT_ID = 'OI0OQXI525ZTEC0S3BE4FQDR5XT1DHRWYAKAST2ELENL04OE' # your Foursquare ID
CLIENT_SECRET = 'XP2C5RFLCFIWXAT34H4MN3SID1JPHB14ERDLEWWRJXRRIATG' # your Foursquare Secret
VERSION = '20180605' # Foursquare API version

print('Your credentails:')
print('CLIENT_ID: ' + CLIENT_ID)
print('CLIENT_SECRET:' + CLIENT_SECRET)
```

    Your credentails:
    CLIENT_ID: OI0OQXI525ZTEC0S3BE4FQDR5XT1DHRWYAKAST2ELENL04OE
    CLIENT_SECRET:XP2C5RFLCFIWXAT34H4MN3SID1JPHB14ERDLEWWRJXRRIATG



```python
LIMIT=100
radius=500
```


```python
df_toronto = df_merged[df_merged['Borough'] == "Downtown Toronto"]

```

### get_category_type function from the Foursquare lab:


```python
# function that extracts the category of the venue
def get_category_type(row):
    try:
        categories_list = row['categories']
    except:
        categories_list = row['venue.categories']
        
    if len(categories_list) == 0:
        return None
    else:
        return categories_list[0]['name']
```

### Function to repeat the same process above to all the neighborhoods in Downtown Toronto


```python
def getNearbyVenues(names, latitudes, longitudes, radius=500):
    
    venues_list=[]
    for name, lat, lng in zip(names, latitudes, longitudes):
        print(name)
            
        # create the API request URL
        url = 'https://api.foursquare.com/v2/venues/explore?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&limit={}'.format(
            CLIENT_ID, 
            CLIENT_SECRET, 
            VERSION, 
            lat, 
            lng, 
            radius, 
            LIMIT)
            
        # make the GET request
        results = requests.get(url).json()["response"]['groups'][0]['items']
        
        # return only relevant information for each nearby venue
        venues_list.append([(
            name, 
            lat, 
            lng, 
            v['venue']['name'], 
            v['venue']['location']['lat'], 
            v['venue']['location']['lng'],  
            v['venue']['categories'][0]['name']) for v in results])

    nearby_venues = pd.DataFrame([item for venue_list in venues_list for item in venue_list])
    nearby_venues.columns = ['Neighborhood', 
                  'Neighborhood Latitude', 
                  'Neighborhood Longitude', 
                  'Venue', 
                  'Venue Latitude', 
                  'Venue Longitude', 
                  'Venue Category']
    
    return(nearby_venues)
```

### Running the function above for all neighboorhods and create a dataframe called toront_venues


```python
toronto_venues = getNearbyVenues(names=df_toronto['Neighborhood'],
                                   latitudes=df_toronto['Latitude'],
                                   longitudes=df_toronto['Longitude']
                                  )
```

    Regent Park / Harbourfront
    Queen's Park / Ontario Provincial Government
    Garden District, Ryerson
    St. James Town
    Berczy Park
    Central Bay Street
    Christie
    Richmond / Adelaide / King
    Harbourfront East / Union Station / Toronto Islands
    Toronto Dominion Centre / Design Exchange
    Commerce Court / Victoria Hotel
    University of Toronto / Harbord
    Kensington Market / Chinatown / Grange Park
    
    Rosedale
    Stn A PO Boxes
    St. James Town / Cabbagetown
    First Canadian Place / Underground city
    Church and Wellesley


### Some characteristics of the dataframe


```python
toronto_venues.shape
```




    (1284, 7)




```python
toronto_venues.head()
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
      <th>Neighborhood</th>
      <th>Neighborhood Latitude</th>
      <th>Neighborhood Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Regent Park / Harbourfront</td>
      <td>43.65426</td>
      <td>-79.360636</td>
      <td>Roselle Desserts</td>
      <td>43.653447</td>
      <td>-79.362017</td>
      <td>Bakery</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Regent Park / Harbourfront</td>
      <td>43.65426</td>
      <td>-79.360636</td>
      <td>Tandem Coffee</td>
      <td>43.653559</td>
      <td>-79.361809</td>
      <td>Coffee Shop</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Regent Park / Harbourfront</td>
      <td>43.65426</td>
      <td>-79.360636</td>
      <td>Cooper Koo Family YMCA</td>
      <td>43.653249</td>
      <td>-79.358008</td>
      <td>Distribution Center</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Regent Park / Harbourfront</td>
      <td>43.65426</td>
      <td>-79.360636</td>
      <td>Body Blitz Spa East</td>
      <td>43.654735</td>
      <td>-79.359874</td>
      <td>Spa</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Regent Park / Harbourfront</td>
      <td>43.65426</td>
      <td>-79.360636</td>
      <td>Morning Glory Cafe</td>
      <td>43.653947</td>
      <td>-79.361149</td>
      <td>Breakfast Spot</td>
    </tr>
  </tbody>
</table>
</div>



### Venues per neighboorhood:


```python
toronto_venues.groupby('Neighborhood').count()
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
      <th>Neighborhood Latitude</th>
      <th>Neighborhood Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
    <tr>
      <th>Neighborhood</th>
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
      <th></th>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
    </tr>
    <tr>
      <th>Berczy Park</th>
      <td>57</td>
      <td>57</td>
      <td>57</td>
      <td>57</td>
      <td>57</td>
      <td>57</td>
    </tr>
    <tr>
      <th>Central Bay Street</th>
      <td>76</td>
      <td>76</td>
      <td>76</td>
      <td>76</td>
      <td>76</td>
      <td>76</td>
    </tr>
    <tr>
      <th>Christie</th>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
    </tr>
    <tr>
      <th>Church and Wellesley</th>
      <td>79</td>
      <td>79</td>
      <td>79</td>
      <td>79</td>
      <td>79</td>
      <td>79</td>
    </tr>
    <tr>
      <th>Commerce Court / Victoria Hotel</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>First Canadian Place / Underground city</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Garden District, Ryerson</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Harbourfront East / Union Station / Toronto Islands</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Kensington Market / Chinatown / Grange Park</th>
      <td>75</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
    </tr>
    <tr>
      <th>Queen's Park / Ontario Provincial Government</th>
      <td>34</td>
      <td>34</td>
      <td>34</td>
      <td>34</td>
      <td>34</td>
      <td>34</td>
    </tr>
    <tr>
      <th>Regent Park / Harbourfront</th>
      <td>46</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
    </tr>
    <tr>
      <th>Richmond / Adelaide / King</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Rosedale</th>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>St. James Town</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>St. James Town / Cabbagetown</th>
      <td>48</td>
      <td>48</td>
      <td>48</td>
      <td>48</td>
      <td>48</td>
      <td>48</td>
    </tr>
    <tr>
      <th>Stn A PO Boxes</th>
      <td>97</td>
      <td>97</td>
      <td>97</td>
      <td>97</td>
      <td>97</td>
      <td>97</td>
    </tr>
    <tr>
      <th>Toronto Dominion Centre / Design Exchange</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>University of Toronto / Harbord</th>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>




```python
print('There are {} uniques categories.'.format(len(toronto_venues['Venue Category'].unique())))
```

    There are 204 uniques categories.


### Function to sort venues in descending order


```python
def return_most_common_venues(row, num_top_venues):
    row_categories = row.iloc[1:]
    row_categories_sorted = row_categories.sort_values(ascending=False)
    
    return row_categories_sorted.index.values[0:num_top_venues]
```


```python
# one hot encoding
toronto_onehot = pd.get_dummies(toronto_venues[['Venue Category']], prefix="", prefix_sep="")

# add neighborhood column back to dataframe
toronto_onehot['Neighborhood'] = toronto_venues['Neighborhood'] 

# move neighborhood column to the first column
fixed_columns = [toronto_onehot.columns[-1]] + list(toronto_onehot.columns[:-1])
toronto_onehot = toronto_onehot[fixed_columns]

toronto_onehot.head()
toronto_grouped = toronto_onehot.groupby('Neighborhood').mean().reset_index()
```

### Show the top 10 venues per neighboorhood


```python
import numpy as np
num_top_venues = 10

indicators = ['st', 'nd', 'rd']

# create columns according to number of top venues
columns = ['Neighborhood']
for ind in np.arange(num_top_venues):
    try:
        columns.append('{}{} Most Common Venue'.format(ind+1, indicators[ind]))
    except:
        columns.append('{}th Most Common Venue'.format(ind+1))

# create a new dataframe
neighborhoods_venues_sorted = pd.DataFrame(columns=columns)
neighborhoods_venues_sorted['Neighborhood'] = toronto_grouped['Neighborhood']

for ind in np.arange(toronto_grouped.shape[0]):
    neighborhoods_venues_sorted.iloc[ind, 1:] = return_most_common_venues(toronto_grouped.iloc[ind, :], num_top_venues)

neighborhoods_venues_sorted.head()
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
      <th>Neighborhood</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td></td>
      <td>Airport Service</td>
      <td>Airport Lounge</td>
      <td>Airport Terminal</td>
      <td>Boutique</td>
      <td>Sculpture Garden</td>
      <td>Plane</td>
      <td>Rental Car Location</td>
      <td>Bar</td>
      <td>Harbor / Marina</td>
      <td>Boat or Ferry</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Berczy Park</td>
      <td>Coffee Shop</td>
      <td>Cocktail Bar</td>
      <td>Farmers Market</td>
      <td>Cheese Shop</td>
      <td>Bakery</td>
      <td>Seafood Restaurant</td>
      <td>Beer Bar</td>
      <td>Restaurant</td>
      <td>Café</td>
      <td>Indian Restaurant</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Central Bay Street</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Café</td>
      <td>Sandwich Place</td>
      <td>Japanese Restaurant</td>
      <td>Department Store</td>
      <td>Bubble Tea Shop</td>
      <td>Burger Joint</td>
      <td>Spa</td>
      <td>Middle Eastern Restaurant</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Christie</td>
      <td>Grocery Store</td>
      <td>Café</td>
      <td>Park</td>
      <td>Baby Store</td>
      <td>Coffee Shop</td>
      <td>Nightclub</td>
      <td>Gas Station</td>
      <td>Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Diner</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Church and Wellesley</td>
      <td>Coffee Shop</td>
      <td>Japanese Restaurant</td>
      <td>Gay Bar</td>
      <td>Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Gastropub</td>
      <td>Pub</td>
      <td>Burger Joint</td>
      <td>Hotel</td>
      <td>Café</td>
    </tr>
  </tbody>
</table>
</div>



### Now let's cluster the neighboorhoods

#### Cluster using k-means with k=5


```python
from sklearn.cluster import KMeans

# set number of clusters
kclusters = 5

toronto_grouped_clustering = toronto_grouped.drop('Neighborhood', 1)

# run k-means clustering
kmeans = KMeans(n_clusters=kclusters, random_state=0).fit(toronto_grouped_clustering)

# check cluster labels generated for each row in the dataframe
kmeans.labels_[0:10] 
```




    array([3, 4, 4, 2, 0, 4, 4, 0, 4, 0], dtype=int32)



### Cluster as well as the top 10 venues for each neighborhood in a new dataframe:


```python
# add clustering labels
neighborhoods_venues_sorted.insert(0, 'Cluster Labels', kmeans.labels_)

toronto_merged = df_toronto

# merge toronto_grouped with toronto_data to add latitude/longitude for each neighborhood
toronto_merged = toronto_merged.join(neighborhoods_venues_sorted.set_index('Neighborhood'), on='Neighborhood')

toronto_merged.head() # check the last columns!
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park / Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
      <td>4</td>
      <td>Coffee Shop</td>
      <td>Pub</td>
      <td>Bakery</td>
      <td>Park</td>
      <td>Mexican Restaurant</td>
      <td>Restaurant</td>
      <td>Breakfast Spot</td>
      <td>Café</td>
      <td>Theater</td>
      <td>Yoga Studio</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park / Ontario Provincial Government</td>
      <td>43.662301</td>
      <td>-79.389494</td>
      <td>4</td>
      <td>Coffee Shop</td>
      <td>Diner</td>
      <td>Yoga Studio</td>
      <td>Italian Restaurant</td>
      <td>Café</td>
      <td>Distribution Center</td>
      <td>Discount Store</td>
      <td>Bar</td>
      <td>Bank</td>
      <td>Mexican Restaurant</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
      <td>43.657162</td>
      <td>-79.378937</td>
      <td>0</td>
      <td>Clothing Store</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Middle Eastern Restaurant</td>
      <td>Bubble Tea Shop</td>
      <td>Cosmetics Shop</td>
      <td>Japanese Restaurant</td>
      <td>Lingerie Store</td>
      <td>Electronics Store</td>
      <td>Bookstore</td>
    </tr>
    <tr>
      <th>15</th>
      <td>M5C</td>
      <td>Downtown Toronto</td>
      <td>St. James Town</td>
      <td>43.651494</td>
      <td>-79.375418</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Italian Restaurant</td>
      <td>Cocktail Bar</td>
      <td>Restaurant</td>
      <td>Gastropub</td>
      <td>Beer Bar</td>
      <td>Bakery</td>
      <td>Clothing Store</td>
      <td>American Restaurant</td>
    </tr>
    <tr>
      <th>20</th>
      <td>M5E</td>
      <td>Downtown Toronto</td>
      <td>Berczy Park</td>
      <td>43.644771</td>
      <td>-79.373306</td>
      <td>4</td>
      <td>Coffee Shop</td>
      <td>Cocktail Bar</td>
      <td>Farmers Market</td>
      <td>Cheese Shop</td>
      <td>Bakery</td>
      <td>Seafood Restaurant</td>
      <td>Beer Bar</td>
      <td>Restaurant</td>
      <td>Café</td>
      <td>Indian Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



### Visualization of the clusters:


```python
import matplotlib.cm as cm
import matplotlib.colors as colors
# create map
map_clusters = folium.Map(location=[latitude, longitude], zoom_start=13)

# set color scheme for the clusters
x = np.arange(kclusters)
ys = [i + x + (i*x)**2 for i in range(kclusters)]
colors_array = cm.rainbow(np.linspace(0, 1, len(ys)))
rainbow = [colors.rgb2hex(i) for i in colors_array]

# add markers to the map
markers_colors = []
for lat, lon, poi, cluster in zip(toronto_merged['Latitude'], toronto_merged['Longitude'], toronto_merged['Neighborhood'], toronto_merged['Cluster Labels']):
    label = folium.Popup(str(poi) + ' Cluster ' + str(cluster), parse_html=True)
    folium.CircleMarker(
        [lat, lon],
        radius=5,
        popup=label,
        color=rainbow[cluster-1],
        fill=True,
        fill_color=rainbow[cluster-1],
        fill_opacity=0.7).add_to(map_clusters)
       
map_clusters
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfY2I3ZmU5MmE5MWU5NGMyZGIyNzQ4MjhmNzI4MThiZmIgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwX2NiN2ZlOTJhOTFlOTRjMmRiMjc0ODI4ZjcyODE4YmZiIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF9jYjdmZTkyYTkxZTk0YzJkYjI3NDgyOGY3MjgxOGJmYiA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF9jYjdmZTkyYTkxZTk0YzJkYjI3NDgyOGY3MjgxOGJmYicsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjUzNDgxNywtNzkuMzgzOTM0N10sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB6b29tOiAxMywKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIG1heEJvdW5kczogYm91bmRzLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgbGF5ZXJzOiBbXSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHdvcmxkQ29weUp1bXA6IGZhbHNlLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgY3JzOiBMLkNSUy5FUFNHMzg1NwogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfN2I3MTJmM2Y4NzQzNDQ1N2I0ZGY1ZGUwNDA2MTc0OTUgPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICdodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZycsCiAgICAgICAgICAgICAgICB7CiAgImF0dHJpYnV0aW9uIjogbnVsbCwKICAiZGV0ZWN0UmV0aW5hIjogZmFsc2UsCiAgIm1heFpvb20iOiAxOCwKICAibWluWm9vbSI6IDEsCiAgIm5vV3JhcCI6IGZhbHNlLAogICJzdWJkb21haW5zIjogImFiYyIKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfY2I3ZmU5MmE5MWU5NGMyZGIyNzQ4MjhmNzI4MThiZmIpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2RlNTY0ZmU4Yzk5NzRiYmJiOGYzYzRlMDg4Y2M3ZDA0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU0MjU5OSwtNzkuMzYwNjM1OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jYjdmZTkyYTkxZTk0YzJkYjI3NDgyOGY3MjgxOGJmYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83YjA2NTlhNDcwNjY0ZWQ2OTY0MGU1MTQ4ZjE1ZDkwMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hZjExNDM5MzE5MDI0MWRlODA5YWMyZGQ0MzcyMjM1OSA9ICQoJzxkaXYgaWQ9Imh0bWxfYWYxMTQzOTMxOTAyNDFkZTgwOWFjMmRkNDM3MjIzNTkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJlZ2VudCBQYXJrIC8gSGFyYm91cmZyb250IENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2IwNjU5YTQ3MDY2NGVkNjk2NDBlNTE0OGYxNWQ5MDMuc2V0Q29udGVudChodG1sX2FmMTE0MzkzMTkwMjQxZGU4MDlhYzJkZDQzNzIyMzU5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RlNTY0ZmU4Yzk5NzRiYmJiOGYzYzRlMDg4Y2M3ZDA0LmJpbmRQb3B1cChwb3B1cF83YjA2NTlhNDcwNjY0ZWQ2OTY0MGU1MTQ4ZjE1ZDkwMyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lZDQ3MTg3NDNlYmU0NjBlYjBlZTFkNDY0ZDZlNmUzMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjMwMTUsLTc5LjM4OTQ5MzhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfY2I3ZmU5MmE5MWU5NGMyZGIyNzQ4MjhmNzI4MThiZmIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2I3MTM5YWQ0MWU0NDA0Zjg5NzlmNzNjZGYyZGMxZTEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzgxODk1ODBiMGIyNDg2ZmE4NzRkNDRlZjE4NmQ2OTkgPSAkKCc8ZGl2IGlkPSJodG1sXzM4MTg5NTgwYjBiMjQ4NmZhODc0ZDQ0ZWYxODZkNjk5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5RdWVlbiYjMzk7cyBQYXJrIC8gT250YXJpbyBQcm92aW5jaWFsIEdvdmVybm1lbnQgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zYjcxMzlhZDQxZTQ0MDRmODk3OWY3M2NkZjJkYzFlMS5zZXRDb250ZW50KGh0bWxfMzgxODk1ODBiMGIyNDg2ZmE4NzRkNDRlZjE4NmQ2OTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZWQ0NzE4NzQzZWJlNDYwZWIwZWUxZDQ2NGQ2ZTZlMzIuYmluZFBvcHVwKHBvcHVwXzNiNzEzOWFkNDFlNDQwNGY4OTc5ZjczY2RmMmRjMWUxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2E2MGE0YWRkNjI3YzQ3ODlhZWY0Y2ZhODk5OGRmNTJlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU3MTYxOCwtNzkuMzc4OTM3MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfY2I3ZmU5MmE5MWU5NGMyZGIyNzQ4MjhmNzI4MThiZmIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTk1YjJmMzJkYTQwNDNkODkzMDNlYjc4OTNlYmIyN2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMmJmZmJmMGI3YjNiNGMwYTg0MGEwNGM5NzViNjk3ZjEgPSAkKCc8ZGl2IGlkPSJodG1sXzJiZmZiZjBiN2IzYjRjMGE4NDBhMDRjOTc1YjY5N2YxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HYXJkZW4gRGlzdHJpY3QsIFJ5ZXJzb24gQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lOTViMmYzMmRhNDA0M2Q4OTMwM2ViNzg5M2ViYjI3Yy5zZXRDb250ZW50KGh0bWxfMmJmZmJmMGI3YjNiNGMwYTg0MGEwNGM5NzViNjk3ZjEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYTYwYTRhZGQ2MjdjNDc4OWFlZjRjZmE4OTk4ZGY1MmUuYmluZFBvcHVwKHBvcHVwX2U5NWIyZjMyZGE0MDQzZDg5MzAzZWI3ODkzZWJiMjdjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzAxZmEwZjU5OTc0ZjQ5NDZiYmVlYjdkM2JmMzc3MTFjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUxNDkzOSwtNzkuMzc1NDE3OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jYjdmZTkyYTkxZTk0YzJkYjI3NDgyOGY3MjgxOGJmYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jNzMyMDM5YjIyYmE0OGUzODhlNTE3YTA1MzE4ZDlkNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lNzE3YWExZjk1YWU0ZjIxODhkYWJkZTA2NDI3NjJiNyA9ICQoJzxkaXYgaWQ9Imh0bWxfZTcxN2FhMWY5NWFlNGYyMTg4ZGFiZGUwNjQyNzYyYjciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0LiBKYW1lcyBUb3duIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzczMjAzOWIyMmJhNDhlMzg4ZTUxN2EwNTMxOGQ5ZDYuc2V0Q29udGVudChodG1sX2U3MTdhYTFmOTVhZTRmMjE4OGRhYmRlMDY0Mjc2MmI3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzAxZmEwZjU5OTc0ZjQ5NDZiYmVlYjdkM2JmMzc3MTFjLmJpbmRQb3B1cChwb3B1cF9jNzMyMDM5YjIyYmE0OGUzODhlNTE3YTA1MzE4ZDlkNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zZmRiY2NjNTRiMDg0YjQ4OGY4M2E4ZDM2NjcyYmVlMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NDc3MDc5OTk5OTk5NiwtNzkuMzczMzA2NF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jYjdmZTkyYTkxZTk0YzJkYjI3NDgyOGY3MjgxOGJmYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hOGI4NGY0MmNiOTg0MjI3YjNiZDQwMzAxNjdmNjVkOCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85NWNjZjBjYzg5ZDU0YjA2YWI5ZTY1YzU0NjFmMmI1YiA9ICQoJzxkaXYgaWQ9Imh0bWxfOTVjY2YwY2M4OWQ1NGIwNmFiOWU2NWM1NDYxZjJiNWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJlcmN6eSBQYXJrIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYThiODRmNDJjYjk4NDIyN2IzYmQ0MDMwMTY3ZjY1ZDguc2V0Q29udGVudChodG1sXzk1Y2NmMGNjODlkNTRiMDZhYjllNjVjNTQ2MWYyYjViKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzNmZGJjY2M1NGIwODRiNDg4ZjgzYThkMzY2NzJiZWUxLmJpbmRQb3B1cChwb3B1cF9hOGI4NGY0MmNiOTg0MjI3YjNiZDQwMzAxNjdmNjVkOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xMWMxM2VmMDFlNDY0MzQ3YWJiMmVkM2QxOTE1ZDEyNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1Nzk1MjQsLTc5LjM4NzM4MjZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfY2I3ZmU5MmE5MWU5NGMyZGIyNzQ4MjhmNzI4MThiZmIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTYwNzMwOWQzZmRkNDBkYmIxMjNjMGMyYjJjMjE3MjQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWZjMmVjMmJlNWFlNGFiNGExMGY2YTZjM2E3YmFmZmUgPSAkKCc8ZGl2IGlkPSJodG1sXzVmYzJlYzJiZTVhZTRhYjRhMTBmNmE2YzNhN2JhZmZlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DZW50cmFsIEJheSBTdHJlZXQgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85NjA3MzA5ZDNmZGQ0MGRiYjEyM2MwYzJiMmMyMTcyNC5zZXRDb250ZW50KGh0bWxfNWZjMmVjMmJlNWFlNGFiNGExMGY2YTZjM2E3YmFmZmUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTFjMTNlZjAxZTQ2NDM0N2FiYjJlZDNkMTkxNWQxMjcuYmluZFBvcHVwKHBvcHVwXzk2MDczMDlkM2ZkZDQwZGJiMTIzYzBjMmIyYzIxNzI0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzVhZmFkMDZiMzNmZDQyOGJhNjE0NjM2OWE5MjgwMmE0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY5NTQyLC03OS40MjI1NjM3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiMwMGI1ZWIiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMDBiNWViIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2NiN2ZlOTJhOTFlOTRjMmRiMjc0ODI4ZjcyODE4YmZiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Q1MWYyODJiZmUwNzRiZjc4MWI1NDIyODZkY2EyNTBkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2UwZTYwNzdmODhjYzQzZmQ5OGNkZWI0NjA4Njc3MzYwID0gJCgnPGRpdiBpZD0iaHRtbF9lMGU2MDc3Zjg4Y2M0M2ZkOThjZGViNDYwODY3NzM2MCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2hyaXN0aWUgQ2x1c3RlciAyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kNTFmMjgyYmZlMDc0YmY3ODFiNTQyMjg2ZGNhMjUwZC5zZXRDb250ZW50KGh0bWxfZTBlNjA3N2Y4OGNjNDNmZDk4Y2RlYjQ2MDg2NzczNjApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNWFmYWQwNmIzM2ZkNDI4YmE2MTQ2MzY5YTkyODAyYTQuYmluZFBvcHVwKHBvcHVwX2Q1MWYyODJiZmUwNzRiZjc4MWI1NDIyODZkY2EyNTBkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I3ZGMyYzM5ZDE1ZDQ1OWM5ZWM5OTIyYjY0NDU2MTU3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUwNTcxMjAwMDAwMDEsLTc5LjM4NDU2NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfY2I3ZmU5MmE5MWU5NGMyZGIyNzQ4MjhmNzI4MThiZmIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWJlZGVjOWE0MGNlNGU5ODk4ZTVhZmZkZDFlMWQyOTUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfY2YyMjk4MTQ3NTdkNGYxMzg2NWQ0ZWJiZDcwZGQ5YWYgPSAkKCc8ZGl2IGlkPSJodG1sX2NmMjI5ODE0NzU3ZDRmMTM4NjVkNGViYmQ3MGRkOWFmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SaWNobW9uZCAvIEFkZWxhaWRlIC8gS2luZyBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzFiZWRlYzlhNDBjZTRlOTg5OGU1YWZmZGQxZTFkMjk1LnNldENvbnRlbnQoaHRtbF9jZjIyOTgxNDc1N2Q0ZjEzODY1ZDRlYmJkNzBkZDlhZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iN2RjMmMzOWQxNWQ0NTljOWVjOTkyMmI2NDQ1NjE1Ny5iaW5kUG9wdXAocG9wdXBfMWJlZGVjOWE0MGNlNGU5ODk4ZTVhZmZkZDFlMWQyOTUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzU4MGE5NmFiZjM3NDMwN2I1MmM5ZGVjYmViNjhlOTcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDA4MTU3LC03OS4zODE3NTIyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jYjdmZTkyYTkxZTk0YzJkYjI3NDgyOGY3MjgxOGJmYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ZDkyYjgzNmQ3MjY0NzViODM1YjI3MGI4NzkzMzliZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80MTAzOGU1NDJjMDU0YzFlYjc1ZTQwMTEzNjU4Yzc3OCA9ICQoJzxkaXYgaWQ9Imh0bWxfNDEwMzhlNTQyYzA1NGMxZWI3NWU0MDExMzY1OGM3NzgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhhcmJvdXJmcm9udCBFYXN0IC8gVW5pb24gU3RhdGlvbiAvIFRvcm9udG8gSXNsYW5kcyBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzdkOTJiODM2ZDcyNjQ3NWI4MzViMjcwYjg3OTMzOWJmLnNldENvbnRlbnQoaHRtbF80MTAzOGU1NDJjMDU0YzFlYjc1ZTQwMTEzNjU4Yzc3OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83NTgwYTk2YWJmMzc0MzA3YjUyYzlkZWNiZWI2OGU5Ny5iaW5kUG9wdXAocG9wdXBfN2Q5MmI4MzZkNzI2NDc1YjgzNWIyNzBiODc5MzM5YmYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTgyNGRmNTc0MzNhNGMwMDk3MjI3YjFlMTg3NGYzNTcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDcxNzY4LC03OS4zODE1NzY0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmZiMzYwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9jYjdmZTkyYTkxZTk0YzJkYjI3NDgyOGY3MjgxOGJmYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80ZjQ5ODFiZTBmOWU0YjE4YjEyZThhYzhkNTFkNDk5ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85MmFkNTc3NGZiYTQ0NjllYjEyZjI2OTMyODY5NzZjYyA9ICQoJzxkaXYgaWQ9Imh0bWxfOTJhZDU3NzRmYmE0NDY5ZWIxMmYyNjkzMjg2OTc2Y2MiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRvcm9udG8gRG9taW5pb24gQ2VudHJlIC8gRGVzaWduIEV4Y2hhbmdlIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNGY0OTgxYmUwZjllNGIxOGIxMmU4YWM4ZDUxZDQ5OWUuc2V0Q29udGVudChodG1sXzkyYWQ1Nzc0ZmJhNDQ2OWViMTJmMjY5MzI4Njk3NmNjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E4MjRkZjU3NDMzYTRjMDA5NzIyN2IxZTE4NzRmMzU3LmJpbmRQb3B1cChwb3B1cF80ZjQ5ODFiZTBmOWU0YjE4YjEyZThhYzhkNTFkNDk5ZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lZWYzMWIxMjU1OWY0ODJhYTcyMWRiYjBjYTc1ZGFhYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODE5ODUsLTc5LjM3OTgxNjkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2NiN2ZlOTJhOTFlOTRjMmRiMjc0ODI4ZjcyODE4YmZiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlhMmFjYjI0Y2JlNDQ0NDk4ZjhmNjlmZTI4YjJiYWE4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzdhNTE3YjRjMTM5YTRlMzg5Y2NhODU2MDg3MDM4YjBhID0gJCgnPGRpdiBpZD0iaHRtbF83YTUxN2I0YzEzOWE0ZTM4OWNjYTg1NjA4NzAzOGIwYSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q29tbWVyY2UgQ291cnQgLyBWaWN0b3JpYSBIb3RlbCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzlhMmFjYjI0Y2JlNDQ0NDk4ZjhmNjlmZTI4YjJiYWE4LnNldENvbnRlbnQoaHRtbF83YTUxN2I0YzEzOWE0ZTM4OWNjYTg1NjA4NzAzOGIwYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lZWYzMWIxMjU1OWY0ODJhYTcyMWRiYjBjYTc1ZGFhYy5iaW5kUG9wdXAocG9wdXBfOWEyYWNiMjRjYmU0NDQ0OThmOGY2OWZlMjhiMmJhYTgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjVmOWNhNzM1YmJjNDNlMmE1M2FlZDM1NmZkYmVmNmYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjI2OTU2LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2NiN2ZlOTJhOTFlOTRjMmRiMjc0ODI4ZjcyODE4YmZiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzg4OWIxNjFkMThiMzRiOThhNjQ0NjM4MDc5YzcyZTk2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzg0M2ExM2FkNDE1ZjQ0ODE4Y2YyNzEwZThmYzA2ZWZjID0gJCgnPGRpdiBpZD0iaHRtbF84NDNhMTNhZDQxNWY0NDgxOGNmMjcxMGU4ZmMwNmVmYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VW5pdmVyc2l0eSBvZiBUb3JvbnRvIC8gSGFyYm9yZCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg4OWIxNjFkMThiMzRiOThhNjQ0NjM4MDc5YzcyZTk2LnNldENvbnRlbnQoaHRtbF84NDNhMTNhZDQxNWY0NDgxOGNmMjcxMGU4ZmMwNmVmYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mNWY5Y2E3MzViYmM0M2UyYTUzYWVkMzU2ZmRiZWY2Zi5iaW5kUG9wdXAocG9wdXBfODg5YjE2MWQxOGIzNGI5OGE2NDQ2MzgwNzljNzJlOTYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMmZmZjg1NDJhNzNiNDZiZDgxNjY5YTZlODJhYjFiNzQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTMyMDU3LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2NiN2ZlOTJhOTFlOTRjMmRiMjc0ODI4ZjcyODE4YmZiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2FiZThmZGU4YmZlZjRmNTQ5OGFmNGNiNmIxOWUwYjAzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2FkMWRhZDE1MzhlNDQ5YWNhZmJhNTA2Y2Q1MDkzNTBiID0gJCgnPGRpdiBpZD0iaHRtbF9hZDFkYWQxNTM4ZTQ0OWFjYWZiYTUwNmNkNTA5MzUwYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S2Vuc2luZ3RvbiBNYXJrZXQgLyBDaGluYXRvd24gLyBHcmFuZ2UgUGFyayBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2FiZThmZGU4YmZlZjRmNTQ5OGFmNGNiNmIxOWUwYjAzLnNldENvbnRlbnQoaHRtbF9hZDFkYWQxNTM4ZTQ0OWFjYWZiYTUwNmNkNTA5MzUwYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yZmZmODU0MmE3M2I0NmJkODE2NjlhNmU4MmFiMWI3NC5iaW5kUG9wdXAocG9wdXBfYWJlOGZkZThiZmVmNGY1NDk4YWY0Y2I2YjE5ZTBiMDMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfN2Q0YjhiNzI3OGEwNGRlOGIyNGIwNThmYTQzNWM0ZTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg5NDY3LC03OS4zOTQ0MTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2NiN2ZlOTJhOTFlOTRjMmRiMjc0ODI4ZjcyODE4YmZiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2RjMTA1OGM4M2U4NjQ4OTNiNTQ1NjA0ZmRjN2Y3ODliID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZhMWFhMGE3M2Q2MjQ4OGNhN2E1NDZiYjM1M2FkODkxID0gJCgnPGRpdiBpZD0iaHRtbF9mYTFhYTBhNzNkNjI0ODhjYTdhNTQ2YmIzNTNhZDg5MSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IENsdXN0ZXIgMzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZGMxMDU4YzgzZTg2NDg5M2I1NDU2MDRmZGM3Zjc4OWIuc2V0Q29udGVudChodG1sX2ZhMWFhMGE3M2Q2MjQ4OGNhN2E1NDZiYjM1M2FkODkxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzdkNGI4YjcyNzhhMDRkZThiMjRiMDU4ZmE0MzVjNGU0LmJpbmRQb3B1cChwb3B1cF9kYzEwNThjODNlODY0ODkzYjU0NTYwNGZkYzdmNzg5Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mZTJiNTNlMjcwZDc0YzdmYmJlNzlhODE0ZmQzM2E3NyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3OTU2MjYsLTc5LjM3NzUyOTQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MDAwZmYiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODAwMGZmIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2NiN2ZlOTJhOTFlOTRjMmRiMjc0ODI4ZjcyODE4YmZiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzIzNzI5OWViMmY2MzQ1Mjg4NjM2OGYwZjg5NGVmYjllID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzdkMGNmMWMzZGZlYTQ0MWNiYzIzZGQzNTYwOGI2MjA5ID0gJCgnPGRpdiBpZD0iaHRtbF83ZDBjZjFjM2RmZWE0NDFjYmMyM2RkMzU2MDhiNjIwOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um9zZWRhbGUgQ2x1c3RlciAxPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yMzcyOTllYjJmNjM0NTI4ODYzNjhmMGY4OTRlZmI5ZS5zZXRDb250ZW50KGh0bWxfN2QwY2YxYzNkZmVhNDQxY2JjMjNkZDM1NjA4YjYyMDkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZmUyYjUzZTI3MGQ3NGM3ZmJiZTc5YTgxNGZkMzNhNzcuYmluZFBvcHVwKHBvcHVwXzIzNzI5OWViMmY2MzQ1Mjg4NjM2OGYwZjg5NGVmYjllKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2ZlNDQxYmYxNmU0MDQwN2E5ZjQzN2NkYTQzZTU0ZTA4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ2NDM1MiwtNzkuMzc0ODQ1OTk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfY2I3ZmU5MmE5MWU5NGMyZGIyNzQ4MjhmNzI4MThiZmIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTQxZTEwMjhhMDNmNGIyZGI1OTJjNzFkMDE4MDA4MzIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTVlZTU5MDlmNWNkNDdmNmEyOGNkZjY0NmEzYjBiNTAgPSAkKCc8ZGl2IGlkPSJodG1sX2U1ZWU1OTA5ZjVjZDQ3ZjZhMjhjZGY2NDZhM2IwYjUwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdG4gQSBQTyBCb3hlcyBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE0MWUxMDI4YTAzZjRiMmRiNTkyYzcxZDAxODAwODMyLnNldENvbnRlbnQoaHRtbF9lNWVlNTkwOWY1Y2Q0N2Y2YTI4Y2RmNjQ2YTNiMGI1MCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mZTQ0MWJmMTZlNDA0MDdhOWY0MzdjZGE0M2U1NGUwOC5iaW5kUG9wdXAocG9wdXBfMTQxZTEwMjhhMDNmNGIyZGI1OTJjNzFkMDE4MDA4MzIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODUzNzA1MjljZWFhNGMyMTgzZjk1MWYzZjYzMzBkMmEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njc5NjcsLTc5LjM2NzY3NTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfY2I3ZmU5MmE5MWU5NGMyZGIyNzQ4MjhmNzI4MThiZmIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTZhNjNlYWNiYzA4NGUzMzgyYjAwMjRlOTk0YWUxYWIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDQwNmFhZjMyYzA4NDc5ZTgzNGVkMjJkMzJlOGE1ZTIgPSAkKCc8ZGl2IGlkPSJodG1sX2Q0MDZhYWYzMmMwODQ3OWU4MzRlZDIyZDMyZThhNWUyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdC4gSmFtZXMgVG93biAvIENhYmJhZ2V0b3duIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTZhNjNlYWNiYzA4NGUzMzgyYjAwMjRlOTk0YWUxYWIuc2V0Q29udGVudChodG1sX2Q0MDZhYWYzMmMwODQ3OWU4MzRlZDIyZDMyZThhNWUyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzg1MzcwNTI5Y2VhYTRjMjE4M2Y5NTFmM2Y2MzMwZDJhLmJpbmRQb3B1cChwb3B1cF8xNmE2M2VhY2JjMDg0ZTMzODJiMDAyNGU5OTRhZTFhYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iYTE4MGRhMDlhOTA0ZDY5YjMxODk2YmNhNDc4OGYwOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODQyOTIsLTc5LjM4MjI4MDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfY2I3ZmU5MmE5MWU5NGMyZGIyNzQ4MjhmNzI4MThiZmIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjMwNzcxODJmMzQwNDcwM2FlMGE5YjgxMmU5YjY3OTUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTU5YTAwZjIwZTIzNDI4NDhjNTY5MTcxZWJlZDY1YWYgPSAkKCc8ZGl2IGlkPSJodG1sXzk1OWEwMGYyMGUyMzQyODQ4YzU2OTE3MWViZWQ2NWFmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5GaXJzdCBDYW5hZGlhbiBQbGFjZSAvIFVuZGVyZ3JvdW5kIGNpdHkgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yMzA3NzE4MmYzNDA0NzAzYWUwYTliODEyZTliNjc5NS5zZXRDb250ZW50KGh0bWxfOTU5YTAwZjIwZTIzNDI4NDhjNTY5MTcxZWJlZDY1YWYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYmExODBkYTA5YTkwNGQ2OWIzMTg5NmJjYTQ3ODhmMDkuYmluZFBvcHVwKHBvcHVwXzIzMDc3MTgyZjM0MDQ3MDNhZTBhOWI4MTJlOWI2Nzk1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU4YTY1MDA3YjRlZjQ2NmJiNDQyYTlkYmFjNDAxMjVlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY1ODU5OSwtNzkuMzgzMTU5OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfY2I3ZmU5MmE5MWU5NGMyZGIyNzQ4MjhmNzI4MThiZmIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZGMzNGRjOWM4MTI3NGVjNzhkODdjMWZmOGU5MzAxMjEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzRjOTYwMDk4OTI4NGJhOTkwNzE0NTM1NGJmNzViNDkgPSAkKCc8ZGl2IGlkPSJodG1sXzc0Yzk2MDA5ODkyODRiYTk5MDcxNDUzNTRiZjc1YjQ5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DaHVyY2ggYW5kIFdlbGxlc2xleSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2RjMzRkYzljODEyNzRlYzc4ZDg3YzFmZjhlOTMwMTIxLnNldENvbnRlbnQoaHRtbF83NGM5NjAwOTg5Mjg0YmE5OTA3MTQ1MzU0YmY3NWI0OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81OGE2NTAwN2I0ZWY0NjZiYjQ0MmE5ZGJhYzQwMTI1ZS5iaW5kUG9wdXAocG9wdXBfZGMzNGRjOWM4MTI3NGVjNzhkODdjMWZmOGU5MzAxMjEpOwoKICAgICAgICAgICAgCiAgICAgICAgCjwvc2NyaXB0Pg== onload="this.contentDocument.open();this.contentDocument.write(atob(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>




```python

```
