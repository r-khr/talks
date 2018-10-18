# Angular 2 Component Architecture

## Why does it matter to create a distinction of concerns?

When proper distinction of concern exists within an application it becomes easier to reuse code. It becomes easier to manage application development. And over time its allows for an increase in development speed since less needs to be built because there are reuseable components available.

## Smart Components vs. Presentation Components

Smart components have encapsulated logic within them. They responsible for bringing in values and dispatching changes, to and from the component. Smart components typically sit at the top of the hierarchy. They would be pages, tables and other top level components.

Presentation components provide a template with bindings. There should be no business logic within presentation components. It's okay to include presentation logic within a component which will be common for all use-cases of the component. Good presentation components require time to develop and refine. Presentation components should be easily reusable without making them too granular.

## Why are smart components important?

Smart components are important because they provide business logic to the application. They are responsible for delegating values to presentation components.

## Why are presentation components important?

Presentation components provide reusable building blocks for the application. They decrease templating by providing template encapsulation for reusable html blocks.

## Splitting the application into different types of components

There are many approaches of building good component based architecture. Generally it depends on the experience of the developer. When starting with this processes it is better to build everything within the smart component and only segment out presentation components once the reusable aspects become very clear. It is easy to fall into a mentality of over-engineering by trying to get ahead of the game. This mentality can quickly create unnecessary code which just adds to the complexity to the project without any significant value.

The processes of good component segmentation requires time. It is far better to refactor to increase complexity in the future rather than to reduce it. To that perspective

## Some advanced use cases of inputs and outputs

- something a person wouldn't normally think about

## Live example of how to break up components

- We don't want to create a mentality of over-engineerings
- Discuss the steps
