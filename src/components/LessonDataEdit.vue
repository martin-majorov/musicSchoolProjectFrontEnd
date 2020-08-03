<template>
    <tr>
        <td>{{lesson.name}}</td>
        <td>
            <input  v-on:keyup.13="$emit('editedLesson', updatedLesson)"
                    v-model="updatedLesson.lessonDate"
            >
        </td>
        <td>
            <input  v-on:keyup.13="$emit('editedLesson', updatedLesson)"
                    v-model="updatedLesson.lessonLength"
            >
        </td>
        <td><button class="menu-button"><font-awesome-icon icon="trash-alt" 
                    @click="$emit('deleteLesson', lesson.id)"
        /></button></td>
    </tr>
</template>

<script>
export default {
    name: 'LessonDataEdit',
    props: [
        'lesson'
    ],
    data() {
        return {
            updatedLesson: {
                id: this.lesson.id,
                lessonDate: this.lesson.date,
                lessonLength: this.lesson.lesson_length
            }
        }
    }
}
</script>