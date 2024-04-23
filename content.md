# Building For Mobile 📲

## Introduction
Creating a mobile-friendly experience is essential in today’s app development because a significant portion of internet traffic comes from mobile devices. This lesson covers strategies for making Ruby on Rails applications work effectively on mobile platforms.

## 1. Mobile Responsive Design

### Overview
Responsive design ensures your web application adjusts seamlessly to fit the screen size of various devices, providing an optimal viewing experience. This is crucial for accessibility and user satisfaction.

### Implementation
To make a Rails application responsive, you start by setting the viewport in the HTML layout. This tells the browser how to control the page's dimensions and scaling.

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

### Tips for Responsive UI
- Use CSS frameworks like [Bootstrap](https://getbootstrap.com/) or [Tailwind CSS](https://tailwindcss.com/) that include responsive design by default.
- Employ [CSS media queries](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Media_queries) to alter styles based on device characteristics.
- Design mobile-first, which means designing for the smallest screen and scaling up. The majority of web traffic comes from mobile devices.

![](/assets/web-traffic.webp) [source](https://www.oberlo.com/statistics/mobile-internet-traffic)

- Test responsiveness using [DevTools](https://developer.chrome.com/docs/devtools/), [your device](https://dev.to/heratyian/sharing-your-localhost-with-other-devices-easily-4o5), or [Simulator](https://developer.apple.com/videos/play/wwdc2020/10647/) on macOS to test different devices.

![](/assets/simulator-1.png)
![](/assets/simulator-2.png)

## 2. Progressive Web Apps (PWAs)

### Overview
PWAs use modern web capabilities to deliver an app-like experience to users. They can be designed to work offline and perform fast, mimicking a native app on mobile and desktop environments.

### Key Benefits
- Native-like feel with capabilities like push notifications, offline work mode, and home screen installation.
- Broad accessibility via a standard web browser without the need for app store submission.

### Limitations
- **App Store Restrictions**: Not allowed in Apple’s iOS App Store, which might limit their visibility on iOS devices.
- **Limited access to native device features**: Cannot access certain native components of the device, such as ARKit, Face ID, or Bluetooth. 
- and [more...](https://firt.dev/notes/pwa-ios/)

Despite these limitations, PWAs on iOS have been evolving, and many of these restrictions have been addressed or improved over time. As an aside, [Steve Jobs' orginal vision for the iPhone was 100% web apps](https://9to5mac.com/2011/10/21/jobs-original-vision-for-the-iphone-no-third-party-native-apps/).

### Steps to Convert Your Rails App into a PWA
1. **Add Routes, Controller, and Views**

```ruby
# app/controllers/service_worker_controller.rb
class ServiceWorkerController < ApplicationController
  # protect manifest cross-origin request forgery 
  protect_from_forgery except: :service_worker

  # if authenticate_user! set in ApplicationController
  skip_before_action :authenticate_user!

  def service_worker; end
  def manifest; end
end
```

```ruby
# config/routes.rb  
get "/service-worker.js", to: "service_worker#service_worker"
get "/manifest.json", to: "service_worker#manifest"
```

2. **Setup the Web App Manifest**: Include a manifest file with metadata about the app (e.g., name, icons). This is usually available at `/manifest.json`.

```erb
// app/views/service_worker/manifest.json.erb
{
  "short_name": "YourAppName",
  "name": "YourAppName",
  "id": "/",
  "icons": [
    {
      "src": "<%= image_path "your-icon.png" %>",
      "sizes": "144x144",
      "type": "image/png"
    }
  ],
  "start_url": "/",
  "background_color": "#000000",
  "display": "standalone",
  "scope": "/",
  "theme_color": "#000000"
}
```

3. **Service Workers**: Implement service workers for handling offline caching and resource retrieval strategies. Think of this as a "background assistant" for your progressive web app. This should be available at `/service-worker.js`.

```js
self.addEventListener("install", event => {
  console.log("Service worker installed");
});

self.addEventListener("activate", event => {
  console.log("Service worker activated");
});

self.addEventListener('fetch', event => {
  console.log("Service worker fetching");
});
```

<aside>
You only need to setup the `service-worker.js` if you plan to add offline caching functionality. For now we just want to confirm everything is hooked up properly by adding console logs.
</aside>

4. **Add to Home Screen**: Add `<link>` to prompt users to install the app on their home screens. 

```erb
<!-- app/views/layout/application.html.erb -->

<!-- crossorigin attribute adds support for CORS  -->
<link rel="manifest" crossorigin="use-credentials" href="/manifest.json" />
```

If setup properly, you should now see an install prompt when opening your application.

![](/assets/install.png)

Refer to these guides for detailed instructions:

- [Make your Rails app work offline. Part 1: PWA setup](https://alicia-paz.medium.com/make-your-rails-app-work-offline-part-1-pwa-setup-3abff8666194)
- [Make your Rails app work offline. Part 2: caching assets and adding an offline fallback](https://alicia-paz.medium.com/make-your-rails-app-work-offline-part-2-caching-assets-and-adding-an-offline-fallback-334729ade904)
- [Make your Rails app work offline. Part 3: CRUD actions with IndexedDB and Stimulus](https://alicia-paz.medium.com/make-your-rails-app-work-offline-part-3-crud-actions-with-indexeddb-and-stimulus-ad669fe0141c)
- [https://whatpwacando.today/](https://whatpwacando.today/)
- [web.dev](https://web.dev/explore/progressive-web-apps)
- [MDN](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps)
- [PWA Builder](https://docs.pwabuilder.com/)

## 3. Hybrid Mobile Apps

### Overview
Hybrid Application frameworks are technologies used to build for multiple platforms using the same codebase. They can be distributed through app stores and take advantage of the device's native features.

### Popular Frameworks
- [Turbo Native](https://turbo.hotwired.dev/handbook/native) and [Strada](https://strada.hotwired.dev/): Designed specifically for Rails applications.
- [React Native](https://reactnative.dev/): Build mobile apps using JavaScript and React.
- [Xamarin](https://dotnet.microsoft.com/en-us/apps/xamarin): Uses C# and .NET framework for building apps for any mobile platform.
- [Flutter](https://flutter.dev/): Google's UI toolkit for crafting natively compiled applications for mobile, web, and desktop from a single codebase.
- [Electron](https://www.electronjs.org/): Build cross-platform desktop apps with JavaScript, HTML, and CSS
- and many more!

4. Full Native Approach
Going fully native means using the platform-specific programming language and tools.

- [iOS](https://developer.apple.com/): Develop with Swift or Objective-C.
- [Android](https://developer.android.com/): Develop with Kotlin or Java.

This approach allows the best performance and access to all device capabilities but requires a separate codebases for each platform.

## Conclusion
Building for mobile offers multiple pathways: responsive web design, PWAs, hybrid apps, or full native development. Each has its advantages and trade-offs, and the best choice depends on your specific project needs and audience.
