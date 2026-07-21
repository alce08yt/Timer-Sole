LOCATION-BASED TANNING TIMER — build notes
============================================

SUGGESTED PACKAGE NAME (Play Console):
  com.alessio.tanningtimer
  (must stay identical forever once published — double-check before first upload)

WHAT'S IN THIS FOLDER:
  index.html      - the app (fully in English)
  manifest.json   - PWA manifest (name, icons, theme)
  sw.js           - service worker (offline cache + notification handling)
  icon-192.png / icon-512.png - app icons

HOW TO PUBLISH (recap):
  1. Upload these files to GitHub Pages (or any static host) so you have a
     public https:// URL.
  2. Go to pwabuilder.com, paste the URL, generate the Android package.
     - For a quick installable APK: choose "Signed APK".
     - For Play Store: choose "Google Play package" (gives you an .aab +
       a keystore file — KEEP THE KEYSTORE, you need it for every future update).
  3. Create a Google Play Console account (one-time $25) and upload the .aab.

FEATURES IMPLEMENTED:
  - Fitzpatrick skin type selector (I-VI)
  - UV index via GPS (Open-Meteo, worldwide) or manual city search
    (Open-Meteo geocoding, works for any country/city, not just Italy)
  - Manual UV entry fallback
  - Daily UV curve chart
  - Rotation timer (timestamp-based, self-corrects after phone sleep/throttling)
  - Wake Lock (keeps screen on while timer runs, main defense against
    background throttling)
  - Web Notifications: 1-minute-before warning + flip alert, both with
    sound + vibration
  - Persistent-style status notification updated every 30s while running
    ("Back - 04:20 left")
  - App badge (icon shows minutes remaining)

KNOWN LIMITATION (by design, not a bug):
  Web notifications cannot be made "ongoing/non-dismissible" the way a native
  Android foreground-service notification can. If the phone deep-sleeps for a
  long time with the screen off and the app not in focus, timing precision
  can degrade. A fully native background service would need a from-scratch
  Android app (Kotlin), not a wrapped web app.

ADS — NOT YET INTEGRATED (waiting on AdMob IDs from Alessio):
  Ads cannot be added by editing this HTML — Play Store policy requires
  AdMob (not AdSense) for apps, and AdMob's SDK must be added to the native
  Android project PWABuilder generates, not to the website itself.

  Once you have from admob.google.com:
    - App ID (goes in AndroidManifest.xml)
    - Ad Unit ID, type Interstitial (goes in MainActivity code)

  Steps (need Android Studio on a PC for this one part only):
    1. On PWABuilder, download "Source code" for Android instead of the
       ready APK/AAB.
    2. Open the project in Android Studio.
    3. Add to app/build.gradle:
         implementation 'com.google.android.gms:play-services-ads:23.0.0'
    4. In AndroidManifest.xml, inside <application>, add:
         <meta-data
           android:name="com.google.android.gms.ads.APPLICATION_ID"
           android:value="YOUR_ADMOB_APP_ID"/>
    5. In MainActivity (the TWA launcher activity), initialize MobileAds
       and load + show one InterstitialAd before the TWA/timer screen opens.
       Show it once per app open, not repeatedly — keeps it non-invasive.
    6. If you have EU/EEA users, add Google's User Messaging Platform (UMP)
       SDK for GDPR consent before requesting ads.
    7. Rebuild the .aab in Android Studio and upload that to Play Console
       instead of the ad-free one.
