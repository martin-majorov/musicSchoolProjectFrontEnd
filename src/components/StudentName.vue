<template>
    <button class="menu-button" @click="$emit('studentInfo', student)">{{ student.name }} {{student.surname}}</button>
</template>

<script>
export default {
    name: "StudentName"
}
</script>