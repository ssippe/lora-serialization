# [![Build Status](https://travis-ci.org/thesolarnomad/lora-serialization.svg?branch=master)](https://travis-ci.org/thesolarnomad/lora-serialization) LoRaWAN serialization/deserialization library for The Things Network

This library allows you to encode your data on the Arduino site and decode it on the [TTN](https://staging.thethingsnetwork.org/) side. It provides both a C-based encoder and a JavaScript-based decoder.

## Usage

### Unix time
```cpp
byte buffer[4];
unixtimeToBytes(buffer, 1467632413);
// buffer == {0x1d, 0x4b, 0x7a, 0x57}
```
and then in the TTN frontend, use the following method:

```javascript
unixtime(bytes.slice(x, x + 4)) // 1467632413
```

### GPS coordinates
```cpp
byte buffer[8];
latLngToBytes(buffer, -33.905052, 151.26641);
// buffer = {0x64, 0xa6, 0xfa, 0xfd, 0x6a, 0x24, 0x04, 0x09}
```
and then in the TTN frontend, use the following method:

```javascript
latLng(bytes.slice(x, x + 8)) // [-33.905052, 151.26641]
```

### Composition in the TTN decoder frontend with the `decode` method

The `decode` method allows you to specify a mask for the incoming byte buffer (that was generated by this library) and apply decoding functions accordingly.

```javascript
decode(byte Array, mask Array [,mapping Array])
```

#### Example
Paste everything from `src/decoder.js` into the decoder method and use like this:

```javascript
function (bytes) {
    // code from src/decoder.js here
    return decode(bytes, [latLng, unixtime], ['coords', 'time']);
}
```
This maps the incoming byte buffer of 12 bytes to a sequence of one `latLng` (8 bytes) and one `unixtime` (4 bytes) sequence and maps the first one to a key `coords` and the second to a key `time`.

You can use: `64 A6 FA FD 6A 24 04 09 1D 4B 7A 57` for testing, and it will result in:

```json
{
  "coords": [
    -33.905052,
    151.26641
  ],
  "time": 1467632413
}
```
