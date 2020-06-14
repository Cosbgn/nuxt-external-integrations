# nuxt-external-integrations
Handle easily external integrations (like Google Anlytics, Intercom, etc) with nuxt.
These integrations will run on all pages!
This is a slightly modified version of the code from this article: https://deltener.com/blog/nuxt-third-party-code-is-poison

If you need to run something only on a specific page have a look at the article above for more info on that.

# create a plugin

create a plugin in the `/plugins` folder with the following code:

```
const onLoad = (callback, delay = 1) => { 
	// Load script only when everything else has been loaded
	if (document.readyState === "complete") {
	  setTimeout(() => callback(), delay);
	} else {
	  window.addEventListener("load", function() {
		setTimeout(() => callback(), delay);
	  });
	}
}

export default ({ isDev }) => {
	
  const integrations = [
		{
			name:"Intercom Live Chat",
			description: "Live chat widget to chat with users",
			active:!isDev,
			script: `(function(d,h,w)...`
		}
	]

	onLoad(() => {
		for (let integration of integrations) {
			if (integration.active){
				const script_element = document.createElement('script')
				script_element.text = integration.script
				const position = integration.head ? "head" : "body"
				document[position].appendChild(script_element)
			} else {
				console.log(`Skipping ${integration.name}`)
			}
		}
	})
}
```


# add the plugin to nuxt.config.js

```
	plugins: [
		{ src: '~/plugins/integrations.js', mode: 'client' }
	]
 ```
 
 # Add new integrations.
 Add as many integrations as you want in the `integrations` array inside `integrations.js`.
 
 You can use the `active` key to activate integrations only on your live server or compleately disable integrations (setting `active` to `false`).
 
 By default the integrations are added to the `body`. If you need them in the `head`, simply add `head:true` to the integration.
 
 This will keep your code clean and allow you to add integrations easily to your nuxt app.
