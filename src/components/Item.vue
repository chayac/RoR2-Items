<template>
	<div
		class="flex flex-col flex-nowrap overflow-hidden mb-4 rounded-md rounded-bl-none border shadow-md transition hover:shadow-xl"
		:class="[border, background, width]"
	>
		<div class="flex items-center content-center justify-between p-1 text-xl border-b" :class="[border]">
            <div v-lazyload class="flex-grow-0 flex-shrink-0 border-r" :class="[border]">
				<img
					title="Click to copy give command"
					width="90"
					height="90"
					:data-url="`img/${item.image}.jpg`"
					@click="copyGive"
				/>
			</div>
			{{ item.name }}
			<div>
				<button
					class="p-1 mr-1 rounded-md text-sm border border-blue-800 bg-blue-600 transition hover:border-blue-900 hover:bg-blue-700"
					v-if="item.unlock"
					@click.prevent="toggleModal"
				>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						fill="none"
						viewBox="0 0 24 24"
						stroke-width="1.5"
						stroke="currentColor"
						class="w-2 h-2 mr-1 text-white"
					>
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							d="M13.5 10.5V6.75a4.5 4.5 0 1 1 9 0v3.75M3.75 21.75h10.5a2.25 2.25 0 0 0 2.25-2.25v-6.75a2.25 2.25 0 0 0-2.25-2.25H3.75a2.25 2.25 0 0 0-2.25 2.25v6.75a2.25 2.25 0 0 0 2.25 2.25Z"
						/>
					</svg>
				</button>
			</div>
		</div>
		<div class="item-body flex flex-row flex-nowrap">
			<!-- <div v-lazyload class="flex-grow-0 flex-shrink-0 border-r" :class="[border]">
				<img
					title="Click to copy give command"
					width="90"
					height="90"
					:data-url="`img/${item.image}.jpg`"
					@click="copyGive"
				/>
			</div> -->
			<div class="flex-grow p-1 overflow-y-auto whitespace-pre-line" v-html="description"></div>
		</div>
	</div>
</template>

<script lang="ts" setup>
import { computed } from 'vue';
import Rarity from '../Rarity';

const props = defineProps<{ item: ItemDescription }>();

const emit = defineEmits(['showModal']);

const isLunarEquipment = computed(() => {
	return false; // return this.$store.state.lunarEquipments.indexOf(this.item.id) >= 0;
});

const border = computed(() => {
	switch (props.item.rarity) {
		case Rarity.COMMON:
			return `border-common-light`;
		case Rarity.UNCOMMON:
			return `border-uncommon-light`;
		case Rarity.RARE:
			return `border-rare-light`;
		case Rarity.UNIQUE:
			return `border-unique-light`;
		case Rarity.CORRUPTED:
			return `border-corrupted-light`;
		case Rarity.LUNAR:
			return `border-lunar-light`;
	}
	// case Rarity.EQUIPMENT:
	return `border-equipment-light`;
});

const width = computed(() => {
	switch (props.item.rarity) {
		case Rarity.COMMON:
			return `w-40`;
		case Rarity.UNCOMMON:
			return `w-40`;
		case Rarity.RARE:
			return `w-60`;
		case Rarity.UNIQUE:
			return `w-60`;
		case Rarity.CORRUPTED:
			return `w-60`;
		case Rarity.LUNAR:
			return `w-60`;
	}
	// case Rarity.EQUIPMENT:
	return `w-60`;
});

const background = computed(() => {
	switch (props.item.rarity) {
		case Rarity.COMMON:
			return `bg-common`;
		case Rarity.UNCOMMON:
			return `bg-uncommon`;
		case Rarity.RARE:
			return `bg-rare`;
		case Rarity.UNIQUE:
			return `bg-unique`;
		case Rarity.CORRUPTED:
			return `bg-corrupted`;
		case Rarity.LUNAR:
			return `bg-lunar`;
	}
	// case Rarity.EQUIPMENT:
	return `bg-equipment`;
});

const description = computed(() => {
	return props.item.description
		.replaceAll(/{(offense|defense|debuff|misc|corrupt):(.+?)}/g, `<span class="is-$1">$2</span>`)
		.replaceAll(/{(.+?)\$}/g, `<span class="is-stackable not-per-stack">$1</span>`)
		.replaceAll(/{(.+?)\|(.+?)}/g, `<span class="is-stackable">($1 $2)</span>`)
		.replaceAll(/{(.+?)}/g, `<span class="is-stackable">($1 per stack)</span>`);
});

const copyGive = () => {
	let command = 'give_item';
	if (props.item.rarity == Rarity.EQUIPMENT || (props.item.rarity == Rarity.LUNAR && isLunarEquipment.value)) {
		command = 'give_equip';
	}
	navigator.clipboard.writeText(`${command} ${props.item.id} 1`);
};

const toggleModal = () => {
	emit('showModal', props.item);
};
</script>

<!-- <style scoped>
.item-body {
	height: 150px;
}
</style> -->
