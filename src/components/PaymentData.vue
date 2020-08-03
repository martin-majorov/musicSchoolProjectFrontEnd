<template>
        <tr>
                <td>{{payment.name}}</td>
                <td>{{payment.payment_amount}} Eur</td>
                <td>{{payment.date}}</td>
                <td><button class="menu-button"><font-awesome-icon icon="trash-alt" 
                            @click="$emit('deletePayment', payment.id)"
                /></button></td>
        </tr>
</template>
<script>
export default {
        name: 'PaymentData',
        props: [
                'payment'
        ]
}
</script>