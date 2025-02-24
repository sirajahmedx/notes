# Google Maps Integration in React Modals: Issue & Solution

## Problem Description

When implementing Google Maps within React modals, several issues can occur:

1. Map not reinitializing when opening the modal with different locations
2. Map instance persisting after modal closes
3. Loading states not handling properly
4. Multiple modal instances conflicting with each other
5. Memory leaks from improper cleanup

These issues typically manifest when:
- Opening different bookings/locations in succession
- Rapidly switching between modal views
- Having multiple map instances on the same page

## Root Cause

The primary causes of these issues are:

1. The Google Maps script loading strategy (`LoadScript` component) doesn't handle modal contexts well
2. React's state management with map instances in modals
3. Improper cleanup of map instances when modals close
4. Missing forced reinitialization when locations change

## Solution

### Updated Implementation

```javascript
"use client";

import { useState, useEffect } from "react";
import { GoogleMap, useJsApiLoader, Marker } from "@react-google-maps/api";
import { Card, CardContent } from "@/components/ui/card";
import { Skeleton } from "@/components/ui/skeleton";

const GoogleMapLocation = ({ location }) => {
  const [map, setMap] = useState(null);
  const [mapKey, setMapKey] = useState(0);

  const { isLoaded } = useJsApiLoader({
    id: 'google-map-script',
    googleMapsApiKey: process.env.NEXT_PUBLIC_GOOGLE_MAPS_API_KEY || "",
  });

  const center = {
    lat: location?.coordinates?.[1] || 33.6844,
    lng: location?.coordinates?.[0] || 73.0479,
  };

  // Reset map when location changes
  useEffect(() => {
    if (map) {
      map.panTo(center);
      map.setZoom(14);
    }
  }, [center, map]);

  // Cleanup when component unmounts
  useEffect(() => {
    return () => {
      if (map) {
        setMap(null);
        setMapKey(prev => prev + 1);
      }
    };
  }, [map]);

  // Force map reload when modal opens with new location
  useEffect(() => {
    setMapKey(prev => prev + 1);
  }, [location?.coordinates]);

  const onLoad = (map) => {
    setMap(map);
  };

  const onUnmount = () => {
    setMap(null);
  };

  if (!isLoaded) {
    return (
      <Card className="overflow-hidden">
        <CardContent className="p-0">
          <Skeleton className="w-full h-[300px]" />
        </CardContent>
      </Card>
    );
  }

  return (
    <Card className="overflow-hidden">
      <CardContent className="p-0">
        <GoogleMap
          key={mapKey}
          mapContainerStyle={{
            width: "100%",
            height: "300px",
            borderRadius: "0.5rem",
          }}
          center={center}
          zoom={14}
          onLoad={onLoad}
          onUnmount={onUnmount}
          options={{
            disableDefaultUI: true,
            zoomControl: true,
            streetViewControl: true,
            fullscreenControl: true,
          }}
        >
          <Marker position={center} />
        </GoogleMap>
      </CardContent>
    </Card>
  );
};

export default GoogleMapLocation;
```

### Key Improvements

1. **Script Loading**: 
   - Switched from `LoadScript` to `useJsApiLoader` hook
   - Better handling of script initialization and cleanup

2. **State Management**:
   - Added `mapKey` state for forced remounting
   - Proper map instance management with `useState`

3. **Cleanup Handling**:
   - Implemented cleanup effect for component unmounting
   - Proper map instance disposal

4. **Location Updates**:
   - Added forced reinitialization when location changes
   - Smooth transitions with `panTo`

### Usage Example

```javascript
// In your modal component
<div className="mt-2 rounded-md overflow-hidden">
  <GoogleMapLocation
    location={{
      coordinates: [longitude, latitude]
    }}
  />
</div>
```

### Prerequisites

1. Install required dependencies:
```bash
npm install @react-google-maps/api
```

2. Set up Google Maps API key in your environment variables:
```env
NEXT_PUBLIC_GOOGLE_MAPS_API_KEY=your_api_key_here
```

## Benefits

The improved implementation provides:

1. ✅ Proper reinitialization with new locations
2. ✅ Clean modal integration
3. ✅ Memory leak prevention
4. ✅ Smooth loading states
5. ✅ Multiple modal support
6. ✅ Proper cleanup on unmount

## Troubleshooting

If you encounter issues:

1. Verify your Google Maps API key is correctly set
2. Ensure coordinates are in the correct format `[longitude, latitude]`
3. Check if the modal component is properly unmounting
4. Verify the location prop is being updated correctly

## Common Pitfalls to Avoid

1. Don't use `LoadScript` component in modals
2. Don't forget to handle loading states
3. Don't skip cleanup when component unmounts
4. Don't forget to force remount when location changes

This solution ensures proper Google Maps functionality in React modals while maintaining performance and preventing common issues.
