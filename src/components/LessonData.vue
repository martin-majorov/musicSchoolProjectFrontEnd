<template>
    <tr>
        <td>{{lesson.name}}</td>
        <td>{{lesson.date}}</td>
        <td>{{lesson.lesson_length}} min</td>
        <td><button class="menu-button"><font-awesome-icon icon="trash-alt" 
                    @click="$emit('deleteLesson', lesson.id)"
        /></button></td>
    </tr>
</template>

<script>
export default {
    name: 'LessonData',
    props: [
        'lesson'
    ]
}
</script>