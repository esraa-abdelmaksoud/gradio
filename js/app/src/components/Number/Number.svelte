<script lang="ts">
	import { Number } from "@gradio/form";
	import { Block } from "@gradio/atoms";
	import StatusTracker from "../StatusTracker/StatusTracker.svelte";
	import type { LoadingStatus } from "../StatusTracker/types";

	export let label = "Number";
	export let info: string | undefined = undefined;
	export let elem_id = "";
	export let elem_classes: string[] = [];
	export let visible = true;
	export let container = true;
	export let scale: number | null = null;
	export let min_width: number | undefined = undefined;
	export let value = 0;
	export let show_label: boolean;
	export let minimum: number | undefined = undefined;
	export let maximum: number | undefined = undefined;
	export let loading_status: LoadingStatus;
	export let mode: "static" | "dynamic";
	export let value_is_output = false;
	export let step: number | null = null;
</script>

<Block
	{visible}
	{elem_id}
	{elem_classes}
	padding={container}
	allow_overflow={false}
	{scale}
	{min_width}
>
	<StatusTracker {...loading_status} />

	<Number
		bind:value
		bind:value_is_output
		{label}
		{info}
		{show_label}
		{minimum}
		{maximum}
		{step}
		{container}
		disabled={mode === "static"}
		on:change
		on:input
		on:submit
		on:blur
		on:focus
	/>
</Block>
