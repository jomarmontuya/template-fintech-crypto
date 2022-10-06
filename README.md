# <img src="https://user-images.githubusercontent.com/729972/155242158-157ca88c-9047-4671-bd09-2bbef7035022.png" width="25" style="margin-bottom:-3px"> Zesty.io + Next.js

> Quick start [Next.js](https://nextjs.org/) v12 with [Zesty.io]() as a data source

## Getting Started

0. Requirements

- [Node.js 16](https://nodejs.org/en/)
- [npm](https://www.npmjs.com/)

1. Install

Add Zesty CLI (you may need to sudo this command)
```Bash
npm install -g @zesty-io/cli
```

Run the Next.js starter
```Bash
npx create-next-app --example https://github.com/zesty-io/nextjs-starter
```

*The install process will let you use an existing account or create a new. When using an existing account you will be prompted to select from your available instances.*

2. Change directory to your project

```Bash
# Use the name you chose for your directory
cd my-app
```

3. Start development server

```Bash
npm run dev
```

4. Open application

open browser to http://localhost:3000/

## Syncing Zesty.io Models

As you develop your Zesty.io instance you will commonly add new content models. In order for new content pages to render in nextjs, there needs to be a relative model component in `views/zesty` to get the lastest models components you can run a script to sync. Do so by running the following command at the root of your project.

```
npm run sync
```

This sync script will create new files where needed, but will not overwrite existing files.

## Working with Zesty View Components

After a `npm run sync` a view component is created for each Zesty Content Model in the `views/zesty` directory. Zesty Content Items that have URL will automatically resolve to the component in that `views` directory that is assocaited with the content models name.

Each Component loads with a {content} object, this object is a direct feed of that URLs ?toJSON response.  [Read about toJSON](https://zesty.org/services/web-engine/introduction-to-parsley/parsley-index#tojson)

![Diagram showing toJSON data fetching](https://jvsr216n.media.zestyio.com/nextjs-external-delivery-architecture.jpg)


# Custom Integration and the next.config.js file

Here is an explanation of the next.js zesty integration, use this information to setup a custom integration or to modify this starter in your own project. 

**Required files:**

These files should only be modified for customize integrations.

* `pages/[...slug].js` - this is a dynamic catch all routes file, hard written paths and files will superceed it. This file will look for content in zesty relative to the requested path `/about/` for example looks for content in zesty that matches the `/about/` path, if it fails to find content it will 404. Instead of 404ing, you can code this to default to your base application component. 
* `pages/index.js` - if you intent to run zesty to power you homepage, you need index.js to reference the [...slug].js file
* `lib/sync.js` this file read the next.config.js file to create new views/zesty/ components to map to the connected instance's content models it can be modified to ignore specific models. Sync will attempt to overwrite your next.config.js file if there are missing values.
* `lib/ZestyView.js` the dynamic component which autoloads the relative content model components from views/zesty based on the url path in the request.
* `lib/api.js` this file includes the dynamic fetch request to get zesty content and navigation bsed on the relative url path form the request.
* `components/ZestyHead.js` Used by ZestyView.js, an optional `<head>` component that dybamic sets up meta data for zesty content items that have pages in nextjs.

**Optional Files**

These files can be removed if there references are removed.

* `components/Header.js` starter example file, not needed.
* `components/Footer.js` starter example file, not needed.
* `components/ZestyTutorial.js` starter example file, not needed.
* `lib/zestyLink.js` an optional component which it used to make URL path lookup given a relative content ZUID. It requires `zestyURL+'/-/headless/routing.json` as the nav array, and content item ZUID e.g. `7-xyz-xyz`.  
* `layout/` this directory is used to create a generic page layouts, and can be removed or customized.


### next.config.js

In order for the integration to work, you need `trailingSlash: true` and the `env.zesty: {}` object. See the below example.

```next.config.js
// generated by lib/sync.js
module.exports = {
  trailingSlash: true,
  eslint: {
    ignoreDuringBuilds: true
  },
  env: {
      zesty: {
          instance_zuid: "", // zesty unique id of content instance
          stage: "", // e.g. https://XYZ-dev.webengine.zesty.io
          production: "", // e.g. https://www.acme.com
          stage_password: "",
          src_dir: "", // where the next project has pages, components, etc folders
          options: {
            skip_config_overwrite: false, // for setups with custom config files, after initial setup of the env.zesty object, set to true
            model_ignore_list: [
              '6-xyz-xyz',
              '6-xyz-xyz' // an array of models ZUIDs to ignore when creating component files in views/zesty
            ]
          }

      }
  }
}
```

*If trailingSlash needs to be false in your project, then the `lib/api.js` fetch call will need to modifed to append a trailing slash. In this scenario, webengine preview proxying your nextjs app will fail.*

# How to uninstall the Starting Tutorial

The starting tutorial comes with a couple packages and components

* MUI (mui.com)
* Material Icons
* views/tutorials (directory)

### Uninstall Material UI (MUI)

```bash
npm uninstall @mui/material @mui/styled-engine-sc styled-components
```

### Uninstall Material Icons

```bash
npm uninstall @mui/icons-material
```

### Delete Tutorials

From terminal change directory to project root. 

```bash
rm -Rf views/tutorials
```

Open `pages/index.js` delete the line `import Tutorial from 'views/tutorial/` and the line `<Tutorial/>`

