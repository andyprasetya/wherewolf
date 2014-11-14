Wherewolf
=========

A server-less boundary service. Find what geographic feature (e.g. an election district) a given point lies in.

[Very brief example code]

[blah blah blah intro]

[examples of when we use it at WNYC]

[To locate an address instead of a lat/lng, you need to geocode it first.  see the examples.]

[including in browser]

[installing via npm]

# Dependencies

If you're using it in the browser, and your data is GeoJSON, there are no dependencies.

If you're using it in the browser, and your data is TopoJSON, include the topojson library first.

# API

## Wherewolf.add(layerName,features[,key])

## Wherewolf.addAll(topology)

## Wherewolf.find(point[,options])

## Wherewolf.get(layerName)

## Wherewolf.remove(layerName)

## Wherewolf.layerNames()

# Examples

* Basic
* Multiple layers
* Add all TopoJSON objects at once
* Options
* Geocode an address
* New York boundary service
* Use as a Node module

# Performance

As a stress test of performance, we tried adding all 3141 counties in the United States as a Wherewolf layer and then searching it for 5000 random nearby points.  In most instances, finding the containing feature for a point took less than a tenth of a millisecond.  In the worst case, it took about 8 milliseconds.

* **Chrome 38 (OS X, Macbook Pro):** 53ms to initialize, 0.08ms to search each point
* **Safari 7.0.2 (OS X, Macbook Pro):** 11ms to initialize, 0.07ms to search each point
* **Firefox 33 (OS X, Macbook Pro):** 23ms to initialize, 0.07ms to search each point
* **Chrome 34 (Android 4.4.4, Nexus 5):** 53ms to initialize, 0.72ms to search each point
* **IE9 (Windows 7):** 32ms to initialize, 0.62ms to search each point
* **IE10 (Windows 7):** 20ms to initialize, 0.54ms to search each point
* **Firefox 29 (Android 2.3.4):** 267ms to initialize, 8.2ms to search each point
* **Mobile Safari (iOS 7, iPhone 5C):** 48ms to initialize, 1.1ms to search each point
* **Chrome 38 (iOS 7, iPad Mini):** 53ms to initialize, 1.74ms to search each point

The main performance limitation when using Wherewolf is the size of the GeoJSON or TopoJSON files you have to load in before you can search.  But you can get these files pretty small by a) converting to TopoJSON, b) removing extraneous attribute info, and c) gzipping.  Even the file of all the counties in the US is only 78k as gzipped TopoJSON, which will download in about 1.8 seconds on a 3G connection, or 73 milliseconds on a 30mbps Wifi connection.

If you have concerns about initial load time due to file size, a fancy approach would to be divide the features into subsets and only initially load a GeoJSON file with the bounding box for each subset.  For example, you could have `northwest-us.topojson`, `southwest-us.topojson`, `northeast-us.topojson`, and `southeast-us.topojson`, and `quadrants.topojson`.  Then you could do something like:

    ww.add("quadrants",quadrants);

    //When you need to search...
    var quadrant = ww.find([lng,lat],{layer:"quadrants"});

    //If they are in one of the four rectangles,
    //Load that file and search it
    //You're only loading/search 25% of the counties
    if (quadrant.name) {
      $.getJSON(quadrant.name+".topojson",function(counties){
        ww.add("counties",counties);
        var theCountyTheyAreIn = ww.find([lng,lat],{layer:"counties"});
      });
    }

In this way, you load very little data up front, but the downside is you introduce a bit of extra delay when they search.

# Fine print

* This will probably not work for a feature that crosses the North or South Pole.
* This may not work for a very special case of a point that lies right on the antimeridian being checked against a feature that crosses the antimeridian. It's unclear whether any scenario on the actual earth can cause this problem.  Don't worry, the Aleutian Islands work fine.
* [Geo data precision]

# Credits/License

By [Noah Veltman](https://twitter.com/veltman) and [Jenny Ye](https://twitter.com/thepapaya)

Special thanks to:

* [WNYC](http://www.wnyc.org/)
* [OpenNews](http://opennews.org) - this was released as part of the November 2014 OpenNews Code Convening
* [Substack](https://github.com/substack) for his [point-in-polygon module](https://github.com/substack/point-in-polygon)
* [Mike Bostock](https://github.com/mbostock) for [TopoJSON](https://github.com/mbostock/topojson)

Available under the MIT license.

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.