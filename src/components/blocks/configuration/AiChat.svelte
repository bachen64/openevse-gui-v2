<script>
	import AiChatHelp			from "../../help/AiChatHelp.svelte";
	import Borders 				from "../../ui/Borders.svelte";
	import { onMount } 			from "svelte";
	import { _ } 			    from 'svelte-i18n'
	import { status_store } 	from "../../../lib/stores/status.js";
	import { config_store } 	from "../../../lib/stores/config.js";
	import { submitFormData}	from "../../../lib/utils.js"
	import InputForm 			from "../../ui/InputForm.svelte";
	import Box 					from "../../ui/Box.svelte";
	import Switch 				from "../../ui/Switch.svelte";

	let mounted = false
	let formdata = {
		aichat_enabled: {val: false, status: "", input: undefined, req: false},
		aichat_group:	{val: null,  status: "", input: undefined, req: true},
		aichat_max_pwr:	{val: null,  status: "", input: undefined, req: true},
		aichat_cs_url:	{val: null,  status: "", input: undefined, req: false}
	}

	let updateFormData = () => {
		formdata.aichat_enabled.val = $config_store.aichat_enabled
		formdata.aichat_group.val = $config_store.aichat_group
		formdata.aichat_max_pwr.val = $config_store.aichat_max_pwr
		formdata.aichat_cs_url.val = $config_store.aichat_cs_url
	}

	let toggleAiChat = async () => {
		await submitFormData({form: formdata, prop_enable: "aichat_enabled", i18n_path: "config.aichat.missing-"})
	}

	let setProperty = async (prop) => {
		await submitFormData({prop: prop, form: formdata , prop_enable: "aichat_enabled", i18n_path: "config.aichat.missing-"})
	}

	onMount( () => {
		updateFormData()
		mounted = true
	})

	// $: formdata = formdata
</script>

{#if mounted}
<Box title={$_("config.titles.aichat")} icon="fa6-solid:robot" back={true} has_help={true} >
	<div slot="help"><AiChatHelp /></div>
	<div class="columns is-centered">
		<div class="column is-three-quarters is-full-mobile">
			<div class="mb-2 is-flex is-align-items-center is-justify-content-center">
				<Borders classes={formdata.aichat_enabled.val?"has-background-primary-light":"has-background-light"}>
					<Switch
						name="aichatswitch"
						label={$_("enable")}
						onChange={toggleAiChat}
						bind:this={formdata.aichat_enabled.input}
						bind:checked={formdata.aichat_enabled.val}
						bind:status={formdata.aichat_enabled.status}
						disabled={formdata.aichat_enabled.status == "loading"}
					/>
					{#key $status_store.aichat_connected}
					<div class:is-hidden={!$config_store.aichat_enabled} class="has-text-weight-bold {$status_store.aichat_connected?"has-text-primary":"has-text-danger"}">
						{$_("config.aichat.connected")}: {$status_store.aichat_connected?$_("yes"):$_("no")}
					</div>
					{/key}
				</Borders>
			</div>

			<div class="is-size-7 mb-2 has-text-centered">{$_("config.aichat.desc")}</div>

			<div class="is-flex is-justify-content-center">
				<Borders grow>
					<div class="mr-2">
						<InputForm
							title="{$_("config.aichat.group")}*"
							type="text"
							bind:this={formdata.aichat_group.input}
							bind:value={formdata.aichat_group.val}
							bind:status={formdata.aichat_group.status}
							disabled={formdata.aichat_group.status == "loading"}
							placeholder="grp1"
							onChange={()=>setProperty("aichat_group")}
						/>
						<div class="is-size-7 has-text-left">{$_("config.aichat.group-desc")}</div>
					</div>
					<div class="mr-2">
						<InputForm
							title="{$_("config.aichat.maxpower")}*"
							type="number"
							bind:this={formdata.aichat_max_pwr.input}
							bind:value={formdata.aichat_max_pwr.val}
							bind:status={formdata.aichat_max_pwr.status}
							disabled={formdata.aichat_max_pwr.status == "loading"}
							placeholder="11520"
							onChange={()=>setProperty("aichat_max_pwr")}
						/>
						<div class="is-size-7 has-text-left">{$_("config.aichat.maxpower-desc")}</div>
					</div>
					<div class="mr-2">
						<InputForm
							title="{$_("config.aichat.server")}"
							type="text"
							bind:this={formdata.aichat_cs_url.input}
							bind:value={formdata.aichat_cs_url.val}
							bind:status={formdata.aichat_cs_url.status}
							disabled={formdata.aichat_cs_url.status == "loading"}
							onChange={()=>setProperty("aichat_cs_url")}
						/>
						<div class="is-size-7 has-text-left">{$_("config.aichat.server-desc")}</div>
					</div>
				</Borders>
			</div>
		</div>
	</div>
</Box>
{/if}