<template>
        <div class="background-green student-info-form">
        New student has been added successfully!<br><br>
    <table>
        <tr>
            <td class="table-left">Name:</td>
            <td class="table-right" ><span class="new-student-text"> {{ name }}</span></td>
        </tr>
        <tr>
            <td class="table-left">Surname:</td>
            <td class="table-right"><span class="new-student-text">{{ surname }}</span></td>
        </tr>
        <tr>
            <td class="table-left">Hourly payment:</td>
            <td class="table-right"><span class="new-student-text">{{ paymentRate }}</span></td>
        </tr>
    </table>
</div>
</template>

<script>
export default {
    name: 'NewStudent',
    props: [
        'name',
        'surname',
        'paymentRate'
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