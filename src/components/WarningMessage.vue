<template>
    <div class="student-not-found">{{ warningMessage }}</div>
</template>

<script>

export default {
    name: 'WarningMessage',
    props: ['warningMessage']

}
</script>