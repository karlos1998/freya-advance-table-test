<script setup>

import { ref } from 'vue';

const products = ref([
    {name: "Karol", surname: "Nowak"}
]);

</script>

<template>
    <div class="card">
        <DataTable :value="products" tableStyle="min-width: 50rem">

            <slot/>

        </DataTable>
    </div>
</template>
