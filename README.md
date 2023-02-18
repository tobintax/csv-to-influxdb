# csv-to-influxdb
Simple python script that inserts data points read from a csv file into a influxdb database.

To create a new database, specify the parameter ```--create```. This will drop any database with a name equal to the one supplied with ```--dbname```.

## Usage

```
usage: csv-to-influx.py [-h] -i [INPUT] [-d [DELIMITER]] [-s [SERVER]] [--ssl]
                        --dbname [DBNAME] [--create] [-m [METRICNAME]]
                        [-tc [TIMECOLUMN]] [-tf [TIMEFORMAT]] [-tz TIMEZONE]
                        [--fieldcolumns [FIELDCOLUMNS]]
                        [--tagcolumns [TAGCOLUMNS]] [-g] [-b BATCHSIZE] [-f]
                        --token [TOKEN] --org [ORG] --bucket [BUCKET]

Csv to influxdb.

optional arguments:
  -h, --help            show this help message and exit
  -i [INPUT], --input [INPUT]
                        Input csv file.
  -d [DELIMITER], --delimiter [DELIMITER]
                        Csv delimiter. Default: ','.
  -s [SERVER], --server [SERVER]
                        Server address. Default: localhost:8086
  --ssl                 Use HTTPS instead of HTTP.
  -u [USER], --user [USER]
                        User name.
  -p [PASSWORD], --password [PASSWORD]
                        Password.
  --dbname [DBNAME]     Database name.
  --create              Drop database and create a new one.
  -m [METRICNAME], --metricname [METRICNAME]
                        Metric column name. Default: value
  -tc [TIMECOLUMN], --timecolumn [TIMECOLUMN]
                        Timestamp column name. Default: timestamp.
  -tf [TIMEFORMAT], --timeformat [TIMEFORMAT]
                        Timestamp format. Default: '%Y-%m-%d %H:%M:%S' e.g.:
                        1970-01-01 00:00:00
  -tz TIMEZONE, --timezone TIMEZONE
                        Timezone of supplied data. Default: UTC
  --fieldcolumns [FIELDCOLUMNS]
                        List of csv columns to use as fields, separated by
                        comma, e.g.: value1,value2. Default: value
  --tagcolumns [TAGCOLUMNS]
                        List of csv columns to use as tags, separated by
                        comma, e.g.: host,data_center. Default: host
  -g, --gzip            Compress before sending to influxdb.
  -b BATCHSIZE, --batchsize BATCHSIZE
                        Batch size. Default: 10000.
                        
  --token [TOKEN]       InfluxDB API token.
  --org [ORG]           Organization name.
  --bucket [BUCKET]     Bucket name.

```

## Example

Considering the csv file:
```
timestamp,value,computer
1970-01-01 00:00:00,51.374894,0
1970-01-01 00:00:01,74.562764,1
1970-01-01 00:00:02,17.833757,2
1970-01-01 00:00:03,40.125102,0
1970-01-01 00:00:04,88.160817,1
1970-01-01 00:00:05,28.401695,2
1970-01-01 00:00:06,98.670792,3
1970-01-01 00:00:07,69.532011,0
1970-01-01 00:00:08,39.198964,0
```

The following command will insert the file into a influxdb database:

```python csv-to-influxdb.py --dbname test --input data.csv --tagcolumns computer --fieldcolumns value```


## Another Example with another csv, exported through the influx cli on the influx host: 

```influx -precision rfc3339 -database 'historisch' -execute 'SELECT * from Current_AC_Phase_1' -format csv >Current_AC_Phase_1.csv```

The csv looks like: 
```
name,time,Current_AC_Phase_1
Current_AC_Phase_1,2017-03-31T17:40:00Z,0.05
Current_AC_Phase_1,2017-03-31T17:45:00Z,0.15
Current_AC_Phase_1,2017-03-31T17:55:00Z,0
Current_AC_Phase_1,2017-03-31T18:00:00Z,0
```

The following command will insert the file "Current_AC_Phase_1.csv" to Server "192.168.3.240" on Port "8087" which holds the dbname "historisch" with token "xxxxxxx" and the org "test", bucket "historisch", metricname "Current_AC_Phase_1", fieldcolumn "Current_AC_Phase_1"

```python csv-to-influx.py -i Current_AC_Phase_1.csv -s 192.168.3.240:8087 --dbname historisch --token "xxxxxxxxxxxxxxxxxx" --org test --bucket historisch -m Current_AC_Phase_1 --fieldcolumns Current_AC_Phase_1 -tc time -tf "%Y-%m-%dT%H:%M:%SZ" -g```
