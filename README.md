# SkyFortressDataStyles# cUAS to TAK Data Integration Plan v2.2

## ⚠️ Items Requiring Review
The following items involve interpretation or best practices rather than confirmed standards:

1. **2525 Symbols for Rockets** - Using missile symbols (UCM) but specific rocket symbols may exist
2. **Color Values** - Using standard hex codes (-65536=red, 255=blue, -256=yellow, -8355712=gray)
3. **Error Values** - Suggesting 10m for high confidence, 25m for low confidence
4. **GCCS Track Quality Mappings** - TQ values 0-9 are estimates based on common practice
5. **Cancelled Track Type** - Using "a-p-G" (pending/unknown point) as best guess
6. **uid Element Format** - Using `<uid Droid="..."/>` based on TAK examples
7. **Fusion Field Names** - All `_cuas_fusion` fields are placeholders pending fusion design
8. **Display Suffix Convention** - -P and -X suffixes are recommendations

---

## Overview
This document defines data standards for integrating Counter-UAS (cUAS) detection data into Team Awareness Kit (TAK) and military C2 systems, with provisions for future data fusion capabilities.

## Track Classifications
- **DRONE** - Generic unmanned aerial system
- **QUADROCOPTER** - Multi-rotor aircraft  
- **ROCKET** - Rocket-propelled projectile

## Verification Status Mapping

| Original State | Standard Status | Description | Display Suffix | CoT Type | 2525C Symbol | 2525D SIDC ||
|----------------|-----------------|-------------|----------------|----------|--------------|------------|--|
| `high_confidence` | VERIFIED | Human operator confirmed | None | a-u-G-U-C* | SUGPUC---- | 10030000151211000000 |![SUGPUC----](./SUGPUC----.png)|
| `our` | FRIENDLY | Human verified as friendly | None | a-f-G-U-C | SFGPUC---- | 10033000151211000000 |![SUGPUC----](./SFGPUC----.png)|
| `automoderated` | PROBABLE | Algorithm confirmed | None | a-u-G-U-C | SUGPUC---- | 10030000151211000000 |![SUGPUC----](./SUGPUC----.png)|
| `event` | DETECTED | Sensor confirmed | None | a-u-G-U-C | SUGPUC---- | 10030000151211000000 |![SUGPUC----](./SUGPUC----.png)|
| `for_moderation` | POSSIBLE | Sensor uncertain | -P | a-u-G-U-C | SUGPUC---- | 10030000151211000000 |![SUGPUC----](./SUGPUC----.png)|
| `cancelled` | FALSE | Determined false positive | -X | a-p-G | SPGP------ | 10031500000000000000 |![SUGPUC----](./SPGP------.png)|

*Changes to a-h-G-U-C when hostile determination is made


🔴 I propose the following changes to the symbol codes combining state and classification:
|| Original State | Classifications | CoT Type | 2525C Symbol ||
|--|----------------|-----------------|----------|--------------|----------|
|🔴| `high_confidence` | DRONE | a-h-A-C-F-q | SHAPMFQ---***** |![SHAPMFQ---*****](./SHAPMFQ---.png)|
|🔴| `our` | DRONE | a-f-A-C-F-q | SUAPMFQ---***** |![SFAPMFQ---*****](./SFAPMFQ---.png)|
|🔴| `automoderated` | DRONE | a-u-A-C-F-q | SUAPMFQ---***** |![SUAPMFQ---*****](./SUAPMFQ---.png)|
|🔴| `event` | DRONE | a-u-A-C-F-q | SUAPMFQ---***** |![SUAPMFQ---*****](./SUAPMFQ---.png)|
|🔴| `for_moderation` | DRONE| a-u-A-C-F-q | SUAPMFQ---***** |![SUAPMFQ---*****](./SUAPMFQ---.png)|
|🔴| `cancelled` | DRONE | a-p-G | SUZP------***** |![SUZP------*****](./SUZP------.png)|
|🔴| `high_confidence` | QUADROCOPTER | a-h-A-C-H-q | SHAPMHQ---***** |![SHAPMHQ---*****](./SHAPMHQ---.png)|
|🔴| `our` | QUADROCOPTER | a-f-A-C-H-q | SUAPMHQ---***** |![SFAPMHQ---*****](./SFAPMHQ---.png)|
|🔴| `automoderated` | QUADROCOPTER | a-u-A-C-H-q | SUAPMHQ---***** |![SUAPMHQ---*****](./SUAPMHQ---.png)|
|🔴| `event` | QUADROCOPTER | a-u-A-C-H-q | SUAPMHQ---***** |![SUAPMHQ---*****](./SUAPMHQ---.png)|
|🔴| `for_moderation` | QUADROCOPTER| a-u-A-C-H-q | SUAPMHQ---***** |![SUAPMHQ---*****](./SUAPMHQ---.png)|
|🔴| `cancelled` | QUADROCOPTER | a-p-G | SUZP------***** |![SUZP------*****](./SUZP------.png)|
|🔴| `high_confidence` | ROCKET | a-h-A-W-M | SHAPWM----***** |![SHAPWM----*****](./SHAPWM----.png)|
|🔴| `our` | ROCKET | a-f-A-W-M | SUAPWM----***** |![SFAPWM----*****](./SFAPWM----.png)|
|🔴| `automoderated` | ROCKET | a-u-A-W-M | SUAPWM----***** |![SUAPWM----*****](./SUAPWM----.png)|
|🔴| `event` | ROCKET | a-u-A-W-M | SUAPWM----***** |![SUAPWM----*****](./SUAPWM----.png)|
|🔴| `for_moderation` | ROCKET| a-u-A-W-M | SUAPWM----***** |![SUAPWM----*****](./SUAPWM----.png)|
|🔴| `cancelled` | ROCKET | a-p-G | SUZP------***** |![SUZP------*****](./SUZP------.png)|


## Track Identification

### Full System Track ID
```
[SYSTEM]-[SITE]-[UUID]
Example: CUAS-ALPHA-550e8400-e29b-41d4-a716-446655440000
```

### Display Format
```
[TYPE][LAST4][-SUFFIX]
```
- **TYPE**: D (Drone), Q (Quadrocopter), R (Rocket)
- **LAST4**: Last 4 digits of UUID
- **SUFFIX**: -P (Possible), -X (Cancelled/False), none for others

## Core CoT Structure

```xml
<event version="2.0" 
       uid="CUAS-ALPHA-550e8400-e29b-41d4-a716-446655440000" 
       type="a-u-G-U-C">
  <point lat="[latitude]" 
         lon="[longitude]" 
         hae="[altitude_meters]" 
         ce="[circular_error]" 
         le="[linear_error]"/>
  <detail>
    <contact callsign="Q0000-P"/>
    <track classification="QUADROCOPTER"
           verificationStatus="POSSIBLE"
           course="[0-359]"
           speed="[m/s]"/>
    
    <!-- CRITICAL: Store full UUID here -->
    <uid Droid="CUAS-ALPHA-550e8400-e29b-41d4-a716-446655440000"/>
    
    <!-- 2525 Symbology -->
    <_2525c symbol="SUGPUC----"/>
    <_2525d sidc="10030000151211000000"/>
    
    <!-- Error Ellipse -->
    <ellipse major="[meters]" 
             minor="[meters]" 
             angle="[degrees]"/>
    
    <!-- Velocity Vector -->
    <velocity heading="[degrees]" 
              speed="[m/s]" 
              climb="[m/s]"/>
    
    <!-- Extended Metadata -->
    <_cuas_core systemId="CUAS-ALPHA"
                fullTrackId="550e8400-e29b-41d4-a716-446655440000"
                shortId="0000"
                detectionTime="2024-01-10T10:44:00Z"
                sensorType="RADAR"
                updateCount="15"/>
    
    <!-- Fusion Placeholders -->
    <_cuas_fusion trackCorrelationId=""
                  estimatedLaunchPoint=""
                  estimatedLaunchTime=""
                  operatorLocation=""
                  launchVehicle=""/>
    
    <remarks>POSSIBLE - Awaiting verification</remarks>
  </detail>
</event>
```

## Symbol Assignments by Type and Status

### Drone/Quadrocopter Symbols
| Status | 2525C | 2525D | Description |
|--------|-------|-------|-------------|
| FRIENDLY | SFGPUCUD-- | 10033000151211000000 | Blue UAV |
| HOSTILE | SHGPUCUD-- | 10031000151211000000 | Red UAV |
| UNKNOWN | SUGPUCUD-- | 10030000151211000000 | Yellow UAV |

### Rocket Symbols  
| Status | 2525C | 2525D | Description |
|--------|-------|-------|-------------|
| FRIENDLY | SFGPUCM--- | 10033000151206000000 | Blue Missile |
| HOSTILE | SHGPUCM--- | 10031000151206000000 | Red Missile |
| UNKNOWN | SUGPUCM--- | 10030000151206000000 | Yellow Missile |

### Cancelled/False Tracks
- 2525C: SPGP------  (Pending/Unknown point)
- 2525D: 10031500000000000000
- Gray color, immediate stale time

## Color Mapping Values

| Status | Hex Value | Decimal | Appearance |
|--------|-----------|---------|------------|
| FRIENDLY | #0000FF | 255 | Blue |
| HOSTILE | #FF0000 | -65536 | Red |
| UNKNOWN/PROBABLE/DETECTED/POSSIBLE | #FFFF00 | -256 | Yellow |
| FALSE | #808080 | -8355712 | Gray |

## Data Field Categories

### 1. Core Fields (Always Present)
| Field | Description | Example |
|-------|-------------|---------|
| Track ID | Unique identifier | CUAS-ALPHA-550e8... |
| Classification | Track type | QUADROCOPTER |
| Position | Lat/Lon/Alt with errors | 38.889/-77.035/152m |
| Verification Status | Confidence level | VERIFIED |
| Display Name | Operator-visible ID | Q7821 |

### 2. Kinematic Fields (When Available)
| Field | Description | Example |
|-------|-------------|---------|
| Course | Direction of travel | 270° |
| Speed | Ground speed | 15 m/s |
| Climb Rate | Vertical velocity | -2 m/s |
| Error Ellipse | Position uncertainty | 25m x 15m @ 45° |

### 3. Fusion Fields (Future Capability)
| Field | Description | Data Source |
|-------|-------------|-------------|
| Track Correlation ID | Multi-sensor track ID | Fusion engine |
| Estimated Launch Point | Calculated origin | Track history analysis |
| Estimated Launch Time | Back-calculated | Trajectory analysis |
| Operator Location | Suspected controller | AdTech + RF analysis |
| Launch Vehicle | Delivery platform | Vehicle tracking fusion |

### 4. Enrichment Fields (Planned)
| Field | Description | Source |
|-------|-------------|---------|
| Track History | Previous positions | Point aggregation |
| Behavior Pattern | Movement classification | AI/ML analysis |
| Threat Assessment | Risk evaluation | Rule engine |
| Sensor Fusion Score | Multi-source confidence | Fusion algorithm |

## Status-Specific Handling

### VERIFIED (high_confidence)
```xml
<track verificationStatus="VERIFIED"/>
<remarks>Human operator verified</remarks>
<!-- Standard error values -->
<point ce="10" le="10"/>
```

### FRIENDLY (our)
```xml
<event type="a-f-G-U-C"> <!-- Friendly type -->
<color value="255"/> <!-- Blue -->
<track verificationStatus="FRIENDLY"/>
<remarks>Verified friendly UAV</remarks>
```

### PROBABLE (automoderated)
```xml
<track verificationStatus="PROBABLE"/>
<color value="-23296"/> <!-- Orange -->
<remarks>Algorithm verified - High probability</remarks>
```

### DETECTED (event)
```xml
<track verificationStatus="DETECTED"/>
<remarks>Sensor detection - Verification pending</remarks>
```

### POSSIBLE (for_moderation)
```xml
<contact callsign="Q7821-P"/> <!-- -P suffix -->
<track verificationStatus="POSSIBLE"/>
<point ce="25" le="25"/> <!-- Higher uncertainty -->
<remarks>Low confidence - Requires verification</remarks>
```

### FALSE (cancelled)
```xml
<contact callsign="R3492-X"/> <!-- -X suffix -->
<track verificationStatus="FALSE"/>
<color value="-8355712"/> <!-- Gray -->
<stale value="[immediate]"/> <!-- Remove quickly -->
<remarks>False positive - Disregard</remarks>
```

🔴  I propose the FALSE / cancelled detection event sends a Cursor on Target delete message

🔴  Key event attributes:

🔴 &nbsp;&nbsp;&nbsp;&nbsp;type='t-x-d-d'

🔴 &nbsp;&nbsp;&nbsp;&nbsp;stale='STALE DTG' (set STALE DTG to current time in case systems do not honor the delete message)

🔴  Key detail child tag:

🔴 &nbsp;&nbsp;&nbsp;&nbsp;<__forcedelete/>

```xml
<?xml version='1.0' standalone='yes'?>
<event how='m-a'
       opex='OPEX VALUE'
       qos='3-r-g'
       access='ACCESS VALUE'
       stale='STALE DTG'
       start='START DTG'
       time='TIME DTG'
       type='t-x-d-d'
       uid='TRACK_UID_VALUE'
       version='2.0'>
	<detail>
		<link uid='TRACK_UID_VALUE'
		      relation='none'
		      type='none'/>
		<__forcedelete/>
	</detail>
	<point le='9999999'
	       ce='9999999'
	       hae='1234.56'
	       lon='12.3456'
	       lat='23.4567'/>
</event>
```


## System Propagation Rules

### TAK to GCCS-J/GCCS-A
| Verification Status | Track Quality | IFF Setting |
|-------------------|---------------|-------------|
| FRIENDLY | 9 | Mode 4 Valid |
| VERIFIED | 8 | N/A |
| PROBABLE | 6 | N/A |
| DETECTED | 4 | N/A |
| POSSIBLE | 2 | 7700 |
| FALSE | 0 | N/A |

## Future Fusion Data Flow
```
1. Raw Detection → Track Generation
   - Aggregate points into continuous tracks
   - Calculate velocity from position history
   
2. Track Analysis → Origin Estimation  
   - Backtrack trajectory to launch point
   - Correlate with terrain/infrastructure
   
3. Origin + AdTech → Operator Location
   - Fuse cell tower data with launch area
   - Identify concentrated device signatures
   
4. Origin + Vehicle Tracking → Launch Platform
   - Correlate vehicle positions with launch time/location
   - Track pattern analysis
   
5. Multi-Source Fusion → Enhanced Track
   - Combine multiple sensor feeds
   - Improve accuracy and fill gaps
```

## Implementation Examples with Visual Rendering

### Example 1: Verified Hostile Drone
```xml
<event uid="CUAS-NORTH-7f8e9d10-2a3b-4c5d-6e7f-8a9b0c1d2e3f" 
       type="a-h-G-U-C">
  <point lat="38.8951" lon="-77.0364" hae="152" ce="10" le="10"/>
  <detail>
    <contact callsign="D2e3f"/>
    <uid Droid="CUAS-NORTH-7f8e9d10-2a3b-4c5d-6e7f-8a9b0c1d2e3f"/>
    <track classification="DRONE"
           verificationStatus="VERIFIED"
           course="270"
           speed="15"/>
    <_2525c symbol="SHGPUCUD--"/>
    <_2525d sidc="10031000151211000000"/>
    <_cuas_core systemId="CUAS-NORTH"
                fullTrackId="7f8e9d10-2a3b-4c5d-6e7f-8a9b0c1d2e3f"
                shortId="2e3f"/>
    <color value="-65536"/> <!-- Red -->
    <remarks>VERIFIED - Hostile UAV confirmed</remarks>
  </detail>
</event>
```

**Visual Rendering:**
- **Symbol**: Red diamond with UAV wings modifier
- **Label**: "D2e3f" in white text below symbol
- **Vector**: Line showing direction (270°) and speed
- **Size**: Standard military icon size
- **Why**: Red indicates confirmed hostile, diamond shape is standard for hostile air tracks, UAV modifier shows it's a drone

### Example 2: Possible Quadrocopter (For Moderation)
```xml
<event uid="CUAS-SOUTH-4a5b6c7d-8e9f-0a1b-2c3d-4e5f6a7b8c9d" 
       type="a-u-G-U-C">
  <point lat="38.8977" lon="-77.0365" hae="98" ce="25" le="25"/>
  <detail>
    <contact callsign="Q8c9d-P"/>
    <uid Droid="CUAS-SOUTH-4a5b6c7d-8e9f-0a1b-2c3d-4e5f6a7b8c9d"/>
    <track classification="QUADROCOPTER"
           verificationStatus="POSSIBLE"/>
    <_2525c symbol="SUGPUCUD--"/>
    <_2525d sidc="10030000151211000000"/>
    <_cuas_core systemId="CUAS-SOUTH"
                fullTrackId="4a5b6c7d-8e9f-0a1b-2c3d-4e5f6a7b8c9d"
                shortId="8c9d"/>
    <color value="-256"/> <!-- Yellow -->
    <remarks>POSSIBLE - Low confidence detection</remarks>
  </detail>
</event>
```

**Visual Rendering:**
- **Symbol**: Yellow square with UAV wings modifier
- **Label**: "Q8c9d-P" in black text (note the -P suffix)
- **Uncertainty**: Larger dashed circle around icon showing 25m error
- **Size**: Standard military icon size
- **Why**: Yellow indicates unknown/unverified, square shape is standard for unknown affiliation, -P suffix alerts operator to low confidence

### Example 3: High-Value Target with Fusion Data
```xml
<event uid="CUAS-WEST-9a8b7c6d-5e4f-3a2b-1c0d-9e8f7a6b5c4d"
       type="a-h-G-U-C">
  <point lat="38.895" lon="-77.041" hae="175" ce="10" le="10"/>
  <detail>
    <contact callsign="D5c4d"/>
    <uid Droid="CUAS-WEST-9a8b7c6d-5e4f-3a2b-1c0d-9e8f7a6b5c4d"/>
    <track classification="DRONE"
           verificationStatus="VERIFIED"
           course="090"
           speed="22"/>
    <_2525c symbol="SHGPUCUD--"/>
    <_2525d sidc="10031000151211000000"/>
    <_cuas_core systemId="CUAS-WEST"
                fullTrackId="9a8b7c6d-5e4f-3a2b-1c0d-9e8f7a6b5c4d"
                shortId="5c4d"/>
    <_cuas_fusion trackCorrelationId="FUSION-2024-001"
                  estimatedLaunchPoint="38.894,-77.042"
                  estimatedLaunchTime="2024-01-10T10:38:00Z"
                  operatorLocation="38.894,-77.042,HIGH"
                  launchVehicle="VEH-TRACK-7734"/>
    <color value="-65536"/> <!-- Red -->
    <remarks>VERIFIED - Launch site identified - Operator likely at 38.894,-77.042</remarks>
  </detail>
</event>
```

**Visual Rendering:**
- **Symbol**: Red diamond with UAV wings
- **Label**: "D5c4d" plus additional info box
- **Trail**: Dotted line back to launch point
- **Additional Icons**: Small person icon at operator location, vehicle icon for launch platform
- **Info Box**: Shows "Launch: 10:38Z, Op: HIGH confidence"
- **Why**: Multiple data sources create rich picture, red shows hostile confirmed, trail helps visualize flight path

### Example 4: Cancelled/False Track
```xml
<event uid="CUAS-EAST-3c2d1e0f-9a8b-7c6d-5e4f-3a2b1c0d9e8f"
       type="a-p-G">
  <point lat="38.891" lon="-77.038" hae="120" ce="50" le="50"/>
  <detail>
    <contact callsign="R9e8f-X"/>
    <uid Droid="CUAS-EAST-3c2d1e0f-9a8b-7c6d-5e4f-3a2b1c0d9e8f"/>
    <track classification="ROCKET"
           verificationStatus="FALSE"/>
    <_2525c symbol="SPGP------"/>
    <_2525d sidc="10031500000000000000"/>
    <_cuas_core systemId="CUAS-EAST"
                fullTrackId="3c2d1e0f-9a8b-7c6d-5e4f-3a2b1c0d9e8f"
                shortId="9e8f"/>
    <color value="-8355712"/> <!-- Gray -->
    <remarks>FALSE POSITIVE - Bird flock</remarks>
  </detail>
</event>
```

**Visual Rendering:**
- **Symbol**: Gray circle with X through it
- **Label**: "R9e8f-X" in gray text (note -X suffix)
- **Transparency**: 50% opacity to de-emphasize
- **Auto-hide**: Disappears after 30 seconds
- **Why**: Gray and X clearly show false positive, transparency reduces clutter, -X suffix ensures operators know to ignore

## Notes for Implementers

1. **Null Field Handling**: All fusion fields may be null/empty until fusion capability is online
2. **Extensibility**: Additional attributes can be added to `_cuas_fusion` and `_cuas_core` elements
3. **Backwards Compatibility**: Systems not understanding new fields should ignore them
4. **Error Propagation**: Uncertainty increases with track age and decreases with additional sensors
5. **Display Priority**: Show -P and -X suffixes prominently for operator awareness
6. **Stale Times**: Adjust based on track confidence - high confidence tracks can persist longer
7. **Symbol Fallback**: If 2525D not supported, use 2525C; if neither, use basic shapes with colors
