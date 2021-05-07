<template>
    <div class="w-full my-2">
        <div class="relative">
            <div class="w-full  svelte-1l8159u">
                <div class="my-2 p-1 flex border border-gray-200 bg-white rounded svelte-1l8159u">
                    <div class="grid grid-cols-12">
                        <span class="col-span-11 mt-1 px-2">Select Columns</span>
                        <div class="col-span-1">
                            <input placeholder="" class="bg-transparent p-1 px-2 appearance-none outline-none h-full w-full text-gray-800">
                        </div>
                    </div>
                    <div class="text-gray-300 w-8 py-1 pl-2 pr-1 border-l flex items-center border-gray-200 svelte-1l8159u">
                        <button class="cursor-pointer w-6 h-6 text-gray-600 outline-none focus:outline-none" @click="toggleDropDown = !toggleDropDown">
                            <svg v-if="!toggleDropDown" class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg>
                            <svg v-if="toggleDropDown" class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 15l7-7 7 7"></path></svg>
                        </button>
                    </div>
                </div>
            </div>
            <div class="absolute shadow top-100 bg-white z-40 w-full lef-0 rounded max-h-select overflow-y-auto svelte-5uyqqj" v-if="toggleDropDown">

                <div class="flex flex-col w-full" v-for="el in elements" :key="el.value" :class="el.visible ? 'bg-blue-200' : 'bg'">
                    <div class="cursor-pointer w-full border-gray-100 rounded-t border-b hover:bg-teal-100" @click="selectValue(el)">
                        <div class="flex w-full items-center p-2 pl-2 border-transparent border-l-2 relative hover:border-teal-100">
                            <div class="w-full items-center flex">
                                <div class="mx-2 leading-6" > {{el.text}} </div>
                            </div>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            toggleDropDown: false
        }
    },
    props: ['elements'],
    methods: {
        selectValue(val) {
            this.$emit('selectValue', val)
        }
    }
}
</script>

<style>
    .top-100 {top: 100%}
    .bottom-100 {bottom: 100%}
    .max-h-select {
        max-height: 300px;
    }
</style>