<template>
    <div class="calc">
        <Calculator  @hide-calc="hideCalc" :show="showCalc" />
    </div>
</template>
<script>
    import Calculator from 'Calculator'
    export default {
        name: '',
        components: {
            Calculator
        },
        data: {
            showCalc: false,
        },
        methods: {
            hideCalc: function() {
                this.showCalc = false
            }
        }
    }
</script>