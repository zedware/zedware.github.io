---
layout: post
title: Making Use of Boost Geometry in MySQL GIS
original: https://blog.csdn.net/zedware/article/details/23378005
---
看样子MySQL还是打算改进GIS支持。

#Making Use of Boost Geometry in MySQL GIS
[April
 9, 2014](http://mysqlserverteam.com/making-use-of-boost-geometry-in-mysql-gis/ "Permalink to Making Use of Boost Geometry in MySQL GIS")[GIS](http://mysqlserverteam.com/category/gis/ "View all posts in GIS"), [MySQL](http://mysqlserverteam.com/category/mysql/ "View all posts in MySQL")[Boost](http://mysqlserverteam.com/tag/boost/), [GEOMETRY](http://mysqlserverteam.com/tag/geometry/), [GIS](http://mysqlserverteam.com/tag/gis/), [OGC](http://mysqlserverteam.com/tag/ogc/), [SPATIAL](http://mysqlserverteam.com/tag/spatial/), [WKB](http://mysqlserverteam.com/tag/wkb/), [WKT](http://mysqlserverteam.com/tag/wkt/)[David
 Zhao](http://mysqlserverteam.com/author/david/ "View all posts by David Zhao")This post talks about how we make use of Boost.Geometry in MySQL to implement reliable and efficient GIS functionality, as well as changes to the GIS features in
 the [lab release](http://labs.mysql.com/).
###Prerequisite
This article assumes the reader knows about the basic geometry database concepts defined by the OGC. That includes WKT, WKB, the 8 kinds of spatial relationship checks—contains, within, intersects,
 disjoint, crosses, touches, overlaps, and equals—along with the 4 types of spatial set operations—intersection, union, difference, and symdifference. You can find a list of OGC Simple Feature Access standards, along with additional information on the aforementioned
 topics [here](http://www.opengeospatial.org/standards/sfa).
###Terms & Abbreviations
BG: Boost.Geometry
CCW: Counter-clockwise
CW: Clockwise
GIS: Geographic Information System
OGC: Open Geospatial Consortium
SRID: Spatial Reference Identifier
WKB: Well Known Binary geometry representation
WKT: Well Known Text geometry representation
Now that InnoDB is the default storage engine for MySQL, our user base is rapidly transitioning to InnoDB. One capability that they have been demanding is a performant and scalable GIS implementation.
 Along with adding R-tree index support to InnoDB, we also decided to replace the original GIS algorithms with a more powerful, reliable, effective and efficient geometric engine. After many comparisons of the available open source geometry engines on multiple
 aspects, BG was finally chosen. Please see [here](http://mysqlserverteam.com/why-boost-geometry-in-mysql/) for additional information on how we came to choose Boost.Geometry.
So far the implementation covered in this article is only in the [“April” MySQL
 lab release](http://labs.mysql.com/), it is not available in a MySQL 5.7 DMR just yet. In the lab release, what has changed is the internal geometric algorithms used for GIS computations in the spatial relation check functions (e.g. st_contains, etc.) and the spatial set operations
 (e.g. st_intersection, etc.). They are now implemented using Boost.Geometry, and these GIS functions are expected to work more reliably, precisely, and efficiently than previous versions of MySQL. This also means that MySQL now depends on the Boost library.
 So far we only depend specifically on Boost.Geometry, but users will still need the complete Boost library in order to build the MySQL lab release. For additional information on how to build MySQL with Boost.Geometry, please see [here](http://mysqlserverteam.com/building-mysql-with-boost/).
Also, now unclosed polygon rings coming from user data are automatically closed before they are stored or provided to other GIS functions, and this is backward compatible in terms of historical
 user data because previous versions of MySQL simply didn’t accept polygons with open rings. Additionally, now binary GIS functions require arguments with identical SRID values; this allows for future work where we will support the Geography type, and standard
 spatial reference systems like WGS 84. And finally, users can also now use spatial indexes with the InnoDB storage engine for better query performance. Other aspects of MySQL GIS remain the same as in previous versions to end users, including the GEOMETRY
 data format, and all the spatial functions.
###Challenges
Boost.Geometry is an excellent geometric engine which can do many kinds of geometry computations with standard OGC defined geometries, it has good performance and reliablity, and it has an active
 and vibrant development community. As great as BG is though, we couldn’t make use of it very easily in MySQL, mainly because of the following challenges:
1. BG currently only reads WKT geometry data, and outputs geometry results as WKT as well. But in MySQL GIS, a geometry’s internal data format is the `GEOMETRY` format,
 which is a blob consisting of an SRID (4 byte unsigned integer) followed by its WKB byte string, and all the integers and doubles inside the GEOMETRY blob are little endian. This format is both the durable storage format within the storage engines, and the
 in-memory format when a geometry object is passed around Item_xxx nodes during query execution. We don’t want to be constantly doing conversions between WKT and WKB when passing arguments to BG functions, and when receiving results from them. Instead (for
 performance reasons) we want to always operate in `GEOMETRY` format everywhere, including within BG, and without doing any unnecessary conversions or duplication of data.
2. BG uses exceptions to indicate errors to its caller/user. MySQL on the other hand has not historically used C++ exceptions.
3. BG uses a generic programming paradigm, just as the entire Boost library does. BG is mostly composed of function templates and class templates, so it’s not OK to use the dynamic polymorphism
 paradigm here, we instead have to statically dispatch geometry objects of each type to the right version of each BG spatial function call.
4. BG doesn’t have a ‘geometry collection’ type, which is defined by the OGC, so we had to implement support for geometry collections in MySQL using BG’s existing features.
5. BG doesn’t support all types of arguments in some spatial functions, so we had to adapt to this in the short term (until we can make further improvements to BG itself, and share that upstream).
And finally, we had to merge the support for BG into the existing MySQL GIS implementation, by keeping the useful and good components of the original implementation, while removing the old geometric
 algorithms.
###Approaches
How were we able to meet these challenges and make use of BG in an efficient and compatible (with MySQL) way?
For challenge #1, we expanded the existing geometry classes so that they can be used by BG.
BG defines a clean interface for geometry data in the form of concepts. Each type of geometry is defined as a concept, so we have point, linestring, polygon, multipoint, multilinestring and multipolygon
 concepts in BG. See [here](http://www.boost.org/doc/libs/1_55_0/libs/geometry/doc/html/geometry/reference/concepts.html) for additional details.
All BG algorithms manipulate geometry data via these concepts, so that not only BG’s own model of these geometry concepts can be used, but also any other existing GIS implementation can make
 use of BG algorithms as long as it implements these geometry concepts properly. Each BG algorithm is a function template, and BG uses traits or meta functions to access the type information as well as interface function templates defined for the function’s
 type argument. An implementation of such concepts would need to implement the traits classes, as well as defining a few member function templates for existing geometry classes.
We do so in MySQL by expanding our existing geometry classes, namely Gis_point, Gis_line_string, Gis_polygon, Gis_multi_point, Gis_multi_line_string, and Gis_multi_polygon. Firstly we needed
 to add a few member functions for Gis_point and Gis_polygon, then we needed to define a few trait classes for all of the geometry classes, as required by the concepts above, see gis_bg_traits.h for our used traits definitions; also, BG’s linestring, multipoint,
 multilinestring and multipolygon concepts all use the Boost.Range concept, so we also needed to implement a model of the Boost.Range concept. So we implemented a class template called Gis_wkb_vector, whose instantiations are now the new parent classes for
 the Gis_line_string (Gis_wkb_vector), Gis_multi_point (Gis_wkb_vector), Gis_multi_line_string (Gis_wkb_vector), and Gis_multi_polygon (Gis_wkb_vector) classes, the bracketed type is the parent class for each. The code for the Gis_wkb_vector class template
 can be found in gis_wkb_vector.h.
As before, the Geometry class is the root base class for all concrete geometry classes mentioned above, all data members are defined there, so that all geometry instances are of equal size. In
 the Geometry class, we now hold the WKB buffer address and length, along with other geometric information about this geometry instance. In the Geometry class, we also now have a vector of components of the geometry. For example, for a linestring its components
 are points. We hold the components for fast access, which is required by BG algorithms. Point and polygon concepts are different, as defined by BG, they don’t use the Boost.Range concept. Beyond that, BG assumes that a polygon consists of an outer ring object
 and an ‘inner rings’ object, which contains a vector of inner rings (BG sees a ring as different from a linestring).
The Gis_wkb_vector class template is the key interface between BG and MySQL. It is used for BG to read WKB data from instances of Gis_xxx classes from MySQL (in read-only mode), to write WKB
 data from BG into instances of these classes (write mode), and to read geometry data from them (read-write mode). BG also uses such instances during geometric computation in its various internal algorithms in read-only and/or read-write mode. The principles
 of designing this class template is performance — we want to avoid unnecessary data copies and conversions, as well as repetitive WKB parsing. Note that WKB data tends to be large, often a few dozen to a few hundred bytes for a single geometry, or even larger.
 A standalone 2D point is 25 bytes (SRID of 4 bytes, WKB header of 5 bytes, and 2 double precision coordinates of 16 bytes each), a 2D linestring containing 5 points takes 93 bytes. In read-only mode, no WKB data is copied; in read-write mode, a copy is only
 done at WKB buffer reallocation when a WKB buffer isn’t large enough to fit more data. And WKB data is always kept as a single valid WKB byte string to avoid reassembling them when handing back the geometry result to MySQL. WKB data are of different lengths
 and are often nested, e.g. imagine a multilinestring’s WKB data. This makes read and write access less than straight forward, as detailed below.
When used in read-only mode, a Gis_wkb_vector instance is created using an existing WKB buffer, which often comes from MySQL Item_xxx::val_str. The WKB is parsed once so that the internal structure
 is set up. For example given a WKB of a multilinestring X, in Gis_multi_line_string’s constructor, X’s WKB is parsed, all linestrings within X are stored into X.m_geo_vect which is a vector of Geometry objects; and recursively in each of the linestring objects,
 i.e. Gis_line_string object L, the Gis_point objects are stored into L.m_geo_vect, the Gis_multi_line_string X is ultimately constructed as a tree of Geometry objects, where the root node is X, and X has a few Gis_line_string child nodes, each of which has
 some Gis_point nodes. Each of the nodes refer to different positions of the same WKB buffer of X. No WKB data is copied in read-only mode, all these Gis_xxx objects do is read-only access to the WKB data (often used as input arguments to BG algorithms).
In read-write mode, a geometry object is created empty, then geometry data is written into the object piece by piece, via Gis_wkb_vector::push_back, Gis_wkb_vector::resize and Gis_point::set
 functions (by BG algorithms or MySQL GIS code). These functions are the only ways needed by BG, and supported in our implementation, to modify a geometry. Why is ‘resize’ also used?
The iterators of the Boost.Range concept are read-only in BG, except when the range contains all points. A read-only iterator is never used to modify/delete an existing element in the range. Typically BG writes data into a Boost.Range instance via the ‘push_back’
 function, which appends a geometry object into the range, and geometry components already in a range are never updated unless the range is a point range. A point range can be updated via an iterator at any position of the range, and this is OK since points
 are of equal size and have no components. In our implementation, besides the geometry object append, we also append its WKB data into the owner’s WKB buffer and increment the ‘geometry count’ field in the WKB byte string, so that all components always refer
 to different positions of the same piece of the WKB buffer.
When the geometry object is completely created, its WKB data can be directly used without reassembling the WKB pieces of the geometry components by MySQL, to pass to other Item_xxx nodes during query execution.
The polygon concept is an exception to the ‘single WKB buffer, no reassembling’ rule, because BG assumes a polygon has an outer ring and an ‘inner rings’ object. And BG modifies them separately
 without any defined order, which means we have to keep the two geometry objects’ WKB buffers separate, each maintaining its own WKB buffer. Before handing over the polygon to MySQL, we have to reassemble the polygon’s two parts into a single WKB buffer.
BG also updates the inner rings of a polygon differently — it has to first resize the ‘inner rings’ object by appending exactly one empty ring into it, then call ‘push_back’ to append points
 into the newly appended ring. That’s why Gis_wkb_vector::resize may also be used in an update, and we must always make the polygon’s inner rings use one piece of a WKB buffer.
The implementation of the above interface also ensures that BG gets integers/doubles of local endianness, and BG writes such data elements into a geometry’s WKB buffer as little endian, which
 is MySQL’s portable format for all data.
With the above design and implementation, support for BG is merged into the existing MySQL GIS architecture. We use `WKB/GEOMETRY` data
 internally everywhere, we use the same group of Geometry classes whose instances can be used both by BG and by MySQL seamlessly, and BG algorithms get geometry data from our Geometry objects via BG’s defined interface.
The rest of the challenges are easily resolved when we have everything above.
For #2, we catch Exceptions in each Item_xxx::val_xxx function where BG functions are called, to make sure any Exception that can be thrown by BG is caught and properly handled using the MySQL
 error handling facilities. We use std::auto_ptr to do resource management in contexts where exceptions may be thrown (this latest change isn’t pushed to this lab release though). I’d also like to take this opportunity talk a bit more about error handling in
 MySQL GIS regarding user data validation. Firstly, we now have more robust checks for invalid `GEOMETRY/WKB` data input, so that the server is able to handle almost any invalid binary input properly,
 by rejecting such data.  Also, BG’s polygon concept requires defined ring orientation and closedness, and we use counter-clockwise (CCW) orientation and closed rings, that means that the outer ring must be CCW and inner rings (if any) must be clockwise and
 all rings must be closed. If the input polygon data (in the form of WKT or WKB) isn’t like this, it’s automatically reversed on the fly and closed permanently (in the permanently stored data within tables, the rings are not reversed but they are closed). This
 means the polygons stored in tables may not be literally (in WKB/WKT form) identical to the input data, although they are equivalent. Historical data will work correctly, and unmodified, because older versions of MySQL don’t accept polygons with open rings,
 so all polygons in existing data all have closed rings. This new functionality better provides users with pre-normalized polygon rings in order to avoid extra CPU cycles to normalize them. The check for ring orientation can also identify some invalid rings
 as a side effect, e.g. some of those with spikes, or polygon rings which degrade to linestrings. Note that although users can provide `GEOMETRY` data directly to MySQL, we don’t internally close
 rings of polygons provided in the form of a `GEOMETRY` format/object (the rings will be reversed on the fly just as WKT/WKB data), or do any endianess conversions, as we believe it’s valid in every
 way, because the `GEOMETRY` format is MySQL’s internal geometry data format. If users want to directly use this format, then they are required to provide valid data from clients, otherwise GIS
 algorithms may not work correctly.
Finally, if a user explicitly specifies an SRID value when creating a geometry, we now require the two arguments have identical SRIDs. Since SRID usage isn’t effective yet, it’s advised that
 users don’t explicitly specify any SRID values. Again, the SRID usage here is really to support future work when we add support for Geography types and standard spatial coordinate systems like WGS 84. If you specify an SRID value, its max value is 0xFFFFFFFF,
 and values larger than this are truncated to use the lower 4 bytes as a 32bit unsigned integer SRID value.
For #3, we have to do type specific dispatches to call the right version of BG functions — all BG algorithms used so far in MySQL are function templates which require input arguments of an exact
 geometry type, and we can’t pass a Geometry pointer directly into such functions, so we have to create a specific type of geometry object using WKB data and pass it to the BG functions according to their geometric types.
For #4, we have implemented support for geometry collections using existing features in BG according to OGC defined semantics for each spatial operation. Geometry collection support is very important
 because the set operations, i.e. intersection/union/difference/symdifference, will often produce geometry collections. If geometry collections are not supported, users would not generally be able to call GIS functions in any nested way, e.g. this query: “select
 st_within(st_union(GeomFromText(‘point(1 1)’), GeomFromText(‘linestring(3 3, 4 4)’)), GeomFromText(‘polygon((0 0, 5 0, 5 5, 0 5, 0 0))’))” would not be supported without geometry collection support. The detailed algorithms about such support won’t be covered
 in this article, but it may be covered in a separate article in future.
For #5, we have dedicated BG developers to implement missing features in BG, which will also be contributed back upstream and included in future BG releases. Before all such new features are
 available, we have to fall back to old MySQL GIS algorithms to do geometric computation when BG doesn’t support a specific type or type combination. For example, currently BG can’t do LINESTRING WITHIN LINESTRING computations, and when it’s called by a query,
 old GIS algorithms are used. This also means that although many old GIS bugs are gone now, some old GIS bugs will have to exist yet for a while. When all missing features are available in BG, we can remove old GIS algorithms entirely and the old GIS bugs which
 were caused by bugs of old GIS algorithms are expected to disappear completely. Our goal is to cover all seven geometry types (as defined by OGC) in all unary geometry functions, and cover all type combinations (7*7=49) for all binary geometry functions, so
 that any nested GIS queries can be executed.
Note that for some binary GIS functions, some type combinations are inapplicable or not supported, and we return false or NULL respectively for such cases.
Undefined/inapplicable type combinations for each function are:
- overlap: any combination of two argument types of different dimensions
- crosses: any of the two argument types is point or multipoint
- touches: the 1st argument is polygon or multipolygon, AND/OR the 2nd argument is point or multipoint

Unsupported type combinations for each function are: cross/overlaps/touches with one or both geometry collection argument(s).
Finally, there are some type combinations for some geometry functions that are not directly supported by BG but can be easily implemented using BG’s existing features, e.g. multipoint DISJOINT
 multipoint, for such cases we have implemented them in MySQL and BG won’t have to directly support them.
Using all of the above strategies, we are now able to seamlessly make use of BG efficiently and reliably in MySQL, while also making good use of historical MySQL GIS code as much as possible,
 in order to avoid reinventing the wheels.


