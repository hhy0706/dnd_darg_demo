<script lang="ts" setup>
import { useDrop } from 'vue3-dnd'
import Card from './Card.vue'
import { ItemTypes } from './ItemTypes'
import { ref } from 'vue'

const ITEMS = [
  {
    id: 1,
    text: 'AAAAAAAAAAAAAAA',
    type: 'left'
  },
  {
    id: 2,
    text: 'BBBBBBBBB',
    type: 'left'
  },
  {
    id: 3,
    text: 'CCCCCCCCC',
    type: 'left'
  },
  {
    id: 4,
    text: 'DDDDDDDDD',
    type: 'left'
  },
  {
    id: 5,
    text: 'EEEEEEEE',
    type: 'left'
  },
  {
    id: 6,
    text: 'FFFFFFFF',
    type: 'left'
  },
  {
    id: 7,
    text: 'GGGGGGGG',
    type: 'left'
  },
]
type ItemType = {
  card: {
    id: number
    text: string
    type: 'left'
  };
  index: number
}
const cards = ref(ITEMS)
const rightCards = ref<any[]>([]);
const findCard = (type: string, id: string) => {
  let arr = type === 'left' ? cards.value : rightCards.value
  const card = arr.filter(c => `${c.id}` === id)[0] as {
    id: number
    text: string
    type: string
  }
  return {
    card,
    index: arr.indexOf(card),
  }
}

const moveCard = (type: string, id: string, atIndex: number, same: boolean) => {
  
  const [arr1, arr2] = type === 'left' ? [cards.value, rightCards.value] : [rightCards.value, cards.value]
  const { card, index } = findCard(type, id);
  if (same  ) {
    console.log("moveCard",arr1[atIndex])
    arr1.splice(index, 1);
    arr1.splice(atIndex, 0, card);
    
  }
}

const [collect, drop] = useDrop(() => ({
  accept: ItemTypes.CARD, collect(monitor) {
    return {
      isMyOver: monitor.isOver({ shallow: true })
    }
  },
  drop(item: any, monitor) {
    if (item.type == 'left') return;
    // if (!collect.value.isMyOver) return;
    rightCards.value.splice(findCard(item.type, item.id).index, 1)
    item.type = 'left'
    cards.value.push(item)

  }
}))
const [rightCollect, rightDrop] = useDrop(() => ({
  accept: ItemTypes.CARD,
  collect(monitor) {
    return {
      isMyOver: monitor.isOver({ shallow: true })
    }
  },
  drop(item: any, monitor) {
    // if (!rightCollect.value.isMyOver) return;
    if (item.type == 'right') return;
    console.log('right --', item.originalIndex)
    
    cards.value.splice(findCard(item.type, item.id).index, 1)
    item.type = 'right'
    rightCards.value.push(item)

  }
}))
</script>

<template>
  <div class="body">
    <div :ref="drop" style="width: 400px;height: 800px;background:yellow">
      <Card v-for="card in cards" :type="card.type" :id="`${card.id}`" :key="card.id" :text="card.text"
        :move-card="moveCard" :find-card="findCard" />
    </div>
    <div :ref="rightDrop" style="width: 400px;height: 800px;background: red;overflow: auto;">
      <Card v-for="card in rightCards" :type="card.type" :id="`${card.id}`" :key="card.id" :text="card.text"
        :move-card="moveCard" :find-card="findCard" />
    </div>
  </div>
</template>
<style lang="less" scoped>
.body {
  display: flex;
}
</style>