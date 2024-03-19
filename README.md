# FPS controls

> First person shooter controls to easily create 3D experiences of shooters (or other first person experiences)

- 💡 Works out of the box
- ⚡️ Powered by [Tresjs](https://tresjs.org/) VueJs + ThreeJs
- 🦾 Fully configurable

## Installation

```bash
pnpm i fpsControls
```

## Demos

<!-- - [Stackblitz Collection](https://stackblitz.com/@alvarosabu/collections/tresjs) -->

## How to use

To get started you can simply import the main component and use it.

```vue{15}
<script setup>
import { TresCanvas } from '@tresjs/core'
import { fpsControls } from 'fpsControls'
</script>

<template>
  <TresCanvas window-size>
    <TresPerspectiveCamera
      :position="[0, 0, 3]"
      :fov="45"
      :aspect="1"
      :near="0.1"
      :far="1000"
    />
    <fpsControls />
  </TresCanvas>
</template>
```

Just by that you're going to have a shooter controller. Move using WASD keys and using your mouse to point eyes.

:::info
All the examples can be found in: [Examples](https://github.com/JaimeTorrealba/fps-controls/tree/main/playground/src/pages).
:::
:::info
This package use [PointerLockControls](https://cientos.tresjs.org/guide/controls/pointer-lock-controls.html) from [cientos](https://cientos.tresjs.org/).
:::

### ControlsKey

We can provide and `controlsKeys` prop to change, add or remove some of the basic functionalities

```
  controlsKeys: {
    type: Array,
    default: () => [
      { name: 'forward', key: 'w' },
      { name: 'backward', key: 's' },
      { name: 'leftward', key: 'a' },
      { name: 'rightward', key: 'd' },

      // Optionals

      { name: 'jump', key: 'space' },
      { name: 'run', key: 'Shift', speed: moveSpeed * 2 }, // run affect headbobbing, make it faster
      { name: 'creep', key: 'ctrl', speed: moveSpeed * 0.25 }, // creep affect headbobbing, make it slower

      // Mouse actions

      { name: 'leftClick', action: () => { } },
      { name: 'rightClick', action: () => { } },
      { name: 'middleClick', action: () => { } },
      { name: 'wheelActionUp', action: () => { } },
      { name: 'wheelActionDown', action: () => { } },

      // Key actions

      {
        name: 'actions', actions: // You can have as much events as you want 🥳
                    [
                      { name: 'action', key: 'e', action: () => {} },
                      { name: 'action', key: 'q', action: () => {} },
                      { name: 'action', key: 'r', action: () => {} },
                      { name: 'action', key: 'f', action: () => {} },
                    ],
      },
    ],
  },
```

Under the hood we use [useMagicKeys](https://vueuse.org/core/useMagicKeys/#usemagickeys). Check out [all the possible keycodes](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code/code_values).

As you can see we can provide different actions, including an array of actions using the keyboard.

```
<script setup>
import { TresCanvas } from '@tresjs/core'
import { fpsControls } from 'fpsControls'

const keyboardMap = [
  { name: 'jump', key: 'Space' },
  { name: 'run', key: 'q', speed: 0.5 },
  { name: 'creep', key: 'e' },
  { name: 'leftClick', action: () => animationSword() },
  {
    name: 'actions',
    actions: [
      { name: 'action2', key: 'f', action: () => console.log('F press') },
      { name: 'action4', key: 'r', action: () => console.log('R press') },
    ],
  },
  { name: 'wheelActionUp', action: () => console.log('up') },
  { name: 'wheelActionDown', action: () => console.log('down') },
]
</script>

<template>
  <TresCanvas window-size>
    <TresPerspectiveCamera
      :position="[0, 0, 3]"
      :fov="45"
      :aspect="1"
      :near="0.1"
      :far="1000"
    />
    <fpsControls
      :controlsKeys="keyboardMap"
    />
  </TresCanvas>
</template>
```

### Other props

| Prop            | Description                                                                               | Type              | Default     |
| :-------------- | :---------------------------------------------------------------------------------------- | ----------------- | ----------- |
| **moveSpeed**   | Move speed                                                                                | Number            | `0.1`       |
| **headBobbing** | headBobbing params (active, speed, amplitude)                                             | IHeadBobbing      |             |
| **camera**      | The camera to control.                                                                    | Camera            | `undefined` |
| **domElement**  | The dom element to listen to.                                                             | HTMLCanvasElement | `undefined` |
| **selector**    | Accept an id element as string, if it is set, the new element will be used as the trigger | String            | `undefined` |

```
interface IHeadBobbing {
    active: boolean // default true
    speed: number // default 5
    amplitude: number // default 0.25
}
```

### Add Weapons (models)

Do you want to add a weapon? like a pistol, that is always follow your character, it couldn't be easier:

Just add your desire model as a slot

```
...
    <fpsControls>
      <Suspense>
        <GLTFModel
          path="/PixelArt Medieval Sword.glb"
          :scale="0.4"
          :position="[-4.5, -3, -5]" // Don't forget set the z axis
          :rotation="[0, 1, 0]"
        />
      </Suspense>
    </fpsControls>
...
```

This will make your model update their position each time you move.

:::warning
Normally you should set up the z axis, so the model will be in front of you camera.
:::

### Expose Methods

Do you still need more configurations?

We provide all the methods for you, so you can use it as you like

```
  root: PointerLockControlsRef,
  models: wrapperRef,
  moveMethods: {
    forward: () => walkSystem.moveForward(),
    backward: () => walkSystem.moveBackward(),
    leftward: () => walkSystem.moveLeftward(),
    rightward: () => walkSystem.moveRightward(),
    run: () => walkSystem.applyRun(),
    creep: () => walkSystem.applyCreep(),
    stopCreep: () => walkSystem.stopCreep(),
    stopRun: () => walkSystem.stopRun(),
    stopSideward: () => walkSystem.stopSideward(),
    stopForward: () => walkSystem.stopForward(),
  }
```

For granular control each method comes with his `stop` method.

You can access all this method using [Template ref](https://vuejs.org/guide/essentials/template-refs.html#template-refs), as you normally do with your TresJs components

### Events

We also provide some reactive events

const emit = defineEmits(['state', 'isLock', 'change'])

| Event      | Description                                                |
| :--------- | :--------------------------------------------------------- |
| **state**  | trigger when the state changes.                            |
| **isLock** | trigger if the pointer is lock.                            |
| **change** | trigger when the character makes a move (move the camera). |

Posible states:

```
  idle: 'idle',
  walking: 'walking',
  running: 'running',
  jumping: 'jumping',
  creeping: 'creeping',
```

## Mobile (in progress)

You can use the `MobileJoystick` that creates a joystick in your screen to easily move using your fingers in mobile devices. (currently only work on touch devices)

```
<script setup>
import { fpsControls, MobileJoystick } from 'fpsControls'
...
</script>

<template>
...
    <fpsControls>
      <MobileJoystick /> // Important, this component need to go inside the `fpsControls`
    </fpsControls>
...
</template>
```

### Props

| Prop                | Description                      | Type   | Default                                                                                                                                                                                           |
| :------------------ | :------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **containerStyles** | Styles of the joystick container | String | `position:absolute; bottom:35px; width:160px; height:160px; background:rgba(126, 126, 126, 0.5); border:#fff solid medium; border-radius:50%; left:20%; transform:translateX(-50%);z-index:9999;` |
| **joystickStyles**  | Joystick styles                  | String | `position: absolute; left: 50px; top: 50px; width: 60px; height: 60px; border-radius: 50%; background: #fff;`                                                                                     |
| **maxRadius**       | Max length of the joystick       | Number | `40`                                                                                                                                                                                              |

## Contributing

We are open to contributions, please read the [contributing guide](https://github.com/Tresjs/tres/blob/main/CONTRIBUTING.md).

## License

[MIT](/LICENSE)

## Sponsors

If you like this package you can support my job [here](https://github.com/sponsors/JaimeTorrealba ☺️. A github star or just some word of appreciation are increíble regarded
