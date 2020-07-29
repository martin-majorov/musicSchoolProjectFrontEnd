<template>
        <div class="background-green student-info-form">
        New payment has been added successfully!<br><br>
    <table>
        <tr>
            <td class="table-left">Name:</td>
            <td class="table-right" ><span class="new-student-text"> {{ name }}</span></td>
        </tr>
        <tr>
            <td class="table-left">Payment date:</td>
            <td class="table-right"><span class="new-student-text">{{ date }}</span></td>
        </tr>
        <tr>
            <td class="table-left">Payment amount:</td>
            <td class="table-right"><span class="new-student-text">{{ paymentAmount }}</span></td>
        </tr>
    </table>
</div>
</template>

<script>
export default {
    name: 'NewPayment',
    props: [
        'name',
        'date',
        'paymentAmount'
    ]
}
</script>

<style>

.new-student-text {
    font-size: 1.1em;
    font-weight: bold;
    padding: 10px;
}

</style>