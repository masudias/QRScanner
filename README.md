First of all, you will need some gradle dependencies. So in your `build.gradle` file, add the following dependencies. 

    dependencies {
        compile 'com.android.support:design:25.3.1'
        compile 'com.google.android.gms:play-services-vision:10.2.1'
    }

Then, you need to have the following five classes in your project. I am adding the classes here. Please import the missing classes if necessary. 

 1. [CameraSource.java][1]
 2. [CameraSourcePreview.java][2]
 3. [BarcodeCaptureActivity.java][3]
 4. [BarcodeGraphicTracker.java][4]
 5. [BarcodeTrackerFactory.java][5]

Now `BarcodeCaptureActivity` has a layout which you need to put in your `layout` folder as well. 

Here's the `barcode_capture.xml` layout that you need. 

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/topLayout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:keepScreenOn="true"
        android:orientation="vertical">
    
        <!-- Do not forget to replace with your package name where the class is located -->
        <com.example.yourpackage.camera.CameraSourcePreview
            android:id="@+id/preview"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    
    </LinearLayout>

You will need some strings and an activity entry in manifest for the `BarcodeCaptureActivity`. Here are the strings that you need to put in your `strings.xml`. 

    <!-- QR Code related strings -->
    <string name="permission_camera_rationale">Access to the camera is needed for detection</string>
    <string name="no_camera_permission">This application cannot run because it does not have the camera permission.  The application will now exit.</string>
    <string name="low_storage_error">Face detector dependencies cannot be downloaded due to low device storage</string>
    <string name="ok">OK</string>

And the `AndroidManifest.xml` should have a new entry for the `BarcodeCaptureActivity` like the following. 

    <activity
        android:name=".util.scanner.BarcodeCaptureActivity"
        android:theme="@style/Theme.AppCompat" />

Now your setup is done and you are ready to open your camera for scanning a barcode or QR code. Just call the following `initiateScan` function where necessary.

    public static final int RC_BARCODE_CAPTURE = 9001;

    public void initiateScan() {
        Intent intent = new Intent(YourActivity.this, BarcodeCaptureActivity.class);
        startActivityForResult(intent, RC_BARCODE_CAPTURE);
    }

Please note that, you need to ask for camera permission to the user before you call `initiateScan` function. On granting the permission of the camera you will call the `initiateScan` function. 

The `initiateScan` function will open the scanner and then after a successful scan, it will return to the calling `Activity` or `Fragment`. So you need to have a `onActivityResult` function in the calling `Activity` or `Fragment`. 

    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        if (resultCode == CommonStatusCodes.SUCCESS && requestCode == RC_BARCODE_CAPTURE) {
            if (data == null) return;
            Barcode barcode = data.getParcelableExtra(BarcodeCaptureActivity.BarcodeObject);
            final String scanResult = barcode.displayValue;
            if (scanResult == null) return;

            doSomethingWithTheScanResult(scanResult);
        }
    }

Do not forget to add the `CAMERA` permission in your `AndroidManifest.xml` file as well. 

    <uses-permission android:name="android.permission.CAMERA" />

Hope that helps for easier integration of QR code scanner using Google Vision API.


  [1]: https://gist.github.com/masudias/f8f37e8cf5e78c2c704cb4b6cbc5ddc8
  [2]: https://gist.github.com/masudias/4810ccf3ae0c577e5da18d728afd1bb0
  [3]: https://gist.github.com/masudias/87cb9e525dc846d7b02fd8b5c5186ead
  [4]: https://gist.github.com/masudias/15063d72dc9dde69858055cff0527798
  [5]: https://gist.github.com/masudias/3f3b142b041b2dd2b33c6f114af57575
  [6]: https://github.com/masudias/QRScanner
