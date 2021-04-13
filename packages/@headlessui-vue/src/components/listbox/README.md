## Listbox (Select)

[View live demo on CodeSandbox](https://codesandbox.io/s/headlessuivue-listbox-example-forked-070j0?file=/src/App.vue)

The `Listbox` component and related child components are used to quickly build custom listbox components that are fully accessible out of the box, including correct ARIA attribute management and robust keyboard navigation support.

- [Installation](#installation)
- [Basic example](#basic-example)
- [Styling the active and selected option](#styling-the-active-and-selected-option)
- [Showing/hiding the listbox](#showinghiding-the-listbox)
- [Using a custom label](#using-a-custom-label)
- [Disabling an option](#disabling-an-option)
- [Transitions](#transitions)
- [Rendering additional content](#rendering-additional-content)
- [Rendering a different element for a component](#rendering-a-different-element-for-a-component)
- [Component API](#component-api)

## Installation

Please note that **this library only supports Vue 3**.

```sh
# npm
npm install @headlessui/vue

# Yarn
yarn add @headlessui/vue
```

### Basic example

Listboxes are built using the `Listbox`, `ListboxButton`, `ListboxOptions`, `ListboxOption` and `ListboxLabel` components.

The `ListboxButton` will automatically open/close the `ListboxOptions` when clicked, and when the menu is open, the list of items receives focus and is automatically navigable via the keyboard.

```vue
<template>
  <Listbox v-model="selectedPerson">
    <ListboxButton>{{ selectedPerson.name }}</ListboxButton>
    <ListboxOptions>
      <ListboxOption
        v-for="person in people"
        :key="person.id"
        :value="person"
        :disabled="person.unavailable"
      >
        {{ person.name }}
      </ListboxOption>
    </ListboxOptions>
  </Listbox>
</template>

<script>
import { ref } from 'vue'
import {
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'

export default {
  components: {
    Listbox,
    ListboxLabel,
    ListboxButton,
    ListboxOptions,
    ListboxOption,
  },
  setup() {
    const people = [
      { id: 1, name: 'Durward Reynolds', unavailable: false },
      { id: 2, name: 'Kenton Towne', unavailable: false },
      { id: 3, name: 'Therese Wunsch', unavailable: false },
      { id: 4, name: 'Benedict Kessler', unavailable: true },
      { id: 5, name: 'Katelyn Rohan', unavailable: false },
    ]
    const selectedPerson = ref(people[0])

    return {
      people,
      selectedPerson,
    }
  },
}
</script>
```

### Styling the active and selected option

This is a headless component so there are no styles included by default. Instead, the components expose useful information via [scoped slots](https://v3.vuejs.org/guide/component-slots.html#scoped-slots) that you can use to apply the styles you'd like to apply yourself.

To style the active `ListboxOption` you can read the `active` slot prop, which tells you whether or not that listbox option is the option that is currently focused via the mouse or keyboard.

To style the selected `ListboxOption` you can read the `selected` slot prop, which tells you whether or not that listbox option is the option that is currently the `value` passed to the `Listbox`.

You can use this state to conditionally apply whatever active/focus styles you like, for instance a blue background like is typical in most operating systems. For the selected state, you might conditionally render a checkmark next to the seleted option.

```vue
<template>
  <Listbox v-model="selectedPerson">
    <ListboxButton>{{ selectedPerson.name }}</ListboxButton>
    <ListboxOptions>
      <!-- Use the `active` state to conditionally style the active option. -->
      <!-- Use the `selected` state to conditionally style the selected option. -->
      <ListboxOption
        as="template"
        v-slot="{ active, selected }"
        v-for="person in people"
        :key="person.id"
        :value="person"
      >
        <li :class="{ 'bg-blue-500 text-white': active, 'bg-white text-black': !active }">
          <CheckmarkIcon v-show="selected" />
          {{ person.name }}
        </li>
      </ListboxOption>
    </ListboxOptions>
  </Listbox>
</template>

<script>
import { ref } from 'vue'
import {
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'
import CheckmarkIcon from './CheckmarkIcon'

export default {
  components: {
    Listbox,
    ListboxLabel,
    ListboxButton,
    ListboxOptions,
    ListboxOption,
    CheckmarkIcon,
  },
  setup() {
    const people = [
      { id: 1, name: 'Durward Reynolds' },
      { id: 2, name: 'Kenton Towne' },
      { id: 3, name: 'Therese Wunsch' },
      { id: 4, name: 'Benedict Kessler' },
      { id: 5, name: 'Katelyn Rohan' },
    ]
    const selectedPerson = ref(people[0])

    return {
      people,
      selectedPerson,
    }
  },
}
</script>
```

### Using a custom label

By default the `Listbox` will use the button contents as the label for screenreaders. However you can also render a custom `ListboxLabel` which will be automatically linked to the listbox with a generated ID.

```vue
<template>
  <Listbox v-model="country">
    <ListboxLabel>Country:</ListboxLabel>
    <ListboxButton>{country}</ListboxButton>
    <ListboxOptions>
      <ListboxOption value="australia">Australia</ListboxOption>
      <ListboxOption value="belgium">Belgium</ListboxOption>
      <ListboxOption value="canada">Canada</ListboxOption>
      <ListboxOption value="england">England</ListboxOption>
      <!-- ... -->
    </ListboxOptions>
  </Listbox>
</template>

<script>
import { ref } from 'vue'
import {
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'

export default {
  components: {
    Listbox,
    ListboxLabel,
    ListboxButton,
    ListboxOptions,
    ListboxOption,
  },
  setup() {
    const country = ref('belgium')

    return {
      country,
    }
  },
}
</script>
```

### Showing/hiding the listbox

By default, your `ListboxOptions` instance will be shown/hidden automatically based on the internal `open` state tracked within the `Listbox` component itself.

```vue
<template>
  <Listbox v-model="option">
    <ListboxButton>{{ person.name }}</ListboxButton>

    <!-- By default, this will automatically show/hide when the ListboxButton is pressed. -->
    <ListboxOptions>
      <ListboxOption v-for="person in people" :key="person.id" :value="person">
        {{ person.name }}
      </ListboxOption>
    </ListboxOptions>
  </Listbox>
</template>

<script>
import { ref } from 'vue'
import {
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'

export default {
  components: {
    Listbox,
    ListboxLabel,
    ListboxButton,
    ListboxOptions,
    ListboxOption,
  },
  setup() {
    const people = [
      { id: 1, name: 'Durward Reynolds' },
      { id: 2, name: 'Kenton Towne' },
      { id: 3, name: 'Therese Wunsch' },
      { id: 4, name: 'Benedict Kessler' },
      { id: 5, name: 'Katelyn Rohan' },
    ]
    const selectedPerson = ref(people[0])

    return {
      people,
      selectedPerson,
    }
  },
}
</script>
```

If you'd rather handle this yourself (perhaps because you need to add an extra wrapper element for one reason or another), you can add a `static` prop to the `ListboxOptions` instance to tell it to always render, and inspect the `open` slot prop provided by the `Listbox` to control which element is shown/hidden yourself.

```vue
<template>
  <Listbox v-model="option" v-slot="{ open }">
    <ListboxButton>{{ person.name }}</ListboxButton>

    <div v-show="open">
      <!-- Using `static`, `ListboxOptions` is always rendered and ignores the `open` state. -->
      <ListboxOptions static>
        <ListboxOption v-for="person in people" :key="person.id" :value="person">
          {{ person.name }}
        </ListboxOption>
      </ListboxOptions>
    </div>
  </Listbox>
</template>

<script>
import { ref } from 'vue'
import {
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'

export default {
  components: {
    Listbox,
    ListboxLabel,
    ListboxButton,
    ListboxOptions,
    ListboxOption,
  },
  setup() {
    const people = [
      { id: 1, name: 'Durward Reynolds' },
      { id: 2, name: 'Kenton Towne' },
      { id: 3, name: 'Therese Wunsch' },
      { id: 4, name: 'Benedict Kessler' },
      { id: 5, name: 'Katelyn Rohan' },
    ]
    const selectedPerson = ref(people[0])

    return {
      people,
      selectedPerson,
    }
  },
}
</script>
```

### Disabling an option

Use the `disabled` prop to disable a `ListboxOption`. This will make it unselectable via keyboard navigation, and it will be skipped when pressing the up/down arrows.

```vue
<template>
  <Listbox v-model="selectedPerson">
    <ListboxButton>{{ selectedPerson.name }}</ListboxButton>
    <ListboxOptions>
      <!-- Disabled options will be skipped by keyboard navigation. -->
      <ListboxOption
        v-for="person in people"
        :key="person.id"
        :value="person"
        :disabled="person.unavailable"
      >
        <span :class="{ 'opacity-75': person.unavailable }">{{ person.name }}</span>
      </ListboxOption>
    </ListboxOptions>
  </Listbox>
</template>

<script>
import { ref } from 'vue'
import {
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'

export default {
  components: {
    Listbox,
    ListboxLabel,
    ListboxButton,
    ListboxOptions,
    ListboxOption,
  },
  setup() {
    const people = [
      { id: 1, name: 'Durward Reynolds', unavailable: false },
      { id: 2, name: 'Kenton Towne', unavailable: false },
      { id: 3, name: 'Therese Wunsch', unavailable: false },
      { id: 4, name: 'Benedict Kessler', unavailable: true },
      { id: 5, name: 'Katelyn Rohan', unavailable: false },
    ]
    const selectedPerson = ref(people[0])

    return {
      people,
      selectedPerson,
    }
  },
}
</script>
```

### Transitions

To animate the opening/closing of the menu panel, use Vue's built-in `transition` component. All you need to do is wrap your `ListboxOptions` instance in a `<transition>` element and the transition will be applied automatically.

```vue
<template>
  <Listbox v-model="selectedPerson">
    <ListboxButton>{{ selectedPerson.name }}</ListboxButton>

    <transition
      enter-active-class="transition duration-100 ease-out"
      enter-from-class="transform scale-95 opacity-0"
      enter-to-class="transform scale-100 opacity-100"
      leave-active-class="transition duration-75 ease-out"
      leave-from-class="transform scale-100 opacity-100"
      leave-to-class="transform scale-95 opacity-0"
    >
      <ListboxOptions>
        <ListboxOption
          v-for="person in people"
          :key="person.id"
          :value="person"
          :disabled="person.unavailable"
        >
          {{ person.name }}
        </ListboxOption>
      </ListboxOptions>
    </transition>
  </Listbox>
</template>

<script>
import { ref } from 'vue'
import {
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'

export default {
  components: {
    Listbox,
    ListboxLabel,
    ListboxButton,
    ListboxOptions,
    ListboxOption,
  },
  setup() {
    const people = [
      { id: 1, name: 'Durward Reynolds', unavailable: false },
      { id: 2, name: 'Kenton Towne', unavailable: false },
      { id: 3, name: 'Therese Wunsch', unavailable: false },
      { id: 4, name: 'Benedict Kessler', unavailable: true },
      { id: 5, name: 'Katelyn Rohan', unavailable: false },
    ]
    const selectedPerson = ref(people[0])

    return {
      people,
      selectedPerson,
    }
  },
}
</script>
```

### Rendering a different element for a component

By default, the `Listbox` and its subcomponents each render a default element that is sensible for that component.

For example, `ListboxLabel` renders a `label` by default, `ListboxButton` renders a `button` by default, `ListboxOptions` renders a `ul` and `ListboxOption` renders a `li` by default. `Listbox` interestingly _does not render an extra element_, and instead renders its children directly by default.

This is easy to change using the `as` prop, which exists on every component.

```vue
<template>
  <Listbox as="div" v-model="selectedPerson">
    <ListboxButton>{{ selectedPerson.name }}</ListboxButton>
    <ListboxOptions as="div">
      <ListboxOption as="span" v-for="person in people" :key="person.id" :value="person">
        {{ person.name }}
      </ListboxOption>
    </ListboxOptions>
  </Listbox>
</template>

<script>
import { ref } from 'vue'
import {
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'

export default {
  components: {
    Listbox,
    ListboxLabel,
    ListboxButton,
    ListboxOptions,
    ListboxOption,
  },
  setup() {
    const people = [
      { id: 1, name: 'Durward Reynolds' },
      { id: 2, name: 'Kenton Towne' },
      { id: 3, name: 'Therese Wunsch' },
      { id: 4, name: 'Benedict Kessler' },
      { id: 5, name: 'Katelyn Rohan' },
    ]
    const selectedPerson = ref(people[0])

    return {
      people,
      selectedPerson,
    }
  },
}
</script>
```

To tell an element to render its children directly with no wrapper element, use `as="template"`.

```vue
<template>
  <Listbox v-model="selectedPerson">
    <!-- Render no wrapper, instead pass in a button manually. -->
    <ListboxButton as="template">
      <button>{{ selectedPerson.name }}</button>
    </ListboxButton>
    <ListboxOptions>
      <ListboxOption v-for="person in people" :key="person.id" :value="person">
        {{ person.name }}
      </ListboxOption>
    </ListboxOptions>
  </Listbox>
</template>

<script>
import { ref } from 'vue'
import {
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'

export default {
  components: {
    Listbox,
    ListboxLabel,
    ListboxButton,
    ListboxOptions,
    ListboxOption,
  },
  setup() {
    const people = [
      { id: 1, name: 'Durward Reynolds' },
      { id: 2, name: 'Kenton Towne' },
      { id: 3, name: 'Therese Wunsch' },
      { id: 4, name: 'Benedict Kessler' },
      { id: 5, name: 'Katelyn Rohan' },
    ]
    const selectedPerson = ref(people[0])

    return {
      people,
      selectedPerson,
    }
  },
}
</script>
```

### Component API

#### Listbox

```vue
<template>
  <Listbox v-model="selectedPerson">
    <ListboxButton>
      {{ selectedPerson.name }}
    </ListboxButton>
    <ListboxOptions>
      <ListboxOption v-for="person in people" :key="person.id" :value="person">
        {{ person.name }}
      </ListboxOption>
    </ListboxOptions>
  </Listbox>
</template>

<script>
import { ref } from 'vue'
import {
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from '@headlessui/vue'

export default {
  components: {
    Listbox,
    ListboxLabel,
    ListboxButton,
    ListboxOptions,
    ListboxOption,
  },
  setup() {
    const people = [
      { id: 1, name: 'Durward Reynolds' },
      { id: 2, name: 'Kenton Towne' },
      { id: 3, name: 'Therese Wunsch' },
      { id: 4, name: 'Benedict Kessler' },
      { id: 5, name: 'Katelyn Rohan' },
    ]
    const selectedPerson = ref(people[0])

    return {
      people,
      selectedPerson,
    }
  },
}
</script>
```

##### Props

| Prop       | Type                | Default                           | Description                                              |
| :--------- | :------------------ | :-------------------------------- | :------------------------------------------------------- |
| `as`       | String \| Component | `template` _(no wrapper element_) | The element or component the `Listbox` should render as. |
| `v-model`  | `T`                 | -                                 | The selected value.                                      |
| `disabled` | Boolean             | `false`                           | Enable/Disable the `Listbox` component.                  |

##### Slot props

| Prop       | Type    | Description                             |
| :--------- | :------ | :-------------------------------------- |
| `open`     | Boolean | Whether or not the listbox is open.     |
| `disabled` | Boolean | Whether or not the listbox is disabled. |

#### ListboxButton

```vue
<ListboxButton v-slot="{ open }">
  <span>{{ selectedPerson.name }}</span>
  <ChevronRightIcon class={`${open ? 'transform rotate-90' : ''}`} />
</ListboxButton>
```

##### Props

| Prop | Type                | Default  | Description                                                    |
| :--- | :------------------ | :------- | :------------------------------------------------------------- |
| `as` | String \| Component | `button` | The element or component the `ListboxButton` should render as. |

##### Slot props

| Prop       | Type    | Description                             |
| :--------- | :------ | :-------------------------------------- |
| `open`     | Boolean | Whether or not the listbox is open.     |
| `disabled` | Boolean | Whether or not the listbox is disabled. |

#### ListboxLabel

```vue
<ListboxLabel>Enable notifications</ListboxLabel>
```

##### Props

| Prop | Type                | Default | Description                                                   |
| :--- | :------------------ | :------ | :------------------------------------------------------------ |
| `as` | String \| Component | `label` | The element or component the `ListboxLabel` should render as. |

##### Slot props

| Prop       | Type    | Description                             |
| :--------- | :------ | :-------------------------------------- |
| `open`     | Boolean | Whether or not the listbox is open.     |
| `disabled` | Boolean | Whether or not the listbox is disabled. |

#### ListboxOptions

```vue
<ListboxOptions>
  <ListboxOption value="option-a"><!-- ... --></ListboxOption>
  <!-- ... -->
</ListboxOptions>
```

##### Props

| Prop      | Type                | Default | Description                                                                       |
| :-------- | :------------------ | :------ | :-------------------------------------------------------------------------------- |
| `as`      | String \| Component | `ul`    | The element or component the `ListboxOptions` should render as.                   |
| `static`  | Boolean             | `false` | Whether the element should ignore the internally managed open/closed state.       |
| `unmount` | Boolean             | `true`  | Whether the element should be unmounted or hidden based on the open/closed state. |

##### Slot props

| Prop   | Type    | Description                         |
| :----- | :------ | :---------------------------------- |
| `open` | Boolean | Whether or not the listbox is open. |

#### ListboxOption

```vue
<ListboxOption value="option-a">Option A</ListboxOption>
```

##### Props

| Prop       | Type                | Default | Description                                                                             |
| :--------- | :------------------ | :------ | :-------------------------------------------------------------------------------------- |
| `as`       | String \| Component | `li`    | The element or component the `ListboxOption` should render as.                          |
| `value`    | `T`                 | -       | The option value.                                                                       |
| `disabled` | Boolean             | `false` | Whether or not the option should be disabled for keyboard navigation and ARIA purposes. |

##### Slot props

| Prop       | Type    | Description                                                                          |
| :--------- | :------ | :----------------------------------------------------------------------------------- |
| `active`   | Boolean | Whether or not the option is the active/focused option in the list.                  |
| `selected` | Boolean | Whether or not the option is the selected option in the list.                        |
| `disabled` | Boolean | Whether or not the option is the disabled for keyboard navigation and ARIA purposes. |