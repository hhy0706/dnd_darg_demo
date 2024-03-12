<script setup lang="ts">
import { useDrag,DragPreviewImage  } from 'vue3-dnd'

const [collectedProps, dragSource, dragPreview] = useDrag({
    type: 'BOX',
    item: { id: '1' },
    collect: (monitor) => ({ isDragging: monitor.isDragging() })
})
</script>

<template>
    <div  v-if="collectedProps.isDragging" :ref="dragPreview" >dragPreview</div >
    <div v-else :ref="dragSource">dragSource</div>
</template>
