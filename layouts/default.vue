<script setup lang="ts">
import Charm from '../components/Charm.vue';
import RandomGopher from '../components/RandomGopher.vue';

</script>

<template>
    <div class="slidev-layout default">
        <slot/>
        <RandomGopher/>
        <Charm/>
    </div>
</template>