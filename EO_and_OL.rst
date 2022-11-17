****************************************************************************
WebMapping & Remote Sensing
****************************************************************************

1.1. Introduction
=================

This is an activity to discover how to visualize remote sensing images and perform band operations usign the webmapping library **OpenLayers**.
For that purpose, we are going to use `WebGLTile <https://openlayers.org/en/latest/apidoc/module-ol_layer_WebGLTile-WebGLTileLayer.html>`_, an OpenLayers component to add support to WebGL GeoTIFF images render.

In relation to the images, we are going to work with Sentinel-2, offered by `Copernicus <https://www.unigis.es/copernicus-observacion-tierra/>`_.




1.2. How to obtain the images
==================================

We are going to use the `Amazon Web Services <https://registry.opendata.aws/sentinel-2/>`_ cloud , with the complet copernicus image catalog at your disposal.

In order to identify the path to a certain image, the `AWS Command Line Interface <https://aws.amazon.com/cli/>`_ is available. The basic parameters to find an image using this command line interface are the **acquisition date** and **image ID**. This last parameter can be easily obtained from `EO Browser https://apps.sentinel-hub.com/eo-browser<>`_, for example.

AWS CLI is multi platform, so it can be installed on windows, Linux and macOS. The installation process is described on the AWS CLI page: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

Once AWS CLI is installed on the system, and knowing the image acquisition date and it's pixel ID, we can run the following instruction using the command line:


.. code-block:: bash

  aws s3 ls s3://sentinel-cogs/sentinel-s2-l2a-cogs/31/T/DG/2022/7/ --no-sign-request


This is a query to obtain the path to scenes over Catalonia on July 2022.

The path to band 4 of one of this scenes, is:

.. code-block:: bash

  https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/31/T/DG/2021/2/S2A_31TDG_20210201_0_L2A/B04.tif


And to obtain the natural color composition, we could use:

.. code-block:: bash

  https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/31/T/DG/2021/2/S2A_31TDG_20210201_0_L2A/TCI.tif


1.3. OpenLayers
=================

As indicated, we are going to use OpenLayers library to interactively visualize the images in a web environment.

First of all, we must create a `node <https://nodejs.org/en/>`_ project and install OpenLayers library and `parcel-bundler <https://github.com/parcel-bundler/parcel#readme>`_.

.. code-block:: bash

  $npm install ol
  $npm install parcel-bundler

Then, create a file **index.html** with the html code of the application, and another file **main.ja** with the javascript code:

Also configure the document *package.js* with *start* and *build* functions:

.. code-block:: javascript

  {
    "name": "post_ol_satellite",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "start": "parcel index.html",
      "build": "parcel build --public-url . index.html"
    },
    "author": "josep sitjar",
    "license": "ISC",
    "dependencies": {
      "ol": "^6.14.1",
      "parcel-bundler": "^1.12.5"
    }
  }

This could be the html with the page structure:

.. code-block:: html

  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>OpenStreetMap Reprojection</title>
    <!-- Pointer events polyfill for old browsers, see https://caniuse.com/#feat=pointer -->
    <script src="https://unpkg.com/elm-pep@1.0.6/dist/elm-pep.js"></script>
    <!-- The lines below are only needed for old environments like Internet Explorer and Android 4.x -->
    <script src="https://cdn.polyfill.io/v3/polyfill.min.js?features=fetch,requestAnimationFrame,Element.prototype.classList,TextDecoder"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/core-js/3.18.3/minified.js"></script>
    <style>
      .map {
        width: 100%;
        height:100vh;
      }
    </style>
  </head>
  <body>
    <div id="map" class="map"></div>
    <script src="main.js"></script>
  </body>
  </html>

It's a very simple page, as for now we just need to show the images, with no other additional functionality.

And finally, on the *main.js* document, we'll add the necessary javascript code to create the map using OpenLayers and display the image in natural color for one of the Sentinel-2 scenes previously identified.

.. code-block:: javascript

  import 'ol/ol.css';
  import Map from 'ol/Map';
  import OSM from 'ol/source/OSM';
  import TileLayer from 'ol/layer/WebGLTile';
  import View from 'ol/View';
  import GeoTIFF from 'ol/source/GeoTIFF';

  // Almacenamos la fuente de datos en formato GeoTIFF
  // Een este caso, una imagen Sentinel-2 en color natural
  const source = new GeoTIFF({
    sources: [
      {
        url: 'https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/31/T/DG/2021/2/S2A_31TDG_20210201_0_L2A/TCI.tif',
      },
    ],
  });

  // Creamos la capa TileLayer a partir de la fuente de datos creada anteriormente
  const layer = new TileLayer({
    source: source,
  });

  const map = new Map({
    layers: [
      // Añadimos la capa (layer) al mapa
      layer
    ],
    target: 'map',
    view: source.getView(),
  });


Visualize the result


1.4. Fals Color composition
============================

From the previous code, it's not complicated to visualize the same scene in false color, using for example the bands relative to **NIR** (band number 8), **Red** (band number 4) and **Green** (band number 3).

Just add this bands to GeoTIFF object:

.. code-block:: javascript

  const source = new GeoTIFF({
    sources: [
      {
        // banda correspondiente al NIR
        url: 'https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/31/T/DG/2021/2/S2A_31TDG_20210201_0_L2A/B08.tif',
        max: 5000,
      },
      {
        // banda correspondiente al Rojo
        url: 'https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/31/T/DG/2021/2/S2A_31TDG_20210201_0_L2A/B04.tif',
        max: 5000,
      },
      {
        // banda correspondiente al Verde
        url: 'https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/31/T/DG/2021/2/S2A_31TDG_20210201_0_L2A/B03.tif',
        max: 5000,
      },
    ],
  });

Visualize the result


1.5. Vegetation indices
============================

**WebGLTile** layers accept the **style** property, which can be used to control the rendering of the data source, and also apply mathematical expressions.
This will be useful to create and represent on the fly the NDVI values.

It's necessary to modify the layers of the data source, adding the necessary ones for the NDVI index (RED and NIR).

.. code-block:: javascript

  const source = new GeoTIFF({
    sources: [
      {
        url: 'https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/31/T/DG/2021/2/S2A_31TDG_20210201_0_L2A/B04.tif',
        max: 10000,
      },
      {
        url: 'https://sentinel-cogs.s3.us-west-2.amazonaws.com/sentinel-s2-l2a-cogs/31/T/DG/2021/2/S2A_31TDG_20210201_0_L2A/B08.tif',
        max: 10000,
      },
    ],
  });


And also add the **style** property to WebGLTile layer:

.. code-block:: javascript

  const layer = new TileLayer({
    // indicamos la fuente de datos
    source: source,
    // añadimos la propiedad 'style'
    style: {
      color: [
        // aplicamos una interpolación
        'interpolate',
        ['linear'],
        // expresión para calcular el NDVI a partir de las bandas de la fuente de datos
        ['/', ['-', ['band', 2], ['band', 1]], ['+', ['band', 2], ['band', 1]]],
        // asignación de colores a cada valor de la capa NDVI
        -0.2, // a los valores ndvi <= -0.2 se les asignará el color RGB 191,191,191
        [191, 191, 191],
        0, // a los valores ndvi entre -0.2 y 0, se les asignará un color resultado de la interpolación entre el color anterior y posterior
        [255, 255, 224],
        0.2,
        [145, 191, 82],
        0.4,
        [79, 138, 46],
        0.6,
        [15, 84, 10],
      ],
    },
  });

Visualize the result. 
