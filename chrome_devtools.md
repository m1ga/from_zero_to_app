# [From zero to app](https://github.com/m1ga/from_zero_to_app)

<span class="badge-patreon"><a href="https://www.patreon.com/michaelgangolf" title="Donate to this project using Patreon"><img src="https://img.shields.io/badge/patreon-donate-yellow.svg" alt="Patreon donate button" /></a></span>

To debug WebViews have a look at <a href="./debugging_webviews.md">Debugging WebViews</a>.<br/>
If you are interested in debugging using VS Code have a look at <a href="https://devblog.axway.com/mobile-apps/vs-code-debugging-for-titanium/">https://devblog.axway.com/mobile-apps/vs-code-debugging-for-titanium/</a>

## Debugging using Chrome DevTools

Sometimes you have an error in your app where you don't know why and it is hard to find the source. Then you end up adding a lot of `console.log()` in your code or you can use the Chrome DevTools to debug your app and even use breakpoints.

In a Titanium app you can add `--debug-host localhost:8989` to your build command to start a debug listener.

```bash
appc run -p android  -T device --debug-host localhost:8989
```

When the app starts up it will freeze at the Splashscreen and you will see an URL it the console:
<img src="images/chrome_1.png"/>

open that `chrome-devtools://...` URL inside Chrome and it will open the DevTools for you. To get your app running again you have to press the `Pause/Play` button in the top right corner.

Now you can use the DevTools to check values, set breakpoints or inspect items like you would do in a web page:

<img src="images/chrome_2.png"/>
