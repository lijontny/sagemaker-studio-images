FROM python:3.7

ARG NB_USER="sagemaker-user"
ARG NB_UID="1000"
ARG NB_GID="100"

# Setup the "sagemaker-user" user with root privileges.

RUN \
    apt-get update && \
    apt-get install -y sudo && \
    useradd -m -s /bin/bash -N -u $NB_UID $NB_USER && \
    chmod g+w /etc/passwd && \
    echo "${NB_USER}    ALL=(ALL)    NOPASSWD:    ALL" >> /etc/sudoers && \
    # Prevent apt-get cache from being persisted to this layer.
    rm -rf /var/lib/apt/lists/*

# Install Python Packages
RUN pip install poetry pyspark ipykernel sagemaker_pyspark sagemaker && python -m ipykernel install --sys-prefix
# Disable virtual environments (see notes in README.md)
RUN poetry config virtualenvs.create false --local
# Copy the environment definition file and install the environment
COPY pyproject.toml /
RUN poetry install

# Configure the kernel
RUN python -m ipykernel install --sys-prefix

####################
## Spark Configuration ##
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

# Spark dependencies
ARG spark_version="3.1.2"
ARG hadoop_version="3.2"
ARG openjdk_version="11"

ENV APACHE_SPARK_VERSION="${spark_version}" \
    HADOOP_VERSION="${hadoop_version}"


RUN apt-get -y update && \
    apt-get install --no-install-recommends -y \
    "openjdk-${openjdk_version}-jre-headless" \
    ca-certificates-java && \
    apt-get clean && rm -rf /var/lib/apt/lists/*


# Spark installation
WORKDIR /tmp
RUN wget -q "https://archive.apache.org/dist/spark/spark-${APACHE_SPARK_VERSION}/spark-${APACHE_SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz" && \
    tar xzf "spark-${APACHE_SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz" -C /usr/local --owner root --group root --no-same-owner && \
    rm "spark-${APACHE_SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz"

WORKDIR /usr/local

# Configure Spark Environment

ENV SPARK_HOME=/usr/local/spark-${APACHE_SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}
ENV PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.10.9-src.zip:$PYTHONPATH
ENV PATH=$SPARK_HOME/bin:$SPARK_HOME/python:$PATH
ENV PYSPARK_SUBMIT_ARGS=pyspark-shell

#ENV SPARK_OPTS="--driver-java-options=-Xms1024M --driver-java-options=-Xmx4096M --driver-java-options=-Dlog4j.logLevel=info"
ENV PATH=$PATH:$SPARK_HOME/bin


# Fix Spark installation for Java 11 and Apache Arrow libraryg

RUN cp -p "$SPARK_HOME/conf/spark-defaults.conf.template" "$SPARK_HOME/conf/spark-defaults.conf" && \
    echo 'spark.driver.extraJavaOptions -Dio.netty.tryReflectionSetAccessible=true' >> $SPARK_HOME/conf/spark-defaults.conf && \
    echo 'spark.executor.extraJavaOptions -Dio.netty.tryReflectionSetAccessible=true' >> $SPARK_HOME/conf/spark-defaults.conf


RUN wget https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-core/1.11.1034/aws-java-sdk-core-1.11.1034.jar -P $SPARK_HOME/jars/&& \
    wget https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk/1.11.1034/aws-java-sdk-1.11.1034.jar -P $SPARK_HOME/jars/&& \
    wget https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-s3/1.11.1034/aws-java-sdk-s3-1.11.1034.jar -P $SPARK_HOME/jars/&& \
    wget https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-dynamodb/1.11.1034/aws-java-sdk-dynamodb-1.11.1034.jar -P $SPARK_HOME/jars/&& \
    wget https://repo1.maven.org/maven2/org/apache/httpcomponents/httpclient/4.5.13/httpclient-4.5.13.jar -P $SPARK_HOME/jars/&& \
    wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/3.2.2/hadoop-aws-3.2.2.jar -P $SPARK_HOME/jars/&& \
    wget https://repo1.maven.org/maven2/net/java/dev/jets3t/jets3t/0.9.4/jets3t-0.9.4.jar -P $SPARK_HOME/jars/&& \
    wget https://repo1.maven.org/maven2/com/google/guava/guava/23.0/guava-23.0.jar -P $SPARK_HOME/jars/

ENV SHELL=/bin/bash

USER $NB_UID
WORKDIR /home/sagemaker-user