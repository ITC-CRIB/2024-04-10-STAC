![ITC-CRIB Logo](/codimd/uploads/8f24674fa3405411f0ed7e800.png)

# Publishing Spatiotemporal Data with STAC

10/04/2024, ITC, Enschede

**Welcome to The Workshop Collaborative Document!**

This document is synchronized as you type, so that everyone viewing this page sees the same text. This allows you to collaborate seamlessly.

---

This document is available at https://crib.utwente.nl/codimd/2024-04-10-STAC

---

## Code of Conduct

Participants are expected to follow these guidelines:

* Use welcoming and inclusive language.
* Be respectful of different viewpoints and experiences.
* Gracefully accept constructive criticism.
* Focus on what is best for the community.
* Show courtesy and respect towards other community members.

## License

All content is publicly available under the Creative Commons Attribution License: [creativecommons.org/licenses/by/4.0/](https://creativecommons.org/licenses/by/4.0/).

## Getting help

Contact nearby *helper* or put on *red* sticker note on your laptop if you need assistance.

---

## Setup

### Python
Make sure you have Conda for Python installed on your laptop. If you don't have it installed, please download [Miniconda](https://docs.anaconda.com/free/miniconda/miniconda-install/) and follow the provided instructions.

### NodeJS 

*This is required for the STAC Browser*

Make sure you have NodeJS installed on your laptop. If don't have it installed, please download for [Windows / MacOS](https://nodejs.org/en/download/) or [Linux](https://nodejs.org/en/download/package-manager/all) and follow the provided instructions.

### Setting up the workshop environment

If Anaconda was properly installed, you should have access to the conda command in your terminal (use the Anaconda prompt on Windows).

1. Test that conda is correctly installed by typing:

```bash=
conda --version
```

2. Create the Python environment for the workshop by running:

```bash=
conda env create -n stac -f https://raw.githubusercontent.com/ITC-CRIB/2024-04-10-STAC/main/setup/environment.yaml
```

Note that this step can take several minutes.

3. When installation has finished you should see the following message in the terminal:

```output
# To activate this environment, use
#    $ conda activate stac
#
# To deactivate an active environment, use
#    $ conda deactivate
```

4. Activate the geospatial environment:

```bash=
conda activate stac
```

If successful, the text (base) in your terminal prompt will now read (stac) indicating that you are now in the Anaconda virtual environment named stac.

### Starting JupyterLab

In order to follow the lesson, you should launch JupyterLab. After activating the geospatial conda environment, enter the following command in your terminal (use the Anaconda prompt on Windows):

```bash=
jupyter lab
```

Once you have launched JupyterLab, create a new Python 3 notebook, type the following code snippet in a cell and press the “Play” button:

```python=
from pystac_client import Client
```

If all the steps above completed successfully you are ready to follow along with the lesson!

---

## Instructors

* Serkan Girgin, s.girgin@utwente.nl
* Jay Gohil, j.h.gohil@utwente.nl

## Helpers

* Indupriya Mydur, i.mydur@student.utwente.nl
* Adhitya Bhawiyuga, a.bhawiyuga@utwente.nl

## Roll Call
Name / Job, role / Social media (twitter, github, ...) / Background or interests (optional)



## Agenda

| Time          | Topic                                                      |
| ------------- | ---------------------------------------------------------- |
| 09:00 - 09:15 | Welcome and icebreaker                                     |
| 09:15 - 09:45 | Introduction to STAC                                       |
| 09:45 - 10:30 | Searching spatiotemporal data using STAC                   |
| 10:30 - 10:45 | Coffee Break                                               |
| 10:45 - 12:00 | Retrieving spatiotemporal data using STAC for geocomputing |
| 12:00 - 13:00 | Lunch                                                      |
| 13:00 - 14:30 | Creating STAC catalogs for spatiotemporal data             |
| 14:30 - 14:45 | Coffee Break                                               |
| 14:45 - 15:45 | Publishing STAC catalogs                                   |
| 15:45 - 16:00 | Wrap-Up                                                    |
| 16:00         | END                                                        |

---


---

## Collaborative Notes

```python=
from pystac_client import Client
```
```python=
api_url = "https://earth-search.aws.element84.com/v1"
```
```python=
client = Client.open(api_url)
```
```python=
collections = client.get_collections()
```
```python=
for collection in collections:
    print (collection)
```
```python=
s1_collection = client.get_collection("sentinel-1-grd")
s1_collection
```
```python=
import leafmap
```
```python=
m = leafmap.Map(center=(6.85418, 52.237955), zoom=16, height="800")
m
```
```python=
if m.user_rois is not None:
    point = m.user_rois['features'][0]['geometry']
else:
    point = dict(type="Point", coordinates=(6.85418, 52.237955))
```
```python=
search_results = client.search(
    collections = ["sentinel-2-l2a"],
    intersects = point,
    max_items=12
)
```
```python=
print(search_results.matched())
```
```python=
items = search_results.item_collection()
```
```python=
len(items)
```
```python=
items[0]
```
```python=
print(items[0].geometry)
```
```python=
m.add_geojson(items[0].geometry)
m
```
```python=
search = client.search(
    collections = ["sentinel-2-l2a"],
    intersects = point,
    datetime = "2023-01-01/2023-09-22"
)
```
```python=
print(search.matched())
```
```python=
search = client.search(
    collections = ["sentinel-2-l2a"],
    intersects = point,
    datetime = "2023-01-01/2023-09-22",
    query = ["eo:cloud_cover<5"],
    max_items=10
)
```
```python=
print(search.matched())
```
Search extention eo - https://github.com/stac-extensions/eo
```python=
search = client.search(
    collections = ["sentinel-2-l2a"],
    intersects = point,
    datetime = "2023-01-01/2023-09-22",
    query = ["eo:cloud_cover<5"],
    sortby = ["+properties.eo:cloud_cover"],    # + for accending 
    max_items=10
)
```
```python=
items = search.item_collection()
```
```python=
len(items)
```
```python=
for item in items:
    print(item.properties["eo:cloud_cover"])
```
```python=
items.save_object("search.json")
```
### Exercise
Search query - Decending sort, with cloud cover <10, max items = 20, 2022 year

```python=
exercise_search = client.search(
    collections = ["sentinel-2-l2a"],
    intersects = point,
    datetime = "2022-01-01/2022-12-31",
    query = ["eo:cloud_cover<10"],
    sortby = ["-properties.eo:cloud_cover"],    # - for decending 
    max_items=20
)
```
```python=
exercise_items = exercise_search.item_collection()
for ex_item in exercise_items:
    print(ex_item.properties["eo:cloud_cover"])
```
```python=
selected_item = items[1]
```
```python=
assets = selected_item.assets
print(assets.keys())
```
```python=
for key, asset in assets.items():
    print(f"{key}:{asset.title}")
```
```python=
print(assets["thumbnail"].href)
```
```python=
import requests
img_data = requests.get(assets["thumbnail"].href).content
img_data
```
```python=
import matplotlib.pyplot as plt
from PIL import Image
import io
```
```python=
plt.figure(figsize=(10,10))
plt.imshow(Image.open(io.BytesIO(img_data)))
```
```python=
import rioxarray
```
```python=
nir_href = assets["nir"].href
nir = rioxarray.open_rasterio(nir_href)
nir
```
```python=
%%timeit -n 5 -r 1
plt. imshow(nir[0, :, :], cmap='gray')
plt.clim(vmin=10, vmax=5000)
```
```python=
%%timeit -n 5 -r 1
plt.imshow(nir[0, 10400:10980, 6000:7000], cmap="gray")
plt.clim(vmin=10, vmax=5000)
```
```python=
nir[0,10400:10980, 6000:7000].rio.to_raster("nir_subset.tif")
```
### Using *cloud-native* geoprocessing
https://stackstac.readthedocs.io/en/latest/

```python=
!pip install stackstac
```
```python=
import stackstac
```
```python=
lat, long = -105.75, 35.79
```
```python=
import pystac_client
```
```python=
url = "https://earth-search.aws.element84.com/v1
```
```python=
catalog = pystac_client.Client.open(url)
```
```python=
%%time 
items = catalog.search(
    intersects = dict(type="Point", coordinates=[lat,lon]),
    collections=["sentinel-2-l2a"],
    datetime = "2020-03-01/2020-06-01",
).item_collection()

len(items)
```
```python=
%time 
stack = stackstac.stak(items)
stack
```
```python=
lowcloud = stack[stack["eo:cloud_cover"]<20]
rgb = lowcloud.sel(band=["red", "green", "blue"])
monthly = rgb.resample(time="MS").median("time", keep_attrs=True)
monthly
```
```python=
import pyproj
x_utm, y_utm = pyproj.Proj(monthly.crs)(lat,lon)
buffer = 2000
```
```python=
aoi = monthly.loc[..., y_utm+buffer:y_utm-buffer, x_utm-buffer:x_utm+buffer]
aoi
```
```python=
data = aoi.compute()
```
```python=
data.plot.imshow(row="time", rgb="band", robust = True, size=6)
```
### Creating Catalog
https://stacspec.org/en

https://github.com/radiantearth/stac-spec/

https://github.com/radiantearth/stac-spec/blob/master/catalog-spec/catalog-spec.md

STAC browser - https://radiantearth.github.io/stac-browser/#/

```
git clone --branch=v3.1.0 https://github.com/radiantearth/stac-browser.git
```
```
npm install
```
```
npm install http-server
```

```
mkdir catalog
cd catalog
```
```
npx http-server -p 8000 --cors
```
Empty json object - catalog.json

```json=
{ 
    "type": "Catalog",
    "stac_version": "1.0.0",
    "stac_extension":[],
    "id": "enschede",
    "title": "Enschede Data Cube"
    "description": "A STAC catalog of public datasets on Enschede"
    "links": [
        {
            "href": "http://localhost:8000/catalog.json",
            "rel": "self",
            "type": "application/json"
        },
        {
            "href": "./catalog.json",
            "rel": "root",
            "type": "application/json"
        },
        {
            "rel":"child",
            "href": "./cop-dem-glo-30/collection.json",
            "type": "application/json",
            
        }

    ]

}
```
```
npx  #disable caching
```

```
npm start -- --open --catalogurl=
```
https://data.crib.utwente.nl/training/202404-STAC/enschede.zip

Extract folders in catalog directory

Make collection.json as follows

```json=
{
    "type":"Collection",,
    "stac-version": "1.0.0",
    "stac-extensions": [],
    "id": "cop-dem-glo-30",
    "title": "Copernicus Global DEM GLO-30"
    "description": "The Copernicus DEM is a Digital Surface Model which represents surfacec of the Earth including buildings, infrastructure, and vegetation",
    "keywords": [],
    "license": "CC-BY-4.0",
    "providers": [],
    "extent": {
        "spatial": {
            "bbox": [
                [6.70,
                 52.15,
                6.98,
                52.33]
            ]
        },
        "temporal":{
            "interval": [
                ["2019-01-01T00:00:00Z", "null"]
            ]
        }
        
    },
"links": [
    {
        "rel": "self",
         "href": "_",
         "type": "application/json"        
    },
    {
        "rel": "root",
         "href": "../catalog.json",
         "type": "application/json"        
    },
    {
        "rel": "parent",
         "href": "../catalog.json",
         "type": "application/json"        
    },
    {
        "rel": "item",
         "href": ".//item.json",
         "type": "application/geo+json"        
    },
]
]

}

```
Make item.json as follows
'Extract Layer Extent' in QGIS

```json=
{
    "type": "Feature",
    "stac_version": "1.0.0",
    "stac_extensions": [],
    "id": "Copernicus_DEM_N52_15_E006_",
    "geometry": "<copy paste from QGIS - extract layer extent and save as GeoJSON>",
    "bbox":"<copy from QGIS properties>",
    "properties": {
        "datetime": "2019-01-01T00:00:00Z
        <copy paste more properties from https://radiantearth.github.io/stac-browser/#/external/catalogue.dataspace.copernicus.eu/stac/collections/COP-DEM>
    },
    "links": [
        {
            "rel": "root",
            "href": "../../catalog.json",
            "type":"application/json"
        },
        {
            "rel": "self",
            "href": "<>",
            "type":"application/geo+json",
        },
        {
            "rel": "parent",
            "href": "../collection.json",
            "type":"application/json",
        },
        {
            "rel": "collection",
            "href": "../collection.json",
            "type":"application/json",
        },
    ],
    "assets": {
        "data":{
            "href": "<>",
            "title": "DEM",
            "type": "image/tiff; application=geotiff",
            "roles": [
                "data"
            ]
        }
        "thumbnail": 
        {
            "href": "./thumbnail.jpg",
            "role": ["thumbnail"],
            "title": "thumbnail",
            "type": "image/jpg"
        
    }
    },
    "collection": "cop-dem-glo-30"
}

```
https://jsonlint.com/

---



Post-Workshop Survey : [LINK](https://www.itc.nl/about-itc/centres-of-expertise/big-geodata/surveys/post-workshop-form-stac-2024-03-14/)

