<template>
  <div :class="wrapperClass">

    <div v-show="hostedFieldsInstance">
      <label for="card-number">Card Number
        <div class="input-field" id="number"></div>
      </label>

      <div class="line">
      <label for="cvv">CVV
        <div class="input-field" id="cvv"></div>
      </label>

      <label for="expiration-date">Expiration Date
        <div class="input-field" id="expiration-date"></div>
      </label>
      </div>

      <button type="submit" style="padding-top: 1rem;" disabled id="submitTransaction" @click="tokenizeHF">TOKENIZE</button>
    </div>

  </div>
</template>

<script>
  export default {
    props: {
      authToken: {
        value: String
      },
      wrapperClass: {
        value: String,
      },
      loaderClass: {
        value: String
      }
    },
    created() {
      this.createBT();
    },
    data () {
      return {
        errorMessage: '',
        clientInstance: '',
        hostedFieldsInstance: '',
        tokenizePayload: '',
      }
    },
    methods: {
      createBT () {
        var client = require('braintree-web/client');
        client.create({
          authorization: this.authToken
        }, (clientErr, clientInstance) => {
          if (clientErr) {
            this.errorMessage = 'There was an error setting up the client instance. Message: ' + clientErr.message;
            this.$emit('bthferror', this.errorMessage);
            return;
          } else {
            this.clientInstance = clientInstance
            this.createHF();
          }
        });
      },
      createHF () {
        var hostedFields = require('braintree-web/hosted-fields');
        hostedFields.create({
          client: this.clientInstance,
          styles: {
            'input': {
              'font-size': '18px'
            },
            'input.invalid': {
              'color': 'red'
            },
            'input.valid': {
              'color': 'green'
            }
          },
          fields: {
            number: {
              selector: '#number',
              placeholder: '4111 1111 1111 1111'
            },
            cvv: {
              selector: '#cvv',
              placeholder: '123'
            },
            expirationDate: {
              selector: '#expiration-date',
              placeholder: '10/2019'
            }
          }
        }, (hostedFieldsErr, hostedFieldsInstance) => {
          if (hostedFieldsErr) {
            // Handle error in Hosted Fields creation
            this.errorMessage = 'There was an error setting up the hosted fields! Message: ' + hostedFieldsErr.message;
            this.$emit('bthferror', this.errorMessage);
            return;
          } else {
            //enable submit button
            document.querySelector('#submitTransaction').removeAttribute('disabled');
            this.hostedFieldsInstance = hostedFieldsInstance;
          }

        });
      },
      tokenizeHF () {
        this.hostedFieldsInstance.tokenize( (tokenizeErr, payload) => {
          if (tokenizeErr) {
            this.errorMessage = 'There was an error tokenizing! Message: ' + tokenizeErr.message;
            this.$emit('bthferror', this.errorMessage);
            return;
          } else {
            this.tokenizePayload = payload;
            this.$emit('bthfpayload', payload);
            this.teardownHF();
          }

        });
      },
      teardownHF () {
        this.hostedFieldsInstance.teardown( (teardownErr) => {
            if (teardownErr) {
              this.errorMessage = 'There was an error tearing it down! Message: ' + teardownErr.message;
              this.$emit('bthferror', this.errorMessage);
              return;
            } else {
              this.hostedFieldsInstance = '';
              return;
            }
        });

      }

    }
  }
</script>