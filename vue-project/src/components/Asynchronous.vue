<template>
    <div class="asynchronous w-full relative" ref="searchContainer1">
        <!-- Getting user input for Asynchronous search -->
        <input 
            type="text" 
            :value="modelValue" 
            placeholder="Asynchronous search"
            @click="handleClick"
            @input="handleInput" 
            @keydown.down.prevent="moveDown"
            @keydown.up.prevent="moveUp"
            @keydown.escape="closeDropdown"
            @keydown.enter.prevent="toggleCheckbox(searchResults[selectedIndex])"
            class="px-5 py-3 w-full border border-gray-300 rounded-md">
        <div
            v-if="isLoading"
            class ="absolute inset-y-0 right-0 flex items-center pr-3"
            >
            Loading...
        </div>
            <ul class="mt-1 w-full max-h-60 border border-gray-300 rounded-md bg-white absolute overflow-y-auto" 
            v-show="isOpen">
            <li
                class ="px-4 py-3 border-b border-gray-200 text-stone-600 cursor-pointer hover:bg-gray-100 transition-colors"
                v-if="searchResults.length === 0"  
            >
                No results found
            </li>
            <li 
                class="px-4 py-3 border-b border-gray-200 text-stone-600 cursor-pointer hover:bg-gray-100 transition-colors"
                v-for="(result, index) in searchResults" 
                :key="result.name"
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
const isLoading = ref(false)
const isOpen = ref(false)
const search = ref('')

const searchResults = computed(() => {
    // Returns the empty list when there is no input 
    if (search.value === '') {
        return []
    }
    return props.source.filter(item => {
        if (item.name.toLowerCase().includes(search.value.toLowerCase())) {
            return item
        }
    })
})

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

const handleClick = event => {
    if (!isOpen.value && searchResults.value.length === 0) {
        isOpen.value = true;
    }
}

const handleInput = async event => {
    console.log("Im inside")
    search.value = event.target.value
    emit('update:modelValue', search.value)
    isLoading.value = true;
    setTimeout(() => {
        isLoading.value = false;
        if (!isLoading.value) {
            isOpen.value = true;
        }
    }, 1000)
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
    const searchContainer1 = event.currentTarget.querySelector('.asynchronous');
    if (!searchContainer1 || !searchContainer1.contains(event.target)) {
        isOpen.value = false;
    } else {
        isOpen.value = true;
    }
    console.log("The isOpen value is: " ,isOpen.value)
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