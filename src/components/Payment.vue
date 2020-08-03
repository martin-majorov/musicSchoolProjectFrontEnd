<template>
    <div class='background-darkblue student-info-form'>
        <table>
            <tr class="title">
                <td>Payment name</td>
                <td>Payment amount</td>
                <td>Student date</td>
                <td><button class="menu-button" @click="setDataEdit"><font-awesome-icon icon="pencil-alt" /></button></td>
            </tr>
            <component  v-bind:is="this.currentPaymentTab" 
                        v-bind:payment="this.payment"
                        @editedPayment="updatePayment"
                        @deletePayment="$emit('deletePayment', payment.id)"
            />
        </table>
    </div>
</template>

<script>

import PaymentData from './PaymentData'
import PaymentDataEdit from './PaymentDataEdit'

export default {
    name: 'Payment',
    components: {
        PaymentData,
        PaymentDataEdit
    },
    props: [
        'payment'
    ],
    methods: {
        setDataEdit: function() {
            this.currentPaymentTab = 'PaymentDataEdit'
        },
        updatePayment: function(payment) {
            this.currentPaymentTab = 'PaymentData'
            this.$emit('updatePayment', payment)
        }
    },
    data() {
        return {
            currentPaymentTab: 'PaymentData'
        }
    }
}
</script>

<style>
    .background-darkblue {
        background-color: #b67929;
    }

    table {
        width: 100%;
    }
</style>