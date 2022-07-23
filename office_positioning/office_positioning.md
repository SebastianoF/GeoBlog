---
title: How well positioned is your office?
subtitle: Time to question your workplace location
slug: office-positioning
tags: learning, tutorial, data-science, geospatial
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1658607085481/7KbyixY8x.png?auto=compress
domain: geods.hashnode.dev
publishAs: SebastianoF 
ignorePost: true
---




Have you ever wondered if your office has the ideal location in respect to your house and your colleague’s houses? Imagine to discover that not only you, but also all your colleagues are located South of your office. How much time and money would all you and your colleague save if the office were to be relocated closer to where everyone lives?

In this blog post we explore some simple techniques from geospatial data science to investigate an instance of this problem and to visualise the situation on an interactive map. You can setup your own Jupyter Notebook and python environment, and copy paste the code, or directly clone the repository from https://github.com/SebastianoF/GeoBlog.

#### Content
1. Problem statement
2. Setup and requirements
3. Download and visualise the dataset
4. Narrow down the dataset to the city of London
5. Select the office location
6. Compute bearing and distance from the selected office to all the commuters residences
7. Visualise the results in a radar-histogram plot
8. Can we do better?


**Source** [https://github.com/SebastianoF/GeoBlog/tree/master/office_positioning](https://github.com/SebastianoF/GeoBlog/tree/master/office_positioning)

## 1. Problem statement

[Geospatial data science](https://geographicdata.science/book/intro.html) is the branch of data science transforming geospatial data into valuable insights through the application of the scientific method. In this tutorial we use its principles to find the answer to a very simple question:

> Is your company office optimally located in respect to the position of its employees?

Discovering that all the employees are located South in respect to the position of the office, would inform the decision of saving everyone time and money.

I will provide a methodology to answer this question in the specific case of the city of **London**.

You will be using the following components:

- A python interpreter with the required libraries pre-installed — virtualenv or conda environment.
- Kepler GL, for results visualisation and user queries
- The ukcommute dataset from Keper Gl website.
- osmnx to narrow down the dataset's offices located within the borders of London.
- A handful of Python functions to compute distances and angles on the surface of a sphere and to visualise some results.

## 2. Setup and requirements

There are several options to create a python 3.9 environment, the code below uses conda:

```python 
conda create -n geods python=3.9
conda activate geods
```

With the environment activated, install the requirements listed below:

```bash
# requirements.txt
geopandas==0.10.2
jupyter==1.0.0
keplergl==0.3.2
matplotlib==3.5.1
osmnx==1.1.2
pandas==1.4.2
seaborn==0.11.2
```

You can install each library individually with the pip command `pip install <copy paste each line here>`, or you can copy paste all the [requirements](https://note.nkmk.me/en/python-pip-install-requirements/) in a file in the root of your project `requirements.txt` and install them all in one go via:

```
pip install -r requirements.txt
```

Now all should be ready and set up to go!

## 3. Download and visualise the dataset

After importing the required library with…

```python
import contextily as cx
import geopandas as gpd
import matplotlib.pyplot as plt
import numpy as np
import osmnx
import pandas as pd

from keplergl import KeplerGl
from shapely.geometry import shape
```

...we can download the chosen dataset using pandas read_csv method:

```python
# About 10 seconds
df_commuter = pd.read_csv("https://github.com/uber-web/kepler.gl-data/raw/master/ukcommute/data.csv")
df_commuter.head()
```

![commuter_header](https://cdn.hashnode.com/res/hashnode/image/upload/v1658607781882/BRclpOr_4.png?auto=compress)

Throughout the tutorial we will be using the library [Kepler Gl](https://kepler.gl/), starting with a preliminary look at the data we just downloaded:

```python 
config = {
    'version': 'v1',
    'config': {
        'mapState': {
            'latitude': 51.536265,
            'longitude': -0.039740,
            'zoom': 10
        }
    }
}
map_1 = KeplerGl(data={'commuters': df_commuter}, config=config, height=800)

display(map_1)
```




## 4. Narrow down the dataset to the city of London
## 5. Select the office location
## 6. Compute bearing and distance from the selected office to all the commuters residences
## 7. Visualise the results in a radar-histogram plot
## 8. Can we do better?

