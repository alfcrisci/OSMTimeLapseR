---
layout: post
title: Check Visualization Assumptions
published: true
status: publish
permalink: examples/check-assumptions.html
---
 
OSMTimeLapseR creates a time lapse of OSM node-level edits based on the `timestamp` attribute. This attribute represents the last time the node was edited, and doesn't include historical data. It also misses out nodes that were deleted. While skipping nodes that were deleted seems totally fine, you might want to check what percentage of history you are representing when making a visualization with OSMTimeLapseR. In this article, we walk through the process of checking this percentage, which will help you decide whether an OSMTimeLapseR based visualization is a rough representation of OSM growth in your community!
 
Lets get started!
---
 
First, lets read both datasets into memory, making sure to read the version of each node.
 

    ## Load the library
    require(OSMTimeLapseR)
    ## You may need to download the file first download.file(url =
    ## 'http://osm-extracted-metros.s3.amazonaws.com/sochi.osm.pbf',
    ## destfile='sochi.osm.pbf') download.file(url =
    ## 'http://osm-extracted-metros.s3.amazonaws.com/kathmandu.osm.pbf',
    ## destfile='kathmandu.osm.pbf')
    
    
    ## We'll use read_OSM to read the datasets explicitly from the .pbf file, and
    ## make sure that @version is included in the columns
    kathmandu_2013 = read_OSM("kathmandu.osm.pbf", columns = "@lat @lon @timestamp @version")
    sochi_2013 = read_OSM("sochi.osm.pbf", columns = "@lat @lon @timestamp @version")

 
Okay, great! Lets check how many of the nodes in Kathmandu (and Sochi) were actually only ever edited once (and therefore, are still at version 1).
 

    cat(100 * mean(kathmandu_2013$version == 1), "%")

    ## 86.58 %

    cat(100 * mean(sochi_2013$version == 1), "%")

    ## 66.97 %

 
For Kathmandu, 90% of the nodes have only been edited once! This is because Kathmandu's OSM community is fairly young. It also means that using just the last timestamp to visualize edits over time doesn't seem that bad!
 
On the other hand, only 2/3rd of the points in our visualization in Sochi were edited once. This seems much worse. We probably want to see what percentage of all edits show up in our visualization then?
 

    # nrow(kathmandu_2013) gives the number of nodes we have
    # sum(kathmandu_2013$version) gives us the total number of edits on nodes
    # (ignoring deletes)
    cat(100 * nrow(kathmandu_2013)/sum(kathmandu_2013$version), "%")

    ## 81.63 %

    cat(100 * nrow(sochi_2013)/sum(sochi_2013$version), "%")

    ## 62.06 %

 
This is pretty close to the earlier result. Again, for Kathmandu, we are representing about 86% of historical edits (ignoring deletion), and for Sochi, the number is closer to 62%. If you are visualizing a different area or city, you should run similar code on your city, see what percentage of history you are capturing, and decide whether that is acceptable for you!
