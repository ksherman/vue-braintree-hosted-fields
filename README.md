# VueJS Braintree Hosted Fields Component

Welcome!

This is a Vue component (.vue single file) for Braintree's Hosted Fields, using v3 of the JS SDK. This component includes the credit card number, CVV, and expiration date. Hosted fields are completely un-styled. I have some suggested styles below, or you can roll your own!

Let me know what else anyone would like to see! Happy to add features/functionality.

### Installation

Published on NPM. Includes `braintree-web` as a dependency.

```npm install vue-braintree-hosted-fields --save```

###### Import
`import HostedFields from 'vue-braintree-hosted-fields'`

###### Using the Component
```
<hosted-fields
    wrapperClass="constrain" // pass in a wrapper class
    authToken="sandbox_c8xxxxxb_qxxxxxxxxxxxxxxj" // authToken, either Braintree Client Token or Static Tokenization Key
    v-on:bthferror="btHFError" // method that listenes for error message emited
    v-on:bthfpayload="btHFPayload" // method that listens for tokenization payload
></hosted-fields>
```

###### Methods
Since the component broadcasts and/or receives events, you'll need a few methods for that. First is a method that receives the error messaged emitted by the component. This is useful when there are any issues setting up or tokenizing payment information. Then, you'll need to $emit an event to trigger the fields tokenization. Lastly, another method is needed for receiving the tokenization payload (which includes the nonce). From there, the rest is on your integration to choose how to handle sending information to your server side. For example:

```
  methods: {
    btHFError(message) {
      console.error(message);
      // do something with the error message
    },
    btHFPayload(payload) {
      console.log(payload);
      // do something with the token payload
    },
    submitTransaction() {
      this.$emit('tokenize');
    }
  }
```

###### Styles
Using `label` and `.input-field`:

```
<style>
  label {
    font-size: 16px;
    display: block;
    text-align: left;
    font-weight: bold;
    padding: 0.25rem;
  }

  .input-field {
    height: 50px;
    box-sizing: border-box;
    width: 100%;
    padding: 0.8rem;
    display: inline-block;
    box-shadow: none;
    font-weight: 600;
    font-size: 0.8rem;
    border-radius: 6px;
    border: 1px solid #dddddd;
    line-height: 1.2;
    background: #fcfcfc;
    margin-top: 0.1rem;
    margin-bottom: 0.8rem;
    background: linear-gradient(to right, white 50%, #fcfcfc 50%);
    background-size: 200% 100%;
    background-position: right bottom;
    transition: all 300ms ease-in-out;
    font-size: 18px;
    text-align: left;
  }
</style>
```

##### Example Parent Vue Component

```
<template>
  <div>
    <hosted-fields
        wrapperClass="constrain"
        authToken="sandbox_c8xxxxxb_qxxxxxxxxxxxxxxj"
        v-on:bthferror="btHFError"
        v-on:bthfpayload="btHFPayload"
    ></hosted-fields>

    <button @click="submitTransaction">Submit</button>
  </div>
</template>

<script>
import HostedFields from 'vue-braintree-hosted-fields'

export default {
  components: {
    HostedFields
  },
  methods: {
    btHFError(message) {
      console.error(message);
      // do something with the error message
    },
    btHFPayload(payload) {
      console.log(payload);
      // do something with the token payload
    },
    submitTransaction() {
      this.$emit('tokenize');
    }
  }
}
</script>

<style>
  label {
    font-size: 16px;
    display: block;
    text-align: left;
    font-weight: bold;
    padding: 0.25rem;
  }

  .input-field {
    height: 50px;
    box-sizing: border-box;
    width: 100%;
    padding: 0.8rem;
    display: inline-block;
    box-shadow: none;
    font-weight: 600;
    font-size: 0.8rem;
    border-radius: 6px;
    border: 1px solid #dddddd;
    line-height: 1.2;
    background: #fcfcfc;
    margin-top: 0.1rem;
    margin-bottom: 0.8rem;
    background: linear-gradient(to right, white 50%, #fcfcfc 50%);
    background-size: 200% 100%;
    background-position: right bottom;
    transition: all 300ms ease-in-out;
    font-size: 18px;
    text-align: left;
  }
</style>
```


### To Do:
- [ ] options for which fields to include.
- [ ] styles for better adapting to layout needs. 
- [ ] card type detection with card icons.
- [ ] support for an event bus.

Disclaimer: I work at Braintree as an Overnight Technical Support rep, but this is _not_ an official library or example integration. https://developers.braintreepayments.com/