# Smart Marker For Google Maps and Mapbox
### This library for helper movement marker in your maps

<p align="center">
  <img src="https://i.ibb.co/Wspktd7/ezgif-com-gif-maker-1.gif"/>
</p>

## Table of Content
- [Download](https://github.com/utsmannn/SmartMarker#download)
- [Marker](https://github.com/utsmannn/SmartMarker#add-marker)
    - [Google Maps](https://github.com/utsmannn/SmartMarker#google-maps)
    - [Mapbox](https://github.com/utsmannn/SmartMarker#mapbox)
- [Move Marker](https://github.com/utsmannn/SmartMarker#move-your-marker)
- [Location Watcher](https://github.com/utsmannn/SmartMarker#location-watcher-extension)
    - [Installation](https://github.com/utsmannn/SmartMarker#installation)
    - [Realtime Level](https://github.com/utsmannn/SmartMarker#realtime-level)
    - [Use](https://github.com/utsmannn/SmartMarker#use)
    - [Permission Helper](https://github.com/utsmannn/SmartMarker#permission-helper)
- [Other Extensions](https://github.com/utsmannn/SmartMarker#other-extensions)
- [Simple Example](https://github.com/utsmannn/SmartMarker#simple-example)
    - [Google Maps Activity](https://github.com/utsmannn/SmartMarker#google-maps-1)
    - [Mapbox Activity](https://github.com/utsmannn/SmartMarker#mapbox-1)

## Download
[ ![Download](https://api.bintray.com/packages/kucingapes/utsman/com.utsman.smartmarker/images/download.svg) ](https://bintray.com/kucingapes/utsman/com.utsman.smartmarker/_latestVersion) <br>
```groovy

// the core library
implementation 'com.utsman.smartmarker:core:1.3.2@aar'

// extension for google maps
implementation 'com.utsman.smartmarker:ext-googlemaps:1.3.2@aar'

// extension for Mapbox
implementation 'com.utsman.smartmarker:ext-mapbox:1.3.2@aar'

```
For extensions, you don't need to add mapbox extensions if you not use the sdk mapbox. As well as the google map sdk.

## Add Marker
### Google Maps
Use the default method as usual for google maps. Reference for add marker in google maps [is here](https://developers.google.com/maps/documentation/android-sdk/map-with-marker) <br>
And code look like this
```kotlin
val markerOption = MarkerOptions()
        .position(latLng)

val marker = map.addMarker(markerOption) // this your marker

```

### Mapbox
For Mapbox, adding marker is little hard, so I create helper for it, ***and you must coding after setup ```style```***
```kotlin
// define marker options
val markerOption = MarkerOptions.Builder() // from 'com.utsman.smartmarker.mapbox.MarkerOptions'
    .setId("marker-id", true) // if marker id need unique id with timestamp, default is false
    .setIcon(R.drawable.ic_marker, true) // if marker is not vector, use 'false'
    .setPosition(latLng)
    .setRotation(rotation)
    .build(context)

// add your marker
val marker = map.addMarker(markerOption)
```

## Move Your Marker
```kotlin
SmartMarker.moveMarkerSmoothly(marker, latLng) 
// or for disable rotation
SmartMarker.moveMarkerSmoothly(marker, latLng, false)

// with extensions for kotlin
marker.moveMarkerSmoothly(latLng)
// or for disable rotation
marker.moveMarkerSmoothly(latLng, false)

```

## Location Watcher Extension
I create location extensions for get your location every second with old location and new location, you can setup realtime level. <br>

### Installation
```groovy
implementation 'com.utsman.smartmarker:ext-location:1.2.5@aar'

// for extensions watcher location, you need some library with latest versions
implementation 'com.google.android.gms:play-services-location:17.0.0'
implementation 'pl.charmas.android:android-reactive-location2:2.1@aar'
implementation 'io.reactivex.rxjava2:rxjava:2.2.12'
implementation 'io.reactivex.rxjava2:rxandroid:2.1.1'
implementation 'com.karumi:dexter:6.0.0'
```

### Realtime Level
You can setup realtime level for get every second update, `locationWatcher.getLocationUpdate(priority, listener)`


| level | ms |
| -- | -- |
| `LocationWatcher.Priority.JEDI` | 3 ms |
| `LocationWatcher.Priority.VERY_HIGH` | 30 ms |
| `LocationWatcher.Priority.HIGH` | 50 ms |
| `LocationWatcher.Priority.MEDIUM` | 300 ms |
| `LocationWatcher.Priority.LOW` | 3000 ms |
| `LocationWatcher.Priority.VERY_LOW` | 8000 ms |

### Use
```kotlin
// define location watcher
val locationWatcher: LocationWatcher = LocationWatcher(context)

// get location once time
locationWatcher.getLocation { location ->
    // your location result
}

// get location update every second
locationWatcher.getLocationUpdate(LocationWatcher.Priority.HIGH, object : LocationUpdateListener {
    override fun oldLocation(oldLocation: Location?) {
        // your location realtime result
    }
    
    override fun newLocation(newLocation: Location?) {
        // your location past with delay 30 millisecond (0.03 second)
    }

    override fun failed(throwable: Throwable?) {
        // if location failed
    }
})

// stop your watcher in onStop activity
override fun onDestroy() {
    locationWatcher.stopLocationWatcher()
    super.onDestroy()
}

```

### Permission helper
If you have not applied location permission for your app, you can be set permission with adding context before listener.
```kotlin
// get location once time with permission helper
locationWatcher.getLocation(context) { location ->
    // your location result
}

// get location update every second with permission helper
locationWatcher.getLocationUpdate(context, LocationWatcher.Priority.HIGH, object : LocationUpdateListener {
    override fun oldLocation(oldLocation: Location?) {
        // your location realtime result
    }
    
    override fun newLocation(newLocation: Location?) {
        // your location past with delay 30 millisecond (0.03 second)
    }

    override fun failed(throwable: Throwable?) {
        // if location failed
    }
})
```

Don't forget to add location permission ```android.permission.ACCESS_FINE_LOCATION``` for your apps
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
```

## Other Extensions
```kotlin
// Convert Location to LatLng for Google Maps ('com.google.android.gms.maps.model.LatLng')
location.toLatLngGoogle()

// Convert Location to LatLng for Mapbox ('com.mapbox.mapboxsdk.geometry.LatLng')
location.toLatLngMapbox()

// use marker as vector for Google Maps
bitmapFromVector(context, R.drawable.marker_vector)

```

## Simple Example
### Google Maps
```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var locationWatcher: LocationWatcher
    private var marker: Marker? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        locationWatcher = LocationWatcher(this)
        val googleMapsView = (google_map_view as SupportMapFragment)

        locationWatcher.getLocation(this) { loc ->
            googleMapsView.getMapAsync {  map ->
                val markerOption = MarkerOptions()
                    .position(loc.toLatLngGoogle())
                    .icon(bitmapFromVector(this@MainActivity, R.drawable.ic_marker))

                marker = map.addMarker(markerOption)
                map.animateCamera(CameraUpdateFactory.newLatLngZoom(loc.toLatLngGoogle(), 17f))
            }
        }

        // device tracker
        locationWatcher.getLocationUpdate(this, LocationWatcher.Priority.HIGH, object : LocationUpdateListener {
            override fun oldLocation(oldLocation: Location) {

            }

            override fun newLocation(newLocation: Location) {
                // move your marker smoothly with new location
                marker?.moveMarkerSmoothly(newLocation.toLatLngGoogle())
                                    
                // or use class SmartMarker for java
                // SmartMarker.moveMarkerSmoothly(marker, newLocation.toLatLngMapbox())    
            }

            override fun failed(throwable: Throwable?) {
            }
        })
    }
}
```
### Mapbox
```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var locationWatcher: LocationWatcher
    private var marker: Marker? = null // from 'com.utsman.smartmarker.mapbox.Marker'

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // setup instance with api key mapbox before 'setContentView'
        Mapbox.getInstance(this, "sk.eyJ1Ijoia3VjaW5nYXBlcyIsImEiOiJjazI1eXFqYzQxcGZjM25ueTZiMHU3aDl3In0.EfIuu2NSv2CacIKEhkXhCg")
        setContentView(R.layout.activity_main)

        locationWatcher = LocationWatcher(this)
      
        locationWatcher.getLocation(this) { loc ->
            mapbox_view.getMapAsync {  map ->
                
                // set style before setup your marker
                map.setStyle(Style.OUTDOORS) { style -> 
                
                    val markerOption = MarkerOptions.Builder() // from 'com.utsman.smartmarker.mapbox.MarkerOptions'
                           .setId("marker-id")
                           .addIcon(R.drawable.ic_marker, true)
                           .addPosition(loc.toLatLngMapbox())
                           .build(this)
    
                    val markerLayer = map.addMarker(markerOption)
                    marker = markerLayer.get("marker-id")
                    
                    map.animateCamera(CameraUpdateFactory.newLatLngZoom(loc.toLatLngGoogle(), 17f))
                }
            }
        }

        // device tracker
        locationWatcher.getLocationUpdate(this, LocationWatcher.Priority.HIGH, object : LocationUpdateListener {
            override fun oldLocation(oldLocation: Location) {

            }

            override fun newLocation(newLocation: Location) {
                // move your marker smoothly with new location
                marker?.moveMarkerSmoothly(newLocation.toLatLngGoogle())

                // or use class SmartMarker for java
                // SmartMarker.moveMarkerSmoothly(marker, newLocation.toLatLngMapbox())  
            
            }

            override fun failed(throwable: Throwable?) {
            }
        })
    }
}
```


## My Other Libraries
- [Recycling](https://github.com/utsmannn/Recycling) <br>
A Library for make an easy and faster RecyclerView without adapter

- [rmqa](https://github.com/utsmannn/rmqa)<br>
Rabbit Message Queue for Android

- [Anko Navigation Drawer](https://github.com/utsmannn/AnkoNavigationDrawer)<br>
Library for implementation Navigation Drawer with styles in Anko

- [Easy Google Login](https://github.com/utsmannn/EasyGoogleLogin)<br>
Library for Simplify Firebase Authenticate Google Auth
---
```
Copyright 2019 Muhammad Utsman

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
---
makasih