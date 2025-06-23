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
|----------------|-----------------|-------------|----------------|----------|--------------|------------|------------|
| `high_confidence` | VERIFIED | Human operator confirmed | None | a-u-G-U-C* | SUGPUC---- | 10030000151211000000 |<img alt="" src="data:image/png;base64,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" />|
| `our` | FRIENDLY | Human verified as friendly | None | a-f-G-U-C | SFGPUC---- | 10033000151211000000 ||
| `automoderated` | PROBABLE | Algorithm confirmed | None | a-u-G-U-C | SUGPUC---- | 10030000151211000000 ||
| `event` | DETECTED | Sensor confirmed | None | a-u-G-U-C | SUGPUC---- | 10030000151211000000 ||
| `for_moderation` | POSSIBLE | Sensor uncertain | -P | a-u-G-U-C | SUGPUC---- | 10030000151211000000 ||
| `cancelled` | FALSE | Determined false positive | -X | a-p-G | SPGP------ | 10031500000000000000 ||

*Changes to a-h-G-U-C when hostile determination is made

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
