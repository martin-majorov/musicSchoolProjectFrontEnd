<template>
    <div id="warning" class="student-not-found">{{ warningMessage }}</div>
</template>

<script>

export default {
    name: 'WarningMessage',
    props: ['warningMessage']

}
</script>

<style>

    #warning {
        font-family: 'Roboto', sans-serif;
        font-style: italic;
        font-size: 15px;
        color: white;
    }

</style>