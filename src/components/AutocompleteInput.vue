<script setup>

import { onMounted, onUnmounted, ref, watch } from "vue"

const results = ref([])
const isOpen = ref(false)
const highlightIndex = ref(-1)
const rootElement = ref(null)
const autocompleteElement = ref(null)

const props = defineProps({
    items: {
        type: Array,
        required: false,
        default: () => [],
    },
    inputClasses: {
        type: Object,
        required: false,
        default: {}
    },
    rootClasses: {
        type: Object,
        required: false,
        default: {}
    },
    input: {
        type: String,
        required: false,
        default: ''
    },
    reference: {
        type: Object,
        required: false,
        default: null
    }
})

const emit = defineEmits(['update:input'])

const setResult = (result) => {
    emit('update:input', result)
    isOpen.value = false
}

const onChange = (event) => {
    autocompleteElement.value.scrollTop = 0
    highlightIndex.value = -1

    const inputValue = event.target.value
    results.value = props.items.filter(item => item.toLowerCase().indexOf(inputValue.toLowerCase()) > -1)
    emit('update:input', inputValue)
    isOpen.value = inputValue.length > 0
}

const handleClickOutside = (event) => {
    if (!rootElement.value.contains(event.target)) {
        isOpen.value = false
        autocompleteElement.value.scrollTop = 0
        highlightIndex.value = -1
    }
}

const onClickInput = (event) => {
    const inputValue = event.target.value
    results.value = props.items.filter(item => item.toLowerCase().indexOf(inputValue.toLowerCase()) > -1)
    isOpen.value = inputValue.length > 0
}

const onArrowDown = () => {
    if (highlightIndex.value < results.value.length - 1) {
        highlightIndex.value = highlightIndex.value + 1
        const activedElement = autocompleteElement.value.getElementsByClassName("is-active")[0]
        if (activedElement) {
            const elementCount = Math.floor(autocompleteElement.value.clientHeight / activedElement.clientHeight)
            const surplusOfElement = autocompleteElement.value.clientHeight % activedElement.clientHeight
            if (highlightIndex.value >= elementCount) {
                autocompleteElement.value.scrollTop = surplusOfElement + (highlightIndex.value - elementCount + 1) * activedElement.clientHeight
            }
        }

    }
}

const onArrowUp = () => {
    if (highlightIndex.value > 0) {
        highlightIndex.value = highlightIndex.value - 1
        const activedElement = autocompleteElement.value.getElementsByClassName("is-active")[0]
        if (activedElement && activedElement.offsetTop <= autocompleteElement.value.scrollTop) {
            autocompleteElement.value.scrollTop = activedElement.offsetTop - activedElement.clientHeight
        }
    }
}

const onEnter = () => {
    if (highlightIndex.value >= 0) {
        emit('update:input', results.value[highlightIndex.value])
        highlightIndex.value = -1
        isOpen.value = false
    }
}

onMounted(() => {
    document.addEventListener('click', handleClickOutside)
})

onUnmounted(() => {
    document.removeEventListener('click', handleClickOutside)
})

watch(results, (value) => {
    if (value.length === 0) {
        isOpen.value = false
    } else {

    }
})

</script>

<template>
    <div class="autocomplete" ref="rootElement" :class="props.rootClasses">
        <input type="text" :value="props.input" @input="onChange" @keydown.down.prevent="onArrowDown"
            @keydown.up.prevent="onArrowUp" class="form-control" :class="props.inputClasses"
            @keydown.enter.prevent="onEnter" :ref="props.reference" @click="onClickInput" />
        <ul class="autocomplete-results" v-show="isOpen" ref="autocompleteElement">
            <li v-for="(result, i) in results" :key="i" class="autocomplete-result" @click="() => setResult(result)"
                :class="{ 'is-active': i === highlightIndex }">
                {{ result }}
            </li>
        </ul>
    </div>
</template>

<style>
.autocomplete {
    position: relative;
    flex: 1 1 auto;
}

.autocomplete-results {
    padding: 0;
    margin: 0;
    border: 1px solid #eeeeee;
    height: auto;
    min-height: 1em;
    max-height: 10em;
    overflow: auto;
    position: absolute;
    z-index: 33;
    background: aliceblue;
    width: 100%;
}

.autocomplete-result {
    list-style: none;
    text-align: left;
    padding: 4px 2px;
    cursor: pointer;
}

.autocomplete-result.is-active,
.autocomplete-result:hover {
    background-color: #4AAE9B;
    color: white;
}
</style>