<template>
    <tr>
        <td>{{payment.name}}</td>
        <td><input  v-on:keyup.13="$emit('editedPayment', editedPayment)"
            v-model="editedPayment.editedAmount"
        >   </td>
        <td><input  v-on:keyup.13="$emit('editedPayment', editedPayment)"
            v-model="editedPayment.editedDate"
        >   </td>
        <td><button class="menu-button"><font-awesome-icon icon="trash-alt" 
                            @click="$emit('deletePayment', payment.id)"
        /></button></td>
    </tr>
    
</template>

<script>
export default {
    name: 'PaymentAmountEdit',
    props: [
        'payment'
    ],
    data() {
        return {
            editedPayment: {
                id: this.payment.id,
                editedAmount: this.payment.payment_amount,
                editedDate: this.payment.date
            }
        }
    }
}
</script>