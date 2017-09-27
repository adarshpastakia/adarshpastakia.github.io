---
layout: post
title:  "Code Snippets"
description: "Javascript code snippets."
date:   2017-09-27
tags: [es6]
publish: true
---

<p class="intro"><span class="dropcap">C</span>ode Snippets for javascript. Not written by me, but these methods come in handy.</p>


#### GeoDistance Calculator

```js
function calculateDistance(from, to, unit = "m",
  latProperty = "latitude", lonProperty = "longitude") {

  var radlat1 = Math.PI * from[latProperty] / 180;
  var radlat2 = Math.PI * to[latProperty] / 180;
  var radlon1 = Math.PI * from[lonProperty] / 180;
  var radlon2 = Math.PI * to[lonProperty] / 180;

  var theta = from[lonProperty] - to[lonProperty];
  var radtheta = Math.PI * theta / 180;

  var dist = Math.sin(radlat1) * Math.sin(radlat2) +
    Math.cos(radlat1) * Math.cos(radlat2) * Math.cos(radtheta);

  dist = Math.acos(dist);
  dist = dist * 180 / Math.PI;
  dist = dist * 60 * 1.1515;

  if (unit === "K") {
    // Unit in Kilometers
    dist = dist * 1.609344
  } else if (unit === "N") {
    // Unit in Nautical Miles
    dist = dist * 0.8684
  } else if (unit === "M") {
    // Unit in Miles
    dist = dist;
  } else if (unit === "y") {
    // Unit in Yards
    dist = dist * 1760;
  } else if (unit === "m") {
    // Default to unit in meters
    dist = (dist * 1.609344) * 1000
  }
  return dist;
}
```

#### GeoCoordinates Calculate Center

```js
function calculateCenterPoint(data, latProperty = "latitude", lonProperty = "longitude") {
  var num_coords = data.length;

  var X = 0.0;
  var Y = 0.0;
  var Z = 0.0;

  for (i = 0; i < data.length; i++) {
    var lat = data[i][latProperty] * Math.PI / 180;
    var lon = data[i][lonProperty] * Math.PI / 180;

    var a = Math.cos(lat) * Math.cos(lon);
    var b = Math.cos(lat) * Math.sin(lon);
    var c = Math.sin(lat);

    X += a;
    Y += b;
    Z += c;
  }

  X /= num_coords;
  Y /= num_coords;
  Z /= num_coords;

  var lon = Math.atan2(Y, X);
  var hyp = Math.sqrt(X * X + Y * Y);
  var lat = Math.atan2(Z, hyp);

  var newX = (lat * 180 / Math.PI);
  var newY = (lon * 180 / Math.PI);

  var ret = {};
  ret[latProperty] = newX;
  ret[lonProperty] = newY;
  return ret;
}
```

----

#### Latin2Ascii Converter

```js
function getAscii(str) {
  var conversions   = new Object();
  conversions['ae'] = 'ä|æ|ǽ';
  conversions['oe'] = 'ö|œ';
  conversions['ue'] = 'ü';
  conversions['Ae'] = 'Ä';
  conversions['Ue'] = 'Ü';
  conversions['Oe'] = 'Ö';
  conversions['A']  = 'À|Á|Â|Ã|Ä|Å|Ǻ|Ā|Ă|Ą|Ǎ';
  conversions['a']  = 'à|á|â|ã|å|ǻ|ā|ă|ą|ǎ|ª';
  conversions['C']  = 'Ç|Ć|Ĉ|Ċ|Č';
  conversions['c']  = 'ç|ć|ĉ|ċ|č';
  conversions['D']  = 'Ð|Ď|Đ';
  conversions['d']  = 'ð|ď|đ';
  conversions['E']  = 'È|É|Ê|Ë|Ē|Ĕ|Ė|Ę|Ě';
  conversions['e']  = 'è|é|ê|ë|ē|ĕ|ė|ę|ě';
  conversions['G']  = 'Ĝ|Ğ|Ġ|Ģ';
  conversions['g']  = 'ĝ|ğ|ġ|ģ';
  conversions['H']  = 'Ĥ|Ħ';
  conversions['h']  = 'ĥ|ħ';
  conversions['I']  = 'Ì|Í|Î|Ï|Ĩ|Ī|Ĭ|Ǐ|Į|İ';
  conversions['i']  = 'ì|í|î|ï|ĩ|ī|ĭ|ǐ|į|ı';
  conversions['J']  = 'Ĵ';
  conversions['j']  = 'ĵ';
  conversions['K']  = 'Ķ';
  conversions['k']  = 'ķ';
  conversions['L']  = 'Ĺ|Ļ|Ľ|Ŀ|Ł';
  conversions['l']  = 'ĺ|ļ|ľ|ŀ|ł';
  conversions['N']  = 'Ñ|Ń|Ņ|Ň';
  conversions['n']  = 'ñ|ń|ņ|ň|ŉ';
  conversions['O']  = 'Ò|Ó|Ô|Õ|Ō|Ŏ|Ǒ|Ő|Ơ|Ø|Ǿ';
  conversions['o']  = 'ò|ó|ô|õ|ō|ŏ|ǒ|ő|ơ|ø|ǿ|º';
  conversions['R']  = 'Ŕ|Ŗ|Ř';
  conversions['r']  = 'ŕ|ŗ|ř';
  conversions['S']  = 'Ś|Ŝ|Ş|Š';
  conversions['s']  = 'ś|ŝ|ş|š|ſ';
  conversions['T']  = 'Ţ|Ť|Ŧ';
  conversions['t']  = 'ţ|ť|ŧ';
  conversions['U']  = 'Ù|Ú|Û|Ũ|Ū|Ŭ|Ů|Ű|Ų|Ư|Ǔ|Ǖ|Ǘ|Ǚ|Ǜ';
  conversions['u']  = 'ù|ú|û|ũ|ū|ŭ|ů|ű|ų|ư|ǔ|ǖ|ǘ|ǚ|ǜ';
  conversions['Y']  = 'Ý|Ÿ|Ŷ';
  conversions['y']  = 'ý|ÿ|ŷ';
  conversions['W']  = 'Ŵ';
  conversions['w']  = 'ŵ';
  conversions['Z']  = 'Ź|Ż|Ž';
  conversions['z']  = 'ź|ż|ž';
  conversions['AE'] = 'Æ|Ǽ';
  conversions['ss'] = 'ß';
  conversions['IJ'] = 'Ĳ';
  conversions['ij'] = 'ĳ';
  conversions['OE'] = 'Œ';
  conversions['f']  = 'ƒ';
  for (var i in conversions) {
    var re = new RegExp(conversions[i], "g");
    str    = str.replace(re, i);
  }
  return str;
}
```
