<template>
    <input  v-on:keyup.13="$emit('nameEdited'), $emit('editStudent', editStudentName)" 
            v-model="editStudentName.newName"
            placeholder="Enter name" 
    >
</template>

<script>

export default {
    name: "StudentEdit",
    props: [
        "student"
    ],
    data() {
        return {
            editStudentName: {
                id: this.student.id,
                newName: this.student.name + ' ' + this.student.surname
            }
        }
    }
}
</script>