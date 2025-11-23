
# Building a Progressive Web App (PWA) with JavaScript & React
2025-11-23

**Tags:** `pwa` `web-development` `progressive-web-app` `react` `javascript`

Progressive Web Apps (PWAs) are modern web applications that combine the reach of the web with the capabilities of native apps. They can be installed on a user’s device, work offline, load fast, and feel like a native mobile/desktop app — all using standard web technologies.

In web development, especially when working with **JavaScript and React**, PWAs offer an easy way to enhance user experience without building a separate native app.

---

## What Makes a PWA?

A web app becomes a PWA when it includes:

1. **HTTPS** – secure environment
2. **Web App Manifest** – describes how your app behaves when installed
3. **Service Worker** – enables offline mode, caching, and push notifications

---

## Example: Creating a PWA in React

### 1. Create a React Project

If you don’t have one already:

```bash
npx create-react-app my-pwa-app
cd my-pwa-app
```

>  *For PWAs, Create React App already includes basic service worker support.*

---

##  2. Enable the Service Worker

Open:

```
src/index.js
```

Find this line:

```js
serviceWorkerRegistration.unregister();
```

Change it to:

```js
serviceWorkerRegistration.register();
```

This activates offline caching and other PWA features.

---

##  3. Update the Web App Manifest

Open:

```
public/manifest.json
```

Make sure you have these fields:

```json
{
  "short_name": "MyPWA",
  "name": "My Progressive Web App Example",
  "icons": [
    {
      "src": "icons/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "icons/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ],
  "start_url": ".",
  "display": "standalone",
  "theme_color": "#ffffff",
  "background_color": "#ffffff"
}
```

---

## 4. Build Your PWA

```bash
npm run build
```

Then serve the production build (must be HTTPS or localhost):

```bash
npm install -g serve
serve -s build
```

Now open:

```
https://localhost:3000
```

You should see an **“Install App”** icon in your browser.

---

##  Installing the PWA on Your Device

### On Chrome (Desktop)

1. Open your app in Chrome
2. Click the **Install** icon in the address bar
3. Confirm installation
4. The app opens like a native desktop app

### On Mobile (Android)

1. Visit your site
2. Tap **Add to Home Screen**
3. Confirm installation
4. The app appears on your home screen

---

##  Conclusion

Building a PWA with **JavaScript and React** is straightforward thanks to the built-in service worker setup in Create React App. With just a few adjustments, your website gains offline support, faster loading, and the ability to be installed — bringing a native-like experience to your users.

##  Sources

[Create React App](https://create-react-app.dev/docs/making-a-progressive-web-app/)

[Developer Mozilla](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps)
