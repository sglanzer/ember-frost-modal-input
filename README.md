[ci-img]: https://img.shields.io/travis/ciena-frost/ember-frost-modal-input.svg "Travis CI Build Status"
[ci-url]: https://travis-ci.org/ciena-frost/ember-frost-modal-input

[cov-img]: https://img.shields.io/coveralls/ciena-frost/ember-frost-modal-input.svg "Coveralls Code Coverage"
[cov-url]: https://coveralls.io/github/ciena-frost/ember-frost-modal-input

[npm-img]: https://img.shields.io/npm/v/ember-frost-modal-input.svg "NPM Version"
[npm-url]: https://www.npmjs.com/package/ember-frost-modal-input

[![Travis][ci-img]][ci-url] [![Coveralls][cov-img]][cov-url] [![NPM][npm-img]][npm-url]

# ember-frost-modal-input

 * [Installation](#installation)
 * [API](#api)
 * [Examples](#examples)
 * [Development](#development)

## Installation

```
ember install ember-frost-modal-input
```

## API
| Attribute | Type | Value | Description |
| --------- | ---- | ----- | ----------- |
| `title` | `string` | | Optional custom title |
| `subtitle` | `string` | |  Optional custom subtitle |
| `titleComponent` | `string` | | Optional title component to replace standard title/subtitle styles |

## Examples
### Controller
Inject the modal-forms service into the parent controller
```js
modalForms: Ember.inject.service('modal-forms'),
isModalActive: Ember.computed.readOnly('modalForms.isModalActive'),
```

Change the active state of the modal in your component
```js
willInsertElement () {
  this.get('modalForms').setModalActive(true)
},

willDestroyElement () {
  this.get('modalForms').setModalActive(false)
}
```

###Steps to include perfect-scroll helper in ember-frost-modal-input
This will give you styling of header/footer when content is scrolled underneath either element
Styling includes:  box shadow plus transparency effect in header/footer
See Demo Route:  http://ciena-frost.github.io/ember-frost-modal-input/perfect-scroll

##### 1) Extend component from AbstractModal
Import AbstractModal from 'ember-frost-modal-input'
Extend your component from AbstractModal
Below is an example of component.js
```js
import layout from './template'
import {AbstractModal} from 'ember-frost-modal-input'
const {inject} = Ember

export default AbstractModal.extend({
  layout,
  modalForms: inject.service('modal-forms'),
  userModel: {},

  /* Ember.Component method */
  willInsertElement () {
    this.get('modalForms').setModalActive(true)
  },

  /* Ember.Component method */
  willDestroyElement () {
    this.get('modalForms').setModalActive(false)
  },

  formData: {
    state: {},
    isValid: false
  },

  clearForm () {
    this.set('formData', {
      state: {},
      isValid: false
    })
  },

  actions: {
    cancel () {
      this.clearForm()
      this.sendAction('dismiss')
    },

    formValueChanged (formState) {
      this.set('formData.state', formState)
    },

    onValidation (info) {
      this.set('formData.isValid', info.valid)
    },

    save () {
      if (this.get('formData.isValid')) {
        this.sendAction('save', this.get('formData.state'))
        this.sendAction('dismiss')
        this.clearForm()
      }
    }
  }
})
```
##### 2) Wrap container element with perfect-scroll helper in template
Below is an example of template.hbs
```handlebars
{{#frost-modal-input title='Test title' subtitle='Subtitle'}}
{{!#frost-modal-input titleComponent='myTitle'}}

  {{#perfect-scroll
    wheelSpeed=2
    minScrollbarLength=20
    maxScrollbarLength=150
  }}
  {{frost-bunsen-form
      model=userModel
      view=userView
      onChange=(action 'formValueChanged')
      onValidation=(action 'onValidation')}}
  {{/perfect-scroll}}

  <div class='actions'>
    {{frost-button
      onClick=(action 'cancel')
      size='medium'
      text='Cancel'
      priority='tertiary'}}
    {{frost-button
      onClick=(action 'save')
      size='medium'
      text='Save'
      priority='primary'}}
  </div>
{{/frost-modal-input}}
```
##### 3) Footer needs class 'actions'
See above template for placement of footer
```handlebars
<div class='actions'>
  {{frost-button
    onClick=(action 'cancel')
    size='medium'
    text='Cancel'
    priority='tertiary'}}
  {{frost-button
    onClick=(action 'save')
    size='medium'
    text='Save'
    priority='primary'}}
</div>
```

### Template
Custom classes are applied to the parent template, based on state of the modal
```handlebars
{{liquid-modal class=(if isModalActive 'form-container' '')}}
```

#### Default title component template
```handlebars
{{#frost-modal-input title='Test title' subtitle='Subtitle'}}
    // Custom modal content
{{/frost-modal-input}}
```

#### Custom title component template
```handlebars
{{#frost-modal-input titleComponent='myTitleComponent'}}
  // Custom modal content
{{/frost-modal-input}}
```

## Development
### Setup
```
git clone git@github.com:ciena-frost/ember-frost-modal-input.git
cd ember-frost-modal-input
npm install && bower install
```

### Development Server
A dummy application for development is available under `ember-frost-modal-input/tests/dummy`.
To run the server run `ember server` (or `npm start`) from the root of the repository and
visit the app at http://localhost:4200.

### Testing
Run `npm test` from the root of the project to run linting checks as well as execute the test suite
and output code coverage.
