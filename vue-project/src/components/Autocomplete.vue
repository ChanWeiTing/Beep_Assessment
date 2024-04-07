<template>
    <div class="synchronous w-full relative" ref="searchContainer">
        <!-- Getting user input for Synchronous search -->
        <input 
            type="text" 
            :value="modelValue" 
            placeholder="Synchronous search"
            @click="showOptions" 
            @input="handleInput" 
            @keydown.down.prevent="moveDown"
            @keydown.up.prevent="moveUp"
            @keydown.escape="closeDropdown"
            @keydown.enter.prevent="toggleCheckbox(searchResults[selectedIndex])"
            class="px-5 py-3 w-full border border-gray-300 rounded-md"
            style="position: relative; top: -80px;">
        <ul class="mt-1 -top-9 w-full max-h-60 border border-gray-300 rounded-md bg-white absolute overflow-y-auto z-10" 
            v-show="isOpen">
            <li
                v-if="searchResults.length === 0"
                class ="absolute inset-y-0 right-0 flex items-center pr-3"
            >
                No results found
            </li>
            <li 
                v-for="(result, index) in searchResults" 
                :key="result.name"
                class="px-4 py-3 border-b border-gray-200 text-stone-600 cursor-pointer hover:bg-gray-100 transition-colors"
                :class="{'selected': selectedIndex === index}"
            >
                <label class="flex items-center">
                    <input type="checkbox" 
                    :value="result.name"
                    :checked="isSelected(result)" 
                    @change="selectOption(result)"
                    class="mr-2"
                    >
                    <span>{{ result.name }}</span>
                </label>
            </li>
        </ul>
    </div>
</template>

<script setup>
import { computed, ref, onMounted, onUnmounted } from 'vue'

const props = defineProps({
    source: {
        type: Array,
        required: true,
        default: () => []
    },
    modelValue: String

})

const emit = defineEmits(['update:modelValue'])
const selectedIndex = ref(-1)
const selectedOptions = ref([])

const search = ref('')

const searchResults = computed(() => {
    // Returns the whole list when there is no input 
    if (search.value === '') {
        return props.source
    }

    return props.source.filter(item => {
        if (item.name.toLowerCase().includes(search.value.toLowerCase())) {
            return item
        }
    })
})

const isOpen = ref(false)

// isSelected returns true if item is present in array
const isSelected = item => {
    //console.log(item)
    //console.log("The item is: ",selectedOptions.value.includes(item))
    //console.log("The selected options are ", selectedOptions.value)
    return selectedOptions.value.includes(item);
}

const selectOption = item => {
    if(isSelected(item)) {
        const index = selectedOptions.value.findIndex(option => option.name === item.name);
        console.log("The index is" , index)
        if (index !== -1) {
            selectedOptions.value.splice(index, 1);
        }
    } else {
        selectedOptions.value.push(item);
    }
}

const toggleCheckbox = item => {
    console.log("checkbox toggled")
    selectOption(item);
}

const handleInput = event => {
    isOpen.value = true
    search.value = event.target.value
    emit('update:modelValue', search.value)
}

const showOptions = event => {
    isOpen.value = true;
}

const moveDown = () => {
    if (selectedIndex.value < searchResults.value.length - 1) {
        selectedIndex.value++
        console.log(selectedIndex.value)
    }
}

const moveUp = () => {
    if (selectedIndex.value > 0) {
        selectedIndex.value--
        console.log(selectedIndex.value)
    }
}

// Keyboard escape to close dropdown menu
const closeDropdown = () => {
    isOpen.value = false
}

const handleClickOutside = event => {
    const searchContainer = event.currentTarget.querySelector('.synchronous');
    if (searchContainer && !searchContainer.contains(event.target)) {
        isOpen.value = false;
    }
}

onMounted(() => {
    document.addEventListener('click', handleClickOutside);
});

onUnmounted(() => {
    document.removeEventListener('click', handleClickOutside);
});
</script>

<style scoped>
.selected {
    background-color: #e2e8f0;
}
</style>