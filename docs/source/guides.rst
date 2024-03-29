Guides
======
We provide learning paths for best guidance leveling up your Geospatial Intelligence skills.

Learning path for geourban
--------------------------
Developers get access to simulated spatially enabled traffic grids of urban regions.
These traffic grids represent aggregated simulated movements of pedestrians, bikes and cars. 
Each grid has an accumulated variable like the number of agents, the average speed and the sum of carbon dioxide equivalent emissions (kg/km) of car vehicles. 
The traffic simulation calculates these variables for 24 hours and each generated grid has a temporal resolution of one hour.

A serverless infrastructure allows querying every simulated agent position in space and time.
So that developers are able to use these simulated movements for their own use cases, e.g. geospatial apps visualizing commute traffic.

Ramp up your development environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Setup your development environment and start with the first tutorial "Mapping the Urban Traffic for the city of Bonn".

We are ging to use the `ArcGIS API for Python <https://developers.arcgis.com/python>`__ which enables access to ready-to-use maps and curated geographic data from Esri and other authoritative sources, and works with your own data as well. 
It integrates well with the scientific Python ecosystem and includes rich support for Pandas and Jupyter notebook.

Step 1: Create a dedicated environment
""""""""""""""""""""""""""""""""""""""
Choose your favourite enironment e.g. conda or pip and create a dedicated environment.
Enter the following at the prompt:

*Using conda:*

.. code-block:: console

   conda create -n geoint

**Activate the dedicated environment:**

.. code-block:: console

   conda activate geoint

*Using pipenv:*

.. code-block:: console

   python -m venv geoint

**Activate the dedicated environment:**

*Using pipenv on Linux:*

.. code-block:: console

   source geoint/bin/activate

*Using pipenv on Windows:*

.. code-block:: console

   geoint/Scripts/activate

Step 2: Install and Setup packages
""""""""""""""""""""""""""""""""""
The arcgis package is much more restrictive than our geourban package.
We start setting up arcgis first.
Follow the detailed steps at: `Install and Setup <https://developers.arcgis.com/python/guide/install-and-set-up>`__.

The geourban package only supports pipenv.
You can install any pip package from an activated conda environment, too.
Enter the following at the prompt:

.. code-block:: console

   pip install geourban

The default factory implementation reads the API key using the environment.
You need to set the environment variable ``x_rapidapi_key`` containing your Rapid API key.

After you installed the geourban and arcgis package, you need to verify it.

.. note::
    Make sure you set the environment variable ``x_rapidapi_key``.
    Otherwise, the default factory implementation will raise a :exc:`ValueError`.

Step 3: Verify your environment
"""""""""""""""""""""""""""""""
Navigate to the directory you want to work in.
Start a new Juypter notebook instance:

.. code-block:: console

   jupyter notebook

Create new notebook named ``Mapping Urban Traffic``.
Add the following imports and execute the cell:

.. code-block:: python

    from arcgis.gis import GIS
    from arcgis.features import FeatureSet
    from datetime import date, datetime, timedelta
    from georapid.client import GeoRapidClient
    from georapid.factory import EnvironmentClientFactory
    from geourban.services import aggregate, query, simulations, top
    from geourban.types import GridType, VehicleType

Create a client instance:

.. code-block:: python

    host = "geourban.p.rapidapi.com"
    client: GeoRapidClient = EnvironmentClientFactory.create_client_with_host(host)

.. warning::
    The ``host`` parameter must target the specific host like ``"geourban.p.rapidapi.com"``.
    Furthermore, the factory directly access ``os.environ['x_rapidapi_key']`` and uses the specified API key as a header parameter.
    Otherwise, :py:func:`georapid.factory.EnvironmentClientFactory.create_client_with_host` will raise a :exc:`ValueError`.

Connect to ArcGIS Online anonymously and display a map view:

.. code-block:: python

    gis = GIS()
    bonn_map = gis.map('Bonn, Germany', zoomlevel=13)
    bonn_map

.. figure:: https://github.com/Geospatial-AI-DE/geourban-py/assets/921231/5ab65fcb-3aa2-4895-a068-689062d3a725

    Map showing the city of Bonn, Germany

Step 4: List the available simulations
""""""""""""""""""""""""""""""""""""""
We need to know the available urban regions and their simulation date. 
Every urban region has an unique region code which is needed for accessing the corresponding traffic grids.

.. code-block:: python

    urban_simulations = simulations(client)
    urban_simulations

.. code-block:: python

    [
        {
            "region": "DEA2D",
            "name": "Aachen, Stadt",
            "date": "2023-12-10"
        },
        {
            "region": "DE246",
            "name": "Bayreuth, Landkreis",
            "date": "2023-12-28"
        },
        {
            "region": "DE300",
            "name": "Berlin",
            "date": "2023-08-25"
        },
        {
            "region": "DEA22",
            "name": "Bonn, Kreisfreie Stadt",
            "date": "2023-08-24"
        },
        {
            "region": "DEE01",
            "name": "Dessau-Roßlau, Stadt",
            "date": "2023-08-24"
        },
        {
            "region": "DED21",
            "name": "Dresden, Stadt",
            "date": "2023-12-10"
        },
        {
            "region": "DEA12",
            "name": "Duisburg, Kreisfreie Stadt",
            "date": "2023-12-22"
        },
        {
            "region": "DE113",
            "name": "Esslingen, Landkreis",
            "date": "2023-12-19"
        },
        {
            "region": "DE712",
            "name": "Frankfurt am Main, Kreisfreie Stadt",
            "date": "2023-11-21"
        },
        {
            "region": "DE929",
            "name": "Hannover, Region",
            "date": "2023-11-09"
        },
        {
            "region": "DE115",
            "name": "Ludwigsburg, Landkreis",
            "date": "2023-12-18"
        },
        {
            "region": "DEA1C",
            "name": "Mettmann, Kreis",
            "date": "2023-12-27"
        },
        {
            "region": "DE212",
            "name": "München, Landeshauptstadt, Kreisfreie Stadt",
            "date": "2023-11-01"
        },
        {
            "region": "DE228",
            "name": "Passau, Landkreis",
            "date": "2023-12-28"
        },
        {
            "region": "DEA2C",
            "name": "Rhein-Sieg-Kreis",
            "date": "2023-12-09"
        },
        {
            "region": "DE229",
            "name": "Schwandorf, Landkreis",
            "date": "2023-12-28"
        },
        {
            "region": "DE111",
            "name": "Stuttgart, Landeshauptstadt, Kreisfreie Stadt",
            "date": "2023-11-25"
        }
    ]

Step 5: Request the top five accumulated car traffic grid cells
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
We request these hotspots for the city of Bonn by using the urban region code `DEA22`, 
the simulation date `2023-08-24`, the vehicle type `Car`, and the grid type `agent`. 
The returned GeoJSON features represents the grid cells with the highest car throughput.

.. code-block:: python

    bonn_region_code = 'DEA22'
    simulation_date = date(2023, 8, 24)
    vehicle_type = VehicleType.CAR
    grid_type = GridType.AGENT
    limit = 5
    top_traffic_grid_cells = top(client, bonn_region_code, simulation_date, vehicle_type, grid_type, limit=limit)
    top_traffic_grid_cells

.. code-block:: python

    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                7.07375,
                                50.74601
                            ],
                            [
                                7.07504,
                                50.74601
                            ],
                            [
                                7.07569,
                                50.74672
                            ],
                            [
                                7.07504,
                                50.74743
                            ],
                            [
                                7.07375,
                                50.74743
                            ],
                            [
                                7.0731,
                                50.74672
                            ],
                            [
                                7.07375,
                                50.74601
                            ]
                        ]
                    ]
                },
                "properties": {
                    "start_time": "2023-08-24T08:00:00",
                    "end_time": "2023-08-24T08:59:59",
                    "agent_count": 439
                }
            },
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                7.07375,
                                50.74601
                            ],
                            [
                                7.07504,
                                50.74601
                            ],
                            [
                                7.07569,
                                50.74672
                            ],
                            [
                                7.07504,
                                50.74743
                            ],
                            [
                                7.07375,
                                50.74743
                            ],
                            [
                                7.0731,
                                50.74672
                            ],
                            [
                                7.07375,
                                50.74601
                            ]
                        ]
                    ]
                },
                "properties": {
                    "start_time": "2023-08-24T09:00:00",
                    "end_time": "2023-08-24T09:59:59",
                    "agent_count": 427
                }
            },
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                7.07375,
                                50.74601
                            ],
                            [
                                7.07504,
                                50.74601
                            ],
                            [
                                7.07569,
                                50.74672
                            ],
                            [
                                7.07504,
                                50.74743
                            ],
                            [
                                7.07375,
                                50.74743
                            ],
                            [
                                7.0731,
                                50.74672
                            ],
                            [
                                7.07375,
                                50.74601
                            ]
                        ]
                    ]
                },
                "properties": {
                    "start_time": "2023-08-24T07:00:00",
                    "end_time": "2023-08-24T07:59:59",
                    "agent_count": 422
                }
            },
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                7.07569,
                                50.74672
                            ],
                            [
                                7.07699,
                                50.74672
                            ],
                            [
                                7.07764,
                                50.74743
                            ],
                            [
                                7.07699,
                                50.74814
                            ],
                            [
                                7.07569,
                                50.74814
                            ],
                            [
                                7.07504,
                                50.74743
                            ],
                            [
                                7.07569,
                                50.74672
                            ]
                        ]
                    ]
                },
                "properties": {
                    "start_time": "2023-08-24T08:00:00",
                    "end_time": "2023-08-24T08:59:59",
                    "agent_count": 421
                }
            },
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                7.07569,
                                50.74672
                            ],
                            [
                                7.07699,
                                50.74672
                            ],
                            [
                                7.07764,
                                50.74743
                            ],
                            [
                                7.07699,
                                50.74814
                            ],
                            [
                                7.07569,
                                50.74814
                            ],
                            [
                                7.07504,
                                50.74743
                            ],
                            [
                                7.07569,
                                50.74672
                            ]
                        ]
                    ]
                },
                "properties": {
                    "start_time": "2023-08-24T09:00:00",
                    "end_time": "2023-08-24T09:59:59",
                    "agent_count": 408
                }
            }
        ]
    }

Step 6: Convert the returned GeoJSON result into a FeatureSet
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
The FeatureSet offers direct access to a spatially enabled dataframe. 
We can easily inspect the time frames (`start_time` - `end_time`) and the number of car vehicles `agent_count`.

.. code-block:: python

    top_traffic_fset = FeatureSet.from_geojson(top_traffic_grid_cells)
    top_traffic_sdf = top_traffic_fset.sdf
    top_traffic_sdf

.. figure:: https://github.com/Geospatial-AI-DE/geourban-py/assets/921231/70ffcfda-6335-482e-bc45-24cba4dc0cff

    Spatially Enabled Data Frame of aggregated car vehicles

Step 7: Map the traffic grid cells
""""""""""""""""""""""""""""""""""
The map widget displays the traffic grid cells as geospatial polygon features.

.. code-block:: python

    top_traffic_sdf.spatial.plot(bonn_map, renderer_type='s', colors='#E80000', alpha=0.3)
    bonn_map

.. figure:: https://github.com/Geospatial-AI-DE/geourban-py/assets/921231/924f3a1a-9c15-4edc-a1af-67b1b47973bd

    Map showing aggregated car vehicles

Step 8: Query the simulated agents nearby
"""""""""""""""""""""""""""""""""""""""""
We are using the center of this crossroad intersection, request the simulated agents being within a distance of `250 meters`, and specify a `30 seconds` time window starting at `08:00:00`.

.. code-block:: python

    simulation_datetime = datetime.fromisoformat('2023-08-24T08:00:00')
    (latitude, longitude) = (50.746708, 7.074405)
    (seconds, meters) = (30, 250)
    car_positions = query(client, simulation_datetime, vehicle_type, latitude, longitude, seconds, meters)
    car_positions_fset = FeatureSet.from_geojson(car_positions)
    car_positions_sdf = car_positions_fset.sdf
    car_positions_sdf

.. figure:: https://github.com/Geospatial-AI-DE/geourban-py/assets/921231/587519e8-2851-4e76-8c32-26858d0b6ed2

    Spatially Enabled Data Frame of simulated car positions

Step 9: Map the car positions nearby
""""""""""""""""""""""""""""""""""""
The map widget displays every simulated agent position as geospatial point features.

.. code-block:: python

    bonn_map = gis.map('Bonn, Germay')
    car_positions_sdf.spatial.plot(bonn_map, renderer_type='s', colors='#E80000', marker_size=7, alpha=0.3)
    bonn_map

.. figure:: https://github.com/Geospatial-AI-DE/geourban-py/assets/921231/9ff96ea0-e094-4054-ae98-46368141ad09

    Map showing simulated car positions

Step 10: Accumulate the speed of car traffic between 08:00 and 09:00 AM
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
The traffic grid cells represent the accumulated average speed of car vehicles.

.. code-block:: python

    bonn_region_code = 'DEA22'
    simulation_datetime = datetime.fromisoformat('2023-08-24T08:00:00')
    vehicle_type = VehicleType.CAR
    grid_type = GridType.SPEED
    car_speed_cells = aggregate(client, bonn_region_code, simulation_datetime, vehicle_type, grid_type)
    car_speed_fset = FeatureSet.from_geojson(car_speed_cells)
    car_speed_sdf = car_speed_fset.sdf
    car_speed_sdf

.. figure:: https://github.com/Geospatial-AI-DE/geourban-py/assets/921231/88037041-39d3-4c73-963c-5737ec684725

    Spatially Enabled Data Frame of average car speed

Step 11: Map the accumulated speed of car traffic
"""""""""""""""""""""""""""""""""""""""""""""""""
The map widget displays the traffic grid cells as geospatial polygon features.

.. code-block:: python

    car_speed_sdf.spatial.plot(bonn_map, renderer_type='c', method='esriClassifyNaturalBreaks', class_count=5, col='speed_mean', cmap='RdYlGn')
    bonn_map

.. figure:: https://github.com/Geospatial-AI-DE/geourban-py/assets/921231/39741bcc-a805-47a1-b2c5-7b525bd20418

    Map showing average car speed
