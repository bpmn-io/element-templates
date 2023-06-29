# API

The core element template functionality is exposed through the [`ElementTemplates`](https://github.com/bpmn-io/bpmn-js-properties-panel/blob/master/src/provider/cloud-element-templates/ElementTemplates.js) and [`ElementTemplatesLoader`](https://github.com/bpmn-io/bpmn-js-properties-panel/blob/master/src/provider/cloud-element-templates/ElementTemplatesLoader.js) services. The following documents the most important APIs.

> If you [create your own element templates provider](https://github.com/bpmn-io/element-templates/blob/main/docs/CREATE_ELEMENT_TEMPLATE_PROVIDER.md) then you must at least implement the `ElementTemplates` interface and exposed it as the named `elementTemplates` service.

### `ElementTemplates#get(diagramElement): Template|null`

Get the element template currently applied to a diagram element. Returns `null` if no template is applied or if the applied template is not known to the modeler.

```javascript
const elementTemplates = bpmnJS.get('elementTemplates')

const template = elementTemplates.get('My_Task');
```

### `ElementTemplates#get(templateId, templateVersion?): Template|null`

Get the element template with the given id and optional version. If no version is provided it defaults to latest. Returns `null` if no template matches the search criteria.

```javascript
const elementTemplates = bpmnJS.get('elementTemplates')

const template = elementTemplates.get('MyFancyTemplate', 1);
```

### `ElementTemplates#createElement(template): DiagramElement`

Create a new element from a template.

### `ElementTemplates#applyTemplate(element, template): DiagramElement`

Apply a template to an element (potentially replacing the element, applying property bindings, ...). Returns the element.

### `ElementTemplates#getLatest(element|templateId): Template[]`

Get element templates applicable to `element` or matching template with `templateId`.

### `ElementTemplates#getLatest(): Template[]`

Get list of element templates available.

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
