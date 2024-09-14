<script>
	import { marked } from 'marked';
	import markedKatex from '$lib/utils/marked/katex-extension';
	import { replaceTokens, processResponseContent } from '$lib/utils';
	import { user } from '$lib/stores';

	import MarkdownTokens from './Markdown/MarkdownTokens.svelte';

	export let id;
	export let content;
	export let model = null;

	let isHTMLContent = false;
    let htmlContent = '';


	let tokens = [];

	const options = {
		throwOnError: false
	};

	marked.use(markedKatex(options));


	function executeScripts(element) {

		try {
			try {
				let allScripts = document.head.querySelectorAll('script');

				allScripts.forEach(script => {
					if (script.textContent.includes(element) || element.includes(script.textContent)) {
						script.remove();
					}
				});
			} catch(e) {console.log("SCRIPT REMOVAL ERROR", e)}
			
		
			const newScript = document.createElement('script');
			
			

			const regex = /<script>([\s\S]*?)<\/script>/;
			const match = element.match(regex);

			if (match && match[1]) {
				const content = match[1].trim();
				newScript.textContent = content

				document.head.appendChild(newScript);
			} else {
				console.log("No script content found.");
			}
		} catch {}

		
            
		
        
    }


	$: (async () => {
        if (content) {
            if (content.includes('<!HTML>')) {
                isHTMLContent = true;
                htmlContent = content.substring(7); // Remove the marker and sanitize
				
            } else {
                isHTMLContent = false;
                tokens = marked.lexer(
                    replaceTokens(processResponseContent(content), model?.name, $user?.name)
                );
            }
        }
    })();
</script>

{#key id}
    {#if isHTMLContent}
        {@html htmlContent}
    {:else}
        <MarkdownTokens {tokens} {id} />
    {/if}
{/key}
