# grafana-influxdb-docker
This is a quick Grafana/InfluxDB setup with Docker Compose.

## How to test this
* Install Docker and `docker-compose` if needed.
* Start both containers using `docker-compose up -d`
* Open http://localhost:8083/ for InfluxDB (assuming _localhost_ is your Docker host)
* Execute this command in InfluxDB: `CREATE DATABASE "x42"`

Now start a bash shell loop which posts data to the x42 InfluxDB database:

    while true
	do  
	  curl -i -XPOST 'http://localhost:8086/write?db=x42' --data-binary "Load,component=Bazinator value=$RANDOM"
	  curl -i -XPOST 'http://localhost:8086/write?db=x42' --data-binary "Temperature,component=Foobulator value=$RANDOM"
	  sleep 1
	done


Login to Grafana at http://localhost:3000/login using `admin/admin` as credentials.

Add a Grafana datasource:

    type=InfluxDB
    URL=http://localhost:8086
    access=direct
    database=x42
    user/pwd <empty>

Create a Grafana dashboard containing a graph panel using the 'x42' data source and this query:

    SELECT mean("value") FROM "Load" WHERE $timeFilter GROUP BY time($interval) fill(null)
    
The screencast at https://www.youtube.com/watch?v=sKNZMtoSHN4 for example shows how to do that.    

The Grafana panel should now display the `Load` value generated by the bash loop - you can add another panel or query for the `Temperature` value.
