<script lang="ts" setup>
type ItemType = { name: string; id: number; };

const isLoading = ref(true);
const query = ref('');
const items = ref<ItemType[]>([]);
const selected = ref<ItemType>();

const options = computed(() =>
  items.value
    .filter((el) => el.name.toLowerCase().includes(query.value.toLowerCase()))
    .slice(0, 40),
);

const onSelect = (value: ItemType) => {
  selected.value = value;
};

const getOptionKey = (value: ItemType) => value.id.toString();
const getOptionLabel = (value: ItemType) => value.name;

onMounted(() => {
  $fetch<ItemType[]>('https://jsonplaceholder.typicode.com/users').then((data) => {
    items.value = data;
    isLoading.value = false;
  });
});
</script>

<template>
  <div>
    https://jsonplaceholder.typicode.com/users
    <SearchSelect
      v-model:query="query"
      @select="onSelect"
      :options="options"
      :get-option-key="getOptionKey"
      :get-option-label="getOptionLabel"
      :loading="isLoading"
      placeholder="Name"
    >
      <template #label="{ option }">
        {{ option.name }}
      </template>
    </SearchSelect>
    {{ selected?.name }}
  </div>

  <div id="modals" />
</template>

<style lang="scss">
* {
  box-sizing: border-box;
}
</style>
