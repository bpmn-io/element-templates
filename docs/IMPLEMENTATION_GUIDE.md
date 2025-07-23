## Implementation Guide

This document [lays out technical foundations](#overview) of the [core element templates](../README.md) mechanism. Building on these foundations, a case study shows how to extend element templates with a [new technical binding](#case-study-implementing-zeebeproperty).

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

> [!NOTE]
> Recommended to build on top of [bpmn-js-element-templates](https://github.com/bpmn-io/bpmn-js-element-templates).

An element template implementation realizes the template *behavior*. It can roughtly can be structured in [API](#api), [UI controls](#ui-controls), and [property bindings](#property-bindings).

#### API

The [*API*](./API.md), including the `elementTemplates` service is used by [editor integrations](../README.md#editor-integration) and the embedding applications:

* To validate and set available templates
* To query for applicable templates
* To apply a template to a given diagram element
* To create a diagram element from a template

#### UI Controls

Extending the [properties panel](https://github.com/bpmn-io/bpmn-js-properties-panel), *UI controls* facilitate the domain specific editing of [template properties](../README.md#property-configuration), including:

* Rendering + editing
* Hinting
* Validation

#### Property Bindings

Linking [template properties](../README.md#property-configuration) to the moddle (diagram element) is realized through *property bindings*:

  * Established when creating a new diagram element from a template
  * Set or updated when a template changes, [preserving compatible properties](./LIFE_CYCLE.md#upgrade-behavior).
  * Retrieved from the moddle for rendering, and written back when changed through the UI

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

Let's look into how this property is implemented, across [schema](#implementing-zeebeproperty-schema) and [behavior](#implementing-zeebeproperty-behavior).

### Implementing `zeebe:property` Schema

`zeebe:property` is a valid value of an element templates properties `binding#type` field. Supporting it via JSON schema means to extend the schema appropriately to [allow that value and add test coverage](https://github.com/search?q=repo%3Acamunda%2Felement-templates-json-schema+%22zeebe%3Aproperty%22&type=code).

Additionally, the implementation _may_ extend the JSON schema with custom error handling. While not relevant during template editing, the custom error handling greatly benefits users when validating element templates at run-time. It causes validators to print less technical, hence more human readable error messages.

### Implementing `zeebe:property` Behavior

[bpmn-js-element-templates](https://github.com/bpmn-io/bpmn-js-element-templates) implements element templates for Camunda 7 and Camunda 8. Hence, we look to the library to extend the template behavior.

What [looks complicated at first](https://github.com/search?q=repo%3Abpmn-io%2Fbpmn-js-element-templates+%22zeebe%3Aproperty%22&type=code) realizes three aspects of the `zeebe:property` binding:

* A [create helper](https://github.com/search?q=repo%3Abpmn-io%2Fbpmn-js-element-templates+%22zeebe%3Aproperty%22+path%3Asrc%2Fcloud-element-templates%2Fcreate%2FZeebePropertiesProvider.js&type=code) establishes the binding on a newly created moddle element. 
  * :arrow_forward: Executed when [appending or creating](../README.md#createappend-anything) elements defined through an element template.
* The [`ChangeElementTemplateHandler`](https://github.com/search?q=repo%3Abpmn-io%2Fbpmn-js-element-templates+%22zeebe%3Aproperty%22+path%3Asrc%2Fcloud-element-templates%2Fcmd%2FChangeElementTemplateHandler.js&type=code) sets or updates the binding on a diagram element. The handler implements the [upgrade contract](./LIFE_CYCLE.md#upgrade-behavior).
  * :arrow_forward: Executed when an element template gets applied, either through the properties panel or through the replace menu. 
* The [`propertyUtil`](https://github.com/search?q=repo%3Abpmn-io%2Fbpmn-js-element-templates+%22zeebe%3Aproperty%22+path%3Asrc%2Fcloud-element-templates%2Futil%2FpropertyUtil.js&type=code) reads the value from the moddle and writes it back once changed through the properties panel.