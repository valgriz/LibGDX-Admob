Setup Admob in a GDX application
Valgriz - valgriz.com - youtube.com/valgriz

Original Post:
http://www.quagem.com/archives/642#more-642

Update to the latest version of both LibGDX and GooglePlayServices

Construct a new LibGDX project

Import entire project into Android Studio

Resolve any Gradle issues on import

Right click on android module > Open module settings > Dependencies > Green Plus (upper right corner) > Library Dependency

Select support-v4 and play-services library to import

If you do not see these libraryes, open your SDK manager FROM ANDROID STUDIO and install libraries from that

Make sure to click the "Sync Project with Gradle Files" button before you proceed

Open up AndroidLaunches.java

Change "extends AndroidApplication" to "extends FragmentActivity"

add "implements AndroidFragmentApplication.Callbacks"

add unimplemented methods

Create a new class called "GameFragment" in the same package as your AndroidLauncher class

public class GameFragment extends AndroidFragmentApplication {
	public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
		return initializeForView(new Main());
	}
}

Go back to AndroidLauncher and remove all lines from the onCreate() method, except the super.onCreate()

Add the following line of code:
GameFragment fragment = new GameFragment();

Right click on res > New directory > Name the new directory "layout"

Right click on layout > New > Layout resource file > Name the new layout main_layout.xml

Paste the following code in the layout:

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:ads="http://schemas.android.com/apk/res-auto"

    tools:context=".MainActivity"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">

    <FrameLayout
        android:id="@+id/GameView"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_alignParentTop="true"
        android:layout_centerHorizontal="true"
        android:layout_above="@+id/adView" />

    <com.google.android.gms.ads.AdView
        android:id="@+id/adView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_centerHorizontal="true"
        ads:adUnitId="ca-app-pub-YOURID"
        ads:adSize="SMART_BANNER" />

</RelativeLayout>

Open up AndroidManifest.xml under the manifests directory

Paste the following code right after the <uses-sdk/> block:
 <uses-permission android:name="android.permission.INTERNET"/>
 <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

Paste the following code right after </activity> and before </application>

 <activity
   android:name="com.google.android.gms.ads.AdActivity"
   android:theme="@android:style/Theme.Translucent"
   android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"/>

Sync Project with Gradle Files, ensure there are no errors so far

Go back to AndroidLauncher

Add the following line of code under super.onCreate()
setContentView(R.layout.main_layout);


Add the following line of code under GameFragment declaration:
FragmentTransaction tr = getSupportFragmentManager().beginTransaction();

If you scroll to the top where it says "imports"

Change
import android.app.FragmentTransaction;
to
import android.support.v4.app.FragmentTransaction;

Add the following line of code after FragmentTransaction tr declaration
tr.replace(R.id.GameView, fragment);

In between where the class begins, and the start of the first method, add the following line of code:
AdView adView;


Add the following lines of code immediately after tr.replace()
tr.commit();

		adView = (AdView) findViewById(R.id.adView);

		AdRequest adRequest = new AdRequest.Builder()
				.tagForChildDirectedTreatment(true)
				.build();

		adView.loadAd(adRequest);

Sync Project with Gradle Files
Run app.
QED ~GG

***ADVISORY: In the process of referencing "R.id...." an error may state "R can not be resolved"
to fix this, copy all of the code from your android layout.xml.  Delete the xml file, sync the project.
Create a new .xml file with a different name, sync gradle, clean project, rebuild project then try again***

