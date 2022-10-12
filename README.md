# Element Templates

The tool that powers declarative, domain specific modeling at [bpmn.io](https://bpmn.io/).

## About

Element templates allow you to define re-usable, domain specific building blocks to [supported bpmn.io toolkits](#supported-toolkits). Through [a set of utilities](#related-utilities) they get deeply integrated into the core modeling experience.


## Features

Through a [JSON document](https://docs.camunda.io/docs/next/components/modeler/desktop-modeler/element-templates/defining-templates/) you define different aspects of a re-usable building block:

* Name and description
* Icon
* BPMN element type
* Category
* Documentation references
* Versioning + deprecation information


## Supported Toolkits

Right now element templates are supported in [bpmn-js](https://github.com/bpmn-io/bpmn-js).


## Related Utilities

* [bpmn-js-properties-panel](https://github.com/bpmn-io/bpmn-js-properties-panel) - Provides the `ElementTemplate` core and ships with two implementation for technical property binding of [Camunda](https://camunda.com/) properties.
* [element-templates-icons-renderer](https://github.com/bpmn-io/element-templates-icons-renderer) - Renders embedded element template icons on the modeling canvas.
* [element-template-chooser](https://github.com/bpmn-io/element-template-chooser) - A simple element template chooser that plugs into the properties panel
* [element-template-json-schema](https://github.com/camunda/element-templates-json-schema) - The schema definition for template descriptors
* [bpmn-js-connectors-extension](https://github.com/bpmn-io/bpmn-js-connectors-extension) - A deep integration into the core modeling experience, featuring create + append anything and replacing with template tasks