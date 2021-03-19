<template>
	<div style="background-color: red; height: 120px; font-size: 19px" @click="count++">{{ count }}</div>
</template>

<script>
export default {
	name: "School",
	data: function() {
		return {
			count: 0,
		};
	},
};
</script>
