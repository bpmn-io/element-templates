# API

The core element template functionality is exposed through the [`ElementTemplates`](https://github.com/bpmn-io/bpmn-js-element-templates/blob/main/src/cloud-element-templates/ElementTemplates.js) and [`ElementTemplatesLoader`](https://github.com/bpmn-io/bpmn-js-element-templates/blob/main/src/cloud-element-templates/ElementTemplatesLoader.js) services. The following documents the most important APIs.

> [!NOTE]
> If you [create your own element templates provider](./IMPLEMENTATION_GUIDE.md) then you must at least implement the `ElementTemplates` interface and exposed it as the named `elementTemplates` service.

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

### `ElementTemplates#getUpdate(element): { type, template } | null`

Get the update available for an element: `{ type: 'migration', template }`, if a template declares a [migration path](./LIFE_CYCLE.md#update-paths) from the element's template, else `{ type: 'update', template }` to the latest compatible version of its template, if it differs. Returns `null` otherwise.

### `ElementTemplates#getUpdateDetails(element, template): { message?, changes }`

Get what changing an element to `template` means, as declared by its [update paths](./LIFE_CYCLE.md#update-paths). Works for any element and template, and is usually empty. `changes` are listed oldest first, each as `{ type: 'update' | 'migration', template, version?, text }`.

`text` is untrusted Markdown: escape or sanitize it when you render it.

### `ElementTemplates#setEngines(engines: Record<string, string>): void`

Sets the engines used to determine [element template compatibility](./LIFE_CYCLE.md#compatibility).

```javascript
const elementTemplates = bpmnJS.get('elementTemplates')

const template = elementTemplates.setEngines({
  camunda: '>= 1.25',
  myEditor: '^50'
});
```

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
