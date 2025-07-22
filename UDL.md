# SkyFortress UDL data format

Currently the SkyFortress system supports UDL Track data format.

## Structure of UDL Track data:
```yaml
classificationMarking - classification, "U" as unclassified.
trkId - track ID, unique identifier for the track, e.g. "53".
objNat - object nationality, e.g. "NATO".
objType - object type, e.g. "DRONE" \ "MISSILE".
ts - timestamp of detection, e.g. "2025-06-19T09:04:32.000000Z".
objIdent - object identity, e.g. "HOSTILE" \ "FRIEND".
env - environment of object, e.g. "AIR".
lat - latitude in degrees, e.g. 50.0.
lon - longitude in degrees, e.g. 31.3.
spd - speed in m/s, e.g. 82.8651.
course - course in degrees, e.g. 314.131.
errEllp - array of error ellipsoid parameters, consists of semi-major axis in meters, semi-minor axis in meters, orientation in degrees, e.g. [2321.27, 1508.00, -0.8180].
multiSource - multi-source flag, true if the track is from multiple sources, false otherwise.
source - source of the track, "SkyFortress" in all cases.
dataMode - data mode, "REAL" for actual data, "TEST" for simulated data.
```
## Example of UDL Track data:
```json
{
    "classificationMarking": "U",
    "trkId": "53",
    "objNat": "NATO",
    "objType": "DRONE",
    "ts": "2025-06-19T09:04:32.000000Z",
    "objIdent": "HOSTILE",
    "env": "AIR",
    "lat": 50.0,
    "lon": 31.3,
    "spd": 82.8651627404936,
    "course": 314.13183094216276,
    "errEllp": [
      1570.7401346167785,
      1005.3382565607692,
      -0.7890829899256296
    ],
    "multiSource": false,
    "source": "SkyFortress",
    "dataMode": "TEST"
  }
```