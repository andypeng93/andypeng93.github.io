---
layout: post
title:      "Data Visualizations for Zip Codes"
date:       2020-05-10 22:46:57 +0000
permalink:  data_visualizations_for_zip_codes
---


https://medium.com/@andypeng93/data-visualizations-for-zip-codes-2bbf1e16cab0

How do you make visualizations for features that involve location or zip codes? What happen if you want to display average house prices of different neighborhoods? In this tutorial I will be showing you how to gather the necessary tools and to generate the data visualization below.

To develop the data visualization above we will be using Folium in Python. For the extent of this tutorial we will be using the data set with sale prices for the King County Area. The data set that we will use for coding purposes can be found in this link. This data set was used for our Housing Price project at the Data Science Boot Camp at Flatiron School. For the project we had to do create a model to predict Housing Prices. However if you want to play around with the original data set you can find it through Kaggle.
Getting Started
First we need to import our data set kc_house_data.csv. Assuming our data has already been cleaned, this is how the data should look.
import pandas as pd
df = pd.read_csv('kc_house_data.csv')
dfmap = df[['price', 'zipcode']]
dfmap.head()

The above data frame contains multiple house prices at each unique zip code. Therefore, we need to create a proper data frame containing the information we want. In this new data frame we want a total of four columns. These four columns are average price, zip code, longitude and latitude.
#Create the dataframe containing the data we want.
dfmap.zipcode = dfmap.zipcode.astype(int)
zipcode_means = []
for i in dfmap.zipcode.unique().tolist():
    val = dfmap[dfmap['zipcode'] == i]['price'].mean()
    lat = dfmap[dfmap['zipcode'] == i]['lat'].mean()
    long = dfmap[dfmap['zipcode'] == i]['long'].mean()
    zipcode_means.append([str(i), val, lat, long])
xall = []
yall = []
latall = []
longall = []
for val in zipcode_means:
    xall.append(val[0])
    yall.append(val[1])
    latall.append(val[2])
    longall.append(val[3])
tdf = pd.DataFrame(list(zip(xall, yall, latall, longall)), columns = ['Zipcode', 'Price', 'Latitude', 'Longitude'])
The above code first runs through the dfmap data frame to calculate the mean house prices, latitudes and longitude of each unique zip code.
Mapping
Now that we have our data frame we can begin to develop the base of the map. To build the base of the map, we would run the code below.
import folium
map = folium.Map(location=[47.5112, -122.257], default_zoom_start=15)
map

We now have the base of the map starting at the location with latitude 47.5112 and longitude -122.257. Depending on where you want the map to start, you can change up the location coordinates. Notice how there aren’t any borders representing each zip code in the image above.
To create the borders, we need to find the corresponding geoJSON file for the King County Area. Luckily, I was able to find a KML file that contained the zip codes, shape length and area. However, since the file I downloaded was a KML file, I had to use an online converter to convert KML to geoJSON. Before we start coding we need to go into our geoJSON file to pull out the location of the zip codes in the geoJSON file.


The location of the zip codes in the geoJSON file is ‘feature.properties.ZIPCODE’. Now it is time to input the information we gather into the parameters of the function choropleth.
map.choropleth(geo_data="zip_codes.geojson",
               data=tdf, 
               columns=['Zipcode', 'Price'], 
               key_on='feature.properties.ZIPCODE', 
               fill_color='BuPu',fill_opacity=0.7,line_opacity=0.2,
               legend_name='SALE PRICE')
geo_data is the path to the geoJSON file which has coordinates of zip code areas. In this case it would be the geoJSON file that we have obtained earlier.
data is the data set that we have cleaned up above.
columns represents the columns of the data set that we want to graph. It is important to note whether the Zipcode column is in numeric or string format. Depending on whether the zip codes in your geo_data is in numeric or string format, the Zipcode column in your data needs to match it.
key_on is the path that contains the zip code values in the geoJSON file, which we mentioned above ‘feature.properties.ZIPCODE’.
fill_color is the color schema for displaying the areas.
fill_opacity is the transparency level of the boundary areas when filling in the color.
line_opacity is the transparency level of the boundary lines.
legend_name is the title of the data legend.
Running the above code would result in the map displayed below. We can see that the legend is displayed on the top right corner with the title SALE PRICE. Our choice of color ‘BuPu’ gave us a blue purple color on the map. The black parts of the map represents the zip codes that were in our geoJSON file, but not in our tdf data set.

Next we will get the markers onto our map to display
Zip Code
Average House Price
For this we need a coordinate on the map to display the markers. In case you don’t have the coordinates for it, you can use this link to pull the corresponding zip code’s coordinates. However, for our usage we have already prepared the data frame to use. To display the markers on the map, we will be using the Marker function.
from folium.plugins import MarkerCluster
marker_cluster = MarkerCluster().add_to(map) # create marker clusters
for i in range((tdf.shape[0])):
    location = [tdf['Latitude'][i], tdf['Longitude'][i]]
    tooltip = "Zipcode: {}".format(tdf["Zipcode"][i])
    folium.Marker(location, 
                  popup="""
                  <i>Mean sales price: </i> <br> <b>${}</b> 
                  """.format(round(tdf['Price'][i],2)),
                  tooltip=tooltip).add_to(marker_cluster)
map
location is the latitude and longitude coordinate for the marker.
popup is the text that would be displayed when you click on the marker.
tooltip is the text that would be displayed when you hover over the marker.
After running the code below we can see that when we hover over the marker we display the Zip code. By clicking on the marker it will display the text Mean sales price and the calculated average mean.

We have just learned how to make an interactive data visualizations with zip code data. There are many features in Folium that we haven’t explored yet, which we can explore in the future. For Folium documentation please refer to this link. I hope this tutorial helps you in your future data analysis with data relating to zip codes, latitude and longitude.
