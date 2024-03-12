<script lang="ts" setup>
import { useDrop, DropTargetMonitor } from 'vue3-dnd'
import type { DragItem } from './interfaces'
import { Colors } from './Colors'
import { computed, unref } from 'vue'

const props = defineProps<{
  onDrop: (item: any) => void
  lastDroppedColor?: string
}>()

const [collect, drop] = useDrop(() => ({
  accept: [Colors.YELLOW, Colors.BLUE],
  drop(_item: DragItem, monitor) {
    console.log(1111111,monitor.getItemType())
    props.onDrop(monitor.getItemType())
    return undefined
  },
  collect: (monitor: DropTargetMonitor) => ({
    isOver: monitor.isOver(),
    canDrop: monitor.canDrop(),
    draggingColor: monitor.getItemType() as string,
  }),
}))


const opacity = computed(() => (collect.value.isOver ? 1 : 0.7))
const backgroundColor = computed(() => {
  let backgroundColor = '#fff'
  switch (collect.value.draggingColor) {
    case Colors.BLUE:
      backgroundColor = 'lightblue'
      break
    case Colors.YELLOW:
      backgroundColor = 'lightgoldenrodyellow'
      break
    default:
      break
  }
  return backgroundColor
})
</script>

<template>
  <div
    :ref="drop"
    :data-color="lastDroppedColor || 'none'"
    :style="{ backgroundColor, opacity }"
    class="target-box"
    role="TargetBox"
  >
    <p>Drop here.</p>
    <p v-if="!canDrop && lastDroppedColor">
      Last dropped: {{ lastDroppedColor }}
    </p>
  </div>
</template>

<style lang="less" scoped>
.target-box {
  width: 15rem;
  height: 15rem;
  padding: 2rem;
  text-align: center;
  border: 1px solid gray;
}
</style>