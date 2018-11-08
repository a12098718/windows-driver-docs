---
title: Mobile Plans asynchronous fulfillment
description: Mobile Plans asynchronous fulfillment
ms.assetid: 56AB67D6-59A9-4483-B455-2FCC309C8903
keywords:
- Windows Mobile Plans asynchronous fulfillment mobile operators
ms.date: 10/24/2018
ms.localizationpriority: medium
---

# Mobile Plans asynchronous fulfillment

[!include[Mobile Plans Beta Prerelease](../mobile-plans-beta-prerelease.md)]

## Overview

This topic provides API details for mobile operators that need asynchronous fulfillment for either of the following scenarios:

- Asynchronous connectivity: when the mobile operator can't immediately grant connectivity to a user when the user downloads a profile after purchase.
- Asynchronous profile delivery: when the profile is not available for download at purchase time.

## Asynchronous connectivity

The following diagram shows the high level flow for how the Mobile Plans program supports delayed connectivity.

![Mobile Plans delayed connectivity sequence diagram](images/dynamo_async_connectivity_flow.png)

After the user successfully completes a purchase that requires a profile download from the mobile operator's MO Direct portal, the portal informs the Mobile Plans application that it should trigger the delayed connectivity flow using the `DataMart.notifyPurchaseWithProfileDownload` API. 

### DataMart.notifyPurchaseWithProfileDownload

| Parameter name | Type | Description |
| --- | --- | -- |
| purchaseMetadata | Object | This object contains metadata about the user's purchase. This includes details about the user account, the purchase method or instrument, details if the user is adding a new line, and the name of the plan that the user purchased. All these are used for reporting. |
| activationCode | String | The activation code for downloading the eSIM profile. The ICCID for the profile is inferred from the profile metadata. |
| networkRegistrationInterval | Unsigned integer | The time needed for the mobile operator to provision connectivity to the user. The Mobile Plans app attempts to register to the network within the specified time interval, in minutes. **Note** This time is rounded to the nearest 15 minute interval. For example, if this is set as 5 minutes, the application tries to re-register to the network after approximately 15 minutes (but it might take longer). |

The following Javascript function shows an example of the API to inform the application that the user purchase requires a delayed provisioning of connectivity.

 ```Javascript
function finishPurchaseWithDownload() {
        var metadata = DataMart.createPurchaseMetaData();
        metadata.userAccount = DataMartUserAccount.new;
        metadata.purchaseInstrument = DataMartPurchaseInstrment.new;
        metadata.moDirectStatus = DataMartMoDirectStatus.complete;
        metadata.line = DataMartLineType.new;
        metadata.planName = "2GB Monthly";
        DataMart.notifyPurchaseWithProfileDownload(metadata, "1$smdp.address$", 15);
}
```

| Property Name | Type | Description | Example |
| --- | --- | --- | --- |
| userAccount | String | Possible values: <ul><li>New: Indicates that a new user account was created by the user.</li><li>Existing: Indicates that the user logged on with an existing user account.</li><li>Bailed: Indicates that the user ended the purchase flow at this step.</li><li>None: Indicates that the user didn’t reach this step.</li></ul> | "userAccount":"New" |
| purchaseInstrument | String | Possible values: <ul><li>New: Indicates that a new user account was created by the user.</li><li>Existing: Indicates that the user logged on with an existing user account.</li><li>Bailed: Indicates that the user ended the purchase flow at this step.</li><li>None: Indicates that the user didn’t reach this step.</li></ul> | "purchaseInstrument":"New" |
| line | String | Possible values: <ul><li>New: Indicates that a SIM card was added by the user account.</li><li>Existing: Indicates that the user transferred an existing line to the device.</li><li>Bailed: Indicates that the user ended the purchase flow at this step.</li><li>None: Indicates that the user didn’t reach this step.</li></ul> | "line":New" |
| moDirectStatus | String | Possible values: <ul><li>Complete: Indicates that the user completed the purchase successfully.</li><li>ServiceError: Indicates that the user was unable to complete the purchase due to an MO service error.</li><li>InvalidSIM: Indicates that the ICCID passed to the portal was incorrect.</li><li>LogOnFailed: indicates that the user failed to log in to the MO portal.</li><li>PurchaseFailed: Indicates that the purchase failed due to a billing error.</li><li>ClientError: Indicates that invalid arguments were passed to the portal.</li>BillingError: Indicates that there was an error with the user billing.</li></ul> | "moDirectStatus":"Complete" |
| planName | String | For a successful transaction, this field must not be empty and must provide a descriptive plan name. For an unsuccessful transaction, this field must be an empty string. | "planName":"2GB Monthly"|


## Asynchronous profile delivery

The following diagram shows the high level flow for how the Mobile Plans program supports delayed profile download.

![Mobile Plans delayed profile download sequence diagram](images/dynamo_async_profile_flow.png)

After the user successfully completes a purchase that requires a profile download from the mobile operator's MO Direct portal, the portal informs the Mobile Plans application that it should trigger the delayed profile download flow using the `DataMart.notifyPurchaseDelayedProfile` API. 

### DataMart.notifyPurchaseDelayedProfile

| Parameter Name | Type | Description |
| --- | --- | -- |
| purchaseMetadata | Object | This object contains metadata about the user's purchase. This includes details about the user account, the purchase method or instrument, details if the user is adding a new line, and the name of the plan that the user purchased. All these are used for reporting. |
| profileDownloadDelayInterval | Unsigned integer | The time needed for the mobile operator to create the profile for the user profile and have it ready for download from SM-DS. The Mobile Plans app downloads the profile from SM-DS after this interval, in minutes. **Note** This time is rounded to the nearest 15 minute interval. For example, if this is set as 5 minutes, the application tries to download to the network after approximately 15 minutes (may take longer)|

The following Javascript function shows an example of the API to inform the application that the user purchase requires a delayed profile download using SM-DS.

 ```Javascript
function finishPurchaseWithSMDS() {
        var metadata = DataMart.createPurchaseMetaData();
        metadata.userAccount = DataMartUserAccount.new;
        metadata.purchaseInstrument = DataMartPurchaseInstrment.new;
        metadata.moDirectStatus = DataMartMoDirectStatus.complete;
        metadata.line = DataMartLineType.new;
        metadata.planName = "2GB Monthly";
        DataMart.notifyPurchaseDelayedProfile(metadata, 15);
}
```

## Inline profile delivery

The following diagram shows the high level flow for how the Mobile Plans program supports downloading a profile without control leaving the MODirect portal

![Mobile Plans inline profile download sequence diagram](images/dynamo_inline_profile_flow.png)

When the MO Direct portal is ready for a profile download, install, and activation to occur, the portal should call `DataMartInlineProfile.notifyInlineProfileDownload`.

### DataMartInlineProfile.notifyInlineProfileDownload

| Parameter Name | Type | Description |
| --- | --- | -- |
| purchaseMetadata | Object | This object contains metadata about the user's purchase. This includes details about the user account, the purchase method or instrument, details if the user is adding a new line, and the name of the plan that the user purchased. All these are used for reporting. |
| activationCode | String | The activation code for downloading the eSIM profile. The ICCID for the profile is inferred from the profile metadata. |

The following Javascript function shows an example of the API to inform the application that an inline profile download should start.

```Javascript
function NotifyDataMart() { 
    var purchaseMetaData = DataMart.createPurchaseMetaData(); 
    purchaseMetaData.userAccount = DataMartUserAccount.new; 
    purchaseMetaData.purchaseInstrument = DataMartPurchaseInstrument.new; 
    purchaseMetaData.lineType = DataMartLineType.new; 
    purchaseMetaData.modirectStatus = DataMartMoDirectStatus.complete; 
    purchaseMetaData.planName = "My Plan"; 
    DataMartInlineProfile.registrationChangedScript = onRegistrationChanged;
    DataMartInlineProfile.profileActivationCompleteScript = onActivationComplete;
    DataMartInlineProfile.notifyInlineProfileDownload(purchaseMetaData , "1$smdp.address$"); 
}
```

### Listening for network registration changes

To listen for network registration changes, the `DataMartInlineProfile.registrationChangedScript` must be set to the name of a Javascript function which takes a string for the `registrationArgs`.

The registration args are a string that represents a JSON object.

ProfileRegistrationCompleteArgs
| Property Name | Type | Description |
| --- | --- | -- |
| networkRegistrationState | string | A string representing the current network registration state. The values of which can be seen in `DataMartNetworkRegistrationState`. |
| iccid | string | The iccid for which the network registration state has changed |

DataMartNetworkRegistrationState
| Property Name | Type | Description |
| --- | --- | -- |
| none | string | No connectivity |
| deregistered | string | The device is not registered and is not searching for a network provider |
| searching | string | The device is not registered and is searching for a network provider |
| home | string | The device is on a home network provider |
| roaming | string | The device is on a roaming network provider |
| partner | string | The device is on a roaming partner network provider |
| denied | string | The device was denied registration. |

The below Javascript is an example of how to implement a listener for network registration changed events

```Javascript
function onRegistrationChanged(registrationArgs) {
    var registrationObj = JSON.parse(registrationArgs);
    if(registrationObj.networkRegistrationState == DataMartNetworkRegistrationState.home ||
        registrationObj.networkRegistrationState == DataMartNetworkRegistrationState.home ||
        registrationObj.networkRegistrationState == DataMartNetworkRegistrationState.home)
    {
        Log('Registration Successful!');
    }
}
```

# Listening for profile activation

To listen for profile activation events the `DataMartInlineProfile.profileActivationCompleteScript` must be set to the name of a Javascript function which takes a string for the `activationArgs`

The `activationArgs` is a string that represents a JSON object

ProfileActivationCompleteArgs
| Property Name | Type | Description |
| --- | --- | -- |
| activationResult | string | The result of activation. The values of which can be seen in `DataMartActivationErrors` |
| iccid | string | The iccid of the profile that was activated |

DataMartActivationErrors
| Property Name | Type | Description |
| --- | --- | -- |
| success | string | Indicates that an operation was successful. |
| notAuthorized | string | Indicates that the operation was not authorized. |
| notFound | string | Indicates that the specified eSIM profile was not found. |
| policyViolation | string | Indicates that the operation violates policy. |
| insufficientSpaceOnCard | string | Indicates that there is not enough storage space on the card to complete the operation. |
| serverFailure | string | Indicates that a server failure occurred during the operation. |
| serverNotReachable | string | Indicates that the server could not be reached during the operation. |
| timeoutWaitingForUserConsent | string | Indicates that user consent was not granted within the timeout period of the operation. |
| incorrectConfirmationCode | string | Indicates that the wrong confirmation code was supplied during the operation. |
| confirmationCodeMaxRetriesExceeded | string | Indicates that the wrong confirmation code was supplied during the operation, and that no more retries are permitted. |
| cardRemoved | string | Indicates that the SIM card has been removed. |
| cardBusy | string | Indicates that the SIM card is busy. |
| other | string | Indicates a status that's not accounted for by a more specific status. |
| cardGeneralFailure | string | Indicates that a card error occurred that prevented the download/install/other operation from completing successfully. |
| confirmationCodeMissing | string | Indicates that a confirmation code is needed in order to download the eSIM profile. |
| invalidMatchingId | string | Indicates that the matching ID from the activation code or discovered event was refused. |
| noEligibleProfileForThisDevice | string | Indicates that an eSIM profile compatible with this device could not be found. For example, a profile was found that requires LTE support, but the device only supports 3G. |
| operationAborted | string | Indicates that the operation aborted. |
| eidMismatch | string | Indicates that an eSIM profile on the mobile operator (MO) server is already allocated for a different eSIM EID than the one the device has. |
| profileNotAvailableForNewBinding | string | Indicates that the user is trying to download an eSIM profile that has already been claimed/downloaded. |
| profileNotReleasedByOperator | string | Indicates that the eSIM profile is available, but it is not yet marked as released for download by the mobile operator (MO). You can only download a released profile, so the MO needs to mark the profile as released. |
| operationProhibitedByProfileClass | string | Indicates that the operation is not allowed for the eSIM profile class. |
| profileNotPresent | string | Indicates that an eSIM profile could not be found. |
| noCorrespondingRequest | string | Indicates that no corresponding request could be found. |
| unknownError | string | Indicates that LPA returned an error that is unknown. |
| lpaInitializationError | string | Indicates that an error occurred when trying to initialize LPA. |
| modemNotFound | string | Indicates that no Cellular modem was found on the device. |
| localSettingsAccessFailed | string | Indicates that accessing app local settings failed. |
| invalidCallback | string | Indicates that MO portal has given an invalid callback. |
| invalidActivationCode | string | Indicates that MO portal has given invalid activation code. |
| invalidIccid | string | Indicates that MO portal has given invalid iccid. |

The below Javascript is an example of how to implement a listener for the profile activation event.

```Javascript
function onActivationComplete(activationArgs) {
    var activationObj = JSON.parse(activationArgs);
    if(activationObj.activationResult == DataMartActivationErrors.success)
        Log('Activation Success');
}
```

## Adding balance

When a user completes a purchase in the MO Direct portal by adding more data to their account (no profile download needed because the user used the current profile on the eSIM), the MO portal should invoke the `DataMart.notifyBalanceAddition` API return control back to the Mobile Plans app.

### DataMart.notifyBalanceAddition

| Parameter name | Type | Description |
| --- | --- | -- |
| purchaseMetadata | Object | This object contains metadata about the user's purchase. This includes details about the user account, the purchase method or instrument, details if the user is adding a new line, and the name of the plan that the user purchased. All these are used for reporting. |
| iccid | String | The ICCID to which data is assigned. If this ICCID is not active, the Mobile Plans app activates the corresponding profile.|

The following Javascript function shows an example of the API to inform the application that the user has completed a purchase using a profile already available, but not neccessarily active, on the eSIM.

 ```Javascript
function finishPurchaseWithBalanceAddition() {
        var metadata = DataMart.createPurchaseMetaData();
        metadata.userAccount = DataMartUserAccount.new;
        metadata.purchaseInstrument = DataMartPurchaseInstrment.none;
        metadata.moDirectStatus = DataMartMoDirectStatus.complete;
        metadata.line = DataMartLineType.new;
        metadata.planName = "2GB Monthly";
        DataMart.notifyBalanceAddition(metadata, "89000000000000000000");
    }
```

## Canceling purchase flow

If a user cancels the purchase flow at the MO portal, then the portal must invoke the `DataMart.notifyCancelledPurchase` API to return control back to the Mobile Plans app.

### DataMart.notifyCancelledPurchase

| Parameter name | Type | Description |
| --- | --- | -- |
| purchaseMetadata | Object | This object contains metadata about the user's purchase. This includes details about the user account, the purchase method or instrument, details if the user is adding a new line, and the name of the plan that the user purchased. All these are used for reporting. |

The following Javascript function shows an example of the API to inform the application that the user has canceled a purchase.

 ```Javascript
function finishPurchaseWithCancellation() {
        var metadata = DataMart.createPurchaseMetaData();
        metadata.userAccount = DataMartUserAccount.new;
        metadata.purchaseInstrument = DataMartPurchaseInstrment.new;
        metadata.moDirectStatus = DataMartMoDirectStatus.cancelled;
        metadata.line = DataMartLineType.bailed;
        metadata.planName = "";
        DataMart.notifyCancelledPurchase(metadata);
    }
```

> [!NOTE]
> The cancelation and balance addition flows using the `DataMart.notifyPurchaseResult` API are still supported. 

## Frequently Asked Questions

1. Is *Transaction ID* still required?  

    Mobile operators do not have to return the *Transaction ID* passed to the portal, but they are required to store this value for troubleshooting purposes.  

2. Which API should be used to transfer control back to Mobile Plans when connectivity is available immediately?  

    The `DataMart.notifyPurchaseDelayedProfile` API is supported for this scenario going forward. In this specific case, the *networkRegistrationInterval* parameter should be set to **0**.  

    If you have implemented the `DataMart.notifyPurchaseResult` API as specified in the integration guide, it is still supported.  

3. Is ICCID information still required for the eSIM activation callback?  

    ICCID information is only needed for the adding balance scenario, when the `DataMart.notifyBalanceAddition` API callback is used.  

    If you have implemented the `DataMart.notifyPurchaseResult` API as specified in the integration guide, it is still supported.  

4. What if I still need help?  

    Please contact your Microsoft representative.