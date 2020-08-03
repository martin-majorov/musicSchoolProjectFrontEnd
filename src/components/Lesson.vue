<template>
    <div class='background-blue student-info-form'>
        <table>
            <tr class="title">
                <td>Student name</td>
                <td>Lesson date</td>
                <td>Lesson length</td>
                <td><button class="menu-button" @click="setEditMode"><font-awesome-icon icon="pencil-alt" /></button></td>
            </tr>
            <component  v-bind:is="currentTab"
                        v-bind:lesson="lesson"
                        @editedLesson="updateLesson"
                        @deleteLesson="$emit('deleteLesson', lesson.id)"
            />
        </table>
    </div>
</template>

<script>
import LessonData from './LessonData';
import LessonDataEdit from './LessonDataEdit'

export default {
    name: 'Lesson',
    components: {
        LessonData,
        LessonDataEdit
    },
    props: [
        'lesson'
    ],
    methods: {
        setEditMode: function() {
            this.currentTab = 'LessonDataEdit'
        },
        updateLesson: function(lesson) {
            this.currentTab = 'LessonData';
            this.$emit('updateLesson', lesson)
        }
    },
    data() {
        return {
            currentTab: 'LessonData'
        }
    }
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