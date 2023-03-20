<script lang="ts">
	import { appWindow } from "@tauri-apps/api/window";
	import { getMatches, type ArgMatch } from "@tauri-apps/api/cli";
	import { onMount } from "svelte";

	import { invoke } from "@tauri-apps/api/tauri";
	import { exit } from "@tauri-apps/api/process";
	import { getVersion } from "@tauri-apps/api/app";

	const flagExists = (
		args: {
			[name: string]: ArgMatch;
		},
		flag: string
	) => {
		return flag in args && args[flag].value !== false;
	};

	onMount(async () => {
		const args = (await getMatches()).args;
		console.log(args);

		let isHeadlessMode = false;

		if (flagExists(args, "help")) {
			isHeadlessMode = true;
			const helpInfo = args["help"].value;
			console.log("printing ", helpInfo);
			await invoke("stout", { text: helpInfo });
			await exit(0);
		}
		if (flagExists(args, "headless")) {
			isHeadlessMode = true;
			await invoke("stout", { text: "headless flow" });
			await exit(0);
		}
		if (flagExists(args, "version")) {
			isHeadlessMode = true;
			await invoke("stout", { text: await getVersion() });
			await exit(0);
		}
		if (flagExists(args, "custom")) {
			await invoke("stout", { text: "you're in custom mode" });
		}

		if (!isHeadlessMode) {
			appWindow.show();
		}
	});
</script>

<main class="container" />
