---
title: "Feature Flags and Analytics"
chapter: false
weight: 5
--- 

## Forwarding Flag Data to Analytics Platform
This lab will use CloudBees Feature Flags's `impressionHandler` to forward flag information to a more robust analytics platform (we will use Google Analytics for this lab, but the abstracted process remains the same for integration with other tools). We can pass information about feature flags values evaluated on each client back for analysis. We'll set-up the beginning process for A/B testing process.

**NOTE: If you going through these workshops on your own, feel free to create your own Google Analytics dashboard. Use your own UA property tag when necessary.**


### Adding the ImpressionHandler to Code

1. Switch tabs to bring up the `microblog-frontend` repository. Within the root directory, on the `development` branch, navigate to the public folder. Then select the `index.html` file.
<<<<<<< HEAD
2. Google Analytics requires a site tag. Select the pencil icon to edit the `index.html` file, and remove the comments on **Line 5** and **Line 10** so that the `gtag.js` can be seen. If using your own dashboard replace your `UA` property ID where appropriate.

3. Review the edits below in
<details><summary>Updated <code>index.html</code></summary>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <!-- Global site tag (gtag.js) - Google Analytics -->
    <script async src="https://www.googletagmanager.com/gtag/js?id=UA-165275127-1"></script>
    <script>
        window.dataLayer = window.dataLayer || [];
        function gtag(){dataLayer.push(arguments);}
        gtag('js', new Date());
        gtag('config', 'UA-165275127-1');
    </script>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title>microblog</title>
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.2.0/css/all.css">
</head>
<body>
<noscript>
    <strong>We're sorry but microblog-frontend doesn't work properly without JavaScript enabled. Please enable it to
        continue.</strong>
</noscript>
<div id="app"></div>
<!-- built files will be auto injected -->
</body>
</html>

```
</details>
=======
2. Google Analytics requires a site tag. Select the pencil icon to edit the `index.html` file, and remove the comments on **Line 5** and **Line 13** so that the `gtag.js` can be seen. If using your own dashboard replace your `UA` property ID where appropriate.
3. Review the edits below:
   <details><summary>Updated <code>index.html</code></summary>
   
   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <!-- Global site tag (gtag.js) - Google Analytics -->
       <script async src="https://www.googletagmanager.com/gtag/js?id=UA-165275127-1"></script>
       <script>
           window.dataLayer = window.dataLayer || [];
           function gtag(){dataLayer.push(arguments);}
           gtag('js', new Date());
           gtag('config', 'UA-165275127-1');
       </script>
       <meta charset="utf-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width,initial-scale=1.0">
       <link rel="icon" href="<%= BASE_URL %>favicon.ico">
       <title>microblog</title>
       <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.2.0/css/all.css">
   </head>
   <body>
   <noscript>
       <strong>We're sorry but microblog-frontend doesn't work properly without JavaScript enabled. Please enable it to
           continue.</strong>
   </noscript>
   <div id="app"></div>
   <!-- built files will be auto injected -->
   </body>
   </html>
   ```
   </details>
>>>>>>> 47983e668a8056471a3c6a41e14efcabb3a581fe

4. Create a commit message (e.g. "insert gtag.js"). Then **Commit changes** directly to `development` branch.
5. From the microblog's root directory on the `development` branch, navigate to the `flags.js` file (`src/utils/flags.js`).
6. We want to send data to Google Analytics, but may only want to send _some_ of the flag data, like only if the flag data is used in our A/B **title** experiment. Click the pencil to edit the file. On **Line 19** insert a new line then implement `impresionHandler` constant seen below:
```javascript
export const impressionHandler = (reporting, experiment) => {
  if (experiment.name === 'title') {
    console.log('Title flag, ' + reporting.name + ' ,value is ' + reporting.value)
    window.gtag('event', experiment.name, {
      'event_category': reporting.name,
      'event_label': reporting.value
    })
  } else {
    console.log('Not in title experiment. Flag ' + reporting.name + '. default value ' + reporting.value + ' was used')
  }
}
```

7. Within the `options` constant, include a call to the newly defined `impressionHandler`. After the configurationFetchedHandler call, insert a comma then and make the `impressionHandler` part of the options as seen below:
```javascript
const options = {
  configurationFetchedHandler: configurationFetchedHandler,
  impressionHandler: impressionHandler
}
```

8. Review the edits below in
<details><summary>Final <code>flags.js</code>:</summary>

```javascript
import Rox from 'rox-browser'
import store from '../store'
import { betaAccess } from './users'

export const Flags = {
  sidebar: new Rox.Flag(false),
  title: new Rox.Flag(false)
}

export const configurationFetchedHandler = fetcherResults => {
  console.log('The configuration status is: ' + fetcherResults.fetcherStatus)
  if (fetcherResults.hasChanges && fetcherResults.fetcherStatus === 'APPLIED_FROM_NETWORK') {
    window.location.reload(false)
  } else if (fetcherResults.fetcherStatus === 'ERROR_FETCH_FAILED') {
    console.log('Error occured! Details are: ' + fetcherResults.errorDetails)
  }
}

export const impressionHandler = (reporting, experiment) => {
  if (experiment.name === 'title) {
    console.log('Title flag, ' + reporting.name + ' ,value is ' + reporting.value)
    window.gtag('event', experiment.name, {
      'event_category': reporting.name,
      'event_label': reporting.value
    })
  } else {
    console.log('Not in title experiment. Flag ' + reporting.name + '. default value ' + reporting.value + ' was used')
  }
}
<<<<<<< HEAD

async function initRollout () {
  const options = {
    configurationFetchedHandler: configurationFetchedHandler,
    impressionHandler: impressionHandler     
  }
  Rox.setCustomBooleanProperty('isLoggedIn', store.getters.isLoggedIn)
  Rox.setCustomBooleanProperty('hasBetaAccess', betaAccess())
  Rox.register('default', Flags)
  await Rox.setup(process.env.VUE_APP_ROLLOUT_KEY, options)
}

initRollout().then(function () {
  console.log('Done loading Rollout')
})
=======

async function initRollout () {
  const options = {
    configurationFetchedHandler: configurationFetchedHandler,
    impressionHandler: impressionHandler
  }
  Rox.setCustomBooleanProperty('isLoggedIn', store.getters.isLoggedIn)
  Rox.setCustomBooleanProperty('hasBetaAccess', betaAccess())
  Rox.register('default', Flags)
  await Rox.setup(process.env.VUE_APP_ROLLOUT_KEY, options)
}
>>>>>>> 47983e668a8056471a3c6a41e14efcabb3a581fe

initRollout().then(function () {
  console.log('Done loading Rollout')
})
```
</details>

9. Include a commit message (e.g. "Add impressionHandler"), before committing the changes directly to `development` branch.

### Split Based Test for Title Experiment

1. Navigate to the CloudBees Feature Flags dashboard, and bring up the **title** experiment in the **Development** environment.
2. The premise of this A/B test will be to route 50% of all users to a **True** value and the other 50% to a **False** value. This can be accomplished by changing the **set to** value from **True** to **Split** within the drop down menu. The default split experiment should reflect these weightings to each value.
3. **Update Audience** to apply the changes made in this experiment. ![Split](images/split.png?width=50pc)

### Google Analytics Dashboard

* Open your Google Analytics dashboard, from the **Realtime Reports** panel, select **Events**.

### Lab 6 Completed!
You have successfully completed the introductory CloudBees Feature Flags workshop!
