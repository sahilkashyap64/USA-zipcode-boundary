# USA-zipcode-boundary
How to get USA zipcode boundaries
[Link to article](https://dev.to/sahilkashyap64/how-to-get-usa-zipcode-boundaries-and-show-them-on-map-mapbox-1gm1)

## step 1:
[download cb_2018_us_zcta510_500k.zip](https://www.census.gov/geographies/mapping-files/time-series/geo/carto-boundary-file.html)

#### if you want to keep them in mysql
In your mysql create a db of name :spatialdata

*EPSG:4683 this is epsg is for philiphines i should have used USA epsg that is EPSG:2163. Doesn't matter cuz it still worked*

run this command
```
ogr2ogr -f "MySQL" MYSQL:"spatialdata,host=localhost,user=root" -nln "map" -a_srs "EPSG:4683" cb_2018_us_zcta510_500k.shp -overwrite -addfields -fieldTypeToString All -lco ENGINE=MyISAM
```
ogr2ogr is a tool
i uploaded the file on github [USAspatialdata.zip](https://github.com/sahilkashyap64/USA-zipcode-boundary/blob/master/USAspatialdata.zip)

In your "spatialdata db" there will be 2 table named map & geometry_columns .

1. In 'map' there will be a column named "shape".
shape column is of type "geometry" and it contains polygon/multipolygon files

2. In 'geometry_columns' there will will be srid(in my case it was 4683) defined

how to check if point falls in the polygon
```
SELECT * FROM map WHERE ST_Contains( map.SHAPE, ST_GeomFromText( 'POINT(63.39550 -148.89730 )', 4683 ) )
```
and want to show boundary on a map
```
select `zcta5ce10` as `zipcode`, ST_AsGeoJSON(`SHAPE`) sh from `map` where ST_Contains( map.SHAPE, ST_GeomFromText( 'POINT(34.1116 -85.6092 )', 4683 ) )`
```
"ST_AsGeoJSON" this returns spatial data as geojson.
Use http://geojson.tools/

## if you want to generate topojson

### mapshaper converts shapefile to topojson (no need to convert it to kml file)
```
npx -p mapshaper mapshaper-xl cb_2018_us_zcta510_500k.shp snap -simplify 0.1% -filter-fields ZCTA5CE10 -rename-fields zip=ZCTA5CE10 -o format=topojson cb_2018_us_zcta510_500k.json
```
## If you want to convert shapefile to kml

```
ogr2ogr -f KML tl_2019_us_zcta510.kml -mapFieldType Integer64=Real tl_2019_us_zcta510.shp
```
I have used mapbox gl to display 2 zipcodes

example: [Map I made](https://sahilkashyap64.github.io/USA-zipcode-boundary/)

example 2 [with states,counties,zipcode](https://sahilkashyap64.github.io/USA-zipcode-boundary/index2) (credits to this map : [Darssh](https://github.com/Darssh))
he used api ,uploaded the data in mapbox and then fetched it

example 3 [Using turf js to check if coordinates fall in geofence](https://sahilkashyap64.github.io/USA-zipcode-boundary/mapbox+turf2.html)

[Converted turf.js to php function](https://dev.to/sahilkashyap64/turf-js-to-php-o1a)

you'll find code here :https://github.com/sahilkashyap64/USA-zipcode-boundary

If there are more ways(opensource) to do this kindly share them


