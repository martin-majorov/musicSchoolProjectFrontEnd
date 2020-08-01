<template>
    <div class="background-green student-info-form">{{ successfulMessage }}</div>
</template>

<script>

export default {
    name: 'SuccessfulMessage',
    props: ['successfulMessage']

}
</script>