# Definition

Checkout the [Camunda documentation](https://docs.camunda.io/docs/next/components/modeler/desktop-modeler/element-templates/defining-templates/) to learn how to define an element template.


## General

_To be done._


## Properties

Through `properties` an element template defines what is shown to the user (visible) or otherwise configured once applied to a BPMN element.

```json
{
  "$schema": "https://unpkg.com/@camunda/zeebe-element-templates-json-schema/resources/schema.json",
  "id": "undefined-input-binding",
  ...
  "properties": [
    {
      "type": "Hidden",
      "value": "secret value",
      "binding": {
        "type": "zeebe:input",
        "target": "secret"
      }
    },
    {
      "label": "Mode",
      "type": "Dropdown",
      "choices": [
        {
          "name": "Send",
          "value": "=true"
        },
        {
          "name": "Do not send",
          "value": "=false"
        }
      ]
    }
  ]
}
```

| Name | Value |
| :--- | :--- |
| `label` (`string`) | Visible entry label |
| `type`  ([input type](#input-type)) | Type of input to show to the user |
| `description` (`string`) | Visible entry description |
| `value` (`string`) | Default value |
| `optional` (`boolean`) | If `false` empty (falsy) entries will not be serialized |
| `binding` ([property binding](#property-binding)) | Determines binding to technical property |


#### Input Type


#### Property Binding

