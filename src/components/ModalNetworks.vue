<script setup>
import { ref, computed, watch } from 'vue';
import { useNetworksFilter } from '@/composables/useNetworksFilter';

const props = defineProps({
  open: {
    type: Boolean,
    required: true
  }
});

const emit = defineEmits(['update:modelValue', 'close']);

const searchInput = ref('');
const { filterNetworks, getNetworksSpacesCount, loadingNetworksSpacesCount } =
  useNetworksFilter();
const networks = computed(() => filterNetworks(searchInput.value));

watch(
  () => props.open,
  () => {
    if (props.open) getNetworksSpacesCount();
  }
);

function select(key) {
  emit('update:modelValue', key);
  emit('close');
}
</script>

<template>
  <BaseModal :open="open" @close="$emit('close')">
    <template #header>
      <h3>{{ $t('networks') }}</h3>
    </template>
    <BaseSearch
      v-model="searchInput"
      :placeholder="$t('searchPlaceholder')"
      modal
    />

    <div class="my-4 mx-0 min-h-[339px] md:mx-4">
      <LoadingRow v-if="loadingNetworksSpacesCount" block />
      <div v-else class="space-y-3">
        <div
          v-for="network in networks"
          :key="network.key"
          @click="select(network.key)"
        >
          <BaseNetworkItem :network="network" />
        </div>
        <BaseNoResults v-if="Object.keys(networks).length < 1" />
      </div>
    </div>
  </BaseModal>
</template>
