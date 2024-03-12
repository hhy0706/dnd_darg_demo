<script setup lang="ts">
import { useDrop } from 'vue3-dnd'

const [, drop] = useDrop({
    accept: ['BOX']
})
</script>

<template>
  <div :ref="drop" style="width: 100px;height: 100px;background: red;">Drop Target</div>
</template>

