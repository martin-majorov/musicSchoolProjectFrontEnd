<template>
<div class="background-green student-info-form">
        New lesson has been added successfully!<br><br>
    <table>
        <tr>
            <td class="table-left">Name:</td>
            <td class="table-right" ><span class="new-student-text"> {{ name }}</span></td>
        </tr>
        <tr>
            <td class="table-left">Lesson date:</td>
            <td class="table-right"><span class="new-student-text">{{ date }}</span></td>
        </tr>
        <tr>
            <td class="table-left">Lesson length:</td>
            <td class="table-right"><span class="new-student-text">{{ length }} min</span></td>
        </tr>
    </table>
</div>
</template>

<script>
export default {
    name: 'NewLesson',
    props: [
        'name',
        'date',
        'length'
    ]
}
</script>

<style>

</style>