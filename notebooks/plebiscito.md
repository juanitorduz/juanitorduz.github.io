
# Web scraping: Getting referendum data using Beautiful Soup 

In this post I am going to describe how to get the data of the peace [referendum](https://en.wikipedia.org/wiki/Colombian_peace_agreement_referendum,_2016) (which happened in October the 2nd in Colombia)  from the [official government website](http://plebiscito.registraduria.gov.co/99PL/DPLZZZZZZZZZZZZZZZZZ_L1.htm) using [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#) in python (this a task was suggested by [Sebastian Martinez](https://co.linkedin.com/in/sebasti%C3%A1n-mart%C3%ADnez-00184649)). The data is not directly available but is represened as follows:

<img src="images/plebiscito_datos_image.png" alt="plebiscito_datos_image" style="width: 800px;"/>

The aim is to get the percentages of votes for each town in Colombia by scraping the website.

**Warning:** I want to emphasize that this post **does not** involve analizing the data or getting conclusions from it. These kind of analysis should be done carefully and responsibly.

## Step 1: Setting up the enviorment.


```python
# This is the principal website of the referendum. 
url_main="http://plebiscito.registraduria.gov.co"

# This is the complete url of the website of the referendum. 
url = "http://plebiscito.registraduria.gov.co/99PL/DPLZZZZZZZZZZZZZZZZZ_L1.htm"
```

The html code of *url* looks like:

<img src="images/html_image.png" alt="html" style="width: 800px;"/>


```python
from bs4 import BeautifulSoup
import requests

# This function gets the html of the website. 
def get_soup(s):
    r  = requests.get(s)
    data = r.text
    soup = BeautifulSoup(data, "lxml")
    return soup


soup = get_soup(url)
```

## Step 2: Getting the departments (states) information

Here we pick the information of each department as a list.
Each element of the list is of type bs4.element.Tag

*Remark:* You need to explore thr html code to find out that `find_all("option")[56:90]` actually gets the departments names.


```python
departments_soup = soup.find_all("option")[56:90]
```

We construct from the departments_soup a pandas DataFrame. 
It contains two columns: 
1. The name of the department. 
2. The path to each department result page (where detailed infromation is provided).  


```python
import pandas as pd

departments = pd.DataFrame(columns=("Department", "path"))

for i in range (0,len(departments_soup)):
    row=pd.Series(dict(zip(["Department", "path"],
                           [departments_soup[i].get_text(),
                            departments_soup[i]["value"]])))
    row.name = i
    departments = departments.append(row)

# Let us see the result.    
departments
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
      <th>Department</th>
      <th>path</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AMAZONAS</td>
      <td>../99PL/DPL60ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ANTIOQUIA</td>
      <td>../99PL/DPL01ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ARAUCA</td>
      <td>../99PL/DPL40ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ATLANTICO</td>
      <td>../99PL/DPL03ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BOGOTA D.C.</td>
      <td>../99PL/DPL16ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>5</th>
      <td>BOLIVAR</td>
      <td>../99PL/DPL05ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>6</th>
      <td>BOYACA</td>
      <td>../99PL/DPL07ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>7</th>
      <td>CALDAS</td>
      <td>../99PL/DPL09ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>8</th>
      <td>CAQUETA</td>
      <td>../99PL/DPL44ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>9</th>
      <td>CASANARE</td>
      <td>../99PL/DPL46ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>10</th>
      <td>CAUCA</td>
      <td>../99PL/DPL11ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>11</th>
      <td>CESAR</td>
      <td>../99PL/DPL12ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>12</th>
      <td>CHOCO</td>
      <td>../99PL/DPL17ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>13</th>
      <td>CONSULADOS</td>
      <td>../99PL/DPL88ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>14</th>
      <td>CORDOBA</td>
      <td>../99PL/DPL13ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>15</th>
      <td>CUNDINAMARCA</td>
      <td>../99PL/DPL15ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>16</th>
      <td>GUAINIA</td>
      <td>../99PL/DPL50ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>17</th>
      <td>GUAVIARE</td>
      <td>../99PL/DPL54ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>18</th>
      <td>HUILA</td>
      <td>../99PL/DPL19ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>19</th>
      <td>LA GUAJIRA</td>
      <td>../99PL/DPL48ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>20</th>
      <td>MAGDALENA</td>
      <td>../99PL/DPL21ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>21</th>
      <td>META</td>
      <td>../99PL/DPL52ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>22</th>
      <td>NARIÃO</td>
      <td>../99PL/DPL23ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>23</th>
      <td>NORTE DE SAN</td>
      <td>../99PL/DPL25ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>24</th>
      <td>PUTUMAYO</td>
      <td>../99PL/DPL64ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>25</th>
      <td>QUINDIO</td>
      <td>../99PL/DPL26ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>26</th>
      <td>RISARALDA</td>
      <td>../99PL/DPL24ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>27</th>
      <td>SAN ANDRES</td>
      <td>../99PL/DPL56ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>28</th>
      <td>SANTANDER</td>
      <td>../99PL/DPL27ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>29</th>
      <td>SUCRE</td>
      <td>../99PL/DPL28ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>30</th>
      <td>TOLIMA</td>
      <td>../99PL/DPL29ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>31</th>
      <td>VALLE</td>
      <td>../99PL/DPL31ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>32</th>
      <td>VAUPES</td>
      <td>../99PL/DPL68ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>33</th>
      <td>VICHADA</td>
      <td>../99PL/DPL72ZZZZZZZZZZZZZZZ_L1.htm</td>
    </tr>
  </tbody>
</table>
</div>



Looking into the data we see that there are two deparments (Nariño and Norte de Santander) with misspellings. 


```python
departments.loc[22,"Department"] = "NARIÑO"
departments.loc[23,"Department"] = 'NORTE DE SANTANDER'
```

From each department path we construct the real url and store it in a new column in the deparments DataFrame called "url".


```python
def construct_complete_url(path):
    complete_url = url_main + path.split("..")[1]
    return complete_url

departments["url"]=departments["path"].apply(lambda x: construct_complete_url(x))
```


```python
# Let us see the head of the resulting DataFrame.
departments.head()
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
      <th>Department</th>
      <th>path</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AMAZONAS</td>
      <td>../99PL/DPL60ZZZZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ANTIOQUIA</td>
      <td>../99PL/DPL01ZZZZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ARAUCA</td>
      <td>../99PL/DPL40ZZZZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ATLANTICO</td>
      <td>../99PL/DPL03ZZZZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BOGOTA D.C.</td>
      <td>../99PL/DPL16ZZZZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
    </tr>
  </tbody>
</table>
</div>



We create a new column in the deparments DataFrame called *soup*. This column stores a soup object obtained from their corresponding url.


```python
 departments["soup"]=departments["url"].apply(lambda x : get_soup(x))
```

## Step 3: Getting the  towns information

We make from the departments a pandas DataFrame of towns. It contains three columns: 
1. The name of the department. 
2. The name of the town. 
3. The path to each department result. 


```python
towns = pd.DataFrame(columns=("Department", "Town", "path"))

for j in range(0,departments.shape[0]):
    
    towns_in_department = departments["soup"][j].find_all("option")[36:]
    
    for i in range (0,len(towns_in_department)):
        dept = departments.loc[j,"Department"]
        town_name = towns_in_department[i].get_text()
        town_path = towns_in_department[i]["value"]
        row = pd.Series(dict(zip(["Department", "Town", "path"],[dept,town_name,town_path])))
        towns = towns.append(row, ignore_index=True)
```


```python
# Let us see the structure for the first 20 towns.
towns.head(20)
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
      <th>Department</th>
      <th>Town</th>
      <th>path</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AMAZONAS</td>
      <td>EL ENCANTO</td>
      <td>../99PL/DPL60010ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>1</th>
      <td>AMAZONAS</td>
      <td>LA CHORRERA</td>
      <td>../99PL/DPL60013ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>2</th>
      <td>AMAZONAS</td>
      <td>LA PEDRERA</td>
      <td>../99PL/DPL60016ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>3</th>
      <td>AMAZONAS</td>
      <td>LA VICTORIA</td>
      <td>../99PL/DPL60017ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>4</th>
      <td>AMAZONAS</td>
      <td>LETICIA</td>
      <td>../99PL/DPL60001ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>5</th>
      <td>AMAZONAS</td>
      <td>MIRITI PARANA</td>
      <td>../99PL/DPL60019ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>6</th>
      <td>AMAZONAS</td>
      <td>PUERTO ALEGRIA</td>
      <td>../99PL/DPL60030ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>7</th>
      <td>AMAZONAS</td>
      <td>PUERTO ARICA</td>
      <td>../99PL/DPL60040ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>8</th>
      <td>AMAZONAS</td>
      <td>PUERTO NARIÃO</td>
      <td>../99PL/DPL60007ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>9</th>
      <td>AMAZONAS</td>
      <td>PUERTO SANTANDER</td>
      <td>../99PL/DPL60021ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>10</th>
      <td>AMAZONAS</td>
      <td>TARAPACA</td>
      <td>../99PL/DPL60022ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>11</th>
      <td>ANTIOQUIA</td>
      <td>ABEJORRAL</td>
      <td>../99PL/DPL01004ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>12</th>
      <td>ANTIOQUIA</td>
      <td>ABRIAQUI</td>
      <td>../99PL/DPL01007ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>13</th>
      <td>ANTIOQUIA</td>
      <td>ALEJANDRIA</td>
      <td>../99PL/DPL01010ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>14</th>
      <td>ANTIOQUIA</td>
      <td>AMAGA</td>
      <td>../99PL/DPL01013ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>15</th>
      <td>ANTIOQUIA</td>
      <td>AMALFI</td>
      <td>../99PL/DPL01016ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>16</th>
      <td>ANTIOQUIA</td>
      <td>ANDES</td>
      <td>../99PL/DPL01019ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>17</th>
      <td>ANTIOQUIA</td>
      <td>ANGELOPOLIS</td>
      <td>../99PL/DPL01022ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>18</th>
      <td>ANTIOQUIA</td>
      <td>ANGOSTURA</td>
      <td>../99PL/DPL01025ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
    <tr>
      <th>19</th>
      <td>ANTIOQUIA</td>
      <td>ANORI</td>
      <td>../99PL/DPL01028ZZZZZZZZZZZZ_L1.htm</td>
    </tr>
  </tbody>
</table>
</div>



We create a new column in the towns DataFrame called *url*. This coumn stores the real url for each town. 


```python
towns["url"]=towns["path"].apply(lambda x: construct_complete_url(x))
```


```python
# Let us see the result.
towns.head()
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
      <th>Department</th>
      <th>Town</th>
      <th>path</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AMAZONAS</td>
      <td>EL ENCANTO</td>
      <td>../99PL/DPL60010ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>AMAZONAS</td>
      <td>LA CHORRERA</td>
      <td>../99PL/DPL60013ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>AMAZONAS</td>
      <td>LA PEDRERA</td>
      <td>../99PL/DPL60016ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>AMAZONAS</td>
      <td>LA VICTORIA</td>
      <td>../99PL/DPL60017ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>AMAZONAS</td>
      <td>LETICIA</td>
      <td>../99PL/DPL60001ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
    </tr>
  </tbody>
</table>
</div>



## Step 4: Get votes 

Now that we have access to each town website results, we are going to get the percentage of yes, no and null votes.


```python
# This function transform the vote string of the function below 
# in order to get an integer. 

# To understan the logic of this function you need to understand 
# the structure of the html code.

def get_vote_int_from_string(vote_string, is_null_votes):
    
    if(is_null_votes):
        num_v = vote_string.split("Votos nulos")[1]
    else:
        num_v = vote_string.split("(")[1].split( )[0]
    
    num = "".join(num_v.split(".")) 
    return int(num)
    
# This function gets the votation information.
def get_votes(u):
    s=get_soup(u)
    
    yes = s.find_all("div", class_="skill-bar-percent")[1].get_text()
    no = s.find_all("div", class_="skill-bar-percent")[3].get_text()
    null = s.find_all("div", class_="cajaInfTercera")[0].find_all("div", class_="contenido")[0].get_text()

    votes = (get_vote_int_from_string(yes, False), 
             get_vote_int_from_string(no, False),
             get_vote_int_from_string(null, True))
    
    return votes
```

We create a new column in the towns DataFrame called *votes* which stores the information in a tuple.


```python
towns["votes"]=towns["url"].apply(lambda x : get_votes(x))
```


```python
towns.head()
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
      <th>Department</th>
      <th>Town</th>
      <th>path</th>
      <th>url</th>
      <th>votes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AMAZONAS</td>
      <td>EL ENCANTO</td>
      <td>../99PL/DPL60010ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
      <td>(170, 51, 2)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>AMAZONAS</td>
      <td>LA CHORRERA</td>
      <td>../99PL/DPL60013ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
      <td>(300, 62, 3)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>AMAZONAS</td>
      <td>LA PEDRERA</td>
      <td>../99PL/DPL60016ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
      <td>(189, 25, 1)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>AMAZONAS</td>
      <td>LA VICTORIA</td>
      <td>../99PL/DPL60017ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
      <td>(4, 1, 0)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>AMAZONAS</td>
      <td>LETICIA</td>
      <td>../99PL/DPL60001ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
      <td>(4403, 4463, 84)</td>
    </tr>
  </tbody>
</table>
</div>



Next we get the yes, no and null votes separately. We also compute the total of votes.


```python
# We get YES. 
towns["YES"] = towns["votes"].apply(lambda x : x[0])

# We get NO. 
towns["NO"] = towns["votes"].apply(lambda x : x[1])

# We get NULL. 
towns["NULL"] = towns["votes"].apply(lambda x : x[2])

# We comute the total (sum). 
towns["TOTAL"] = towns["votes"].apply(lambda x : sum(x))
```


```python
# Let us see the final strcuture. 
towns.head()
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
      <th>Department</th>
      <th>Town</th>
      <th>path</th>
      <th>url</th>
      <th>votes</th>
      <th>YES</th>
      <th>NO</th>
      <th>NULL</th>
      <th>TOTAL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AMAZONAS</td>
      <td>EL ENCANTO</td>
      <td>../99PL/DPL60010ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
      <td>(170, 51, 2)</td>
      <td>170</td>
      <td>51</td>
      <td>2</td>
      <td>223</td>
    </tr>
    <tr>
      <th>1</th>
      <td>AMAZONAS</td>
      <td>LA CHORRERA</td>
      <td>../99PL/DPL60013ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
      <td>(300, 62, 3)</td>
      <td>300</td>
      <td>62</td>
      <td>3</td>
      <td>365</td>
    </tr>
    <tr>
      <th>2</th>
      <td>AMAZONAS</td>
      <td>LA PEDRERA</td>
      <td>../99PL/DPL60016ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
      <td>(189, 25, 1)</td>
      <td>189</td>
      <td>25</td>
      <td>1</td>
      <td>215</td>
    </tr>
    <tr>
      <th>3</th>
      <td>AMAZONAS</td>
      <td>LA VICTORIA</td>
      <td>../99PL/DPL60017ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
      <td>(4, 1, 0)</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>AMAZONAS</td>
      <td>LETICIA</td>
      <td>../99PL/DPL60001ZZZZZZZZZZZZ_L1.htm</td>
      <td>http://plebiscito.registraduria.gov.co/99PL/DP...</td>
      <td>(4403, 4463, 84)</td>
      <td>4403</td>
      <td>4463</td>
      <td>84</td>
      <td>8950</td>
    </tr>
  </tbody>
</table>
</div>



# Step 5: Export the data into a csv file

We export the towns DataFrame into a .csv file with all the data. 


```python
columns_to_print = ["Department", "Town", "YES", "NO", "NULL", "TOTAL"]

towns[columns_to_print].to_csv("resultados.csv", index=False)
```

# Step 6: Data Exploration

Let us see how to begin a simple exploration of the data.


```python
data = pd.read_csv("resultados.csv", index_col="Department")

data.head()
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
      <th>Town</th>
      <th>YES</th>
      <th>NO</th>
      <th>NULL</th>
      <th>TOTAL</th>
    </tr>
    <tr>
      <th>Department</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>AMAZONAS</th>
      <td>EL ENCANTO</td>
      <td>170</td>
      <td>51</td>
      <td>2</td>
      <td>223</td>
    </tr>
    <tr>
      <th>AMAZONAS</th>
      <td>LA CHORRERA</td>
      <td>300</td>
      <td>62</td>
      <td>3</td>
      <td>365</td>
    </tr>
    <tr>
      <th>AMAZONAS</th>
      <td>LA PEDRERA</td>
      <td>189</td>
      <td>25</td>
      <td>1</td>
      <td>215</td>
    </tr>
    <tr>
      <th>AMAZONAS</th>
      <td>LA VICTORIA</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>5</td>
    </tr>
    <tr>
      <th>AMAZONAS</th>
      <td>LETICIA</td>
      <td>4403</td>
      <td>4463</td>
      <td>84</td>
      <td>8950</td>
    </tr>
  </tbody>
</table>
</div>



In order to get information from each department we group the results by adding the total votes for each category.


```python
results_departments = data.groupby(data.index).sum()
```

Now we compute basic ratios of the results.


```python
results_departments["YES/TOTAL"] = results_departments["YES"]/results_departments["TOTAL"]
results_departments["NO/TOTAL"] = results_departments["NO"]/results_departments["TOTAL"]

results_departments.head()
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
      <th>YES</th>
      <th>NO</th>
      <th>NULL</th>
      <th>TOTAL</th>
      <th>YES/TOTAL</th>
      <th>NO/TOTAL</th>
    </tr>
    <tr>
      <th>Department</th>
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
      <th>AMAZONAS</th>
      <td>6524</td>
      <td>4994</td>
      <td>114</td>
      <td>11632</td>
      <td>0.560867</td>
      <td>0.429333</td>
    </tr>
    <tr>
      <th>ANTIOQUIA</th>
      <td>648051</td>
      <td>1057518</td>
      <td>22416</td>
      <td>1727985</td>
      <td>0.375033</td>
      <td>0.611995</td>
    </tr>
    <tr>
      <th>ARAUCA</th>
      <td>28653</td>
      <td>30274</td>
      <td>1523</td>
      <td>60450</td>
      <td>0.473995</td>
      <td>0.500811</td>
    </tr>
    <tr>
      <th>ATLANTICO</th>
      <td>258121</td>
      <td>168300</td>
      <td>2132</td>
      <td>428553</td>
      <td>0.602308</td>
      <td>0.392717</td>
    </tr>
    <tr>
      <th>BOGOTA D.C.</th>
      <td>1423612</td>
      <td>1114933</td>
      <td>25213</td>
      <td>2563758</td>
      <td>0.555283</td>
      <td>0.434882</td>
    </tr>
  </tbody>
</table>
</div>



### Visualization

Here we plot the `YES/TOTAL` ratio for all the departments.



```python
import matplotlib as plt
import seaborn as sns
%matplotlib inline

results_departments.plot(y="YES/TOTAL",
                         kind='bar',
                         stacked=True,
                         title="YES/TOTAL per Department",
                         ylim=(0,1.0),
                         figsize=(12,12))
plt.pyplot.xticks(rotation=90)
```




    (array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16,
            17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33]),
     <a list of 34 Text xticklabel objects>)




![png](plebiscito_files/plebiscito_40_1.png)


Here we plot the `TOTAL` votation for all the departments.


```python
results_departments.plot(y="TOTAL", 
                         kind='bar',
                         title="Total Votes per Department",
                         figsize=(12,12))
plt.pyplot.xticks(rotation=90)
```




    (array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16,
            17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33]),
     <a list of 34 Text xticklabel objects>)




![png](plebiscito_files/plebiscito_42_1.png)


Now we want to get information within each department.


```python
results_towns = data.copy()

results_towns["YES/TOTAL"]=results_towns["YES"]/results_towns["TOTAL"]
results_towns["NO/TOTAL"]=results_towns["NO"]/results_towns["TOTAL"]

results_towns.head()
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
      <th>Town</th>
      <th>YES</th>
      <th>NO</th>
      <th>NULL</th>
      <th>TOTAL</th>
      <th>YES/TOTAL</th>
      <th>NO/TOTAL</th>
    </tr>
    <tr>
      <th>Department</th>
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
      <th>AMAZONAS</th>
      <td>EL ENCANTO</td>
      <td>170</td>
      <td>51</td>
      <td>2</td>
      <td>223</td>
      <td>0.762332</td>
      <td>0.228700</td>
    </tr>
    <tr>
      <th>AMAZONAS</th>
      <td>LA CHORRERA</td>
      <td>300</td>
      <td>62</td>
      <td>3</td>
      <td>365</td>
      <td>0.821918</td>
      <td>0.169863</td>
    </tr>
    <tr>
      <th>AMAZONAS</th>
      <td>LA PEDRERA</td>
      <td>189</td>
      <td>25</td>
      <td>1</td>
      <td>215</td>
      <td>0.879070</td>
      <td>0.116279</td>
    </tr>
    <tr>
      <th>AMAZONAS</th>
      <td>LA VICTORIA</td>
      <td>4</td>
      <td>1</td>
      <td>0</td>
      <td>5</td>
      <td>0.800000</td>
      <td>0.200000</td>
    </tr>
    <tr>
      <th>AMAZONAS</th>
      <td>LETICIA</td>
      <td>4403</td>
      <td>4463</td>
      <td>84</td>
      <td>8950</td>
      <td>0.491955</td>
      <td>0.498659</td>
    </tr>
  </tbody>
</table>
</div>



Here we plot the `YES/TOTAL` ratio for the deparment `CHOCO`.


```python
results_towns[results_towns.index=="CHOCO"].plot(x="Town", y="YES/TOTAL",
                                                  kind='bar', 
                                                  title="YES/TOTAL for CHOCO",
                                                  figsize=(12,12),
                                                  ylim=(0,1.0))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x11284bf28>




![png](plebiscito_files/plebiscito_46_1.png)


Here we plot the `YES/TOTAL` ratio for the deparment `CHOCO`.


```python
results_towns[results_towns.index=="CALDAS"].plot(x="Town", y="YES/TOTAL",
                                                  kind='bar', 
                                                  title="YES/TOTAL for CALDAS",
                                                  figsize=(12,12),
                                                  ylim=(0,1.0))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x112b87668>




![png](plebiscito_files/plebiscito_48_1.png)


As mentioned in the introduction, now that the data is available it is necessary to make a detailed and structured study of these results. For example, mapping these results to education level, precense of the guerrilla, etc. Of course, this is something which, for now, is beyond the scope of this post. 
