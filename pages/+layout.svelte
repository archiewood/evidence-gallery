<script>
	import '@evidence-dev/tailwind/fonts.css';
	import '../app.css';
	import { EvidenceDefaultLayout } from '@evidence-dev/core-components';
	export let data;
</script>

<EvidenceDefaultLayout {data} hideSidebar hideTOC hideBreadcrumbs fullWidth xProfile=https://x.com/archieemwood githubRepo=https://github.com/archiewood/evidence-gallery>
	<slot slot="content" />
</EvidenceDefaultLayout>
