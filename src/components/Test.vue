<template>
	<div style="background-color: red; height: 120px; font-size: 19px" @click="count++">{{ count }}</div>
</template>

<script>
export default {
	name: "HelloWorld",
	data: function() {
		return {
			count: 0,
		};
	},
};
</script>
