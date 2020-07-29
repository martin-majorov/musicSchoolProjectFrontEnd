<template>
    <div class='background-darkblue student-info-form'>
        <table>
            <tr class="title">
                <td>Payment name</td>
                <td>Payment amount</td>
                <td>Student date</td>
                <td><button class="menu-button"><font-awesome-icon icon="pencil-alt" /></button></td>
            </tr>
            <tr>
                <td>{{payment.name}}</td>
                <td>{{payment.payment_amount}} Eur</td>
                <td>{{payment.date}}</td>
                <td><button class="menu-button"><font-awesome-icon icon="trash-alt" 
                            @click="$emit('deletePayment', payment.id)"
                /></button></td>
            </tr>
        </table>
    </div>
</template>

<script>
export default {
    name: 'Payment',
    props: [
        'payment'
    ]
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