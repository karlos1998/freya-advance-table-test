<script setup>

import AdvanceTable from '@/components/AdvanceTable.vue';
import AdvanceColumn from '@/components/AdvanceColumn.vue';
</script>


<template>

    <AdvanceTable>
        <Column field="name" header="Name" />

        <AdvanceColumn />

    </AdvanceTable>

</template>