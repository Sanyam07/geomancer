# geomancer

[![Build Status](https://cloud.drone.io/api/badges/thinkingmachines/geomancer/status.svg)](https://cloud.drone.io/thinkingmachines/geomancer)
[![PyPI version](https://badge.fury.io/py/geomancer.svg)](https://badge.fury.io/py/geomancer)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Geomancer is a feature engineering library for geospatial data. It leverages
[OpenStreetMap (OSM)](https://www.openstreetmap.org/) alongside a data
warehouse like BigQuery. This project is currently in pre-alpha and is under
active development.


## Features

Geomancer can perform OSM feature engineering for all types of vector data
(i.e. points, lines, polygons).

- Feature primitives for geospatial feature engineering
- Ability to switch out data warehouses (BigQuery, SQLite, PostgreSQL (*In Progress*))
- Compile and share your features (*In Progress*)


## Installation

Geomancer can be installed using `pip`.

```
$ pip install geomancer
```

This will install **all** dependencies for every data-warehouse we support. If
you wish to do this only for a specific warehouse, then you can add an
identifier:

```
$ pip install geomancer[bq] # For BigQuery
$ pip install geomancer[sqlite] # For Spatialite
$ pip install geomancer[psql] # For PostGresSQL
```

Alternatively, you can also clone the repository then run `install`.

```
$ git clone https://github.com/thinkingmachines/geomancer.git
$ cd geomancer
$ python setup.py install
```

## Basic Usage

All of the feature engineering functions in Geomancer are called "spells". For
example, you want to get the distance to the nearest supermarket for each
point.

```python
from geomancer.spells import DistanceToNearest

# Load the dataset in a pandas dataframe
# df = load_dataset()

dist_spell = DistanceToNearest(
    "supermarket",
    source_table="ph_osm.gis_osm_pois_free_1",
    feature_name="dist_supermarket",
).cast(df, dburl="bigquery://project-name")
```

Compose multiple spells into a "spell book" which you can export as a JSON file.

```python
from geomancer.spells import DistanceToNearest
from geomancer.spellbook import SpellBook

spellbook = SpellBook([
    DistanceToNearest(
        "supermarket",
        source_table="ph_osm.gis_osm_pois_free_1",
        feature_name="dist_supermarket",
        dburl="bigquery://project-name",
    ),
    DistanceToNearest(
        "embassy",
        source_table="ph_osm.gis_osm_pois_free_1",
        feature_name="dist_embassy",
        dburl="bigquery://project-name",
    ),
])
spellbook.to_json("dist_supermarket_and_embassy.json")
```

You can share the generated file so other people can re-use your feature extractions
with their own datasets.

```python
from geomancer.spellbook import SpellBook

# Load the dataset in a pandas dataframe
# df = load_dataset()

spellbook = SpellBook.read_json("dist_supermarket_and_embassy.json")
dist_supermarket_and_embassy = spellbook.cast(df)
```

## Contributing

This project is open for contributors! Contibutions can come in the form of
feature requests, bug fixes, documentation, tutorials and the like! We highly
recommend to file an Issue first before submitting a [Pull
Request](https://help.github.com/en/articles/creating-a-pull-request).

Simply fork this repository and make a Pull Request! We'd definitely appreciate:

- Implementation of new features
- Bug Reports
- Documentation
- Testing

Also, we have a
[CONTRIBUTING.md](https://github.com/thinkingmachines/geomancer/blob/master/CONTRIBUTING.md)
and a [Code of
Conduct](https://github.com/thinkingmachines/geomancer/blob/master/CODE_OF_CONDUCT.md),
so please check that one out!

## License

MIT License (c) 2019, Thinking Machines Data Science
