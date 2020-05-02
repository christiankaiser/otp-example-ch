# otp-example-ch

This is an example of how to use [OpenTripPlanner](http://www.opentripplanner.org/) with Swiss public transport data.

It contains an already built OTP jar file. It differs from the official built in that it also accepts some of the additional transport modes found in Swiss timetables.

We assume we have a fresh Ubuntu 18.04LTS install. Below are the steps described to make build the graph and run the server.

## Install Java runtime

```bash
sudo apt install openjdk-8-jdk openjdk-8-jdk-headless
```

## Download the repo

We need git installed to download the repo and wget to download datasets later on:

```bash
sudo apt install git wget
```

Create a new directory in your home directory:

```bash
cd ~
mkdir otp
cd otp
```

and clone the repo directly in this directory:

```bash
git clone https://github.com/christiankaiser/otp-example-ch.git .
```


## Get the GTFS timetable data

You will find the latest timetable data on [opentransportdata.swiss](https://opentransportdata.swiss/de/dataset). Head for the «Timetable 20XX (GTFS)» dataset (where XX is the current year) and download the latest file.

Place the downloaded file inside the directory `~/otp/graphs/ch` and name it `timetable-ch.gtfs.zip`. You can optionnally include the date of the file in the file name so you know from when the timetable data is; you just need to adapt the file name in all later commands.

```
cd ~/otp/graphs/ch
wget -O timetable-ch.gtfs.zip https://opentransportdata.swiss/dataset/6f55f96d-7644-4901-b927-e9cf05a8c7f0/resource/a485003d-2015-42df-ac16-b810e7278629/download/gtfs_fp2020_2020-04-29.zip 
```


## Get the OpenStreetMap dump for Switzerland

We also need the road network. We can get it from [geofabrik.de](http://download.geofabrik.de/europe.html).

We can also directly download it as follows:

```
cd ~/otp/graphs/ch
wget http://download.geofabrik.de/europe/switzerland-latest.osm.pbf
```


## Build the graph

We are now ready to build the graph which will take a bit of time. We also need approximately 16GB of RAM for the process to complete.

```
cd ~/otp
java -Xmx16G -Xverify:none -jar otp-1.4.0-SNAPSHOT-shaded.jar "$@" --build graphs/ch
```

## Run the server

We can now run the server:

```
cd ~/otp
java -Xmx16G -Xverify:none -jar otp-1.4.0-SNAPSHOT-shaded.jar "$@" --router ch --graphs graphs --server
```

The startup process can take a while as the program needs to read and index the graph. Once it is saying «Grizzly server running.», the server is ready for accepting queries.


## Query the server

We can now make queries to the server. At the URL [localhost:8080](http://localhost:8080) is a handy GUI for making some queries.

However, more powerful is the direct use of the API. For example for computing some isochrones, we can call the following URL:

```
http://localhost:8080/otp/routers/ch/isochrone?fromPlace=47.243,8.7774&mode=WALK,TRANSIT&date=11-04-2020&time=8:00am&maxWalkDistance=1500&cutoffSec=1800&cutoffSec=3600
```


