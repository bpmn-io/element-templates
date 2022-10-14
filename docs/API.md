# API

The core element template functionality is exposed through the [`ElementTemplates`](https://github.com/bpmn-io/bpmn-js-properties-panel/blob/master/src/provider/cloud-element-templates/ElementTemplates.js) and [`ElementTemplatesLoader`](https://github.com/bpmn-io/bpmn-js-properties-panel/blob/master/src/provider/cloud-element-templates/ElementTemplatesLoader.js) services. The following documents the most important APIs.

### `ElementTemplatesLoader#setTemplates(templates)`

Validates and sets element templates synchronously. Does not throw on template errors but instead emits them through an `elementTemplates.errors` event.

```javascript
const eventBus = modeler.get('eventBus');

// subscribe to template validation errors
eventBus.on('elementTemplates.errors', ({ errors }) => {
  console.error('template validation errors', errors);
});

const loader = modeler.get('elementTemplatesLoader');

const templates = [
  { id: 'some template', properties: [ ... ] }
];

// set templates
loader.setTemplates(templates);
```

### `ElementTemplates#createElement(template): DiagramElement`

Create a new element from a template.

### `ElementTemplates#applyTemplate(element, template): DiagramElement`

Apply a template to an element (potentially replacing the element, applying property bindings, ...). Returns the element.

### `ElementTemplates#getLatest(element|templateId): Template[]`

Get element templates applicable to `element` or matching template with `templateId`.

### `ElementTemplates#getLatest(): Template[]`

Get list of element templates available.
