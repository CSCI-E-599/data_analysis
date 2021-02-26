# Docker Setup for Jupyter Notebook with PySpark + JDBC

A simple set up for getting started with Data Analysis on PySpark.

## Running the script
1. ```cd ds_notebook```
2. ```docker-compose build```
3. ```docker-compose up```

## Notes
1. Make sure Docker Engine has adequate CPU and Memory allocation
2. In Dockerfile, you can change ```SPARK_DRIVER_MEMORY``` to what you can afford
3. Jupyter notebook's data directory will be ```notebooks```. The demo notebook ```pyspark_demo_notebook.ipynb``` contains code to read and analyse a sample .csv file from the data directory.
4. Jupyter notebook will be accessible at localhost:8888 and the Spark UI at localhost:4040. You can change this in ```docker-compose.yml```.
5. The Jupyter notebook extension ```sparkmonitor``` brings the Spark dashboard into the notebook for convenient monitoring of task progress.
![Screenshot](resources/sparkmonitor.png?raw=true "Sparkmonitor")