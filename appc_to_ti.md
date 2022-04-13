<span class="badge-buymeacoffee"><a href="https://www.buymeacoffee.com/miga" title="donate"><img src="https://img.shields.io/badge/buy%20me%20a%20coke-donate-orange.svg" alt="Buy Me A Coke donate button" /></a></span> <a href="https://github.com/sponsors/m1ga"><img src="./images/gh_sponsor.png" title="Sponsor me"/></a>

# Transfer your app from `appc` CLI to `ti` CLI

## Introduction

Axway announced that they will hand over Titanum to the community at <a href="https://devblog.axway.com/featured/product-update-changes-to-application-development-services-appcelerator/" target="_blank">March 2022</a>. <b>You still create apps or use existing apps!</b>. But you won't have access to the Axway services (e.g. Push, Analytics). You might also see a message like `You cannot perform this operation with your current plan. Please upgrade to build your application for production distribution`. It's time to update the CLI!

Here are some steps you have to have a look at or change if you want to build your apps:

## CLI

<i>If you don't use services you can do the change right away!</i>

The `appc` CLI will go away and you have to use the `ti` commands. Basically they are the same and you can create and compile your apps with both CLIs. E.g.
```bash
appc run -p android  -T device
```
will be
```bash
ti build -p android  -T device
```
You can either look at `ti --help` or use an IDE (Atom or VSCode). Both editor packages support the `ti cli` inside their settings.

## tiapp.xml

The tiapp.xml contains a `<guid>` that is registered at Appcelerator. Currently you have to generate a new GUID e.g. with https://www.guidgenerator.com/ to switch from `appc` to `ti`. Once the server/service is gone it doesn't matter. But currently you have to change that GUID.<br/>
Another entry is `<analytics>false</analytics>`. Set it false and use a different anlytics module if you'll need it.<br/>
And if you have other `<appc-...>` or `<property name="appc-..."">` nodes at the end of your xml you can remove them. Again: only if you don't use the services at the moment!

## Create a new app

If you want to create a new app you should use the open-source CLI:
```bash
ti create
```
and follow that menu. Then inside that folder run `alloy new` to convert the classic app to an Alloy app.

## Editor

Since March 2nd 2022 the editor packages will now only use the `ti` and `alloy` CLI's. If your package is still using the `appc` CLI please check for extension updates.
If you have any issues with the plug-in (e.g. update check doesn't work) you should run `sudo npm i -g titanium alloy` outside of VSCode/Atom and restart the editor.

## Modules

If you use (old) Appcelerator modules you'll have to either remove them and replace them with free ones or check `https://github.com/tidev/` if the module is open source now and replace it with that version.
