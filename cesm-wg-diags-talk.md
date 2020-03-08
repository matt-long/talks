---
title: "Model diagnostics: the path forward"
tags: presentation
slideOptions:
  theme: league
  transition: fade #none/fade/slide/convex/concave/zoom
  progress: true
  center: true  
  minScale: 0.2
  maxScale: 1
---

<style type="text/css">
  .reveal h1 {
    text-align: left;    
    font-size: 150%;
    text-shadow: none;
    font-weight: 500;
  }
  .reveal h2 {
    text-align: left;
    font-size: 120%;
    font-weight: 500;
  }
  .reveal h3 {
    text-align: left;
    font-size: 100%;
    font-weight: 500;
  }  
  .reveal ul {
    display: block;
    text-align: left;
  }
  .reveal ol {
    display: block;
  }
  td, th {
     border: none
  }  
</style>


# Model diagnostics: the path forward


<!-- .slide: data-background="https://images.squarespace-cdn.com/content/v1/5476529ce4b0046dc16f07c7/1448313396926-EFGIOXLDQOJ2CMBRHBOA/ke17ZwdGBToddI8pDm48kCXAmbIgMI78o84NhT6RhWwUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYy7Mythp_T-mtop-vrsUOmeInPi9iDjx9w8K4ZfjXt2dkqliSzw9PV_eXsXtMHfJ-3wGQ0-Rk33P7-x3gh5BgRvZtJ3qR9G2BYeA0wOAaeYNg/GNAM-3889.jpg" -->

Matthew Long
Joe Hamman 
Kevin Paul 
Anderson Banihirwe

---

<img src="https://static1.squarespace.com/static/580fd28d20099ec7189b72cf/581e0a34bebafba66b97b84c/5b0824080e2e72e81bb6a243/1527262120576/LeftRight-Debate_Insta_1080x1080.jpg" style="background:none; border:none; box-shadow:none;" width=400px>

<div style="text-align: center;">

Software is science

</div>

---

## Avoid the hegemony of monoliths

<img src="https://www.pwc.com/gx/en/alliances/partner-logos/workday-logo-16x9.png" style="background:none; border:none; box-shadow:none;" width=400px>

---

## Proactive, not reactive

[funding constraints, ]

---

## Vision: An interactive numerical laboratory for Earth system science

- Seamless integration of routine model evaluation and cutting-edge research
- Enable reproducible science
- Enable novel means of data interactivity and visualization
- Component models are not necessarily a natural organizing principle
    - Be as model agnostic as much as possible
- Scalable
    - Big Data
    - New applications, communities, etc.
- Fluid integration of observations and models
- Community-developed and open-source 

---

## 

<img src="https://raw.githubusercontent.com/pangeo-data/branding/master/logo/pangeo_card_green.png" style="background:none; border:none; box-shadow:none;" width="800px">


<div style="text-align: center;">

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/38/Jupyter_logo.svg/883px-Jupyter_logo.svg.png" style="background:none; border:none; box-shadow:none;" width="200px">
&nbsp;&nbsp;&nbsp;&nbsp;
<img src="https://xarray.pydata.org/en/stable/_static/dataset-diagram-logo.png" style="background:none; border:none; box-shadow:none;" width="400px">
&nbsp;&nbsp;&nbsp;&nbsp;
<img src="https://dask.org/_images/dask_horizontal_white_no_pad.svg" style="background:none; border:none; box-shadow:none;" width="200px">

</div>

---

## Key design concepts

- Modular & extensible
- Re-usable code
- Scalable
- Community-developed and open-source

---

## Key concept: Application programming interface (API)

Example object

```python
class MyThing:

   def __init__(self, data):
       """The initialization"""
       self.data = data
   
   def perform_operation(self):
       """A method"""
       return self.data ** 2
       
a = MyThing(5)
b = a.perform_operation()
```

- Interface with the rest of the world through clearly defined methods
- Hide messy implementation details behind layers of abstraction

---

## Key concept: Re-usable elements

- Packages
- [need help here]

---

## Key concept: Scalability

- Prototype on small problems, deploy on big ones
- Leverage elastic compute on HPC and Cloud 

---

## Key concept: Open development

- Continuous integration

<img src="https://i.imgur.com/6yz4yAh.png" alt="example-1" style="width:50%; background:none; border:none; box-shadow:none;">
<img src="https://i.imgur.com/vCwXhe0.png" alt="example-2" style="width:50%; background:none; border:none; box-shadow:none;">


---

## Outline: High level components

- **Analysis elements:** modular scripts or Notebooks that perform computation and support visualization
- **Data APIs**: abstract data access through APIs
- **Operators**: perform dimensions reductions, compute derived quantities, etc.
- **Visualization**: static plots, web apps
- **Workflow**: Automate, schedule, interact

---

## The Analysis Element

### Typical sequence

1. Access data through data API
1. Apply `operators`
1. Develop visualization

---

## The Analysis Element

### Jupyter Notebooks

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/38/Jupyter_logo.svg/883px-Jupyter_logo.svg.png" style="background:none; border:none; box-shadow:none;" width="300px">

- A thin client: interactive computing in the universal app,  the web browser
- Produce and publish “computational narratives”
- Support "widgets" and web apps

---

## Data access APIs

- Data access based on hard-coded paths is fragile
- Desired product sometimes involves rote computation
  - e.g., `pop_tools.get_grid(...)` reads `CESM INPUTDATA` files via web protocol and 
    does the same computations as the model to derive the grid variables. Super portable. Efficient because of Numba!
  - e.g., Monthly ---> annual or climatology
- Access details are messy
  - Simulations discretized over arbitrary number of files
  - Many file formats, temporal frequencies, spatial resolutions, etc.: APIs enable standardizations steps to be applied en route
- An API can be parameterized
  - Flexibility enables more reuseable components
  - Build codes to perform operation over key dimension in query returns
- Data behind a authentication layers precludes reproducibility

---

## Data access APIs

### Problem: file paths break code portability


```python
import xarray as xr
from pathlib import Path
root_dir = Path("""/glade/collections/cmip/CMIP6/CMIP/NCAR/
        CESM2/1pctCO2/r1i1p1f1/Amon/co2/gn/latest""")
filenames = [
"co2_Amon_CESM2_1pctCO2_r1i1p1f1_gn_000101-005012.nc", 
"co2_Amon_CESM2_1pctCO2_r1i1p1f1_gn_005101-010012.nc", 
"co2_Amon_CESM2_1pctCO2_r1i1p1f1_gn_010101-015012.nc"]
dsets = []
for fn in filenames:
    path = root_dir / fn
    dsets.append(xr.open_dataset(path.as_posix(), 
                                 chunks={'time': 36}))
# Concatenate datasets along the time dimension/axis
ds = xr.concat(dsets, dim='time')
```

----

## Data access APIs

### Solution: Data catolog

**Taking the pain out of data access**

```python
import intake
col = intake.open_esm_datastore("glade-cmip6.json")
cat = col.search(activity_id="CMIP", source_id="CESM2",
                 experiment_id="1pctCO2", variable_id="co2",
                 table_id="Amon", grid_label="gn")
dsets = cat.to_dataset_dict(cdf_kwargs={"chunks": {"time":36}})
```

---

## Operators

### Rely on the open-source stack

<img src="https://res.cloudinary.com/dyd911kmh/image/upload/f_auto,q_auto:best/v1509622333/scipy-eco_kqi2su.png" style="background:none; border:none; box-shadow:none;" width="500px">

- Target [GeoCAT](https://geocat.ucar.edu/) for domain-specific functionality

---

## Operators

### Consume and produce xarray datasets

<!--<img src="https://xarray.pydata.org/en/stable/_static/dataset-diagram-logo.png" style="background:none; border:none; box-shadow:none;">-->

<img src="https://i.imgur.com/pZS3OfJ.png" style="background:none; border:none; box-shadow:none;">

- netCDF-like datamodel
    - `xarray.Dataset` objects encapsulate data and metadata
- i/o well supported: netCDF, zarr (Cloud friendly netCDF), grib
- Built-in methods enable rapid dimension reduction, interpolation, resampling, etc.
- Integrates with `dask`, enabling scalability

---

## Operators

### Be Dask friendly

<img src="https://dask.org/_images/dask_horizontal_white_no_pad.svg" style="background:none; border:none; box-shadow:none;" width="300px">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

<!--<img src="https://docs.dask.org/en/latest/_images/dask-array-black-text.svg" style="background:white;" width="300px">!-->
<img src="https://andersonbanihirwe.dev/talks/images/scipy-stack/grid_search_schedule.gif" style="background:white;">

- Parallelism embedded in the data model
- Distributed computing on Cloud or HPC

---

## Operators

### Built-in provenance tracking

- Capture path-dependence of state throughout workflow
- Leverage Dask task graphs and tools like `xpersist` 

---

## Workflow

- Automated invocation & scheduling
- Dashboards
- Cloud hosted?

---

## How do we get started?

- Build library of `analysis_element` prototypes
    - Aim for best practices, but focus on conceptual objectives and acheiving basic function first
    - Communicate!
- Coalesce as library grows 

---


<!-- .slide: data-background="https://images.squarespace-cdn.com/content/v1/5476529ce4b0046dc16f07c7/1418876547893-F8BEI0DLBEJHNJ5R5LH7/ke17ZwdGBToddI8pDm48kCpX2mwG9slVUzQCwhOMrQF7gQa3H78H3Y0txjaiv_0fDoOvxcdMmMKkDsyUqMSsMWxHk725yiiHCCLfrh8O1z5QPOohDIaIeljMHgDF5CVlOqpeNLcJ80NK65_fV7S1UVDXM9yQ8sG6x3COIEUaadqpk9XPubC0H4MH9Az_c7nPqIjSxZ2rgD2_Fw9U6DWfsg/gnam-7969.jpg" -->

---

## Modularity 

### Plotting code should not do "computation"

- Computation should be clearly separate from visualization
- The data behind plots should be cached
- If the data to make plots is available, we can build web-visualization maps around it

