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

![alt text](data:image/jpeg;base64,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)

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