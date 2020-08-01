<template>
<div class="background-cadetblue student-info-form">
    <table>
        <tr>
            <td>
                <component  v-bind:is="this.currentTab"
                            v-bind:student="this.student"
                            v-on:nameEdited="setName"
                            v-on:studentInfo="$emit('studentInfo', student)"
                            v-on:editStudent="sendEditedName"
                ></component>
            </td>
            <td class="table-right">
                <button class="menu-button">
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

        setName: function() {
            this.currentTab = 'StudentName'
        },

        setEdit: function() {
            this.currentTab = 'StudentEdit'
        },

        sendEditedName: function(newNameObj) {
            this.$emit('editStudent', newNameObj);
        }
    },
    data() {
        return {
            currentTab: 'StudentName'
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