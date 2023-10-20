# radius-map

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur) + [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin).

## Use case

My family is looking to buy a new plot of land in the region of southern Spain, for their animal sanctuary. To get the proper licenses, it is important that there are no other houses in a certain radius. I saw them constantly struggle on Google Maps, manually using measure distance per building, etc.. There are some sites out there that support radius circles, but they struggle with plus code inputs/coordinates (very important for rural areas with no address), or aren't simple/fast enough. Then I realized that some of our family friends have struggled with the exact same issue.
So I figured I would learn some Vue and create a simple lightweight app that utilizes Google Maps API. It is deployed through Vercel and is lightning fast.

The radius can be adjusted (unit is in meters) but is 600 meters by default. You can drop a marker on the map by clicking on any location, or set a location by entering an address, a plus code, or coordinates (`lat,lng`). It works for both map and satellite views.

As it uses Google Maps API, an API key is needed (which has been excluded from this repo for obvious reasons). You can either use a developer key or your own license. You should create an `.env.local` file in root, and specify the key as such:
`VITE_APP_GOOGLE_MAPS_API_KEY=your_api_key`

Some mobile examples showing off both view modes as well as plus code support:
![Map radius tool screenshot 1](https://i.imgur.com/si5zNkP.png)
![Map radius tool screenshot 2](https://i.imgur.com/8pyS0CD.png)

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
