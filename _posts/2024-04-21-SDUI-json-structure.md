---
layout: post
title:  "SDUI: JSON structure"
date:   2024-04-21
tags: [ development, SDUI ] 
---

When starting to develop an SDUI system, it's crucial to establish clear rules for how the backend and frontend should interact. This is a fundamental aspect of development. There are no perfect solutions; it all depends on your needs. If you want a flexible UI, it might require numerous parameters, but this can make it difficult to maintain and may not yield the expected benefits.

I usually recommend starting with a simple MVP solution to create a proof of concept. This approach provides a sense of whether you're on the right direction.

Initially, you need only a simple structure for describing UI elements. This sounds like plain HTML, and that's fine. At the early stages, you might not know exactly what you need, so start simple and iterate.

## Rendering Data on the Frontend
Let's consider creating a simple form with *first name*, *last name*, *email*, and a *register* button.

|![An iPhone screen showing three text fields and a button. The text fields are labeled 'First Name', 'Last Name', and 'Email'. There is also a 'Register' button.](/assets/images/SDUI-json-structure/example_01.png) |
|:--:| 
| *Image 1. Simple form example* |

If we break down *Image 1*, we can see common elements like text fields, text boxes, and buttons. These are common components in a design system. Your designer should define them in Figma or other design software, which will help in building the application.

The next step is to define this UI structure in JSON format. We don't need to implement complex layer structures, but we absolutely understand that we need something like lines or layers for drawing correct UI.

Here's how we might represent each element in JSON:

**Text field**
```json
{
    "type": "textField",
    "text": "First name",
    "textColor": "#000000",
    "style": {
        "float": "left"
    }
}
```

**Text box**
```json
{
    "id": "firstNameTextBox",
    "type": "textBox",
    "placeholder": "Sirius",
    "textColor": "#000000",
    "style": {
        "float": "right"
    }
}
```

**Button**
```json
{
    "type": "button",
    "text": "Register",
    "style": {
        "backgroundColor": "#000000",
        "float": "center"
    },
    "actions": {
        "onClick": {
            "url": "/register"
        }
    }
}
```
In the text box, the id field must be unique within the entire SDUI JSON structure to ensure correct backend data binding.

Combining these elements, we get:

<details markdown="block">
<summary>Combined SDUI JSON</summary>
```json
{
  "lines": [
    [
      {
        "type": "textField",
        "text": "First name",
        "textColor": "#000000",
        "style": {
          "float": "left"
        }
      },
      {
        "id": "firstNameTextBox",
        "type": "textBox",
        "placeholder": "Sirius",
        "textColor": "#000000",
        "style": {
          "float": "right"
        }
      }
    ],
    [
      {
        "type": "textField",
        "text": "Last name",
        "textColor": "#000000",
        "style": {
          "float": "left"
        }
      },
      {
        "id": "lastNameTextBox",
        "type": "textBox",
        "placeholder": "Black",
        "textColor": "#000000",
        "style": {
          "float": "right"
        }
      }
    ],
    [
      {
        "type": "textField",
        "text": "Email",
        "textColor": "#000000",
        "style": {
          "float": "left"
        }
      },
      {
        "id": "emailTextBox",
        "type": "textBox",
        "placeholder": "email@example.com",
        "textColor": "#000000",
        "style": {
          "float": "right"
        }
      }
    ],
    [
      {
        "type": "button",
        "text": "Register",
        "style": {
          "backgroundColor": "#000000",
          "float": "center"
        },
        "actions": {
          "onClick": {
            "url": "/register"
          }
        }
      }
    ]
  ]
}
```
</details>

Frontend engineers will need to parse JSON lines and render the UI according to the specified structure.

However, creating the client side is not the end of the process. The UI also needs to send input data to the backend, and this behavior must be designed to be both complex and reusable.

## User Input on the Frontend
SDUI represents the UI, which is the `view` part of the MVC pattern. As such, SDUI must store user input data. When your frontend receives SDUI JSON, it should be able to mutate it. We need to design the structure to allow for these mutations.

> When your user input data frontend should mutate SDUI JSON

Returning to our components. Text fields, text boxes, and buttons - we need to specify what data they should store for correct rendering and backend processing.

If a user inputs "Alex" into the first name text box, it should be reflected in the JSON object by adding a `value` field. This field type depends on the expected data. If the component operates with strings, store strings; if it deals with entire objects, store the whole selected object.

So, after these impoving our componens should looks like this:

**Text box (firstName)**
```json
{
    "id": "firstNameTextBox",
    "type": "textBox",
    "placeholder": "Sirius",
    "textColor": "#000000",
    "value": null,
    "style": {
        "float": "right"
    }
}
```

**Text box (lastName)**
```json
{
    "id": "lastNameTextBox",
    "type": "textBox",
    "placeholder": "BlackSrius",
    "textColor": "#000000",
    "value": null,
    "style": {
        "float": "right"
    }
}
```

**Text box (email)**
```json
{
    "id": "emailTextBox",
    "type": "textBox",
    "placeholder": "email@example.com",
    "textColor": "#000000",
    "value": null,
    "style": {
        "float": "right"
    }
}
```

You may ask me "what's about button?". Well, button is always component who operates SDUI JSON data in `value` fields. In our case when customer press the button, frontend should send POST/PATCH request to `onClick.url` endpoint with SDUI JSON with object contains `value` fields.

In our case frontend should send json to backend:
```json
{
  "lines": [
    [
      {
        "id": "firstNameTextBox",
        "type": "textBox",
        "placeholder": "Sirius",
        "textColor": "#000000",
        "value": "Alex",
        "style": {
          "float": "right"
        }
      }
    ],
    [
      {
        "id": "lastNameTextBox",
        "type": "textBox",
        "placeholder": "BlackSrius",
        "textColor": "#000000",
        "value": "Ilinykh",
        "style": {
          "float": "right"
        }
      }
    ],
    [
      {
        "id": "emailTextBox",
        "type": "textBox",
        "placeholder": "email@example.com",
        "textColor": "#000000",
        "value": "public@ilinykh.org",
        "style": {
          "float": "right"
        }
      }
    ]
  ]
}
```

This JSON body contains all the information needed for processing in backend side. The structure allows for consistent interactions using SDUI.