.. _image-transformations:

Transforming images on the fly
==============================

What you as an end-user of an Imbo installation will be doing most of the time, is working with images. This is what Imbo was originally made for, and this chapter includes details about all the different image transformations Imbo supports.

All image transformations can be triggered by specifying the ``t`` query parameter. This parameter must be used as an array so that you can provide several image transformations. The transformations will be applied to the image in the same order as they appear in the URL. Each element in this array represents a single transformation with optional parameters, specified as a string. If the ``t`` query parameter is not an array or if any of its elements are not strings, Imbo will respond with ``HTTP 400``.

Below you will find all image transformations supported "out of the box", along with their parameters. Some transformations are rarely used with ``HTTP GET``, but are instead used by event listeners that transform images when they are added to Imbo (``HTTP POST``). If this is the case it will be mentioned in the description of the transformation.

.. _auto-rotate-transformation:

Auto rotate image based on EXIF data - ``t[]=autoRotate``
---------------------------------------------------------

This transformation will auto rotate the image based on EXIF data stored in the image. This transformation is rarely used per request, but is typically used by the :ref:`auto-rotate-image-event-listener` event listener when adding images to Imbo.

**Examples:**

* ``t[]=autoRotate``

.. _border-transformation:

Add an image border - ``t[]=border``
------------------------------------

This transformation will apply a border around the image.

**Parameters:**

``color``
    Color of the border in hexadecimal. Defaults to ``000000`` (You can also specify short values like ``f00`` (``ff0000``)).

``width``
    Width of the border in pixels on the left and right sides of the image. Defaults to ``1``.

``height``
    Height of the border in pixels on the top and bottom sides of the image. Defaults to ``1``.

``mode``
    Mode of the border. Can be ``inline`` or ``outbound``. Defaults to ``outbound``. Outbound places the border outside of the image, increasing the dimensions of the image. ``inline`` paints the border inside of the image, retaining the original width and height of the image.

**Examples:**

* ``t[]=border``
* ``t[]=border:mode=inline``
* ``t[]=border:color=000``
* ``t[]=border:color=f00,width=2,height=2``

.. _canvas-transformation:

Expand the image canvas - ``t[]=canvas``
----------------------------------------

This transformation can be used to change the canvas of the original image.

**Parameters:**

``width``
    Width of the surrounding canvas in pixels. If omitted the width of ``<image>`` will be used.

``height``
    Height of the surrounding canvas in pixels. If omitted the height of ``<image>`` will be used.

``mode``
    The placement mode of the original image. ``free``, ``center``, ``center-x`` and ``center-y`` are available values. Defaults to ``free``.

``x``
    X coordinate of the placement of the upper left corner of the existing image. Only used for modes: ``free`` and ``center-y``.

``y``
    Y coordinate of the placement of the upper left corner of the existing image. Only used for modes: ``free`` and ``center-x``.

``bg``
    Background color of the canvas. Defaults to ``ffffff`` (also supports short values like ``f00`` (``ff0000``)).

**Examples:**

* ``t[]=canvas:width=200,mode=center``
* ``t[]=canvas:width=200,height=200,x=10,y=10,bg=000``
* ``t[]=canvas:width=200,height=200,x=10,mode=center-y``
* ``t[]=canvas:width=200,height=200,y=10,mode=center-x``

.. _compress-transformation:

Compress the image - ``t[]=compress``
-------------------------------------

This transformation compresses images on the fly resulting in a smaller payload. It is advisable to only use this transformation in combination with an image type in the URL (for instance ``.jpg`` or ``.png``). This transformation is not applied to images of type ``image/gif``.

**Parameters:**

``level``
    The level of the compression applied to the image. The effect this parameter has on the image depends on the type of the image. If the image in the response is an ``image/jpeg`` a high ``level`` means high quality, usually resulting in larger files. If the image in the response is an ``image/png`` a high ``level`` means high compression, usually resulting in smaller files. If you do not specify an image type in the URL the result of this transformation is not deterministic as clients have different preferences with regards to the type of images they want to receive (via the ``Accept`` request header).

**Examples:**

* ``t[]=compress:level=40``

.. _convert-transformation:

Convert the image type - ``.jpg/.gif/.png``
-------------------------------------------

This transformation can be used to change the image type. It is not applied like the other transformations, but is triggered when specifying a custom extension to the ``<image>``. Currently Imbo can convert to:

* ``image/jpeg``
* ``image/png``
* ``image/gif``

**Examples:**

* ``curl http://imbo/users/<user>/images/<image>.gif``
* ``curl http://imbo/users/<user>/images/<image>.jpg``
* ``curl http://imbo/users/<user>/images/<image>.png``

.. _crop-transformation:

Crop the image - ``t[]=crop``
-----------------------------

This transformation is used to crop the image.

**Parameters:**

``x``
    The X coordinate of the cropped region's top left corner.

``y``
    The Y coordinate of the cropped region's top left corner.

``width``
    The width of the crop in pixels.

``height``
    The height of the crop in pixels.

``mode``
    The crop mode (optional). Possible values are:

    ``center``
        When using the center mode the ``x`` and ``y`` parameters are ignored, and the center of the cropped area is placed in the center of the original image.

    ``center-x``
        Center the crop on the x-axis. Use the ``y`` parameter to control the upper edge of the crop.

    ``center-y``
        Center the crop on the y-axis. Use the ``x`` parameter to control the left edge of the crop.

**Examples:**

* ``t[]=crop:x=10,y=25,width=250,height=150``
* ``t[]=crop:width=100,height=100,mode=center``
* ``t[]=crop:width=50,height=50,mode=center-x,y=15``
* ``t[]=crop:width=50,height=50,mode=center-y,x=15``

.. _desaturate-transformation:

Make a gray scaled image - ``t[]=desaturate``
---------------------------------------------

This transformation desaturates the image (in practice, gray scales it).

**Examples:**

* ``t[]=desaturate``

.. _flip-horizontally-transformation:

Make a mirror image - ``t[]=flipHorizontally``
----------------------------------------------

This transformation flips the image horizontally.

**Examples:**

* ``t[]=flipHorizontally``

.. _flip-vertically-transformation:

Flip the image upside down - ``t[]=flipVertically``
---------------------------------------------------

This transformation flips the image vertically.

**Examples:**

* ``t[]=flipVertically``

.. _max-size-transformation:

Enforce a max size of an image - ``t[]=maxSize``
------------------------------------------------

This transformation will resize the image using the original aspect ratio. Two parameters are supported and at least one of them must be supplied to apply the transformation.

Note the difference from the :ref:`resize <resize-transformation>` transformation: given both ``width`` and ``height``, the resulting image will not be the same width and height as specified unless the aspect ratio is the same.

**Parameters:**

``width``
    The max width of the resulting image in pixels. If not specified the width will be calculated using the same aspect ratio as the original image.

``height``
    The max height of the resulting image in pixels. If not specified the height will be calculated using the same aspect ratio as the original image.

**Examples:**

* ``t[]=maxSize:width=100``
* ``t[]=maxSize:height=100``
* ``t[]=maxSize:width=100,height=50``

.. _modulate-transformation:

Modulate the image - ``t[]=modulate``
-------------------------------------

This transformation can be used to control the brightness, saturation and hue of the image.

**Parameters:**

``b``
    Brightness of the image in percent. Defaults to 100.

``s``
    Saturation of the image in percent. Defaults to 100.

``h``
    Hue percentage. Defaults to 100.

**Examples:**

* ``t[]=modulate:b=150``
* ``t[]=modulate:b=120,s=130,h=90``

.. _progressive-transformation:

Make a progressive image - ``t[]=progressive``
----------------------------------------------

This transformation makes the image progressive.

**Examples:**

* ``t[]=progressive``

.. _resize-transformation:

Resize the image - ``t[]=resize``
---------------------------------

This transformation will resize the image. Two parameters are supported and at least one of them must be supplied to apply the transformation.

**Parameters:**

``width``
    The width of the resulting image in pixels. If not specified the width will be calculated using the same aspect ratio as the original image.

``height``
    The height of the resulting image in pixels. If not specified the height will be calculated using the same aspect ratio as the original image.

**Examples:**

* ``t[]=resize:width=100``
* ``t[]=resize:height=100``
* ``t[]=resize:width=100,height=50``

.. _rotate-transformation:

Rotate the image - ``t[]=rotate``
---------------------------------

This transformation will rotate the image clock-wise.

**Parameters:**

``angle``
    The number of degrees to rotate the image (clock-wise).

``bg``
    Background color in hexadecimal. Defaults to ``000000`` (also supports short values like ``f00`` (``ff0000``)).

**Examples:**

* ``t[]=rotate:angle=90``
* ``t[]=rotate:angle=45,bg=fff``

.. _sepia-transformation:

Apply a sepia color tone - ``t[]=sepia``
----------------------------------------

This transformation will apply a sepia color tone transformation to the image.

**Parameters:**

``threshold``
    Threshold ranges from 0 to QuantumRange and is a measure of the extent of the sepia toning. Defaults to ``80``

**Examples:**

* ``t[]=sepia``
* ``t[]=sepia:threshold=70``

.. _strip-transformation:

Strip image properties and comments - ``t[]=strip``
---------------------------------------------------

This transformation removes all properties and comments from the image. If you want to strip EXIF tags from the image for instance, this transformation will do that for you.

**Examples:**

* ``t[]=strip``

.. _thumbnail-transformation:

Create a thumbnail of the image - ``t[]=thumbnail``
---------------------------------------------------

This transformation creates a thumbnail of ``<image>``.

**Parameters:**

``width``
    Width of the thumbnail in pixels. Defaults to ``50``.

``height``
    Height of the thumbnail in pixels. Defaults to ``50``.

``fit``
    Fit style. Possible values are: ``inset`` or ``outbound``. Default to ``outbound``.

**Examples:**

* ``t[]=thumbnail``
* ``t[]=thumbnail:width=20,height=20,fit=inset``

.. _transpose-transformation:

Create a vertical mirror image - ``t[]=transpose``
--------------------------------------------------

This transformation transposes the image.

**Examples:**

* ``t[]=transpose``

.. _transverse-transformation:

Create a horizontal mirror image - ``t[]=transverse``
-----------------------------------------------------

This transformation transverses the image.

**Examples:**

* ``t[]=transverse``

.. _watermark-transformation:

Add a watermark to the image - ``t[]=watermark``
------------------------------------------------

This transformation can be used to apply a watermark on top of the original image.

**Parameters:**

``img``
    Image identifier of the image to apply as watermark. Can be set to a default value in configuration by using ``<setDefaultImage>``.

``width``
    Width of the watermark image in pixels. If omitted the width of ``<img>`` will be used.

``height``
    Height of the watermark image in pixels. If omitted the height of ``<img>`` will be used.

``position``
    The placement of the watermark image. ``top-left``, ``top-right``, ``bottom-left``, ``bottom-right`` and ``center`` are available values. Defaults to ``top-left``.

``x``
    Number of pixels in the X-axis the watermark image should be offset from the original position (defined by the ``position`` parameter). Supports negative numbers. Defaults to ``0``

``y``
    Number of pixels in the Y-axis the watermark image should be offset from the original position (defined by the ``position`` parameter). Supports negative numbers. Defaults to ``0``

**Examples:**

* ``t[]=watermark:img=f5f7851c40e2b76a01af9482f67bbf3f``
* ``t[]=watermark:img=f5f7851c40e2b76a01af9482f67bbf3f,width=200,x=5``
* ``t[]=watermark:img=f5f7851c40e2b76a01af9482f67bbf3f,height=50,x=-5,y=-5,position=bottom-right``

If you want to set the default watermark image you will have to do so in the configuration:

.. code-block:: php

    <?php
    return array(
        // ...

        'eventListeners' => array(
            'watermark' => function() {
                $transformation = new Imbo\Image\Transformation\Watermark();
                $transformation->setDefaultImage('some image identifier');

                return $transformation;
            },
        ),

        // ...
    );

When you have specified a default watermark image you are not required to use the ``img`` option for the transformation, but if you do so it will override the default one.
