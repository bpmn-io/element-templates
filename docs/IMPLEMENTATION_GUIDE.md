## Implementation Guide

This document [lays out technical foundations](#overview) of the [core element templates](../README.md) mechanism. Building on these foundations, a [detailed walkthrough](#case-study-implementing-zeebeproperty), shows how to extend element template support.

## Overview

On the high level, element templates are supported through a [JSON Schema](#json-schema) and [the behavior](#behavior).

### JSON Schema

The JSON schema is typically referenced through the `$schema` property in an element template descriptor. Take [Camunda 8](https://github.com/camunda/element-templates-json-schema) as an example:

```json
{
  "$schema": "https://unpkg.com/@camunda/zeebe-element-templates-json-schema/resources/schema.json",
  "name": "Template 1",
  "id": "sometemplate",
  ...
}
```

The schema serves two main purposes:

* It **enables editor support** (auto-completion and validation) during template creation
* It **validates templates at run-time**, ensuring they are _safe to use_

What the schema defines **must** be implemented through corresponding [behavior](#behavior).

### Behavior

An element template implementation realizes the template behavior. It reads templates provided by the user and brings them to live. It offers APIs used by [editor integrations](../README.md#editor-integration) and embedding applications.

Typically it builds upon (or re-uses) sustantial parts of [bpmn-js-element-templates](https://github.com/bpmn-io/bpmn-js-element-templates). Either way, the behavior must provide the following components:

  * An `elementTemplates` service, [the API](./API.md):
    * To validate and set available templates
    * To query for applicable templates
    * To apply a template to a given diagram element
    * To create a diagram element from a template
  * User interface elements that plug into the [properties panel](https://github.com/bpmn-io/bpmn-js-properties-panel) to provide domain-specific rendering and editing capabilities interface for diagram authors
  * Bindings that link the properties to underlying moddle properties
    * Established when creating a new element
    * Set or updated when a template changes, preserving compatible properties
    * Retrieve from the moddle for rendering, and writen to it when changed through the UI

## Case Study: Implementing `zeebe:property`

[`zeebe:property`](https://docs.camunda.io/docs/next/components/modeler/desktop-modeler/element-templates/defining-templates/#zeebeproperty) is a binding for Camunda 8 element templates. A element template author user can use it to create a template property bound to the `zeebe:property` BPMN 2.0 XML extension, i.e. on a `bpmn:Task`:

```xml
<bpmn:definitions>
  <bpmn:process>
    ...
    <bpmn:task>
      <bpmn:extensionElements>
        <zeebe:properties>
          <zeebe:property name="my-name" value="Walt" />
        </zeebe:properties>
      </bpmn:extensionElements>
    </bpmn:task>
    ...
  </bpmn:process>
</bpmn:definitions>
```

The corresponding element template descriptor could look like this:

```json
{
  "$schema": "https://unpkg.com/@camunda/zeebe-element-templates-json-schema/resources/schema.json",
  "name": "Template 1",
  "id": "sometemplate",
  ...
  "properties": [
    {
      "label": "My Name",
      "type": "String",
      "value": "",
      "binding": {
        "type": "zeebe:property",
        "name": "my-name"
      }
    }
  ]
}
```

Let's look in detail into how this property is implemented, across [schema](#json-schema) and [behavior](#behavior).

### `zeebe:property` Schema

`zeebe:property` is a valid value of an element templates properties `binding#type` field. Supporting it via JSON schema means to extend the schema appropriately to [allow that value and add test coverage](https://github.com/search?q=repo%3Acamunda%2Felement-templates-json-schema+%22zeebe%3Aproperty%22&type=code).

Additionally, the implementation _may_ extend the JSON schema with custom error handling. While not relevant during template editing, the custom error handling greatly benefits users when validating element templates at run-time. It causes validators to print less technical, hence more human readable error messages.

### `zeebe:property` Behavior

[bpmn-js-element-templates](https://github.com/bpmn-io/bpmn-js-element-templates) implements element templates for Camunda 7 and Camunda 8, hence we look to that library to extend the template behavior.

What [looks complicated at first](https://github.com/search?q=repo%3Abpmn-io%2Fbpmn-js-element-templates+%22zeebe%3Aproperty%22&type=code) realizes three aspects of the `zeebe:property` binding:

* A [create helper](https://github.com/search?q=repo%3Abpmn-io%2Fbpmn-js-element-templates+%22zeebe%3Aproperty%22+path%3Asrc%2Fcloud-element-templates%2Fcreate%2FZeebePropertiesProvider.js&type=code) sets up the `zeebe:property` binding for a newly created moddle element.
* The [`ChangeElementTemplateHandler`](https://github.com/search?q=repo%3Abpmn-io%2Fbpmn-js-element-templates+%22zeebe%3Aproperty%22+path%3Asrc%2Fcloud-element-templates%2Fcmd%2FChangeElementTemplateHandler.js&type=code) sets or updates `zeebe:property` in the moddle when an element template gets applied to a diagram element; this must be built in a way that adheres to the [element template upgrade contract]()
* The [`propertyUtil`](https://github.com/search?q=repo%3Abpmn-io%2Fbpmn-js-element-templates+%22zeebe%3Aproperty%22+path%3Asrc%2Fcloud-element-templates%2Futil%2FpropertyUtil.js&type=code) reads `zeebe:property` from the moddle and writes it back, if changed by the user, through the properties panel.