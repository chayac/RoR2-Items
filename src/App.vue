<template>
	<div class="container w-full mx-auto p-1">
		<form class="search flex flex-row flex-wrap top-0 py-1 bg-gray-700 shadow-b" @submit.prevent="">
            <div class="flex flew-row flex-nowrap mb-2">
				<button
					class="rounded-l-md bg-red-600 text-white border-2 border-red-700 py-1 px-1 focus:border-red-800 focus:ring focus:ring-red-800"
					@click.prevent="clearFilter"
				>
					Clear
				</button>
				<input
					class="flex-grow text-black placeholder-gray-500 border border-gray-200 rounded-r-md p-1 focus:border-blue-800 focus:ring focus:ring-blue-800"
					v-model="strFilter"
					placeholder="Search name, tags, rarity and description"
					@keydown.enter.prevent=""
				/>
			</div>
			<div class="flex flex-row flex-wrap mb-2">
				<FilterButton rarity="all" name="All" :selected="selectedRarity" v-on:selectRarity="selectRarity" />
				<FilterButton
					v-for="(item, index) in rarities"
					:rarity="index"
					:name="item"
					:key="index"
					:selected="selectedRarity"
					v-on:selectRarity="selectRarity"
				/>
			</div>

		</form>
		<div class="body flex flex-row flex-wrap items-start justify-between mt-1">
			<template v-if="items.length > 0">
				<Item v-for="item in items" :key="item.uid" :item="item" @showModal="showModal" />
			</template>
		</div>
		<!-- <div class="body flex flex-row flex-wrap items-start justify-around mt-1">
			<template v-if="items.length > 0">
				<Item v-for="item in commonItems" :key="item.uid" :item="item" @showModal="showModal" />
			</template>
		</div> -->
	</div>
	<Modal ref="modal" />
</template>

<script lang="ts" setup>
import { computed, onMounted, ref } from 'vue';
import Item from './components/Item.vue';
import Modal from './components/Modal.vue';
import allItems from './assets/list.json';
import Rarity from './Rarity';
import FilterButton from './components/FilterButton.vue';
import UpdateAlert from './components/UpdateAlert.vue';

/**
 * Additional hidden equipments:
 *	[5, 5, 'AffixRed', 'Ifrit\'s Distinction', 'offense,fire,drop', 'fireAspect', 'Become an aspect of fire.', 'Drop from Fire Elite enemies.'],
 *	[6, 5, 'AffixBlue', 'Silent Between Two Strikes', 'offense,lightning,drop', 'lightningAspect', 'Become an aspect of lightning.', 'Drop from Lightning Elite enemies.'],
 *	[9, 5, 'AffixWhite', 'Her Biting Embrace', 'offense,ice,drop', 'iceAspect', 'Become an aspect of ice.', 'Drop from Ice Elite enemies.'],
 *	[10, 5, 'AffixPoison', 'N\'kuhana\'s Retort', 'offense,malachite,debuff,heal', 'affixMalachite', 'Become an aspect of corruption.', 'Drop from Malachite Elite enemies.'],
 *	[32, 5, 'AffixHaunted', 'Affix Haunted', 'utility,defense,haunted,elite,drop', 'whiteSquare', 'Become an haunted aspect.\n{misc:(No in-game image yet)}', 'Drop from Haunter Elite enemies.'],
 *	[29, 5, 'QuestVolatileBattery', 'Fuel Array', 'quest', 'fuelArray', 'Looks like it could power something.\n{offense:EXTREMELY unstable...}.\n{misc:(Not obtainable in-game, used for quest)}'],
 */

const list = ref<ItemDescription[]>([]);
const lunarEquipments = [3, 23, 26];
const strFilter = ref('');
const rarityFilter = ref<'all' | Rarity>('all');
const rarities = ['Common', 'Uncommon', 'Rare', 'Unique', 'Corrupted', 'Lunar', 'Equipment'];
const modal = ref<typeof Modal | null>(null);

const items = computed((): ItemDescription[] => {
	return list.value.filter((item: ItemDescription) => {
		return (
			(strFilter.value == '' ||
				item.name.indexOf(strFilter.value) >= 0 ||
				item.tags.indexOf(strFilter.value) >= 0 ||
				item.uid.indexOf(strFilter.value) >= 0 ||
				item.description.indexOf(strFilter.value) >= 0 ||
				item.image.indexOf(strFilter.value) >= 0 ||
				item.stringRarity.indexOf(strFilter.value) >= 0) &&
			(rarityFilter.value == 'all' || rarityFilter.value == item.rarity)
		);
	});
});

const commonItems = computed((): ItemDescription[] => {
	return list.value.filter((item: ItemDescription) => {
		return (
			(strFilter.value == '' ||
				item.name.indexOf(strFilter.value) >= 0 ||
				item.tags.indexOf(strFilter.value) >= 0 ||
				item.uid.indexOf(strFilter.value) >= 0 ||
				item.description.indexOf(strFilter.value) >= 0 ||
				item.image.indexOf(strFilter.value) >= 0 ||
				item.stringRarity.indexOf(strFilter.value) >= 0) &&
			(Rarity.COMMON == item.rarity)
		);
	});
});

const rarityToString = (rarity: Rarity): string => {
	switch (rarity) {
		case Rarity.COMMON:
			return 'common';
		case Rarity.UNCOMMON:
			return 'uncommon';
		case Rarity.RARE:
			return 'rare';
		case Rarity.UNIQUE:
			return 'unique';
		case Rarity.CORRUPTED:
			return 'corrupted';
		case Rarity.LUNAR:
			return 'lunar';
	}
	//case Rarity.EQUIPMENT:
	return 'equipment';
};

const selectedRarity = computed((): 'all' | Rarity => {
	return rarityFilter.value;
});

const clearFilter = () => {
	strFilter.value = '';
};

const selectRarity = (type: 'all' | Rarity) => {
	rarityFilter.value = type;
};

const showModal = (item: ItemDescription) => {
	if (modal.value) {
		modal.value.show(item);
	}
};

onMounted(() => {
	for (const rawItem of allItems) {
		if (rawItem.length === 1) continue;
		const item = rawItem as RawItemDescription;
		list.value.push({
			id: item[0],
			rarity: item[1],
			stringRarity: rarityToString(item[1]),
			uid: item[2],
			name: item[3],
			tags: item[4],
			image: item[5],
			description: item[6],
			unlock: item[7],
		});
	}
});
</script>
