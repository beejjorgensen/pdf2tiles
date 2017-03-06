pdf2tiles
=========

Bash scripts to convert USGS or USFS GeoPDFs to tiles suitable for
serving via TMS.

*Note: this is just a demo of how to do this. It's not production code,
it's just quick-n-dirty scripts, and I'm not particularly proud of it.
:) I'm just putting it here in case it helps someone else with the
process.*


Usage
-----

Feed it GeoPDFs:

	pdf2tiles *.pdf

It automatically puts the tiles in `~/public_html/gt2tiles/usfs/` or
`~/public_html/gt2tiles/usgs/` depending on which type of map you feed
it.

`pdf2tiles` is just a wrapper around two other scripts.

`pdf2gt` converts a GeoPDF to a GeoTIFF:

	pdf2gt foo.pdf

`gt2tiles` turns a GeoTIFF into tiles:

	gt2tiles foo.tif subdir


TMS Source URL
--------------

Your TMS source URL will be something like one of these, depending on
your local webserver setup:

	http://localhost:8000/{zoom}/{x}/{-y}.png

or

	http://localhost/~user/gt2tiles/usfs/{zoom}/{x}/{-y}.png


Dependencies
------------

You'll need the following installed:

* [GDAL](http://www.gdal.org/)
* [ImageMagick](https://www.imagemagick.org/)

If you want to use the tiles with [JOSM](https://josm.openstreetmap.de/)
or somesuch, you'll need a webserver running. If you don't have one, you
can run one in the root directory of the map tiles with Python which
will listen on port 8000:

Python 2.x:

	python -m SimpleHTTPServer

Python 3.x:

	python -m http.server 8000


Description
-----------

These scripts are just wrappers around GDAL and ImageMagick. Those tools
do 100% of the heavy lifting.

The only weird part was blending the existing tiles with newly-generated
ones. When new tiles are generated that would overwrite existing ones
(e.g. they're the same tile, but just the west or east portion),
ImageMagick (`composite`) is used to blend the two images together.

**NOTE**: These scripts only work with modern, layered GeoPDFs. More
work is required to extract historical bitmap PDFs.


JOSM
----

I wanted to be able to use these maps with
[JOSM](https://josm.openstreetmap.de/) since my usual topo map WMS
sources all seem to be down. Let me know if you have one that works!


Hacking
-------

### Changing DPI

If you want higher-res images, you'll have to mod `pdf2gt`. Look for
`DPI` and also see the note titled "Set up the clipping region".

### More/fewer zoom levels

Change the `ZOOMS` variable in `gt2tiles`. If you want to zoom in
farther, you'll need to also up the resolution of the image, per above,
otherwise you'll just end up with pixelated tiles when zoomed in.

### Different types of maps other than USGS/USFS

Map types are detected by looking for telltail layer names. This happens
in both `pdf2tiles` and `pdf2gt`.

Layer names can be viewed with:

	 gdalinfo foo.pdf -mdd layers 

### Supporting maps without layers (old bitmap maps)

I haven't done this at all, but it at least involves keying off
something besides the layers (since they don't contain the same layers).

There's also some geospatial metadata that seems to get lost. I didn't
follow up since I'm not looking to use those maps in my work.

I'll take pull requests, though. :)


Map Sources
-----------

Links to maps:

* [USFS](https://data.fs.usda.gov/geodata/rastergateway/states-regions/states.php)
* [USGS (National Map)](https://viewer.nationalmap.gov/launch/) (flaky, but usually working)
* [USGS (store)](https://store.usgs.gov/b2c_usgs/usgs/maplocator/%28xcm=r3standardpitrex_prd&layout=6_1_61_48_1&uiarea=2&ctype=catalogEntry&carea=%24ROOT%29/.do) (broken?)

Again, only layered PDFs work with these scripts at the moment.


References
----------

* [How to convert GeoTIFF to tiles](https://nationalmap.gov/ustopo/documents/ustopo2gtif_current.pdf)
* [Using tiles with JOSM](http://wiki.openstreetmap.org/wiki/GDAL2Tiles)


License
-------

All code herein is hereby granted to the public domain.

