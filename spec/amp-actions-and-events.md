<!---
Copyright 2016 The AMP HTML Authors. All Rights Reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS-IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

# Actions and Events in AMP

The `on` attribute is used to install event handlers on elements. The events that are supported depend on the element.

The value for the syntax is a simple domain specific language of the form:

```javascript
eventName:targetId[.methodName[(arg1=value, arg2=value)]]
```

Here's what each part of this means:
**eventName**
__required__
This is the name of the event that an element exposes.

**targetId**
__required__
This is the DOM id for the element, or a predefined [special target](#special-targets) you'd like to execute an action on in response to the event. In the following example, the `targetId` is the DOM id of the `amp-lightbox` target, `photo-slides`.

```html
<amp-lightbox id="photo-slides"></amp-lightbox>
<button on="tap:photo-slides">Show Images</button>
```

**methodName**
__optional__ for elements with default actions
This is the method that the target element (referenced by `targetId`) exposes and you'd like to execute when the event is triggered.

AMP has a concept of a default action that elements can implement. So when omitting the `methodName` AMP will execute that default method.

**arg=value**
__optional__
Some actions, if documented, may accept arguments. The arguments are defined between parentheses in `key=value` notation. The accepted values are:
 - simple unquoted strings: `simple-value`
 - quoted strings: `"string value"` or `'string value'`
 - boolean values: `true` or `false`
 - numbers: `11` or `1.1`
 - dot-syntax reference to event data: `event.someDataVariableName`

## Handling Multiple Events
You can listen to multiple events on an element by separating the two events with a semicolon `;`.

Example: `on="submit-success:lightbox1;submit-error:lightbox2"`

## Multiple Actions For One Event
You can execute multiple actions in sequence for the same event by separating the two actions with a comma ','.

Example: `on="tap:target1.actionA,target2.actionB"`

## Globally defined Events and Actions
Currently AMP defines `tap` event globally that you can listen to on any HTML element (including amp-elements).

AMP also defines `hide`, `show` and `toggleVisibility` actions globally that you can trigger on any HTML element.

{% call callout('Note', type='note') %}Note: {% endcall %} An element can only be shown if it was previously hidden by a `hide` or `toggleVisibility` action, or by using the [`hidden`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/hidden) attribute. `show` does not support elements hidden by CSS `display:none` or AMP's `layout=nodisplay`

For example, the following is possible in AMP.

```html
<div id="warning-message">Warning...</div>

<button on="tap:warning-message.hide">Cool, thanks!</button>
```

## Trust

Each event has a "trust" level that corresponds to the user's intentionality
behind that event. Each action has a "required trust" which is the minimum trust
level of an event to trigger the action.

For example, a medium-trust event  (e.g. `slideChange`) cannot trigger a
high-required-trust action (e.g. `navigateTo`).

See the tables below for the trust levels of each event and required trust for
each action.

## Element Specific Events

### * - all elements
<table>
  <tr>
    <th>Event</th>
    <th>Description</th>
    <th>Trust</th>
  </tr>
  <tr>
    <td>tap</td>
    <td>Fired when the element is clicked/tapped.</td>
    <td>High</td>
  </tr>
</table>

### Input Elements
<table>
  <tr>
    <th>Event</th>
    <th>Description</th>
    <th>Elements</th>
    <th>Data</th>
    <th>Trust</th>
  </tr>
  <!-- change -->
  <tr>
    <td rowspan=4><code>change</code></td>
    <td rowspan=4>Fired when the value of the element is changed and committed.</td>
    <td>input[type="range"]</td>
    <td>
      <code>event.min</code> : The minimum value of the range<br>
      <code>event.value</code> : The current value of the range<br>
      <code>event.max</code> : The maximum value of the range<br>
    </td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>input[type="radio"], input[type="checkbox"]</td>
    <td>
      <code>event.checked</code> : If the element is checked
    </td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>input[type="text"]</td>
    <td>
      <code>event.value</code> : String of the text or selected option
    </td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>select</td>
    <td>
      <code>event.value</code> : String of the text or selected option
    </td>
    <td>High</td>
  </tr>
  <!-- input-debounced -->
  <tr>
    <td><code>input-debounced</code></td>
    <td>Fired when the value of the element is changed. This is similar to the standard <code>input</code> event, but it only fires when 300ms have passed after the value of the input has stopped changing.</td>
    <td>Elements that fire <code>input</code> event.</td>
    <td>Same as above.</td>
    <td>Medium</td>
  </tr>
</table>

### amp-carousel[type="slides"]
<table>
  <tr>
    <th>Event</th>
    <th>Description</th>
    <th>Data</th>
    <th>Trust</th>
  </tr>
  <tr>
    <td>slideChange</td>
    <td>Fired when the user manually changes the carousel's current slide. Does not fire on autoplay or the <code>goToSlide</code> action.</td>
    <td><code>event.index</code> : slide number</td>
    <td>Medium</td>
  </tr>
</table>

### amp-selector
<table>
  <tr>
    <th>Event</th>
    <th>Description</th>
    <th>Data</th>
    <th>Trust</th>
  </tr>
  <tr>
    <td>select</td>
    <td>Fired when the user manually selects an option.</td>
    <td><code>event.targetOption</code> : The <code>option</code> attribute value of the selected element</td>
    <td>High</td>
  </tr>
</table>

### form
<table>
  <tr>
    <th>Event</th>
    <th>Description</th>
    <th>Data</th>
    <th>Trust</th>
  </tr>
  <tr>
    <td>submit</td>
    <td>Fired when the form is submitted.</td>
    <td></td>
    <td>High</td>
  </tr>
  <tr>
    <td>submit-success</td>
    <td>Fired when the form submission response is success.</td>
    <td><code>event.response</code> : JSON response</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>submit-error</td>
    <td>Fired when the form submission response is an error.</td>
    <td><code>event.response</code> : JSON response</td>
    <td>Medium</td>
  </tr>
</table>

## Element Specific Actions

### * (all elements)
<table>
  <tr>
    <th>Action</th>
    <th>Description</th>
    <th>Required Trust</th>
  </tr>
  <tr>
    <td>hide</td>
    <td>Hides the target element.</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>show</td>
    <td>Shows the target element.</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>toggleVisibility</td>
    <td>Toggles the visibility of the target element.</td>
    <td>Medium</td>
  </tr>
</table>

### amp-carousel[type="slides"]
<table>
  <tr>
    <th>Action</th>
    <th>Description</th>
    <th>Required Trust</th>
  </tr>
  <tr>
    <td>goToSlide(index=INTEGER)</td>
    <td>Advances the carousel to a specified slide index.</td>
    <td>Low</td>
  </tr>
</table>

### amp-image-lightbox
<table>
  <tr>
    <th>Action</th>
    <th>Description</th>
    <th>Required Trust</th>
  </tr>
  <tr>
    <td>open (default)</td>
    <td>Opens the image lightbox with the source image being the one that triggered the action.</td>
    <td>Medium</td>
  </tr>
</table>

### amp-lightbox
<table>
  <tr>
    <th>Action</th>
    <th>Description</th>
    <th>Required Trust</th>
  </tr>
  <tr>
    <td>open (default)</td>
    <td>Opens the lightbox.</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>close</td>
    <td>Closes the lightbox.</td>
    <td>Medium</td>
  </tr>
</table>

### amp-live-list
<table>
  <tr>
    <th>Action</th>
    <th>Description</th>
    <th>Required Trust</th>
  </tr>
  <tr>
    <td>update (default)</td>
    <td>Updates the DOM items to show updated content.</td>
    <td>Low</td>
  </tr>
</table>

### amp-sidebar
<table>
  <tr>
    <th>Action</th>
    <th>Description</th>
    <th>Required Trust</th>
  </tr>
  <tr>
    <td>open (default)</td>
    <td>Opens the sidebar.</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>close</td>
    <td>Closes the sidebar.</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>toggle</td>
    <td>Toggles the state of the sidebar.</td>
    <td>Medium</td>
  </tr>
</table>

### amp-user-notification
<table>
  <tr>
    <th>Action</th>
    <th>Description</th>
    <th>Required Trust</th>
  </tr>
  <tr>
    <td>dismiss (default)</td>
    <td>Hides the referenced user notification element.</td>
    <td>Medium</td>
  </tr>
</table>

### amp-video, amp-youtube
<table>
  <tr>
    <th>Action</th>
    <th>Description</th>
    <th>Required Trust</th>
  </tr>
  <tr>
    <td>play</td>
    <td>Plays the video.</td>
    <td>High</td>
  </tr>
  <tr>
    <td>pause</td>
    <td>Pauses the video.</td>
    <td>Low</td>
  </tr>
  <tr>
    <td>mute</td>
    <td>Mutes the video.</td>
    <td>Low</td>
  </tr>
  <tr>
    <td>unmute</td>
    <td>Unmutes the video.</td>
    <td>High</td>
  </tr>
</table>

### form
<table>
  <tr>
    <th>Action</th>
    <th>Description</th>
    <th>Required Trust</th>
  </tr>
  <tr>
    <td>submit</td>
    <td>Submits the form.</td>
    <td>High</td>
  </tr>
</table>

## Special targets

The following are targets provided by the AMP system that have special requirements:

### `AMP`

The `AMP` target is provided by the AMP runtime and implements top-level
actions that apply to the whole document.

<table>
  <tr>
    <th>Action</th>
    <th>Description</th>
    <th>Required Trust</th>
  </tr>
  <tr>
    <td>navigateTo(url=STRING)</td>
    <td>Navigates current window to given URL. Supports <a href="./amp-var-substitutions.md">standard URL subsitutions</a>. Can only be invoked via <code>tap</code> or <code>change</code> events.</td>
    <td>High</td>
  </tr>
  <tr>
    <td>goBack</td>
    <td>Navigates back in history.</td>
    <td>High</td>
  </tr>
  <tr>
    <td>setState</td>
    <td>Updates <code>amp-bind</code>'s state. See <a href="../extensions/amp-bind/amp-bind.md#ampsetstate">details</a>.</td>
    <td>Medium</td>
  </tr>
</table>

### `amp-access`

The `amp-access` target is provided by the [AMP Access extension](../extensions/amp-access/amp-access.md).

It's special because
1. You can't give an arbitrary ID to this target. The target is always `amp-access`.
2. The actions for `amp-access` are dynamic depending on the structure of the [AMP Access Configruation](../extensions/amp-access/amp-access.md#configuration).

See [details](../extensions/amp-access/amp-access.md#login-link) about using the `amp-access` target.
