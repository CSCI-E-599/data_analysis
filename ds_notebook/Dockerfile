FROM python:3.6-slim

ENV PYTHONUNBUFFERED 1

ARG SPARK_VERSION=2.4.7
ARG HADOOP_VERSION=2.7

# Install Java 8 - no longer officially available on Debian 10
# But Spark 2.4.7 + Scala_11 requires Java 8
# Workaround: https://linuxize.com/post/install-java-on-debian-10/
USER root
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get -y install sudo \
    && mkdir -p /usr/share/man/man1 \
    && sudo apt update \
    && sudo apt -y install apt-transport-https ca-certificates wget dirmngr gnupg software-properties-common \
    && wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add - \
    && sudo add-apt-repository --yes https://adoptopenjdk.jfrog.io/adoptopenjdk/deb/ \
    && sudo add-apt-repository --yes https://adoptopenjdk.jfrog.io/adoptopenjdk/deb/ \
    && sudo apt update \
    && sudo apt -y install adoptopenjdk-8-hotspot
ENV JAVA_HOME /usr/lib/jvm/adoptopenjdk-8-hotspot-amd64

# Install Spark
RUN apt-get -y install wget \
    && wget https://www-eu.apache.org/dist/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
    && tar -xvzf spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
    && mv spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION} spark \
    && rm spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
    && cd /
ENV SPARK_HOME /spark
# Install JDBC Driver for Spark
ARG POSTGRES_JDBC_VERSION=42.2.5
RUN wget https://jdbc.postgresql.org/download/postgresql-${POSTGRES_JDBC_VERSION}.jar
# Set spark-submit env vars
ARG SPARK_DRIVER_MEMORY=12G
ENV PYSPARK_SUBMIT_ARGS "--jars /postgresql-${POSTGRES_JDBC_VERSION}.jar --driver-memory=${SPARK_DRIVER_MEMORY} --conf spark.driver.maxResultSize=4g pyspark-shell"

# Install requirements
COPY requirements.txt /requirements.txt
RUN pip install --no-cache-dir -r /requirements.txt \
    && rm -f /requirements.txt

# Add sparkmonitor extension for Jupyter
RUN jupyter nbextension install --py sparkmonitor --user --symlink \
    && jupyter nbextension enable sparkmonitor --user --py \
    && jupyter serverextension enable --py --user sparkmonitor \
    && ipython profile create \
    && echo "c.InteractiveShellApp.extensions.append('sparkmonitor.kernelextension')" >>  $(ipython profile locate default)/ipython_kernel_config.py

# Start Jupyter notebook
WORKDIR /notebooks
CMD jupyter notebook --port=8888 --no-browser --ip=0.0.0.0 --allow-root

ENV PYTHONHASHSEED 1
