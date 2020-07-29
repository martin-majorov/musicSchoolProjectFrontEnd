<template>
<div class="background-cadetblue student-info-form">
    <table>
        <tr>
            <td>
                <button class="menu-button" @click="$emit('studentInfo', student)">{{ student.name }} {{student.surname}}</button>
                <component v-bind:is="this.currentTab"></component>
            </td>
            <td class="table-right">
                <button class="menu-button" @click="$emit('editStudent', student.id)">
                    <font-awesome-icon icon="pencil-alt" @click="setEdit"/>
                </button>
                <button class="menu-button" @click="$emit('deleteStudent', student.id)">
                    <font-awesome-icon icon="trash-alt" />
                </button>
            </td>
        </tr>
    </table>
</div>
</template>

<script>

import StudentEdit from './StudentEdit'
import StudentName from './StudentName'

export default {
    name: 'Student',
    components: {
        StudentEdit,
        StudentName
    },
    props: ["student"],
    methods: {
        setEdit: function() {
            this.currentTab = 'StudentEdit'
        }
    },
    data() {
        return {
            currentTab: 'student',

        }
    }
    
}
</script>

<style>

    .background-cadetblue {
        background-color: #264b77;
    }

    .menu-button {
        font-family: 'Roboto', sans-serif;
        font-size: 1.03em;
        color: white;
        background: none;
        outline: none;
        border: 0
    }

    .menu-button:hover {
        cursor: pointer;
    }
</style>