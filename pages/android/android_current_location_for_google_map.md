---
title: How to get my current location
last_updated: March 28, 2020
keywords: android, app, google map, current location
sidebar: mydoc_sidebar
comments: true
summary: "Tutorial of how to get current location using google map API"
permalink: android_current_location_for_google_map.html
folder: mydoc
---

## #. Before started - granting permission

```kotlin
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

Make sure you set the permission of accessing location in 'AndroidManifest.xml'. We will ask for the permission of it once more as the app is running, but for that, this has to be set.



## 1. Requesting current location

There's several ways of getting current location, but I'll use with 'FusedLocationProviderClient'. This class has 'requestLocationUpdate' function which we'll use. 



### a. Define FusedLocationProviderClient as a global variable

```kotlin
private lateinit var fusedLocationProviderClient: FusedLocationProviderClient
```

There, and now you will use the request function, but before that, we will have to define all the parameters. And also make an instance of 'FusedLocationProviderClient'.

```kotlin
private lateinit var locationRequest: LocationRequest
private lateinit var locationCallback: MyLocationCallback

private fun locationInit(){
    // making an instance
    fusedLocationProviderClient = FusedLocationProviderClient(this)
    
    // initiating parameters
    locationCallback = MyLocationCallback()
    locationRequest = LocationRequest()
    locationRequest.priority = LocationRequest.PRIORITY_HIGH_ACCURACY

}
```

- 'locationCallback' is a callback function after it gets a location information, we will define it soon.

- 'locationRequest 'is literally a request for a location. We changed its priority as 'LocationRequest.PRIORITY_HIGH_ACCURACY' to make sure it will get most accurate location data.

  

### b. Define location callback function

We will override 'LocationCallback' function, here is the example;

```kotlin
inner class MyLocationCallback:LocationCallback(){
    override fun onLocationResult(p0: LocationResult?) {
        super.onLocationResult(p0)

        var location = p0?.lastLocation

        location?.run {
            mMap.addMarker(
                MarkerOptions()
                .position(currentLocation)
                .title("My Current Location!")
                .snippet(
                    "Latitude : $latitude\n" +
                    "Longitude : $longitude"
                )
            )
            
            mMap.moveCamera(CameraUpdateFactory.newLatLngZoom(LatLng(latitude, longitude), 14f))
        }
    }
}
```

Once it gets the location information, we will make camera move to the location.



### c. Put them all into 'onMapReady'

Make a function that will contain 'requestLocationUpdates' function, and add it into 'OnMapReady' as well as 'locationInit()'.

```kotlin
override fun onMapReady(googleMap: GoogleMap) {

    ...
    
    locationInit()
	addLocationListener()
}

private fun addLocationListener(){
    fusedLocationProviderClient.requestLocationUpdates(
        locationRequest,
        locationCallback,
        null
    )
}
```



## 2. Granting a permission

### a. Anko library

To make this process easier, we will use Anko library. Add dependency on 'build.gradle' of app,

```kotlin
dependencies {
	
	...
	
    implementation "org.jetbrains.anko:anko:$anko_version" 
}
```

and on 'build.gradle' of project, add the version at the end of the file.

```kotlin
ext.anko_version = '0.10.4'
```



### b. Handling permission granting and denying

This is logic for checking permission. It's quite straight forward. 

```kotlin
private fun permissionCheck(cancel: () -> Unit, ok: () -> Unit) {
    // check permission for location
    if (ContextCompat.checkSelfPermission(
            this,
            Manifest.permission.ACCESS_FINE_LOCATION
        ) != PackageManager.PERMISSION_GRANTED
    ) {
        // has denied before
        if (ActivityCompat.shouldShowRequestPermissionRationale(
                this,
                Manifest.permission.ACCESS_FINE_LOCATION
            )
        ) {
            cancel()
            // request the permission
        } else {
            ActivityCompat.requestPermissions(
                this,
                arrayOf(Manifest.permission.ACCESS_FINE_LOCATION),
                // 1000 is a code for location permission
                1000
            )
        }
        // when granted
    } else {
        ok()
    }

}
```

We will make an information dialog for when the permission is denied. Since we added dependency of Anke's, it became very easy.

```kotlin
private fun showPermissionInfoDialog() {
    alert("Need a permission for location", "reason") {
        yesButton {
            ActivityCompat.requestPermissions(
                this@MapsActivity,
                arrayOf(Manifest.permission.ACCESS_FINE_LOCATION),
                1000
            )
        }
        noButton {
            System.exit(0)
        }
    }.show()
}
```



### c. Overriding onResume function

put the previous permission check logic into onResume override function to let it run whenever the app is running foreground.


```kotlin
override fun onResume() {
    super.onResume()

    permissionCheck(cancel = {
        // bad
        showPermissionInfoDialog()
    }, ok = {
        // good
    })
}
```



### d. Setting a request permission listener

Once you got a permission, it will call 'onRequestPermissionsResult' function. It's not necessary, but we will put 'addLocationListener()' for when the permission is granted.

```kotlin
override fun onRequestPermissionsResult(
    requestCode: Int,
    permissions: Array<out String>,
    grantResults: IntArray
) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults)
    when (requestCode) {
        1000 -> {
            if ((grantResults.isNotEmpty()
                        && grantResults[0] == PackageManager.PERMISSION_GRANTED)
            ) {
                // granted
                addLocationListener()
            } else {
                toast("denied")
            }
        }
    }
}
```



## 3. DONE!

If you went this far, it should work fine. The result should be like this;

### a. When first started

{% include image.html file="android/android_current_location_for_google_map_01.png"  %}

### b. When denied

{% include image.html file="android/android_current_location_for_google_map_02.png"  %}

### c. When granted

{% include image.html file="android/android_current_location_for_google_map_03.png"  %}

Default location of Android Emulator is googleplex, so if you are seeing googleplex, you are good. If you changed the setting of your current location in Android Emulator's setting, you would see your own location like I do.













