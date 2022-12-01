## Introduction

This document describes the Ucraft widget structure and guides the developers in the process of creating one.

>ℹ This document must be read before creating a widget.

**Widget** is an alias for an independent, unique entity that is possible to use as part of the website built with Ucraft. 

Each widget in Builder must perform the functionality of being dragged, configured, and content changed with some UI. There are two types of widgets: **simple** and **complex**. Simple widgets act themselves, whereas complex ones can contain multiple other widgets or microelements.

## How to Create a Widget?

To create a new widget, the operation is handled inside `FRONTEND/packages/ds/src/widgets` folder where all Ucraft’s widgets are located.

>ℹ All the folder names must be in PascalCase. 

### Step 1 

To create a **MyWidget** widget, the following files are required:

| `MyWidget.builder.tsx`  | In `builder.tsx` the behavior of the widget is set for the builder mode.|
| :---------------------- | :------------------------------------------------------------------------- |
| `MyWidget.config.ts`    | `Config` variable must be a type of `ConfigType`.
|                         | Properties required for widget configuration are the following:

- `hasUiElement` indicates whether the widget has a UI element.
- `defaultChildren` holds the microelements/children data of the widget.
- `style` relates to the Style tab, meaning that the style  that are shown to the user allow the widget to have such features as padding, margin, etc.
- `settings` relates to the Settings tab.
- `content` refers to the Content tab.
- `creation` refers to the options visible to the user while creating a UI Element of the widget.
- `icon` is the icon that appears next to each widget on the Left Panel of the Builder.
- `params` relates to the default parameters, such as text and size of the widget.
- `defaultVariantsStyle` refers to the widget's default styles, such as the background color.
- `defaultContent` relates to the default data structure of the widget whenever it needs to be UI elements.
>ℹ `Config` variable must be exported as default.
`export default config;`
>ℹ Prefix must be exported from the **MyWidget** widget.
`export const prefix = widgetPrefix.X;` |
 | `MyWidget.public.tsx`         |In `public.tsx` the behavior of the widget is set for the public (published) mode. |
`MyWidget.styles.ts`

The widget's constant styling is set here, and the user cannot alter it. For instance, `cursor: pointer;` meaning whenever hovered over the widget the cursor becomes a pointer.

`MyWidget.types.ts`

The custom types of the widget are set here.

`MyWidget.view.tsx`

The shared behaviors of the `builder.tsx` and the `public.tsx` must be included in the `view.tsx`, meaning that it is responsible for rendering the widget valid in both public and view modes.

`MyWidget.index.ts`

All types and components that must be exported from the widget are located in `MyWidget.index.ts`.

### Step 2

After all is set, the widget must be exported in `index.ts` inside the `packages/ds/src/widgets/index.ts` path since it is essential for the application to render the widget. 

### Step 3

The widget types must be exported in `packages/ds/src/constants.ts` under the `WIDGET_TYPES` as follows:

`MyWidget = 'myWidget'`

### Step 4

Afterward, the widget configurations must be inserted inside `widgetConfigs.ts` from `packages/ds/src/widgets/widgetConfigs.ts` path as `MyWidget: MyWidgetConfig.ts` , where **MyWidget** stands for the name of the widget.

### Step 5

The next important step is to make the widget available in the widget menu from the Left Panel of the Builder. In the `packages/builder/src/pages/VisualEditor/components/Widgets/index.tsx` folder under the categories, the `WIDGET_TYPE.MyWidget` must be inserted. For example, under the `key: 'basic',`.

### Step 6

The root element of the widget’s component must spread the result of calling `appendSystemProps` on the root element of the widget. The Root element of the widget refers to the actual element that the user can change in VisualEditor/BuilderMode.

`return <RootElement {...appendSystemProps()}>{children}</RootElement>;`

- On the widget object of the `builder.tsx` file, the mentioned below properties must be defined and exported:

	`// Widget's default props
		WidgetBuilder.defaultProps = { ... };
		WidgetBuilder.config = config;`

## How to Make the Widget Available in the Public Mode?

To make the widget available in the public mode, the corresponding component needs to be put in `packages/public/components/PageLayout.tsx` inside the `elements` variable as follows:

`[WIDGET_TYPE.MyWidget]: MyWidgetPublic,`

## Widget Instance Configuration

As soon as the widget is dropped onto the crafting area, the widget becomes an **Instance**, meaning an independent unit of a widget with particular settings.

Each Instance has unique identifiers:

`hash`

Unique for each instance of a widget (Block, Row, Column and Button (ex.), Label, Icon).

`type`

Identifies the widget type. For example: `type: "gallery"`

`parentHash`

Identifies the **Parent** of the widget. For example: 

`parentHash: "__iHhez-GTc2LO-9LZ3qmOP"`.

`children`

Includes hashes of all children (widgets) as an array inside the **Parent** widget. For example:

    `children: [
      "__GEHMO5w-RXcg9QzUj6xoa",
      "__3Lm_9m5_tve8oPhHV7CYj",
      "__J4Yzjt0l1RlcEqa4eEWdg",`

uiElementId

Shows UI element used for the widget. For example: `uiElementId: "2"`, where the number identifies the **Id** of the UI element used.

`key`

`key` is an identifier for the widget or its microelements, used instead of hash in Ui element style generator to store and generate styles separately for each widget/microelement. Use case for this unique identifier can be the complex widgets with multiple `children` of the same type. 

`variantsStyles`

`variantsStyles` contains all styles applied to a widget. `variantsStyle` is an array with multiple entries, since applied styles can differ according to a condition (like different breakpoints or css states). Each entry can contain:

`breakpointId:` the breakpoint id.

`cssState:` For example hover or normal state.

`styles:` the given styles to a widget. For example: `styles: [{ type: width, value: "80vw" }]`

>ℹ In case of setting different parameters for various breakpoints, the system generates **Entries** for each breakpoint. Find more information [here](https://ucraft.atlassian.net/wiki/spaces/Writers/pages/2642378764).

`settings`

Located under **Params**, each widget can optionally have a `settings` key which is an object containing different kinds of settings specific for the widget. For example:

**Button** widget has a `contentType` property which specifies if it is only a label or a label with an icon.

**Gallery** widget has an `imagesPerRow` property.

`props`

`props` attribute stores translatable text under **Params**. As an example, the label of the Button widget.

`show`

Enables developers to choose whether to display the widget and its children or not.

`showContentInParent`

Shows the content settings of the **Child** inside **Parent** widget.

`renderSection`

Used on **Children** to define the placement on the **Parent** widget.

`labelKey`

Labeling of the **Entity Crumb**. See as an example the Countdown widget which is using a `Container` widget, but users are seeing `Number` instead of Container in the **Entity Crumb**.

`labelKey: ‘number’`

`isMicroElement`

Prevents the user to drag and drop the widget outside the Parent widget in case of being true. This parameter is `true` for most of the `Children` widgets.

`preventDrop`

Prevents the drag and drop inside the widget. Not `true` for Container widget.

`isReadOnly`

When a widget’s `isReadOnly` is `true`, the `Content` tab of the widget settings is not visible to user anymore. An example of this is Login Form, in which user should not be able to add extra fields to login form.

`skipSelect`

If the `skipSelect` is `true`, the widget can not be selected inside the visual editor. See the Contact Details widget as an example in which the user is not able to select Form and Form Item widgets.

## How to Add Micro Elements?

To define **Micro Elements** (Children), the following hierarchy is used:

Inside the config folder, there is the `defaultChildren` key. 

If the widget needs to have **Children** the `defaultChildren` must be an array. 

Inside an array, every **Child** must have a `type`. For example, `type: WIDGET_TYPES.Icon`.

`params` is used for adding parameters to the **Children** in particular cases.

>ℹ The **Micro Elements** can also be nested inside **Children** widgets. An example of that particular case is the **Shipping Details Widget** which includes multiple nested **Row** and **Column** and **FormItem** nested inside each other.

Developers can compare a created small block with its corresponding saved JSON structure in the file below, where a **Countdown widget** and a **Title widget** are being added. Each of these is nested within its corresponding Column widget, which is contained within a Row widget, and the Row is contained within a Block widget. The block is made up of eight widgets, which are visible in the structure. Background colors are used to distinguish the widgets.
![screenshot](https://ibb.co/gDnXwwZ)

	[
	  {
	    "type": "block",
	    "hash": "0f032d70-462a-4618-9363-95843c242bfd",
	    "params": {
	      "show": true,
	      "variantsStyles": [
	        {
	          "breakpointId": "3",
	          "cssState": "normal",
	          "styles": [
	            {
	              "type": "padding-left",
	              "value": "10px"
	            },
	            {
	              "type": "padding-right",
	              "value": "10px"
	            },
	            {
	              "type": "background",
	              "value": "[{\"type\":\"solid\",\"value\":\"rgb(222, 223, 249)\",\"active\":true}]"
	            }
	          ]
	        }
	      ],
	      "settings": []
	    },
	    "children": ["8b21f8b9-9a1a-44c0-b319-bf8269a1c48d"],
	    "layoutId": 22
	  },
	​
	  {
	    "type": "row",
	    "hash": "8b21f8b9-9a1a-44c0-b319-bf8269a1c48d",
	    "params": {
	      "show": true,
	      "variantsStyles": [
	        {
	          "breakpointId": "3",
	          "cssState": "normal",
	          "styles": [
	            {
	              "type": "justify-content",
	              "value": "flex-start"
	            },
	            {
	              "type": "align-items",
	              "value": "center"
	            },
	            {
	              "type": "background",
	              "value": "[{\"type\":\"solid\",\"value\":\"rgb(201, 202, 243)\",\"active\":true}]"
	            },
	            {
	              "type": "padding-top",
	              "value": "10px"
	            },
	            {
	              "type": "padding-bottom",
	              "value": "10px"
	            },
	            {
	              "type": "padding-left",
	              "value": "10px"
	            },
	            {
	              "type": "padding-right",
	              "value": "10px"
	            },
	            {
	              "type": "margin-top",
	              "value": "10px"
	            },
	            {
	              "type": "margin-bottom",
	              "value": "10px"
	            },
	            {
	              "type": "margin-left",
	              "value": "10px"
	            },
	            {
	              "type": "margin-right",
	              "value": "10px"
	            }
	          ]
	        }
	      ],
	      "settings": {
	        "columnsCount": [
	          {
	            "breakpointId": "2",
	            "value": 2
	          }
	        ]
	      }
	    },
	    "children": [
	      "35887780-3587-41b5-aca3-9e9793388f59",
	      "44f76019-aacd-47c2-beb3-6b7bfb2a238c"
	    ],
	    "parentHash": "0f032d70-462a-4618-9363-95843c242bfd"
	  },
	​
	  {
	    "type": "column",
	    "hash": "35887780-3587-41b5-aca3-9e9793388f59",
	    "params": {
	      "show": true,
	      "variantsStyles": [
	        {
	          "breakpointId": "3",
	          "cssState": "normal",
	          "styles": [
	            {
	              "type": "align-items",
	              "value": "center"
	            },
	            {
	              "type": "background",
	              "value": "[{\"type\":\"solid\",\"value\":\"rgb(172, 175, 247)\",\"active\":true}]"
	            }
	          ]
	        }
	      ],
	      "settings": {
	        "size": [
	          {
	            "breakpointId": "3",
	            "value": 6
	          }
	        ]
	      }
	    },
	    "children": ["7cde911f-a680-40c5-aff6-1f04be0094b7"],
	    "parentHash": "8b21f8b9-9a1a-44c0-b319-bf8269a1c48d"
	  },
	​
	  {
	    "type": "countdown",
	    "hash": "7cde911f-a680-40c5-aff6-1f04be0094b7",
	    "params": {
	      "show": true,
	      "variantsStyles": [
	        {
	          "breakpointId": "3",
	          "cssState": "normal",
	          "styles": []
	        }
	      ],
	      "settings": {
	        "separators": {
	          "date": "slash",
	          "time": "colon"
	        },
	        "countdownType": "toDate",
	        "units": {
	          "years": true,
	          "months": true,
	          "days": true,
	          "hours": true,
	          "minutes": true,
	          "seconds": false
	        },
	        "endAction": "displayCounter"
	      },
	      "uiElementId": null,
	      "key": "cdn"
	    },
	    "children": [
	      "18fc2734-48fb-47b1-9b30-987ac2a3ba38",
	      "1a8e7ff4-8e2e-44b5-946d-bde91e21efee",
	      "6185e4a9-b61f-4cf6-a1c2-1115ec6c311f"
	    ],
	    "parentHash": "35887780-3587-41b5-aca3-9e9793388f59"
	  },
	​
	  {
	    "type": "container",
	    "hash": "18fc2734-48fb-47b1-9b30-987ac2a3ba38",
	    "params": {
	      "show": true,
	      "variantsStyles": [
	        {
	          "breakpointId": "3",
	          "cssState": "normal",
	          "styles": [
	            {
	              "type": "justify-content",
	              "value": "center"
	            },
	            {
	              "type": "font-size",
	              "value": "4em"
	            },
	            {
	              "type": "background",
	              "value": "[{\"type\":\"solid\",\"value\":\"rgb(112, 139, 247)\",\"opacity\":1,\"active\":true}]"
	            }
	          ]
	        }
	      ],
	      "settings": [],
	      "isMicroElement": true,
	      "preventDrop": true,
	      "labelKey": "numbers"
	    },
	    "children": [],
	    "parentHash": "7cde911f-a680-40c5-aff6-1f04be0094b7"
	  },
	​
	  {
	    "type": "container",
	    "hash": "1a8e7ff4-8e2e-44b5-946d-bde91e21efee",
	    "params": {
	      "show": true,
	      "variantsStyles": [
	        {
	          "breakpointId": "3",
	          "cssState": "normal",
	          "styles": [
	            {
	              "type": "justify-content",
	              "value": "center"
	            },
	            {
	              "type": "font-size",
	              "value": "20px"
	            },
	            {
	              "type": "background",
	              "value": "[{\"type\":\"solid\",\"value\":\"rgb(86, 116, 243)\",\"opacity\":1,\"active\":true}]"
	            }
	          ]
	        }
	      ],
	      "settings": [],
	      "isMicroElement": true,
	      "preventDrop": true,
	      "labelKey": "labels"
	    },
	    "children": [],
	    "parentHash": "7cde911f-a680-40c5-aff6-1f04be0094b7"
	  },
	​
	  {
	    "type": "column",
	    "hash": "44f76019-aacd-47c2-beb3-6b7bfb2a238c",
	    "params": {
	      "show": true,
	      "variantsStyles": [
	        {
	          "breakpointId": "3",
	          "cssState": "normal",
	          "styles": [
	            {
	              "type": "align-items",
	              "value": "center"
	            },
	            {
	              "type": "background",
	              "value": "[{\"type\":\"solid\",\"value\":\"rgb(118, 123, 233)\",\"active\":true}]"
	            }
	          ]
	        }
	      ],
	      "settings": {
	        "size": [
	          {
	            "breakpointId": "3",
	            "value": 6
	          }
	        ]
	      }
	    },
	    "children": ["83f609ab-970d-4d01-9878-0b6e58ab9383"],
	    "parentHash": "8b21f8b9-9a1a-44c0-b319-bf8269a1c48d"
	  },
	​
	  {
	    "type": "title",
	    "hash": "83f609ab-970d-4d01-9878-0b6e58ab9383",
	    "params": {
	      "show": true,
	      "variantsStyles": [
	        {
	          "breakpointId": "3",
	          "cssState": "normal",
	          "styles": [
	            {
	              "type": "font-size",
	              "value": "40px"
	            },
	            {
	              "type": "line-height",
	              "value": "var(--h3-line-height)"
	            },
	            {
	              "type": "font-family",
	              "value": "var(--h3-font-family)"
	            },
	            {
	              "type": "font-weight",
	              "value": "var(--h3-font-weight)"
	            },
	            {
	              "type": "font-style",
	              "value": "var(--h3-font-style)"
	            },
	            {
	              "type": "letter-spacing",
	              "value": "var(--h3-letter-spacing)"
	            },
	            {
	              "type": "text-decoration",
	              "value": "var(--h3-text-decoration)"
	            },
	            {
	              "type": "text-align",
	              "value": "var(--h3-text-align)"
	            },
	            {
	              "type": "text-transform",
	              "value": "var(--h3-text-transform)"
	            },
	            {
	              "type": "color",
	              "value": "var(--h3-color)"
	            },
	            {
	              "type": "text-shadow-blur-radius",
	              "value": "var(--h3-text-shadow-blur-radius)",
	              "group": "text-shadow"
	            },
	            {
	              "type": "text-shadow-color",
	              "value": "var(--h3-text-shadow-color)",
	              "group": "text-shadow"
	            },
	            {
	              "type": "text-shadow-offset-x",
	              "value": "var(--h3-text-shadow-offset-x)",
	              "group": "text-shadow"
	            },
	            {
	              "type": "text-shadow-offset-y",
	              "value": "var(--h3-text-shadow-offset-y)",
	              "group": "text-shadow"
	            },
	            {
	              "type": "text-shadow-enabled",
	              "value": "var(--h3-text-shadow-enabled)",
	              "group": "text-shadow"
	            }
	          ]
	        }
	      ],
	      "settings": {
	        "level": "h3"
	      },
	      "uiElementId": null,
	      "key": "ttl",
	      "props": {
	        "link": {
	          "url": null,
	          "type": null,
	          "target": null
	        },
	        "text": "{\"blocks\":[{\"key\":\"Title8g4i7\",\"text\":\"Time Until End\",\"type\":\"header-three\",\"depth\":0,\"inlineStyleRanges\":[],\"entityRanges\":[],\"data\":{}}],\"entityMap\":{}}"
	      }
	    },
	    "parentHash": "44f76019-aacd-47c2-beb3-6b7bfb2a238c"
	  }
	]

## Widget Instance Params Merge Logic with UI Elements and Widget Params

Each time a widget instance is rendered, its parameters must be merged with those of the UI elements and widget.

`WidgetComponent` is a valuable name for all the widgets. It has params with properties like:

- `props` includes all the translatable properties, for example, a text.

- `settings` includes style settings, for instance, the `contentType` specifies how the icon is positioned in relation to the label.

>ℹ The order of the filled data must be:

1. `uiElementData`

2. `widget`

>ℹ No data is needed for the widget's contentType to acquire the data from the UI element.

## Widget Instance Class Names

- `uc-widget` applies general styles to widgets. All the widgets in the layout must include this class name.

- `widget.hash` is responsible for the custom widget instance.

- `uc-widget-${widget.type}`  is used to apply default styles to widgets which are specified by the widget type.

- `stateClasses` is responsible for hover or normal states of the widget.

- `widgetStateClasses` is responsible for applying styles in both guest and loggedIn states.

- `widget.params` is where the `cssClass` is provided. If there is no class name in the widget `params`, the widget prefix is used as a class name.

 - `widget.params.key` is where the microelement `key` must be provided.

- `isUiElement` class name is used with widgets that contain UI elements to also apply UI element styling to the widget.

- `elementContext.isCollectionBinding` class name is used to apply the styles that bind to collection fields. For instance, this class name is used to bind a color field in the collection to the text color or background color of a widget on the collection item page.

## How to Make a Widget Compatible with SSR Render?

Whenever an action is needed on the widgets with user data, it must be carried out dynamically. The following configurations must be configured:

1. In `LoginSubmitButton.public.tsx` the `useUserInfo` is provided as `const { user, isLoadingUser } = useUserInfo();`.

2. Whenever the pages are being cashed, the query strings are skipped. Everything that is connected to query string must be dynamic. If there is a need to add a query string for a widget, it must be carried out inside **.env** file in **proxy-v3**, adding widget name next to `orderId` of the `RESERVED_QUERY_ARGS="orderId"`.