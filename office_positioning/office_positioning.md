---
title: How well positioned is your office?
subtitle: Time to question your workplace location
slug: office-positioning
tags: learning, tutorial, data-science, geospatial
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1658607085481/7KbyixY8x.png?auto=compress
domain: geods.hashnode.dev
publishAs: SebastianoF 
ignorePost: false
---


*This article was first published: 24 April 2022 on [medium](https://medium.com/@sebastianof/how-well-positioned-is-your-office-8517256c497e). The version here provided on [geods.hashnode.dev](geods.hashnode.dev) is the maintained one.*


Have you ever wondered if your office has the ideal location in respect to your house and your colleague's houses? Imagine to discover that not only you, but also all your colleagues are located South of your office. How much time and money would all you and your colleague save if the office were to be relocated closer to where everyone lives?

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

![header0-commuter](https://cdn.hashnode.com/res/hashnode/image/upload/v1658607781882/BRclpOr_4.png?auto=compress)

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

![map1-kepler-commuters-point](https://cdn.hashnode.com/res/hashnode/image/upload/v1658608477791/8S1Pj8wmt.png?auto=compress)

At this point, you are invited to **create the map on your own Jupyter notebook**, to look into Kepler GL. This tool is developed by Uber, and if you have not used it before it is worth spending some time playing around with layers, colours and filters, and changing the map tiles (dark, light, satellite, etc…).

Kepler automatically recognises and formats the input DataFrame into independent layers, whose colour and features can be toggled and modified in the sidebar. The input DataFrame is passed as a value of the input dictionary with key `commuters`.

For each row the dataset has two sets of points, `workplace` and `residence`, and each edge between two points on the same row is weighted by the integer column `all_flows`, that we can interpret as the number of times the commuter walked through the path over the period of the data collection. Note that this dataset has no timestamp, and from the dataset alone we can not tell the period the data was collected for.

Also, by observing the precision in the locations for multiple travels, and by the fact that **there are workplaces in impossible locations**, such as the centre of Richmond Park, we may suspect this is not real data (browse around the dataset to find more). Or if it is real data, some heavy pre-processing have happened beforehand, such as outlier detection, complete case analysis, and clustering or averaging of sets of nearby locations.

#### A note on Kepler settings

After changing the settings in the Kepler map, you can export them as a json `config` file with `map_1.config`, and re-import it when creating a new map. To limit the length of this post we have omitted the config used to re-create the images appearing across the post, but you can retrieve them from the `config` folder on the linked repo at:

[https://github.com/SebastianoF/GeoBlog/tree/master/office_positioning](https://github.com/SebastianoF/GeoBlog/tree/master/office_positioning)

If you are not following the code from the repo, or the configs from the `kepler_config.py` module are not imported, then the cells below will be loading the previous config file as its default.


## 4. Narrow down the dataset to the city of London

Early stage explorations indicate that the dataset covers the whole of England and Wales. Also toggling the layers, or making the workplace radii smaller and selecting a different colorscale than the residences, we can see that in many cases some **some workplaces are coincident with the residences**. For the moment, and for the goals of this post, this problem is of no concern.

As what we want to do is to assess the **optimality of a location within the boundaries of London**, the first thing to do is to download the city boundaries and then reduce the dataset to the offices located within these boundaries.

The geometry of a boundary can be encoded as a shapely polygon, and embedded into a *geopandas dataframe*: a pandas dataframe equipped with a column named `geometry` containing a shapely object. We can download the polygon from *Open Street Map**, the open source initiative providing the users with a free editable geographic database of the world.

Fear not, you are not asked to open a new tab on the browser and download the maps from the osm website! The maps can be downloaded programmatically via the very handy `osmnx` python library, and via its method `osmnx.geocode_to_gdf`.

Note that we will query both **London** and the **City of London** geometries, in order to filter the workplace points in our dataset appearing in the union of these two regions.

```python

osmnx.config(use_cache=True, log_console=True)

def gdf_concat(list_gdf: list):
    return gpd.GeoDataFrame( pd.concat(list_gdf, ignore_index=True)) 

query_city = {'city': 'City of London'}
query_london = {'city': 'London'}

gdf = gdf_concat([osmnx.geocode_to_gdf(query_city), osmnx.geocode_to_gdf(query_london)])

gdf.head()

```

![header1-osmnx](https://cdn.hashnode.com/res/hashnode/image/upload/v1658611137244/bjWPhPDz7.png?auto=compress)

Before continuing, it would be good to visualise the polygons we just downloaded. There are multiple ways to do this. For example we can use *contextility* as a basemap and use the embedded method plot of a geopandas dataframe.

```python

gdf_epsg = gdf.to_crs(epsg=3857)
ax = gdf_epsg.plot(figsize=(10, 10), alpha=0.5, edgecolor='k')
cx.add_basemap(ax)

```

![map2-static-contextility-example](https://cdn.hashnode.com/res/hashnode/image/upload/v1658648427079/gpCcunHCA.png?auto=compress)

We can also have a navigable map to visualise the same dataset, again using Kepler GL.

```python

try:
    from kepler_config import config_map_2
except ImportError:
    config_map_2 = config

map_2 = KeplerGl(data={'london' :gdf_epsg}, config=config_map_2, height=800)  # kepler knows what to do when fed with a geodataframe
display(map_2)

```

![map2-kepler-city-outline](https://cdn.hashnode.com/res/hashnode/image/upload/v1658649134288/PQ43Ou1vw.png?auto=compress)

To narrow the commuter dataset to only the offices within the boundary of London, first we cast `df_commuter` into a geopandas DataFrame with the geometry column having for each entry the shapely point of the workplace coordinate.

Then we use this new geodataframe to obtain the mask of the point within the boundaries, and finally we apply the mask to the initial `df_commuter` dataset. Since we are here, we also take a look at the percentage of the offices that are in London over the full dataset (you are of course invited to look into other statistics).

```python
# -- about 17 seconds --
gdf_commuters_workplace = gpd.GeoDataFrame(df_commuter.copy(), geometry=gpd.points_from_xy(df_commuter.workplace_lng, df_commuter.workplace_lat))

# -- about 120 seconds: points in polygon 
mask_points_in_city = gdf_commuters_workplace.intersects(gdf.geometry.iloc[0])
mask_points_in_london = gdf_commuters_workplace.intersects(gdf.geometry.iloc[1])
num_total_rows = len(gdf_commuters_workplace)
num_rows_in_city = len(mask_points_in_city[mask_points_in_city == True])
num_rows_in_london = len(mask_points_in_london[mask_points_in_london == True])
print(f"Number of rows for offices in the city {num_rows_in_city} ({100 * num_rows_in_city / num_total_rows} %)")
print(f"Number of rows for offices in london {num_rows_in_london} ({100 * num_rows_in_london / num_total_rows} %)")

mask_union = mask_points_in_city | mask_points_in_london
num_rows_in_union = mask_union.sum()
print(f"Number of offices in the union of London and the City {num_rows_in_union} ({100 * num_rows_in_union / num_total_rows} %)")

# Sanity check
assert num_rows_in_union == num_rows_in_city + num_rows_in_london

df_commuter_london_office = df_commuter[mask_union]
df_commuter_london_office.reset_index(inplace=True, drop=True)
try:
    from kepler_config import config_map_3
except ImportError:
    config_map_3 = config

# Use the config_3 in kepler_config.py in the repo to reproduce the same image
map_3 = KeplerGl(data={'london':gdf_epsg.copy(),  "commuters": df_commuter_london_office.copy()}, config=config_map_3, height=800)
display(map_3)
```

**NOTE:** here, and from here downwards, we will not show the output of the print statements. Try out the code!

![map3-london-trajectories](https://cdn.hashnode.com/res/hashnode/image/upload/v1658649255230/DhFiblx_X.png?auto=compress)

As noted before, many of the workplaces and residences have the same coordinates and are overlapping on the map. Also in the dataset reduced to the boundaries of London residences and offices are overlapping, but outside the boundary, as expected, there are only residences.

## 5. Select the office location

Now the goal is to select a single office of interest (or a group of nearby offices), and to examine its location in respect to the location of the employees. The functionality of drawing polygons provided by Kepler Gl comes in handy for this purpose.

On the top right menu, selecting the "draw on map" button we can narrow down a region and copy its geometry to clipboard right-clicking on the completed polygon. We can then paste the geometry in the cell of our Jupyter notebook.

The geometry, encoded in a json, can be then parsed into a **shapely polygon**.

As an example we copy pasted two polygons in the cell below (you are invited to continue the tutorial with your own regions though!). The first one encompasses an office in St Luke’s Close, the closest office to the silicon roundabout, Old Street, and the second one in Albert Road, just between the London city airport and the river Thames.


```python

polygon_st_luke_office = {
    "type": "Polygon",
    "coordinates": [
        [
            [-0.0930210043528368, 51.52553386809767],
            [-0.09362754938510826, 51.5257442611004],
            [-0.09398505401347826, 51.52546150215205],
            [-0.09363181940230854, 51.525218817282784],
            [-0.09313761642997592, 51.52527679524477],
            [-0.0930210043528368, 51.52553386809767],
        ]
    ],
}

polygon_albert_road = {
    "type": "Polygon",
    "coordinates": [
        [
            [0.05074120549614755, 51.503014231092195],
            [0.04882522609357891, 51.50189434877025],
            [0.051410997081145014, 51.49996117091324],
            [0.05337913172491038, 51.501678115383754],
            [0.05074120549614755, 51.503014231092195],
        ]
    ],
}

```

Next we narrow the office locations to the selected geometry, embed the geometry in a geopandas DataFrame, and feed the result to a Kepler map to visualise where we are at.

```python 

# -- St Luke office
# narrow dataset to the geometry
mask_st_luke_office = gdf_commuters_workplace.intersects(shape(polygon_st_luke_office))
df_commuters_st_luke_office = df_commuter[mask_st_luke_office]

# embed shape into a geopandas to visualise in kepler
gdf_st_luke_geometry = gpd.GeoDataFrame({'geometry':[shape(polygon_st_luke_office)], "display_name": ["St Luke's Close Office"]})

# -- Same for Albert Road office
mask_albert_road = gdf_commuters_workplace.intersects(shape(polygon_albert_road))
df_commuters_albert_road = df_commuter[mask_albert_road]
gdf_albert_road = gpd.GeoDataFrame({'geometry':[shape(polygon_albert_road)], "display_name": ["St Luke's Close Office"]})

#  --- Visualisation ---

try:
    from kepler_config import config_map_4
except ImportError:
    config_map_4 = config

map_4 = KeplerGl(
    data={
        "St Luke's Close Office": gdf_st_luke_geometry.copy(),  
        "commuters to St Luke": df_commuters_st_luke_office.copy(),
        "Albert Road Office": gdf_albert_road.copy(),
        "commuters to Albert": df_commuters_albert_road.copy(),
    }, 
    config=config_map_4, 
    height=800
)
display(map_4)

print(f"Commuters to St Luke office {len(df_commuters_st_luke_office)} ({100 * len(df_commuters_st_luke_office) / len(df_commuter)} %)" )
print(f"Commuters to Albert Road office {len(df_commuters_albert_road)} ({100 *  len(df_commuters_albert_road) / len(df_commuter)} %)")

```

![map4_office_trajectories](https://cdn.hashnode.com/res/hashnode/image/upload/v1658652547469/a-PDPRcnT.png?auto=compress)


## 6. Compute bearing and distance from the selected office to all the commuters residences

Now that we have the dataset of commuters to two custom selected offices in London, we want to compute the bearing and distance from the office to each employees residence.

There are multiple ways to achieve this result, such as using one of the many python libraries, as haversine, geopy, geographiclib... As our goal is to learn some tools from geodata science, we will try to implement the formulae from scratch.

Given two points (lng1, lat1) and (lng2, lat2), the Haversine formula (geodesic distance on the sphere) is given by:

$$
\mathcal{H} = 2 * R * \arcsin\left(\sqrt{d}~\right)~,
$$

where

$$
d = \sin^2 \left(\frac{\Delta \text{lat}}{2} \right) + \cos(\text{lat1}) \cos(\text{lat2})  \sin^2\left(\frac{\Delta \text{lon}}{2}\right)~,
$$

and $\Delta \text{lat} = \text{lat1} - \text{lat2}$, $\Delta \text{lon} = \text{lon1} - \text{lon2}$, and $$R$$ is the hearth's radius.

The formula for the bearing, as the angle formed by the geodesics between the north pole and (lng1, lat1), and the geodesic between (lng1, lat1) and (lng2, lat2) is, in radiants:

$$
\mathcal{B} = \arctan\left( 
    \frac{
        \sin(\Delta \text{lon}) \cos(\text{lat2}) 
    }{ 
        \cos(\text{lat1}) \sin(\text{lat2}) - \sin(\text{lat1}) \cos(\text{lat2}) \cos\left( \Delta \text{lon} \right)
    } 
\right)
$$

Both formulae are based on the spherical model, which is a reasonable approximation, though in geospatial data science the standard model is the ellipsoid model **World Geodesics System 1984 (WGS84)**, where the distance between the centre and the equator is higher than the distance between the centre and the poles by about 31 Km.

Exposing the reason why these formulae above are correct, and generalising them for the WGS84 model (the Vincenty's formulae), would entail expanding the blog post beyond reason. This topic is therefore left as future work, or to the reader.

```python
from typing import Tuple

from math import radians

def haversine(lng1: float, lat1: float, lng2: float, lat2: float) -> Tuple[float, float]:
    """ returns (haversine distance in km, bearing in degrees from point 1 to point 2), vectorised """

    avg_earth_radius_km = 6371.0072
   
    lng1, lat1, lng2, lat2 = map(np.deg2rad, [lng1, lat1, lng2, lat2])
    d_lat, d_lng = lat2 - lat1, lng2 - lng1
    d = np.sin((d_lat)/2)**2 + np.cos(lat1)*np.cos(lat2) * np.sin((d_lng)/2)**2
    hav_dist = 2 * avg_earth_radius_km * np.arcsin(np.sqrt(d))
   
    y = np.sin(d_lng) * np.cos(lat2)
    x = np.cos(lat1) * np.sin(lat2) - np.sin(lat1) * np.cos(lat2) * np.cos(d_lng)
    bearing = (np.arctan2(y, x) + 2 * np.pi) % (2 * np.pi)
    
    return hav_dist, np.rad2deg(bearing)

def add_bearing_deg_and_distance_km(df: pd.DataFrame) -> pd.DataFrame:
    """bearing between A and B is the angle between the geodesics connecting A and the north pole, and the geodesics connecting A and B.
    Both the bearing and distance are computed on the Spherical model.
    """
    df = df.copy()
    
    lng_work, lat_work = df.workplace_lng.to_numpy(), df.workplace_lat.to_numpy()
    lng_home, lat_home = df.residence_lng.to_numpy(), df.residence_lat.to_numpy()
    
    df["distance_km"], df["bearing_deg"] = haversine(lng_work, lat_work, lng_home, lat_home)
    
    return df


df_commuters_st_luke_office = add_bearing_deg_and_distance_km(df_commuters_st_luke_office)
df_commuters_albert_road = add_bearing_deg_and_distance_km(df_commuters_albert_road)
df_commuters_st_luke_office.head()
```

## 7. Visualise the results in a radar-histogram plot

For distance and bearing visualisation, a circular histogram would do what we need.

We can group the dataset into three categories, based on the radial distance from the office:

- Homes within a radius of 10 Km
- Homes between 10Km and 20 Km
- Homes above 20 Km

Then we compute the histograms in **polar coordinates**. As we have to do it for two different dataset, instead of repeating the same code twice, we embed the transformation and visualisation in a single function.

Within the same function there is some repeated code for each radius. It is left to you to embed the repeated code in a `for` cycle for the `inner`, `between` and `outer` histogram, and to generalise it to any number of radii and distances.

As a further exercise, separating the data pre-processing and the visualisation part in two different function would be better practice than keeping both steps in the same function.


```python

import seaborn as sns

sns.set_style("darkgrid", {"grid.color": ".6", "grid.linestyle": ":"})


def radar_histogram(ax, df):
    """
    Input: 
        df with at least 2 columns distance_km and bearing_deg.
    Output: radar histogram plot.
    """
    # Figures parameter
    directions = 40
    
    bottom = 4
    height_scale = 8
    
    # bearing: degrees from nort pole clockwise
    bearing_bins = np.linspace(0, 360, directions+1, endpoint=False)
    # angle visualisation: rad from east counterclockwise
    theta = - 1 * np.linspace(0, 2 * np.pi, directions, endpoint=False) + np.pi/2
    width = (2*np.pi) / directions
    
    # data binning
    se_bins = pd.cut(df["bearing_deg"].to_numpy(), bearing_bins)
    np_bins = se_bins.value_counts().to_numpy()
    bins =  height_scale * np.array(np_bins) / np.max(np_bins)
    
    # Uncomment to debug figure:
    # bins = range(directions)
    
    # plotting    
    ax_bars = ax.bar(theta, bins, width=width, bottom=bottom, color="blue")

    ax.set_yticklabels([])
    ax.set_xticks(np.linspace(0, 2 * np.pi, 8, endpoint=False))
    ax.set_xticklabels(['E', '', 'N', '', 'W', '', 'S', ''])
    ax.grid(False)

    return ax

def radar_histogram_3_levels(ax, df):
    """
    Input: 
        df with at least 2 columns distance_km and bearing_deg.
    Output: radar histogram plot.
    """
    # Figures parameter
    directions = 40
    height_scale = 2

    bottom_inner = 2
    bottom_betw = 5
    bottom_outer = 8
    
    # bearing: degrees from nort pole clockwise
    bearing_bins = np.linspace(0, 360, directions+1, endpoint=False)
    # angle visualisation: rad from east counterclockwise
    theta = - 1 * np.linspace(0, 2 * np.pi, directions, endpoint=False) + np.pi/2
    width = (2*np.pi) / directions
    
    # data binning
    
    df_inner = df[df["distance_km"] <= 10]
    se_bins_inner = pd.cut(df_inner["bearing_deg"].to_numpy(), bearing_bins)
    np_bins_inner = se_bins_inner.value_counts().to_numpy()
    bins_inner =  height_scale * np.array(np_bins_inner) / np.max(np_bins_inner)
    
    df_betw = df[(df["distance_km"] > 10) & (df["distance_km"] <= 20)]
    se_bins_betw = pd.cut(df_betw["bearing_deg"].to_numpy(), bearing_bins)
    np_bins_betw = se_bins_betw.value_counts().to_numpy()
    bins_betw =  height_scale * np.array(np_bins_betw) / np.max(np_bins_betw)
    
    df_outer = df[df["distance_km"] > 20]
    se_bins_outer = pd.cut(df_outer["bearing_deg"].to_numpy(), bearing_bins)
    np_bins_outer = se_bins_outer.value_counts().to_numpy()
    bins_outer =  height_scale * np.array(np_bins_outer) / np.max(np_bins_outer)
    
    # plotting
    
    ax_bars_inner = ax.bar(theta, bins_inner, width=width, bottom=bottom_inner, color="blue")
    ax_bars_betw = ax.bar(theta, bins_betw, width=width, bottom=bottom_betw, color="blue")
    ax_bars_outer = ax.bar(theta, bins_outer, width=width, bottom=bottom_outer, color="blue")

    
    ax.set_yticklabels([])
    # uncomment to add values on radius axis
    # ax.set_yticks(np.arange(0,10,1.0))
    # ax.set_yticklabels(['', '', '', '<=10Km ', '', '', '>10Km\n <=20Km', '', '', '>20Km'])

    ax.set_xticks(np.linspace(0, 2 * np.pi, 8, endpoint=False))
    ax.set_xticklabels(['E', '', 'N', '', 'W', '', 'S', ''])
    ax.grid(False)

    return ax


fig = plt.figure(figsize=(12, 12))

ax11 = fig.add_subplot(221, polar=True)
ax11 = radar_histogram(ax11, df_commuters_st_luke_office)
ax11.set_title("Saint Luke Office", y=1.08, x=1.1)

ax12 = fig.add_subplot(222, polar=True)
ax12 = radar_histogram_3_levels(ax12, df_commuters_st_luke_office)

ax21 = fig.add_subplot(223, polar=True)
ax21 = radar_histogram(ax21, df_commuters_albert_road)
ax21.set_title("Albert Road Office", y=1.08, x=1.1)

ax22 = fig.add_subplot(224, polar=True)
ax22 = radar_histogram_3_levels(ax22, df_commuters_albert_road)

plt.plot()

```

From the graphs below we can see that the office in Saint Luke is reasonably well balanced across the location of the employees, both overall, and splitting the commuters into three categories based on radial distance.

<figure>
    <img src="https://cdn.hashnode.com/res/hashnode/image/upload/v1658657464208/cpH_OE7PD.png?auto=compress" alt="historgrams" style="width:100%">
    <figcaption align = "left">
        <b>
            Radar histogram plot with the distribution of the location of the employees respect to the office, at the centre of the diagram. In the first column all the residences are shown regardless of the distance from the centre. In the second column the residences are split by distance from the centre: inner circle  &lt; 10Km, mid circle between 10 and 20 Km, outer circle &gt; 20 Km.
        </b>
    </figcaption>
</figure>


The same can not be said for the office located in Albert road office, whose employees should consider relocating to an office further North-East.


## 8. Can we do better?

From the question *"Is your company office optimally located in respect to the position of its employees?"*, we developed a small example showing the geospatial data science capabilities to visualise the employees distribution around in respect to the position of their office. In doing so we showed how to download city boundaries from the OSM python API, how to intersect points in polygons, and how to visualise geospatial data with Keplerl GL.

There are several limitations that are worth mentioning.

1. The bearing and distance between office and residence is not a single metric to justify an office relocation. From the point of view of the employee, there are other factors that have not been considered, such as commuting time, cost, transport links availability, frequency of commute, as well as the employee position in the company hierarchy and its "can't bother" factor. This last metric, is an empirical one of the will (or lack thereof) to make a change, it is entirely based upon the individual opinion, taking into account for example possible facilities in the new office, traffic, proximity to children's schools and so on. All these parameters have to be considered for the current office location and the potential new office location.
2. From the point of view of the employer, there is of course the cost of the new office, as well as the cost of the move, as well as prestige of location in respect to possible investors.
3. The last limitation is inherent to the dataset. The whole post was written around the toy dataset downloaded from the Kepler GL examples page. Is it realistic enough or reliable? Can we for example make further analysis on the dataset and obtain some statistics and insights about commuters' habits? We already noticed that some, if not all commuting locations coincided with the location of the offices. Can we do some further analysis to know how little we should trust the data?

To further investigate the last limitation, we can finish the post with some basic data analysis of the dataset to see if the ratio of number of offices in respect to the number of employees is convincing.

```python

number_of_offices = len(df_commuter.groupby(["workplace_lng", "workplace_lat"]).count())
number_of_residences = len(df_commuter.groupby(["residence_lng", "residence_lat"]).count())

number_of_offices_in_london_and_city = len(df_commuter_london_office.groupby(["workplace_lng", "workplace_lat"]).count())
number_of_residences_commuting_to_london_and_city = len(df_commuter_london_office.groupby(["residence_lng", "residence_lat"]).count())

commuters_office_ratio = number_of_residences / number_of_offices
commuters_office_ratio_in_london_and_city = number_of_residences_commuting_to_london_and_city / number_of_offices_in_london_and_city

print(f"Number of offices in london and the city {number_of_offices_in_london_and_city} ({number_of_offices_in_london_and_city / number_of_offices} %)")
print(f"Number of residences commuting to london and the city {number_of_residences_commuting_to_london_and_city} ({number_of_residences_commuting_to_london_and_city / number_of_residences} %)")
print(f"Number of commuters residences per office {commuters_office_ratio}")
print(f"Number of commuters residences per office in london and the city {commuters_office_ratio_in_london_and_city}")

```

As usual we do not show the answers here, as the code above is intended as a starting point for the reader's further investigations.

If you plotted the results above, it is clear that the ratio of commuter's residences per office tells that there is something wrong with the dataset. 
What is typical in data science is not complainig about the data (as some may argue), though it is rather showing the limitation of the data, hence the limitations of the conclusions. We could speculate about the fact that the dataset is synthetically generated, or that the arrows' direction was swapped when generating the dataset, or both. With no further information, we can only say that no analysis on this dataset can provide us with any reasonable answers or statistics to questions related to commuters in the UK.

#### Resources:

- [https://geopandas.org/en/stable/index.html](https://geopandas.org/en/stable/index.html)
- [https://stackoverflow.com/questions/65064351/python-osmnx-how-to-download-a-city-district-map-from-openstreetmap-based-on-t](https://stackoverflow.com/questions/65064351/python-osmnx-how-to-download-a-city-district-map-from-openstreetmap-based-on-t)
- [https://gis.stackexchange.com/questions/343725/convert-geojson-to-geopandas-geodataframe](https://gis.stackexchange.com/questions/343725/convert-geojson-to-geopandas-geodataframe)
- [https://stackoverflow.com/questions/4913349/haversine-formula-in-python-bearing-and-distance-between-two-gps-points](https://stackoverflow.com/questions/4913349/haversine-formula-in-python-bearing-and-distance-between-two-gps-points)
- [https://anitagraser.github.io/movingpandas/#:~:text=MovingPandas%20is%20a%20Python%20library,movement%20data%20exploration%20and%20analysis](https://anitagraser.github.io/movingpandas/#:~:text=MovingPandas%20is%20a%20Python%20library,movement%20data%20exploration%20and%20analysis)
- [https://stackoverflow.com/questions/17624310/geopy-calculating-gps-heading-bearing](https://stackoverflow.com/questions/17624310/geopy-calculating-gps-heading-bearing)
- [https://geodesy.noaa.gov/CORS/](https://geodesy.noaa.gov/CORS/)
- [https://stackoverflow.com/questions/22562364/circular-polar-histogram-in-python](https://stackoverflow.com/questions/22562364/circular-polar-histogram-in-python)
- [https://stackoverflow.com/questions/12750355/python-matplotlib-figure-title-overlaps-axes-label-when-using-twiny](https://stackoverflow.com/questions/12750355/python-matplotlib-figure-title-overlaps-axes-label-when-using-twiny)
- [https://www.dexplo.org/jupyter_to_medium/](https://www.dexplo.org/jupyter_to_medium/)


#### Who inspired me writing this post:
- [Maxime Labonne](https://mlabonne.github.io/blog/)
- [Khuyen Tran](https://khuyentran1476.medium.com/)
- [Abdishakur](https://medium.com/@shakasom)
- [Herbert Lui](https://herbertlui.medium.com/)
- [Maciej Tarsa](https://medium.com/@maciejtarsa)
