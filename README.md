# Condenser(ing) Postgresql

First, create the Postgresql database.

```
createDB albums
psql albums
```

Next, create a table to import our [Metacritic Best Albums of All Time CSV](https://www.kaggle.com/prasertk/metacritic-best-albums-of-all-time). These varchars are aggressive, but it works.

```
CREATE TABLE best (
  rank SERIAL,
  title VARCHAR(255),
  artist VARCHAR(255),
  release DATE,
  summary VARCHAR(5000),
  metascore VARCHAR(255),
  userscore VARCHAR(255),
  link VARCHAR(255),
  img_url VARCHAR(255),
  PRIMARY KEY (rank)
);
```

Check that the table exists with `\dt`

Import the example CSV file:

```
COPY best(rank, title, artist, release, summary, metascore, userscore, link, img_url)
FROM '/Users/*****/Desktop/metacritic.csv'
DELIMITER ','
CSV HEADER;
```
## [Condenser](https://github.com/TonicAI/condenser)

Update config.json.example and `cp` to config.json

```
{
    "initial_targets": [
        {
            "table": "public.best",
            "percent": 5
        }
    ],
    "db_type": "postgres",
    "source_db_connection_info": {
        "user_name": "user",
        "host": "localhost",
        "db_name": "albums",
        "port": 5432
    },
    "destination_db_connection_info": {
        "user_name": "user",
        "host": "localhost",
        "db_name": "albums-subset",
        "port": 5432
    },
    "keep_disconnected_tables": false,
    "excluded_tables": [ ],
    "passthrough_tables": [ ],
    "dependency_breaks": [ ],
    "fk_augmentation": [ ],
    "upstream_filters": [ ]
 }
```

Run the tool to select a 5% subset of the BEST albums.

```
$ python3 direct_subset.py
```

A successful subsetting run will produce something like:

```
Beginning subsetting with these direct targets: ['public.best']
Processing 1 of 1: {'table': 'public.best', 'percent': 10}
Direct target tables completed in 0.03978133201599121s
Beginning greedy upstream subsetting with these tables: []
Greedy subsettings completed in 4.0531158447265625e-06s
Beginning pass-through tables: []
Pass-through completed in 0.0s
Beginning downstream subsetting with these tables: []
Downstream subsetting completed in 9.5367431640625e-07s
Beginning post subset SQL calls
Completed post subset SQL calls in 3.1948089599609375e-05s
public.best, 12797, 1259, 0.0983824333828241
```


