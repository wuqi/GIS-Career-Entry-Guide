===========
utfgrid标准
===========

:Date: 2024-09-19T09:45:35Z

utfgrid标准
===========

   原文地址
   https://github.com/mapbox/utfgrid-spec/blob/master/1.3/utfgrid.md

UTFGrid
=======

` <#utfgrid>`__

| The UTFGrid encoding scheme encodes interactivity data for a tile in a
  space efficient manner. It is designed to be used in browsers, e.g.
  for displaying tooltips when hovering over certain features of a map
  tile.
| UTFGrid编码方案以空间高效的方式对图块的交互性数据进行编码。它被设计用于浏览器中，例如当鼠标悬停在地图图块的某些特征上时显示工具提示。

| Since slower browsers and machines can't cope with rendering the
  actual polygons used to draw vectors on the map tile, we use a
  grid-based approach where we store the associated information for each
  pixel.
| 由于较慢的浏览器和机器无法科普渲染用于在地图瓦片上绘制矢量的实际多边形，因此我们使用基于网格的方法来存储每个像素的相关信息。

| UTFGrid uses `JSON <http://en.wikipedia.org/wiki/JSON>`__ as a
  container format. It is exclusively geared towards square tiles.
| UTFGrid使用\ `JSON <http://en.wikipedia.org/wiki/JSON>`__\ 作为容器格式。它专门面向方形瓷砖。

Grid 网格
---------

` <#grid>`__

| To achieve reasonable speed in browsers, we store information for a
  pixel in long strings, where each character's Unicode code point is
  the key for retrieving the information associated with that pixel.
  When we have less than 96 unique IDs, this means that the space taken
  up by storing each pixel separately is 256 \* 256 = 64 KB. Gzipping
  the grid data typically reduces it to a size below 2K.
| 为了在浏览器中获得合理的速度，我们将像素的信息存储在长字符串中，其中每个字符的Unicode码位是检索与该像素相关的信息的关键。当我们有少于96个唯一ID时，这意味着单独存储每个像素所占用的空间为256
  \* 256 = 64 KB。对网格数据进行Gzip压缩通常会将其大小减小到2K以下。

| By default, UTFGrid operates on a 4x4 grid, meaning that a tile at
  zoom level 0 that contains the entire world in its extent will have an
  grid resolution of 64x64. We take advantage of UTF-8's variable length
  codepoint encoding: all ASCII characters are encoded as is, that means
  that the first 94 codepoints are encoded with their code number as a
  single byte (codes ``0x20``\ ​, ``0x21``\ ​, ``0x23``\ ​-``0x5B``\ ​ and
  ``0x5D``\ ​-``0x7F``\ ​). IDs with a number larger than that will get
  encoded as multiple bytes.
| 默认情况下，UTFGrid在4x
  4网格上运行，这意味着在缩放级别为0的范围内包含整个世界的图块将具有64
  x64的网格分辨率。我们利用UTF-8的可变长度码点编码：所有ASCII字符都按原样编码，这意味着前94个码点以其代码编号编码为单个字节（代码\ ``0x 20``\ ​，\ ``0x 21``\ ​，\ ``0x 23``\ ​-``0x 5 B``\ ​和\ ``0x 5D``\ ​-``0x 7 F``\ ​）。大于该值的ID将被编码为多个字节。

Encoding IDs 编码ID
-------------------

` <#encoding-ids>`__

| `JSON <http://www.ietf.org/rfc/rfc4627>`__ doesn't allow control
  characters, ``"``\ ​ and ``\``\ ​ to be encoded as their literal UTF-8
  representation. **Encoding** an ID works as follows:
| `JSON <http://www.ietf.org/rfc/rfc4627>`__\ 不允许将控制字符`“`​和\ ``\``\ ​编码为它们的文字UTF-8表示。\ **对ID进行编码的**\ 工作原理如下：

-  Add 3210.
   加32 10。
-  If the result is >= 3410, add 1.
   如果结果为>= 3410，则加1。
-  If the result is >= 9210, add 1.
   如果结果为>= 9210，则加1。

| This ensures that all characters that cannot be represented natively
  are skipped.
| 这样可以确保跳过所有不能以本机方式表示的字符。

| **Decoding** works as follows:
| **解码**\ 工作如下：

-  If the codepoint is >= 9310, subtract 1.
   如果代码点为>= 9310，则减去1。
-  If the codepoint is >= 3510, subtract 1.
   如果代码点为>= 3510，则减去1。
-  Subtract 3210.
   减去3210.

Mapping an ID to a key
----------------------

将ID映射到密钥

` <#mapping-an-id-to-a-key>`__

| The UTFgrid file contains an array in a property named ``grid``\ ​ at
  the root level. Each entry represents a row in the grid. Each array
  entry is a string that contains the UTF-8 encoded codepoint for each
  column. The string length corresponds to the number of entries in the
  ``grid``\ ​ array. Only powers of two are allowed.
| UTFgrid文件在根级别名为\ ``grid``\ ​的属性中包含一个数组。每个条目表示网格中的一行。每个数组条目都是一个字符串，其中包含每列的UTF-8编码代码点。字符串长度对应于\ ``网格``\ ​数组中的条目数。只允许2的幂。

| The keys are stored in an array named ``keys``\ ​ at the root level.
  The index of each key represents the ID that it is associated to.
| 键存储在根级别名为\ ``keys``\ ​的数组中。每个键的索引表示它所关联的ID。

| Retrieving a key from a coordinate works as follows (``json``\ ​ is the
  root level object, ``x``\ ​ and ``y``\ ​ are the coordinates, starting
  from top left at 0, and ``size``\ ​ is the number of entries in the
  ``grid``\ ​ key):
| 从坐标中检索键的工作原理如下（\ ``json``\ ​是根级别对象，\ ``x``\ ​和\ ``y``\ ​是坐标，从左上角的0开始，\ ``size``\ ​是\ ``网格``\ ​键中的条目数）：

-  ​\ ``var factor = 256 / size, row = y / factor, col = x / factor``\ ​
-  ​\ ``var id = json.grid[row].charCodeAt(col);``\ ​ is the character
   that contains the encoded ID.
   ``var id = json.grid[row].charCodeAt(col);``\ ​ 是包含编码ID的字符。
-  Decode the id as described in *"Encoding IDs"*.
   按照_“编码ID”_中所述对ID进行解码。
-  ​\ ``var key = json.keys[id];``\ ​ retrieves the ID associated with the
   coordinate.
   ``var key = json.keys[id];``\ ​检索与坐标关联的ID。

| All divisions are integer divisions.
| 所有分区都是整数分区。

Mapping a key to data
---------------------

将键映射到数据

` <#mapping-a-key-to-data>`__

| The JSON file may contain an optional ``data``\ ​ property at the root
  level. If it isn't present, the client looks up the obtained key in
  its internal data store. If the lookup key is not present, it queries
  the server with the missing keys. If the ``data``\ ​ property is
  present, but the key cannot be found, the client must behave as if
  there were no ``data``\ ​ property.
| JSON文件可以在根级别包含可选的\ ``数据``\ ​属性。如果不存在，客户端将在其内部数据存储中查找所获得的密钥。如果查找键不存在，它将使用缺少的键查询服务器。如果\ ``数据``\ ​属性存在，但找不到键，则客户端的行为必须像没有\ ``数据``\ ​属性一样。

| An empty key signifies the unavailability of information for that
  pixel. No action may be taken to retrieve data for an empty (``""``\ ​)
  key.
| 空键表示该像素的信息不可用。对于空（\ ``“”``\ ​）键，不能采取任何操作来检索数据。

Example UTFGrid JSON file
-------------------------

UTFGrid JSON文件示例

` <#example-utfgrid-json-file>`__

::

   { "grid":
   [ "                                                    !!!#########",
     "                                                    !!!#########",
     "                                                   !!!!#########",
     "                                                   !!!##########",
     "                        !!                         !!!##########",
     "                                                    !!!#########",
     "                                                    !!######### ",
     "                            !                      !!! #######  ",
     "                                                       ###      ",
     "                                                        $       ",
     "                                                        $$    %%",
     "                                                       $$$$$$$%%",
     "                                                       $$$$$$$%%",
     "                                                     $$$$$$$$$%%",
     "                                                    $$$$$$$$$$%%",
     "                                                   $$$$$$$$$$$$%",
     "                                                   $$$$$$$$$%%%%",
     "                                                  $$$$$$$$$%%%%%",
     "                                                  $$$$$$$$%%%%%%",
     "                                                  $$$$$$$%%%%%%%",
     "                                                  $$$$%%%%%%%%%%",
     "                                                 $$$$%%%%%%%%%%%",
     "                                        # # #  $$$$$%%%%%%%%%%%%",
     "                                             $$$$$$$%%%%%%%%%%%%",
     "                                             $$$&&&&'%%%%%%%%%%%",
     "                                            $$$$&&&&'''%%%%%%%%%",
     "                                           $$$$'''''''''%%%%%%%%",
     "                                           $$$$'''''''''''%%%%%%",
     "                                          $$$$&''''''''((((%%%%%",
     "                                          $$$&&''''''''(((((%%%%",
     "                                         $$$&&'''''''''(((((((%%",
     "                                         $$$&&''''''''''(((((((%",
     "                                        $$$&&&''''''''''((((((((",
     "                                        ''''''''''''''''((((((((",
     "                                         '''''''''''''''((((((((",
     "                                         '''''''''''''''((((((((",
     "                                         '''''''''''''''((((((((",
     "                                         '''''''''''''''((((((((",
     "                                         '''''''''''''''((((((((",
     "                            )            '''''''''''''''((((((((",
     "                                         ***'''''''''''''(((((((",
     "                                         *****'''''''''''(((((((",
     "                              ))        ******'''(((((((((((((((",
     "                                        *******(((((((((((((((++",
     "                                        *******(((((((((((((++++",
     "                                        ********((((((((((((++++",
     "                                        ***,,-**((((((((((++++++",
     "                                         ,,,,-------(((((+++++++",
     "                                         ,,---------(((((+++++.+",
     "                                            --------(((((+++....",
     "                                             -///----0000000....",
     "                                             ////----0000000....",
     "                                             /////1---0000000...",
     "                                              ///11--0000000....",
     "                                                111110000000....",
     "                                                 11110000000....",
     "                                                  1111000000....",
     "                                                    1100     .  ",
     "                                                                ",
     "                                                                ",
     "                                                                ",
     "                                                                ",
     "                                                                ",
     "                                                                " ],
   "keys":
    [ "",
      "1",
      "2",
      "3",
      "4",
      "5",
      "6",
      "7",
      "8",
      "9",
      "10",
      "11",
      "12",
      "13",
      "14",
      "15",
      "16" ],
   "data":
    { "1": { "admin": "Portugal" },
      "2": { "admin": "Spain" },
      "3": { "admin": "Morocco" },
      "4": { "admin": "Algeria" },
      "5": { "admin": "Western Sahara" },
      "6": { "admin": "Mauritania" },
      "7": { "admin": "Mali" },
      "8": { "admin": "Cape Verde" },
      "9": { "admin": "Senegal" },
      "10": { "admin": "Burkina Faso" },
      "11": { "admin": "Guinea Bissau" },
      "12": { "admin": "Guinea" },
      "13": { "admin": "Ghana" },
      "14": { "admin": "Sierra Leone" },
      "15": { "admin": "Ivory Coast" },
      "16": { "admin": "Liberia" } } }

| To test implementations,
  ```demo.json`` <https://github.com/mapbox/mbtiles-spec/blob/master/1.1/demo.json>`__\ ​
  contains a grid that consists of 65501 different keys. This is the
  maximum possible in this version of UTFGrid. Implementors should check
  that obtaining a coordinate should return the key ``y * 256 + x``\ ​
  for all x/y, with the exception of y = 255 and x >= 222 and x <= 255
  returning 65501 due to the maximum codepoint allowed in JSON.
| 为了测试实现，\ ```demo.json`` <https://github.com/mapbox/mbtiles-spec/blob/master/1.1/demo.json>`__\ ​包含一个由65501个不同键组成的网格。这是此版本UTFGrid中可能的最大值。实现者应该检查获取坐标时是否应该为所有x/y返回键`y
  \* 256 + x`​，除了y = 255和x >= 222以及x <=
  255返回65501，因为JSON中允许的最大码点。

| A dummy code validation routine is given here:
| 这里给出了一个虚拟代码验证例程：

::

   var json = JSON.parse(/* demo.json */);

   // the resolution of the grid. adjust this for your grid.
   var resolution = 4;

   var key = 0,
       dimension = 256 / resolution;

   for (var y = 0; y < dimension; y++) {
       for (var x = 0; x < dimension; x++) {
           var code = json.grid[y].charCodeAt(x);
           if (code >= 93) code--;
           if (code >= 35) code--;
           code -= 32;

           assert(code == key);

           if (key < 65501) key++;
       }
   }
