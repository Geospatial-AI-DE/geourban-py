[![GitHub release (latest by date)](https://img.shields.io/github/v/release/Geospatial-AI-DE/geourban-py)](https://pypi.org/project/geourban)
![GitHub License](https://img.shields.io/github/license/Geospatial-AI-DE/geourban-py)
[![Read the Docs](https://img.shields.io/readthedocs/geourban)](https://geourban.readthedocs.io/en/latest)
[![Tweet Me](https://img.shields.io/twitter/url?style=social&url=https%3A%2F%2Fgithub.com%2FGeospatial-AI-DE%geourban-py)](https://twitter.com/intent/tweet?text=Outstanding:&url=https%3A%2F%2Fgithub.com%2FGeospatial-AI-DE%geourban-py)


# Geospatial Urban API Service
Direct access to simulated spatially enabled traffic grids of urban regions. This modern Python module represents an idiomatic client accessing the [Geospatial Urban API](https://geospatial-ai.de/?rara-portfolio=geospatial-urban-api-service) being hosted on [Rapid API](https://rapidapi.com/gisfromscratch/api/geourban).


![](https://geospatial-ai.de/wp-content/uploads/2023/10/Screenshot-2023-10-31-000538-1024x419.png)

*Traffic grid accumulating the number of simulated agents moving using bikes through the city of Bonn between 07:00 and 08:00 AM*


![](https://geospatial-ai.de/wp-content/uploads/2024/01/Screenshot-from-2024-01-08-00-47-07.png)

*Simulated agent positions moving by car within a distance of 250 meters of a highway cross road intersection being located at (50.746708, 7.074405) in the city of Bonn starting at 08:00 AM and lasting 30 seconds*

## Why is it important?
The geospatial urban API solves a common challenge for urban digital twins – obtaining insights into mobility behavior of citizens using simulated traffic scenarios. Traffic planners use powerful tools in shaping urban mobility sustainable. But without having access to citizen movement profiles there is always a need for simulating, analyzing, testing and mitigating traffic scenarios.

## Scope and Usage
This repository provides a Python client library for interacting with the Geospatial Urban API, which is offered as a hosted service via RapidAPI.

The library is published as **free and open‑source software** under the `Apache‑2.0` licence. It is developed and maintained by an individual without any commercial intent – no fees, no monetisation, and no donations are solicited or accepted.

**For the purposes of the EU Cyber Resilience Act (Regulation (EU) 2024/2847), this software is NOT “made available on the market in the course of a commercial activity” (see Article 3(22) and Recitals 15, 18). Therefore, the obligations that apply to manufacturers (such as CE marking, conformity assessment, and formal vulnerability handling) do not apply to the provision of this library.**

Anyone who integrates this library into their own commercial products or services are responsible for ensuring that their overall system complies with all applicable regulations, including the Cyber Resilience Act where relevant.

## Next steps
Please, check out the [RapidAPI Account Creation and Management Guide](https://docs.rapidapi.com/docs/account-creation-and-settings).

Start with the [Introduction](https://rapidapi.com/gisfromscratch/api/geourban/details) section for further information.

Follow the [Guides](https://geourban.readthedocs.io/en/latest/guides.html) or take a look at the [Urban Traffic Bonn](https://github.com/Geospatial-AI-DE/geourban-py/blob/main/notebooks/Urban%20Traffic%20Bonn.ipynb) notebook.

## Ready to use
The geospatial urban API offer ready-to-use geospatial features representing simulated spatially enabled traffic grids of urban regions. 

Every geospatial result support the GeoJSON and Esri FeatureSet format out of the box. For best sustainability, the serverless cloud-backend queries the geospatial features from a big data store. You can use these geospatial features to build various mapping and geospatial applications.
