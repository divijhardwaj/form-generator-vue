With **form-generator-vue** you can create forms using the component library of your choice or the components that you have created. You get full control over the generated from wether its css styles or js. It comes with a easy to use and highly flexible validation engine.
#### [contribute](https://github.com/divijhardwaj/form-generator-vue) to make it better.
#### [demo](https://github.com/divijhardwaj/form-generator-vue-demo)

## Features
* reactive schema based form.
* compatible with third party component libraries (Tested with vuetifyjs).
* all types of field and components are supported.
* full control over form.
* custom validators.
* customizable styles.

### Install
```
npm install form-generator-vue
```
#### Versions
 1.0.5
* Code optimization, **scroll** to first invalid field on submit.

1.0.4
* The prop name `custom-componenets-map` has changed to `form-components`.
* The property `name` is now converted to `model` in `field-config`.
* `form-components` now supports `error prop` that a component consumes to show error messages.

## How to use
After this, **Follow Min Config step to get it working.**
```
<template>
    <form-generator-vue>
    </form-generator-vue>
</template>

<script>
import FormGeneratorVue from 'form-generator-vue';
export default {
    components: {
        FormGeneratorVue
    }
}
</script>
```

## Props
|props|type|description|
|----|---|----|
|form-config| Object | Form schema |
|form-components| Object |Fom Components map|
|submit-handler| function ref |`referrence` of  submit `function`. `form data` will be passed as param.|
|form-rules| Object | For user defined validations, validation types, common validators|
|form-editable|Boolean |Sets the editable state of the form. `Default is true`, if `disabled` then form body containing all the fields will be hidden from view. `v-slot:disabled` can be used to show the disabled state.|
|classes|Object |Can be used to add classes to all the rows and columns inside form body. Eg - `{row: 'className', col: 'className'}`  |

## Min Required Props
It needs **two essential props** `form-components` and `form-config` to render a form.
```
    <form-generator-vue
        :form-components="formComponents"
        :form-config="formConfig"
    >
    </form-generator-vue>
```
##### **form-components:**
This prop requires a map of components to know which component will be used for which input type or types.
**IMPORTANT - The components that you want to use must be globally registered. Follow [Official Doc](https://vuejs.org/v2/guide/components-registration.html) to learn how to register component**.
```js
const formComponents = [
  {
    type: ['number', 'password', 'text', 'email'],
    component: { name: 'v-text-field', errorProp: 'errorMessages' }
  },
  {
    type: 'select',
    component: { name: 'v-select', errorProp: 'errorMessages' }
  },
  {
    type: 'checkbox',
    component: { name: 'v-checkbox', errorProp: 'errorMessages' }
  },
];
```

##### **form-config**
this prop requires an object containing the following `options`.
| options | type | required | description |
| ------ | ------ | ------ | ---- |
| fields | Array | yes | contains information of each field |
| activeValidation | Boolean | Optional | enable/disable validations when user inputs. **false** by default |
| logs | Boolean | Optional | shows validation logs for easy debugging |

```js
computed: {
    function formConfig  {
        return {
            activeValidation: false,
            fields: [
              {
                model: 'firstName',
                type: 'number',
                props: {
                    // component props here
                }
              },
              {
                model: 'gender',
                type: 'select',
                props: {
            
                }
              }
            ]
        }
    }
}
```
In above example the `fields` property of `form-config` is an array of objects where each object is a `field-config`. In the above example each object is rendered in a single column row. `fields` also supports multiple columns(components/fields) in same row.see the example below.
```js
computed: {
    function FORM_CONFIG  {
        return {
            activeValidation: false,
            fields: [
              {
                model: 'firstName',
                value: 'divij',
                type: 'number',
                props: {
                    // component props here
                }
              },
              {
                model: 'gender',
                type: 'select',
                props: {}
              },
              [
                {
                    model:'countryCode',
                    type:'select'
                },
                {
                    model: 'mobile',
                    type: 'number'
                }
              ]
            ]
        }
    }
}
```
When the above `form-config` is passed to the `form-generator-vue` as a prop then the following data properties are created inside it.
```js
fields: {
    firstName: 'divij',
    gender: '',
    countryCode: '',
    mobile: ''
},
errors: {
    firstName: '',
    gender: '',
    countryCode: '',
    mobile: ''
}
```
Every key in `fields` has its own component with which it is `v-model`ed with.

properties of `errors` are passed to their respective components as prop **errorMessage** by default unless error prop is explicitly specified in `form-components` or `field-config`.

#### `field-config` Options

| options | type | required | purpose |
| ------ | ------ | ------ | ----- |
| model | String | true | `v-model`s with component.|
| type | String | optional |`Input type`. Component for it is loaded from `form-components`. If not provided then `text` is set as default and the component for `text` will be picked from `form-components` |
|value| any |optional| Default value to be passed|
|props| Object | optional | Component props |
| show | Boolean/Func returning Boolean(form context available as param) |optional| To dynamically hide or show the field. Field is not validated if hidden. When its visible again `field-config` `value` is assigned else empty is assigned.|
|triggers|Func that returns object (form context available as parameter)| optional |For adding events to a component. Assigned to `v-on`.|
|required|Boolean/Func returning Boolean(form context available as param)|optional|Field is not validated if false, validations will run if `activeValidation` is enabled and `rules` are  provided but will not validate onSubmit|
|props.disabled|Boolean/Func returning Boolean(form context available as param)|optional| To disable or enable field.|
|rules|Object|optional| For validations.|
|component|String|optional| For rendering component which is not in `form-components`.|
|errorProp|String|optional|Error prop that component will use to show error message.|

## Get form context
When the form-generator-vue component is loaded then an event is emited to the parent at `created` lifecycle hook. To get the form context you can use the event `setFormContext`.
```
<form-generator-vue
    @setFormContext = "ctx => formCtx = ctx"
>
</form-generator-vue>
<script>
    export default{
        data() {
            return {
                formCtx: undefined
            }
        }
    }
</script>
```
Some usefull properties of form-generator-vue that you can access with the help of its context.
* Data property:
    *   fields
    *   errors
    *   loading (for async submission handling).
*   Computed Properties:
    * fieldsConfig
    * fieldsConfig_FLAT
*   methods:
    *   showErrors(model, msg)
    *   scrollToComponent(ref)

## Helper Component/field
Helper comonents can be added to form to assist a main component. The helper component can be chips to fill in values in the main component field or multiple checkboxes or anything whose sole purpose is to asign value to main field.

There are **two ways of adding helper components**
    * Adding helper components through `form-config`.
    * Slots `before` and `after` a component

**Adding helper component using `form-config`**
```js
computed: {
    function FORM_CONFIG  {
        return {
            fields: [
              {
                model: 'amount',
                type: 'number',
              },
              {
                model: 'amount_formHelper',
                type: 'select',
                required: false
              }
            ]
        }
    }
}
```

In the above example the `field-config`, the `amount_formHelper` is helper field for the `amount` field, similarly for model **'gender'** you can create **'gender_formHelper'** helper component. Now the value of `amount` will be assigned to the `amount_helper` and vice versa. **This wont go inside infinite loop of assignmet as `watcher` is used**, which triggers on value change only.

Helper components are required by default, it is recommended to set `required` equals false for helper fields/component as form will not submit if its value is empty.

## Validation
form-generator-vue comes with a flexible validation engine where user can write ther own vaidators for each field, validation types for generic validation methods like regex or common validators for multiple fields.

The error prop(`errorMessage` is passed as default) which is passsed the component, it contains the error data that can be passed to the componet if the validation fails.

non empty fields are checked by default. If encounter any empty field then it will set the error prop with String 'Required'.

check **field-config** for options like `required`(if you dont want validations on a specific field).

### Writing validators
`form-rules` prop must be passed to the form-generator-vue component.
```
    <form-generator-vue
        :form-rules="FORM_RULES"
    >
    </form-generator-vue>
    <script>
        import FORM_RULES from 'form-rules.js';
        computed: {
            FORM_RULES: () => FORM_RULES
        }
    </script>
```

**form-rules.js** (from demo)
```js
const MASTER_RULES = {
  email: /^(([^<>()\[\]\.,;:\s@\"]+(\.[^<>()\[\]\.,;:\s@\"]+)*)|(\".+\"))@(([^<>()[\]\.,;:\s@\"]+\.)+[^<>()[\]\.,;:\s@\"]{2,})$/i,
  password: /^(?=.*[0-9])(?=.*[!@#$%^&*])[a-zA-Z0-9!@#$%^&*]{6,16}$/,
  // mobile: /^\+\d{1,3}-\d{9,10}$/
}
export default {
  // will be applied to all fields ------------
  COMMON_VALIDATORS: {
    number: value => {
      if (typeof value === 'number') {
        if (value < 0) {
          return 'Cannot be negative';
        }
      }
      return '';
    },
  },

  // VALIDATION TYPES (common logic for multiple regex type validations)---------
  regex: function (value, rules = {}, fields) {
    const regex = rules.regex || MASTER_RULES[rules.ruleName];
    const errMsg = rules.errorMsg || 'Invalid input';
    if (!regex || !regex.constructor == RegExp) {
      console.error(`${regex} is not a valid RegExp`);
      return '';
    }
    return regex.test(value) ? '' : errMsg;
  },

  condition: function (value, rules = {}, fields) {
    console.log('gg');
    const { condition } = rules || { condtion: undefined };
    if (!['function', 'boolean'].includes(typeof condition)) {
      console.error(`condition should be a function returning boolean or boolean itself`);
      return '';
    }
    const result = typeof condition === 'function' ? condition() : condition;
    return result ? '' : rules.errorMsg || 'Invalid input';
  },

  // VALIDATION FOR SPECIFIC FIELD -------------
  confirmPassword: function (value, rules = {}, fields) {
    const password = fields.password;
    const errorMsg = 'Confirm your password';
    const success = '';
    const findMatch = () => {
      return password == value ? success : errorMsg;
    }
    return password ? findMatch() : success;
  }
}
```

`form-rules` exports an object in which all the properties are validation functions, they must return empty string on validation success. If validation fails it must return error data(can be of any data type that your comonent can support).

##### For field specific validator
If your `field-config` contains `model:'fullName'` and you want to write a validation function specifically for this field then your validation function name should be `fullName`.

##### Validation types
You can also create **validation types** for using a common validation function for multiple different type of fields. For that you can create a common validation function for lets say **regex** testing or for **min-max** limit. You can name your function whatever you want.
Lets say you name your function **'regex'** for validating multiple regex type fields then you need to add `type` property to your `field-config`, it will look like this.

```js
    {
        model: 'amount',
        rules: {
            type: 'regex'
        }
    }
```

##### Common validators (`COMMON_VALIDATORS`)
Input sanitization functions can be written here for different input types.

**Following params are available in validators/functions.**
* **param 1:** `value`, value of current field/component.
* **param 2:** `rules`, rules specified in `field-config`.
* **param 3:** `fields`, data of all the fields.

## Slots
#### [Slots Arrangement](https://drive.google.com/file/d/1vq3KcNKR0CAHy8BYKi0FsNeieAwoSGpl/view?usp=sharing)

#### v-slot:`header`
For form header information.

#### v-slot:`sectionLabel`
Single lable for multiple components.
**slot props** `fieldConfig`, `fieldsConfigFlat`.

#### Slots `before` and `after` a component
For adding support components or ui elements before or after a component.
* v-slot:`<field-config.model>_before`
* v-slot:`<field-config.model>_after`
(replace **<field-config.model>** with the **model** property of **field-config**)

#### v-slot:`disabled`
This slot is only visible when the `form-editable` prop of `form-generator-vue` is enabled. When enabled the form body(containing all the components/fields) hides from view and only this slot is visible.
This slot can be used to show disabled state of the form however you want.
**slot prop:** `fieldsConfigFlat`.

#### v-slot:`agreement`
To show terms and condition checkbox, policy checkbox, etc.

#### v-slot:`actions`
For form actions like submit and cancel etc.

### v-slot:`footer`
For footer notes, etc.

## Styling
No default CSS is written in this component, you can write your own styles for the generated form. **BEM** methodology is beign used to enure no class conflicts with third party component library.

#### Classes
* form - **"generated-form"**
    * header - **"generated-form__header"**
    * body - **"generated-form__body"**
        * row - **"generated-form__body__row"**
            * col - **"generated-form__body__row__col**
            * col - **"col-`<field-config.model>`"** (dynamic class, to precisely identify col in which component is rendered).
    * footer - **"generated-form__footer"**


## Scroll to invalid field manually
form-generator-vue automatically scrolls to view the first invalid field it encounters and focus on it. `scrollToComponent` function can be invoked explicitly through form context. It needs **ref** of the component you want to scroll to view. **ref = <field-config.model>**
    