# @bigcommerce/checkout-sdk

[![Build Status](https://travis-ci.com/bigcommerce/checkout-sdk-js.svg?token=pywwZy8zX1F5AzeQ9WpL&branch=master)](https://travis-ci.com/bigcommerce/checkout-sdk-js)

Checkout JS SDK provides you with the tools you need to build your own checkout solution for a BigCommerce store.

The SDK has a convenient application interface for starting and completing a checkout flow. Behind the scene, it handles all the necessary interactions with our Storefront APIs and other third-party SDKs. So you can focus on creating a UI that is unique to your business.


## Install

You can install this library using [npm](https://www.npmjs.com/get-npm).

```sh
npm install --save @bigcommerce/checkout-sdk
```


## Requirements

### Browser support

We release the library in ES5 so you shouldn't have to do additional transpilation in order to use it. However, you do require the [Promise polyfill](https://github.com/stefanpenner/es6-promise) if you need to support older browsers, such as IE11.

### Framework

The library is framework agnostic. In other words, you can use it with any UI framework or library you want.

### CORS

As our Storefront Web APIs currently don't support CORS, you may not be able to use the library outside of a BigCommerce store.


## Usage

Below are a few examples showing you some of the things you can do with the library.

### Initialize checkout

To create a `CheckoutService` instance and load the initial checkout state:

```js
import { createCheckoutService } from '@bigcommerce/checkout-sdk';

const service = createCheckoutService();

service.loadCheckout();
```

### Subscribe to data changes

To subscribe to changes to the current checkout and render the latest data:

```js
service.subscribe(({ checkout, errors, statuses }) => {
    // Log the current checkout
    console.log(checkout.getCheckout());

    // Log an error object if unable to load checkout
    console.log(errors.getLoadCheckoutError());

    // Log `true` if in the process of loading checkout
    console.log(statuses.isLoadingCheckout());
});
```

To get the cached state once it is hydrated:

```js
const { checkout } = service.getState();

console.log(checkout.getCheckout());
```

### Sign in shopper

To sign in a guest shopper to begin the checkout process:

```js
const { checkout } = await service.signInCustomer('foo@bar.com');

console.log(checkout.getCustomer());
```

Or to sign in a returning shopper:

```js
const { checkout } = await service.signInCustomer('foo@bar.com', 'password123');

console.log(checkout.getCustomer());
```

### Update shipping details

To set a shipping address for the order:

```js
const address = {
    firstName: 'Test',
    lastName: 'Tester',
    addressLine1: '12345 Testing Way',
    city: 'Some City',
    provinceCode: 'CA',
    postCode: '95555',
    countryCode: 'US',
    phone: '555-555-5555',
};

const { checkout } = await service.updateShippingAddress(address);

console.log(checkout.getShippingAddress());
```

To select a shipping option available for the shipping address:

```js
const address = checkout.getShippingAddress();
const options = checkout.getShippingOptions();

const { checkout } = await service.selectShippingOption(address.id, options[address.id].id);

console.log(checkout.getSelectedShippingOption());
```

### Update billing details

To set a billing address for the order:

```js
const { checkout } = await service.updateBillingAddress(address);

console.log(checkout.getBillingAddress());
```

### Apply coupon

To apply a coupon or gift certificate to the order:

```js
const { checkout } = await service.applyCoupon('COUPON');

console.log(checkout.getOrder().coupon);
```

```js
const { checkout } = await service.applyGiftCertificate('GIFT');

console.log(checkout.getOrder().giftCertificate);
```

To remove an applied coupon or gift certificate from the order:

```js
await service.removeCoupon('COUPON');
await service.removeGiftCertificate('COUPON');
```

### Load payment methods

To load available payment methods:

```js
const { checkout } = await service.loadPaymentMethods();

console.log(checkout.getPaymentMethods());
```

### Submit order

To submit the order with payment details and complete the checkout process:

```js
const payment = {
    name: 'testgateway',
    paymentData: {
        ccExpiry: { month: 10, year: 20 },
        ccName: 'BigCommerce',
        ccNumber: '4111111111111111',
        ccType: 'visa',
        ccCvv: 123,
    },
};

await service.submitOrder({ payment });
```

To submit the order using a hosted payment method:

```js
const payment = { name: 'braintreepaypal' };

await service.initializePaymentMethod(payment.name);
await service.submitOrder({ payment });
```


## See also

* [Cornerstone example](https://github.com/bigcommerce/cornerstone/compare/master...davidchin:checkout_sdk_demo) - A sample checkout app written in React.
* [Storefront APIs](https://developer.bigcommerce.com/api/v3/storefront.html) - The documentation for Storefront Checkout & Cart Web APIs.


## Contribution

We actively maintain and add new features to the library in order to support our official checkout (Optimized Checkout). But we also accept contributions from the community.

If you want to contribute, please refer to the [contribution guide](CONTRIBUTING.md).


## License

MIT (Pending)
