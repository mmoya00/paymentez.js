# PaymentezJS

PaymentezJS is a library that allows developers to easily connect to the Paymentez CREDITCARDS API

[View working example >](https://developers.paymentez.com/)

![Example](img/example.png)


# Installation

- Bower: `bower install paymentez-js --save`
- NPM: `npm install paymentez-js`
- Source: [Download](https://github.com/paymentez/paymentez.js)

You will need to include both `paymentez.min.js` and `paymentez.min.css` into your web page.




# Usage

For working examples of using PaymentezJS, see the [examples](examples) folder of this project.

## Using the Paymentez Form
Any elements with the class `paymentez-form` will be automatically converted into a basic credit card input with the expiry date and CVC check.

The easiest way to get started with PaymentezForm is to insert the snippet of code:
```html
<div class="paymentez-form" id="my-card" data-capture-name="true"></div>
```

To get a `Card` object from the `PaymentezForm`, you ask the form for its card.

```javascript
var myCard = $('#my-card');
var cardToSave = myCard.PaymentezForm('card');
if(cardToSave == null){
  alert("Invalid Card Data");
}
```

If the returned `Card` is null, error states will show on the fields that need to be fixed. 

Once you have a non-null `Card` object from the widget, you can call [addCard](##addCard).

## Init library
You should initialize the library. 

```javascript
/**
  * Init library
  *
  * @param test_mode false to use production environment
  * @param paymentez_client_app_code provided by Paymentez.
  * @param paymentez_client_app_key provided by Paymentez.
  */
Paymentez.setEnvironment(true, 'PAYMENTEZ_CLIENT_APP_CODE', 'PAYMENTEZ_CLIENT_APP_KEY');
```

## addCard

addCard converts sensitive card data to a single-use token which you can safely pass to your server to charge the user. 

```javascript
/* Add Card converts sensitive card data to a single-use token which you can safely pass to your server to charge the user.
 *
 * @param uid User identifier. This is the identifier you use inside your application; you will receive it in notifications.
 * @param email Email of the user initiating the purchase. Format: Valid e-mail format.
 * @param card the Card used to create this payment token
 * @param success_callback a callback to receive the token
 * @param failure_callback a callback to receive an error
 */
Paymentez.addCard(uid, email, cardToSave, successHandler, errorHandler);

var successHandler = function(cardResponse) {
  console.log(cardResponse.card);
  if(cardResponse.card.status === 'valid'){
    $('#messages').html('Card Successfully Added<br>'+
                  'status: ' + cardResponse.card.status + '<br>' +
                  "Card Token: " + cardResponse.card.token + "<br>" +
                  "transaction_reference: " + cardResponse.card.transaction_reference
                );    
  }else if(cardResponse.card.status === 'review'){
    $('#messages').html('Card Under Review<br>'+
                  'status: ' + cardResponse.card.status + '<br>' +
                  "Card Token: " + cardResponse.card.token + "<br>" +
                  "transaction_reference: " + cardResponse.card.transaction_reference
                ); 
  }else{
    $('#messages').html('Error<br>'+
                  'status: ' + cardResponse.card.status + '<br>' +
                  "message Token: " + cardResponse.card.message + "<br>"
                ); 
  }
  submitButton.removeAttr("disabled");
  submitButton.text(submitInitialText);
};

var errorHandler = function(err) {    
  console.log(err.error);
  $('#messages').html(err.error.type);    
  submitButton.removeAttr("disabled");
  submitButton.text(submitInitialText);
};
```

The third argument to addCard is a Card object. A Card contains the following fields:

+ number: card number as a string without any separators, e.g. '4242424242424242'.
+ holder_name: cardholder name.
+ expiry_month: integer representing the card's expiration month, e.g. 12.
+ expiry_year: integer representing the card's expiration year, e.g. 2013.
+ cvc: card security code as a string, e.g. '123'.


## getSessionId

The Session ID is a parameter Paymentez use for fraud purposes. 
Call this method if you want to Collect your user's Device Information.

```javascript
var session_id = Paymentez.getSessionId();
```

Once you have the Session ID, you can pass it to your server to charge the user.


# PaymentezForm Complete Reference

## Manual Insertion

If you wish to manually alter the fields used by PaymentezForm to add additional classes or set the input field name or id etc,
you can pre-populate the form fields as show below.

```html
<div class="paymentez-form">
  <input class="card-number my-custom-class" name="card-number">
  <input class="name" id="the-card-name-id" name="card-holders-name" placeholder="Name on card">
  <input class="expiry-month" name="expiry-month">
  <input class="expiry-year" name="expiry-year">
  <input class="cvc" name="cvc">
</div>
```






## Reading Values

PaymentezForm provides functionality allowing you to read the form field values directly with JavaScript. This can be useful if you wish to submit the values via Ajax.

Create a PaymentezForm element and give it a unique id (in this example `my-card`)

```html
<div class="paymentez-form" id="my-card" data-capture-name="true"></div>
```

The javascript below demonstrates how to read each value of the form into local variables.

```javascript
var myCard = $('#my-card');

var cardNumber = myCard.PaymentezForm('cardNumber');
var cardType = myCard.PaymentezForm('cardType');
var name = myCard.PaymentezForm('name');
var expiryMonth = myCard.PaymentezForm('expiryMonth');
var expiryYear = myCard.PaymentezForm('expiryYear');
var cvc = myCard.PaymentezForm('cvc');
```






## Functions

To call a function on a PaymentezForm element, follow the pattern below.
Replace the text 'function' with the name of the function you wish to call.

```javascript
$('#my-card').PaymentezForm('function')
```

The functions available are listed below:

| Function    | Description                                    |
| :---------- | :--------------------------------------------- |
| card        | Get the card object                    |
| cardNumber  | Get the card number entered                    |
| cardType    | Get the type of the card number entered        |
| name        | Get the name entered                           |
| expiryMonth | Get the expiry month entered                   |
| expiryYear  | Get the expiry year entered                    |
| cvc         | Get the CVC entered                            |



### CardType Function

The `cardType` function will return one of the following strings based on the card number entered.
If the card type cannot be determined an empty string will be given instead.

| Card Type              |
| :--------------------- |
| AMEX                   |
| Diners                 |
| Diners - Carte Blanche |
| Discover               |
| JCB                    |
| Mastercard             |
| Visa                   |
| Visa Electron          |





## Static functions

If you just want to perform simple operations without the PaymentezForm form, there are a number of static functions provided
by the PaymentezForm library that are made available.


### Card Type from Card Number
```javascript
var cardNumber = '4242 4242 4242 4242'; // Spacing is not important
var cardType = PaymentezForm.cardTypeFromNumber(cardNumber);
```

### Cleaning and Masking
```javascript
// var formatMask = 'XXXX XXXX XXXX XXXX'; // You can manually define an input mask
// var formatMask = 'XX+X X XXXX XXXX XXXX'; // You can add characters other than spaces to the mask
var formatMask = PaymentezForm.CREDIT_CARD_NUMBER_VISA_MASK; // Or use a standard mask.
var cardNumber = '424 2424242 42   42 42';
var cardNumberWithoutSpaces = PaymentezForm.numbersOnlyString(cardNumber);
var formattedCardNumber = PaymentezForm.applyFormatMask(cardNumberWithoutSpaces, formatMask);
```

#### Masks

| Variable Name                             | Mask
| :---------------------------------------- | :------------------ |
| PaymentezForm.CREDIT_CARD_NUMBER_DEFAULT_MASK    | XXXX XXXX XXXX XXXX |
| PaymentezForm.CREDIT_CARD_NUMBER_VISA_MASK       | XXXX XXXX XXXX XXXX |
| PaymentezForm.CREDIT_CARD_NUMBER_MASTERCARD_MASK | XXXX XXXX XXXX XXXX |
| PaymentezForm.CREDIT_CARD_NUMBER_DISCOVER_MASK   | XXXX XXXX XXXX XXXX |
| PaymentezForm.CREDIT_CARD_NUMBER_JCB_MASK        | XXXX XXXX XXXX XXXX |
| PaymentezForm.CREDIT_CARD_NUMBER_AMEX_MASK       | XXXX XXXXXX XXXXX   |
| PaymentezForm.CREDIT_CARD_NUMBER_DINERS_MASK     | XXXX XXXX XXXX XX   |



## Card Expiry Validation
The expiry month can be in the range: 1 = January to 12 = December

```javascript
var month = 3;
var year = 2019;
var valid = PaymentezForm.isExpiryValid(month, year);
```

The expiry month and year can be either and integer or a string.
```javascript
var month = "3";
var year = "2019";
var valid = PaymentezForm.isExpiryValid(month, year);
```

The expiry year can be either 4 digits or 2 digits long.
```javascript
var month = "3";
var year = "19";
var valid = PaymentezForm.isExpiryValid(month, year);
```