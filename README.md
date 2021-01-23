# Cordova Apple Pay and Google Pay integration

This plugin is built as unified method for obtaining payment tokens to forward it to payment processor (eg Stripe, Wayforpay, etc).

Plugin supports iOS 11-14. Tested properly with cordova 10 and iOS 14.3.

## Installation

```
cordova plugin add cordova-plugin-apple-pay-google-pay
```
For Android, register and fill all required forms at https://pay.google.com/business/console.

For iOS, you have to have valid developer account with merchant set up and  ApplePay capability and a merchat id configured in your Xcode project. Merchant id can be obtained from https://developer.apple.com/account/resources/identifiers/list/merchant. 
Do configuration manually or via config.xml:

```
<platform name="ios">
  
  <config-file target="*-Debug.plist" parent="com.apple.developer.in-app-payments">
    <array>
      <string>developer merchant ID here</string>
    </array>
  </config-file>

  <config-file target="*-Release.plist" parent="com.apple.developer.in-app-payments">
    <array>
      <string>production merchant ID here</string>
    </array>
  </config-file>
</platform>
```
## Usage

`canMakePayments()` checks whether device is capable to make payments via Apple Pay or Google Pay.  
```
// use as plain Promise
async function checkForApplePayOrGooglePay(){
    let isAvailable = await cordova.plugins.ApplePayGooglePay.canMakePayments()
}

// OR
let available;

cordova.plugins.ApplePayGooglePay.canMakePayments((r) => {
  available = r
})
```
`makePaymentRequest()` initiates pay session.

```
let request = {
    merchantId: 'merchant.com.example', // obtain it from https://developer.apple.com/account/resources/identifiers/list/merchant
    purpose: `Payment for your order #1`,
    amount: 100,
    countryCode: "US",
    currencyCode: "USD"
}

cordova.plugins.ApplePayGooglePay.makePaymentRequest(request, r => {
        // in success callback, raw response as encoded JSON is returned. Pass it to your payment processor as is. 
        let responseString = r
    
      },
        r => {
          // in error callback, error message is returned.
          // it will be "Payment cancelled" if used pressed Cancel button.
        }
      )
```
All parameters in request object are required.

## For Android
you will have to provide few extra parameters:
```
request.gateway = 'stripe'; // or any another processor you are using: https://developers.google.com/pay/api#participating-processors
request.merchantId = 'XXXXXXX'; // merchant id provided by your processor 
request.gpMerchantName = 'Your Company Name'; // will be displayed in transaction info
request.gpMerchantId = 'XXXXXXXXXXXX'; // obtain it at https://pay.google.com/business/console
```
