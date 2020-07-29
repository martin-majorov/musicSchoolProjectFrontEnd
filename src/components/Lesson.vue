<template>
    <div class='background-blue student-info-form'>
        <table>
            <tr class="title">
                <td>Student name</td>
                <td>Lesson date</td>
                <td>Lesson length</td>
                <td><button class="menu-button"><font-awesome-icon icon="pencil-alt" /></button></td>
            </tr>
            <tr>
                <td>{{lesson.name}}</td>
                <td>{{lesson.date}}</td>
                <td>{{lesson.lesson_length}} min</td>
                <td><button class="menu-button"><font-awesome-icon icon="trash-alt" 
                                                                    @click="$emit('deleteLesson', lesson.id)"
                                                /></button></td>
            </tr>
        </table>
    </div>
</template>

<script>
export default {
    name: 'Lesson',
    props: [
        'lesson'
    ]
}
</script>

<style>

    .background-blue {
        background-color: #3c5b74;
    }

    table {
        width: 100%;
    }
</style>