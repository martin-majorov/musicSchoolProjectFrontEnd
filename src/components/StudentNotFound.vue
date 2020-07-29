<template>
    <div class="student-not-found">Student {{ name }} is not in the list</div>
</template>

<script>

export default {
    name: 'StudentNotFound',
    props: ['name']

}
</script>