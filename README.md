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
			position:"body",
			script: `(function(d,h,w)...`
		}
	]

	onLoad(() => {
		for (let integration of integrations) {
			if (!integration.active){
				console.log(`Skipping ${integration.name}`)
			}
			const script_element = document.createElement('script')
			script_element.text = integration.script
			const position = integration.position || "body"
			document[position].appendChild(script_element)
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
 
 You can use the `position` key to add integrations either to the `head` or to the `body`.
 
 This will keep your code clean and allow you to add integrations easily to your nuxt app.
