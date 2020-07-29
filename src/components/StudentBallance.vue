<template>
    <div class="background-green student-info-form">
    <table>
        <tr>
            <td class="table-left">Name:</td>
            <td class="student-form-names table-right"><span> {{ name }}</span></td>
        </tr>
        <tr>
            <td class="table-left">Lessons left:</td>
            <td class="table-right"><span class="student-form-numbers">{{ paymentballance }}</span></td>
        </tr>
        <tr>
            <td class="table-left">Lessons attended:</td>
            <td class="table-right"><span class="student-form-numbers">{{ lessons }}</span></td>
        </tr>
        <tr>
            <td class="table-left">Total Payments:</td>
            <td class="table-right"><span class="student-form-numbers">{{ payments }} Eur</span></td>
        </tr>
    </table>
</div>
</template>

<script>


export default {
    name: 'StudentBallance',
    props: [
        'name',
        'lessons',
        'payments', 
        'paymentballance'
    ]
}
</script>

<style>

.background-green {
        background-color: #c09669;
    }

.student-form-names {
    text-align: center;
    font-size: 1.1em;
    font-weight: bold;
}

.student-form-numbers {
    font-size: 1.2em;
    font-weight: bold;
}

.table-right {
    text-align: right;
}

.table-left {
    text-align: left;
}

</style>