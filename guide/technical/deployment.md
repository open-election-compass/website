# Deployment

Currently, there are at least three ways to set up and deploy your own election compass.

## Option 1: The simple, One-File-Only Set-up

This method is the simplest to use and is suited for people without a deeper technical
understanding. You use the tools provided on the site to generate a single HTML file. This file is
deployed to your webspace. And thats it. Easy as pie.

### Requirements

- **Webspace**: You need a webserver or a webspace on a shared hosting provider. If you currently
  have a website, you are good to go.
- **FTP-Access**: In order to upload the neccessary files to your webspace, you will need access to
  the server's filesystem. If you have a web developer at hand, you can give her/him access to the
  control panel of your webspace provider, or you can look for the FTP or SFTP credentials.
  Alternatively, most providers have a file manager embedded in their web portal. To upload a single
  file, this will suffice.
- **SSH Certificate**: Your website traffic should be accessible over HTTPS. This is however not a
  strict requirement. It is recommended and has a positive impact on your visitors privacy.

### Step-by-step

1. Gather your content and open the
   [Configuration Editor](/configurator/version-1/configuration-editor.html). **Paste your content
   accordingly.**
2. When you are done editing, click **'Download' and save the generated JSON file** to your hard
   drive. You don't need to understand how this file works. Just know, that it contains all the
   content you pasted into the Configuration Editor. Make sure to not loose this file – if you need
   to make changes later, you can open it again in the Configuration Editor.
3. Open the [HTML Generator](/configurator/version-1/html-generator.html), **upload your JSON file
   and hit 'Generate'**. Your HTML file will start downloading. This file now contains the whole
   software and your content.
4. **Upload the HTML file to your webspace.** You can rename it as long as it keeps the file extension
   `.html`. You could, for example, put it in the root directory and name it: `compass.html`. It
   would then be accessible under `example.com/compass.html`. If it is not, look for a directory
   called `web`, `public` or `static` and try putting it there. If that also won't help, contact a
   professional.

## Option 2: Manual, static set-up

### Include the library

Place in the head of your HTML document. For privacy, security and legal reasons, you should
consider hosting the library yourself. But for a quick test run, the unpkg CDN is fine.

Note, that the UMD build as Vue.js inlined.

```html
<script src="https://unpkg.com/@open-election-compass/client/dist/lib/open-election-compass.umd.min.js" defer>
```

### Loading the content (your configuration)

Now for the content. You need to get your configuration in there. In the static setup, you have two
options:

#### Variant A: Using a script-element

You can place the complete configuration in your HTML using another script tag:

```html
<div id="open-election-compass">
   <open-election-compass load-tag="#oec-content" />
</div>

<script type="application/json" id="oec-content">
   // Place your configuration here. This is what the HTML Generator does.
</script>
```

#### Variant B: Load from separate URL

Alternatively, you can tell the OpenElectionCompass to load the configuration from a given URL. This
will be slower than Variant A.

```html
<div id="open-election-compass">
   <open-election-compass load-url="./your-configuration.json" />
</div>
```

## Option 3: Manual set-up using Nuxt.js

1. Install the client: `npm i @open-election-compass/client` or `yarn add @open-election-compass/client`.
2. Load the module in your `nuxt.config.js`:
   ```javascript
      {
         modules: [
            '@open-election-compass/client/src/nuxtModule.js',
            // ...
         ]
      }
   ```
3. Use the component with the `load-object` property:
   ```html
      <OpenElectionCompass :load-object="yourConfiguration" />
   ```

## HTML Attributes

The `<open-election-compass>` element knows a number of attributes to allow customization:

- `load-tag` – Pass a CSS selector to load configuration from that script tag.
- `load-url` – Pass a URL to load configuration from there.
- `load-object` – Pass the configuration as a JavaScript object.
- `kiosk-mode` – Activates the kiosk mode. Default: `false`.
- `friends` – Activates the "Multiplayer" mode that allows for shared sessions. Default: `false`.
- `friends-host` – The URL of the Peer.js server instance. Default: `'friends.open-election-compass.com'`.
- `friends-port` – The port of the Peer.js server instance. Default: `443`.
- `friends-path` – The path of the Peer.js server instance. Default: `'/'`.