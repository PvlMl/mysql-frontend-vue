<template>
<div>
 <select class="mb-3" 
 @change="$emit('changeSelect',$event.target.value)"
 >
 <option disabled selected>Выберите значение</option>
        <option
          v-for="option in options"
          :key="option.text"
          :value="option.value"
        >
          {{ option.text }}
        </option>
      </select>
</div>
</template>
<script>
export default {
    props: {
        options: {
            type: Array,
            required: true,
        }
    }
}
</script>

<style scoped>
 
</style>