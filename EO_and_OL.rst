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


.. code-block:: console
  aws s3 ls s3://sentinel-cogs/sentinel-s2-l2a-cogs/31/T/DG/2022/7/ --no-sign-request  
