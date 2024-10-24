<template>
  <div class="pagination">
    <div class="first" @click="current=1">
     <p>First</p>
    </div>
    <div class="container">
    <div
      class="prev"
      @click="switchPage(-1)"
      :class="{ disabled: current === 1 }"
    >
      <font-awesome-icon :icon="['fas', 'chevron-left']" />
    </div>
    <ul class="page">
      <li
        v-if="pageCount > 0"
        @click="current = 1"
        :class="{ active: current === 1 }"
      >
        1
      </li>
      <li
        class="more"
        v-if="isPrevMoreVisible"
      >
        ...
      </li>
      <li
        v-for="pager in pagers"
        :key="pager"
        @click="jumpPage(pager)"
        :class="{ active: current === pager }"
      >
        {{ pager }}
      </li>
      <li
        class="more"
        v-if="isNextMoreVisible"
      >
       ...
      </li>
      <li
        v-if="pageCount > 1"
        @click="current = pageCount"
        :class="{ active: current === pageCount }"
      >
        {{ pageCount }}
      </li>
    </ul>
    <div
      class="next"
      :class="{ disabled: current === pageCount }"
       @click="switchPage(1)"
    >
      <font-awesome-icon :icon="['fas', 'chevron-right']"/>
    </div>
    </div>
<div class="last" @click="current=Math.floor(total / perpage) + 1">
     <p>Last</p>
    </div>
  </div>
</template>



<script setup>
import { ref, computed, watchEffect} from "vue"
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { faChevronLeft,faChevronRight } from '@fortawesome/free-solid-svg-icons'
import { library } from '@fortawesome/fontawesome-svg-core'

library.add(faChevronLeft,faChevronRight)
const currentPage = 1
const total= 400
const perpage = 35

const current = ref(currentPage)
const isPrevMoreVisible = ref(false)
const isNextMoreVisible = ref(false)
const pageCount =total % perpage === 0 ? total /perpage : Math.floor(total / perpage) + 1

const pagerCount = 4


const pagers = computed(() => {
  let showPrevMore = false
  let showNextMore = false
  const halfPageCount = (pagerCount - 1) / 2
  if (pageCount > pagerCount) {
    if (current.value > pagerCount - halfPageCount) {
      showPrevMore = true  
    }
    if (current.value < pageCount - halfPageCount) {
      showNextMore = true
    }
  }
  const array = []
  if (showPrevMore && !showNextMore) {
    const startPage = pageCount - (pagerCount - 2)
    for (let i = startPage; i < pageCount; i++) {
      array.push(i)     
    }
  } else if (!showPrevMore && showNextMore) {
    for (let i = 2; i < pagerCount; i++) {
      array.push(i)    
    }
  } else if (showPrevMore && showNextMore) {
    const offset = Math.floor(pagerCount / 2) - 1
    for (let i = current.value - offset; i <= current.value + offset; i++) {
      array.push(i)
    }
  } 
  return array
})

const switchPage = (value) => {
  if (value > 0) {
    current.value += 1
  } else {
    current.value -= 1
  }
}

const jumpPage = (page) => {
  current.value = page
}

watchEffect(() => {
  const halfPageCount = (pagerCount - 1) / 2
  isPrevMoreVisible.value = false
  isNextMoreVisible.value = false
  if (pageCount > pagerCount) {
    if (current.value > pagerCount - halfPageCount) {
      isPrevMoreVisible.value = true
    }
    if (current.value < pageCount - halfPageCount) {
      isNextMoreVisible.value = true
    }
  }
})
</script>

<style scoped>
.pagination {
  display: flex;
  align-items: center;
  justify-content: center;
  line-height: 30px;
}

.pagination .prev {
  margin-right: 10px;
}

.pagination .next {
  margin-left: 10px;
}

.pagination .prev.disabled,
.pagination .next.disabled {
  pointer-events: none;
  color:grey;
}

.page {
  padding:0;
}
.pagination .page {
  display: flex;
  align-items: center;
}

.pagination .page li {
  font-size: 1.3rem;
  padding: 0 4px;
  min-width: 32px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}

.pagination .page li.active {
  background-color: rgb(233,108,17);
  color: white;
  border-radius: 10px;
  padding: 10px;
}

.pagination .more {
  color: black;
  font-size: 1.6rem;
   display: flex;
}
.container{
  display: flex;
  align-items: center;
  margin: 0 30px;
}
.last,.first{
  cursor: pointer;
}
</style>
