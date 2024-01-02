# UMPUtils

**UMPUtils** is an Android library supporting Google's _User Messaging Platform_ (UMP) with
functionality not offered by UMP itself.

## Usage

Check the level of consent given by users of your app:
```kotlin

// provide a bit string containing all vendors used by your ad setup
val targetVendorConfiguration = BitString("11001001")

// get an interpretation of the currently stored consent data
val interpretation = UMPUtility.interpretConfiguration(context, targetVendorConfiguration)

if (interpretation.userDidNotConsent) {
    // do something if the user likely clicked "Do not consent" in the UMP dialog
}

if (interpretation.adDisplayLikely) {
    // based on user consent, it is likely that ads can be shown
} else if (interpretation.adDisplayPossible) {
    // user did not decline completely, but trying to display ads will likely result in "no fills" most of the time
}
```

or check for individual ad types:
```kotlin
val targetVendorConfiguration = BitString("11001001") //
val interpretation = UMPUtility.interpretConfiguration(context, targetVendorConfiguration)

when (interpretation.adLevel) {
    ConsentInterpretation.AdLevel.PERSONALIZED -> {
        // displaying personalized ads should be possible
    }

    ConsentInterpretation.AdLevel.NON_PERSONALIZED -> {
        // at most displaying non-personalized ads should be possible
    }

    ConsentInterpretation.AdLevel.LIMITED -> {
        // at most displaying limited ads should be possible
    }
    
    else -> {
        // no ads can be displayed
    }
}
```

Use the consent status as cached by UMP boiled down to a simple boolean:
```kotlin
if (UMPUtility.consentDialogDisplayNecessary(context)) {
    // dialog needs to be displayed to the user, because consent status is either UNKNOWN or REQUIRED
} else {
    // display unnecessary, consent status is either NOT_REQUIRED or already OBTAINED
}
```

or alternatively:

```kotlin
val statusInt = UMPUtility.getPreviousConsentStatus(context)

when (statusInt) {
    ConsentInformation.ConsentStatus.UNKNOWN -> {
        // no information stored; default value used by UMP
    }
    
    ConsentInformation.ConsentStatus.NOT_REQUIRED -> {
        // value used by UMP if user does NOT need to see UMP dialog (i.e. they are outside EEA/UK)
    }
    
    ConsentInformation.ConsentStatus.REQUIRED -> {
        // value used by UMP if user does need to see UMP dialog
    }
    
    ConsentInformation.ConsentStatus.OBTAINED -> {
        // value used by UMP if user previously saw UMP dialog
    }
}
```

Get the consent age in days:
```kotlin
val ageInDays: Long = UMPUtility.getConsentAgeInDays(context)

if (ageInDays > 365L) {
    // do something if consent is older than a year
}
```

Delete all consent data, for example if you stop using UMP, or because an age check determined that the consent is outdated:
```kotlin
UMPUtility.deleteAllConsentData(context)
```

**Warning:** Calling this function will remove all entries with keys having an "IABTCF_" prefix from
the default shared preferences of the app. Make sure that none of your own entries use matching keys.


## Legalese
_Google_, _AdMob_, _Android_ and other names related to their products are trademarked by Google LLC.
These names are necessarily used in this repository to reference the individual products.
No trademark infringement is intended.
This repository is not endorsed by or affiliated with Google in any way.

### License

TODO license of this library