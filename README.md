# Element Templates

The tool that powers declarative, domain specific modeling at [bpmn.io](https://bpmn.io/).

## About

Element templates allow you to define re-usable, domain specific building blocks to [supported bpmn.io toolkits](#supported-toolkits). Through [a set of utilities](#related-utilities) they get deeply integrated into the core modeling experience.

![Element Templates application demo](./docs/screenshot.png)


## Features

Through a [JSON document](https://docs.camunda.io/docs/next/components/modeler/desktop-modeler/element-templates/defining-templates/) you define different aspects of a re-usable building block.

### [General Configuration](./docs/DEFINITION.md#general)

* Name and description
* Icon
* BPMN element type
* Category
* Documentation references
* Versioning + deprecation information

### [Property Configuration](./docs/DEFINITION.md#properties)

* Type of control
* Technical binding
* Labels, description
* Validation
* Grouping
* Conditional activation criteria


## Editor Integration

Through a [set of utilities](#related-utilities) element templates get deeply integrated into our editor tooling.

### Create/Append Anything

> Provided [through an extension](https://github.com/bpmn-io/bpmn-js-connectors-extension).

* Palette and context pads have a `...` entry to create / append any element
* Building blocks can be discovered via search
* Replace menu is enhanced to work using the same pattern

### Configuration

> Provided [through the properties panel](https://github.com/bpmn-io/bpmn-js-properties-panel).

* Properties panel displays [configuration properties](./docs/PROPERTIES.md#readme)
* Validates inputs according to defined validation rules
* Shows basic template meta-data
* Allows applying, [unlinking and removing](./docs/UNLINK_REMOVE.md#readme) of a template

### Upgrading

> Provided [through the properties panel](https://github.com/bpmn-io/bpmn-js-properties-panel) as well as [an extension](https://github.com/bpmn-io/bpmn-js-connectors-extension).

Templates come with two built-in ways of migration: _Upgrading_ to a newer (compatible) version or _deprecation_ (with the ability to display a custom message).

* Upgrading works alongside the built-in [versioning feature](./docs/VERSIONING.md#readme)
* Migration happens using the built-in [replace feature](./docs/REPLACE.md#readme)
* Upgrade and replace will [preserve defined properties](./docs/REPLACE.md#upgrade-behavior) on a best effort basis (only keeping what is valid in the new template)

## Supported Toolkits

Right now element templates are supported in [bpmn-js](https://github.com/bpmn-io/bpmn-js).


## Creating an Element Template Provider

Refer to the [create a provider guide](./docs/CREATE_ELEMENT_TEMPLATE_PROVIDER.md).


## Related Utilities

* [bpmn-js-properties-panel](https://github.com/bpmn-io/bpmn-js-properties-panel) - Provides the `ElementTemplate` core and ships with two implementation for technical property binding of [Camunda](https://camunda.com/) properties.
* [element-templates-icons-renderer](https://github.com/bpmn-io/element-templates-icons-renderer) - Renders embedded element template icons on the modeling canvas.
* [element-template-chooser](https://github.com/bpmn-io/element-template-chooser) - A simple element template chooser that plugs into the properties panel
* [element-template-json-schema](https://github.com/camunda/element-templates-json-schema) - The schema definition for template descriptors
* [bpmn-js-connectors-extension](https://github.com/bpmn-io/bpmn-js-connectors-extension) - A deep integration into the core modeling experience, featuring create + append anything and replacing with template tasks
