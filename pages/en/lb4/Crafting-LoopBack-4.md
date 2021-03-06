---
lang: en
title: 'Crafting LoopBack Next'
keywords: LoopBack 4.0, LoopBack-Next
tags:
sidebar: lb4_sidebar
permalink: /doc/en/lb4/Crafting-LoopBack-Next.html
summary:
---
## Background

[LoopBack](http://loopback.io) is an open-source [Node.js](https://nodejs.org) framework built for API developers. Its primary goal is to help create APIs as microservices from existing services/databases and expose them as endpoints for client applications, such as web, mobile, and IoT. LoopBack connects the dots between accepting API requests and interacting with backend resources. By facilitating developers to implement API logic with out of box integration capabilities, LoopBack establishes itself as the API composition layer to [differentiate](http://loopback.io/resources/#compare) from other frameworks, such as [Express](https://expressjs.com), [Hapi](https://hapijs.com), and [Sails](http://sailsjs.com).

![loopback-composition](/images/lb4/loopback-composition.png)

Up to version 3.x, LoopBack built on the popular [Express framework](https://expressjs.com). In retrospect, basing LoopBack on Express was the right decision: Standing on the shoulders of Express enabled LoopBack to focus on adding value for API creation experience without reinventing the wheel. LoopBack also has benefitted from the Express ecosystem, especially ready-to-use middleware modules from npm as well as valuable knowledge and support by the community.

With LoopBack, developers can create and expose APIs just like cooking meals by following recipes and adding ingredients. LoopBack introduces a set of [core concepts](http://loopback.io/doc/en/lb3/LoopBack-core-concepts.html) that represent the key aspects of API implementation.  To create APIs out of existing databases or services, developers can simply scaffold a LoopBack application, then add necessary JSON declarations and Node.js code to get their APIs up and running in a few minutes.

Behind the scenes, LoopBack uses Express routing and middleware as the plumbing to build its flavor of request/response pipeline for API use cases, such as authentication, authorization, and routing. Beyond the inbound HTTP processing, LoopBack provides integration facilities such as models, datasources and connectors to allow API logic to interact with various backend systems, including but not limited to, databases, REST APIs, SOAP web services and gRPC microservices. The ability to glue inbound communication and outbound integration makes LoopBack a very powerful framework for API developers. The diagram below illustrates how LoopBack fits into a typical end-to-end API processing flow.

![loopback-overview](/images/lb4/loopback-overview.png)

With many years of development and multiple releases, LoopBack has grown significantly in features and users. Many good parts of LoopBack are well received. As an indication, the community has developed [many extensions](https://github.com/pasindud/awesome-loopback). The core team has also learned a lot from what we have done as well as great feedback from the community.

## Why LoopBack-Next?

Like many projects, we've started to experience growing pains with LoopBack too, especially around the following observations:

1. The code base becomes more complicated over time with more modules and more functionality. We would like to have more maintainers and contributors to help out. But the learning curve is getting steep. One of the things to blame is JavaScript itself, which is weakly-typed and lack of constructs such as interfaces to explicitly define contracts between code. There is quite a bit hidden knowledge that is not explicit or obvious for new comers.

2. Technical debts are accumulating, such as inconsistent designs across modules and feature flags for different behaviors. Here are a few examples:
    - Multiple registries are introduced by various modules to manage different types of artifacts, such as remoting metadata, models, datasources, and middleware.
    - Different flavors are used to allow custom logic to intercept requests/responses at various layers, such as middleware, remote hooks, CRUD operation hooks, and connector hooks.
    - More feature flags were added over time to keep backward compatibility while enabling users to opt-in to new behaviors.

3. It is becoming more difficult to add new features or fix bugs as some areas start to reach the limit of the current design.
    - The loopback-datasource-juggler module is a kitchen sink for many things, such as typing, data modeling, validation, aggregation, persistence, and service integration.
    - Models are overloaded with multiple responsibilities, such as data representation, persistence, and mapping to REST. Models are tied to datasources and it's not easy to reuse the same model definition against different datasources.

4. It's not very easy to extend the framework without requesting the core team to make code changes in LoopBack modules. The current version of LoopBack has ad-hoc extensibility at various layers. Extension points are not consistently defined. For example,
    - Use Express to register middleware.
    - Use remoting hooks to intercept remote method invocations.
    - Use CRUD hooks to add logic around CRUD operations.

5. More projects start to use LoopBack as the underlying platform. Such use cases require more knowledge of LoopBack internals and more flexibility and dynamicity to leverage LoopBack to manage and compose artifacts using a metadata driven approach. Some good examples are:
    - Multi-tenancy which requires artifact isolation between tenants.
    - Metadata APIs to manage/activate model definitions and datasources.
    - New interaction patterns for connectors, such as eventing or messaging.
    - Extra metadata for model definitions.

Since the release of 3.x, the team has been brainstorming about how to sustain and advance LoopBack. We have done a lot of homework, triaged existing GitHub issues, reached out to community members and downstream products, and evaluated relevant frameworks and technologies to answer to the following questions:

- Who is the target audience of LoopBack? Why are they interested in LoopBack? What do they use LoopBack for and how do they use it?
- What are the critical pain points? Can we address them incrementally without rebuilding a new foundation?
- What are the most requested features? Is it possible to add such features with the current design?
- What are latest and greatest technologies in our space? What value will they bring in if we start to adopt them?
- How to scale the development and maintenance of LoopBack? How do we allow larger development teams to collaborate on creating APIs using LoopBack?
- How to further grow the community and expand its ecosystem? What can we do to bring more users and contributors to LoopBack?

LoopBack has gained traction among a spectrum of users beyond Node.js application developers,
including:

- **API developers** - Use LoopBack to create APIs in Node.js.
- **LoopBack maintainers and contributors** - Build and maintain modules by the LoopBack project .
- **Extension developers** - Contribute extensions to LoopBack to augment the framework.
- **Platform developers** - Leverage LoopBack as the base to build their value-added offerings.

![loopback-ecosystem](/images/lb4/loopback-ecosystem.png)

The consensus from the core team is to take a bold move and rebuild LoopBack to meet the needs of all above. The decision leads to the inception of LoopBack Next, a new generation of API creation platform.
See more information at https://strongloop.com/strongblog/announcing-loopback-next/

## Objectives

LoopBack Next is designated to meet the following objectives to further evolve the framework for even better API creation experiences:

1. Catch up with latest and greatest technology advances.
   - Adopt [ES2016/2017](http://exploringjs.com/es2016-es2017/index.html) and [TypeScript](https://www.typescriptlang.org/) for ease of maintenance and productivity.
   - Embrace new industrial standards such as [OpenAPI Spec](https://www.openapis.org/) and [GraphQL](http://graphql.org/).

2. Promote extensibility to grow the ecosystem.
   - Build a minimal core and everything else are extensions
   - Open the door for more [extension points and extensions](https://github.com/strongloop/loopback-next/issues/512).

3. Align with cloud native experience for microservices.
   - Adopt cloud native microservices by adopting initiatives such as [Cloud Native Computing Foundation](https://www.cncf.io/).
   - Make LoopBack a first-class citizen of the microservices ecosystem.

4. Remove the complexity and inconsistency across modules.
   - Use a consistent registry and APIs to manage artifacts and their dependencies.
   - Pay down the technical debts by refactoring complex modules.

5. Separate concerns for better composability.
   - Introduce new concepts such as controllers and repositories to represent different responsibilities.
   - Break down the runtime as a set of services and utilize the extension points/extensions pattern to manage the registration, resolution, and composition.

## Strategy

We decided not to take a "big-bang" approach to build LoopBack-Next. Instead, we are doing it incrementally in multiple stages with smaller steps. This approach allows us to better engage the community from the beginning. We are following the principles below to pursue architectural simplicity and extensibility:

1. **Imperative first, declarative later**

   Everything can be done by code via `APIs`. The LoopBack team or community contributors can then create varieties of user experiences with such APIs. For example, with APIs to define models, we allow applications to declare models in JSON or YAML files so that they can be discovered and loaded. An extension can parse other forms of model definition, such as JSON schemas, ES6 classes with decorators, schemas in OpenAPI spec, or even XML schemas into LoopBack model definitions.

   We can also leverage programming constructs such as [decorators](https://www.typescriptlang.org/docs/handbook/decorators.html) allow developers to supply metadata in code. Furthermore, LoopBack artifacts can be declared in JSON or YAML files, which will be handy for users to generate and manipulate them by hand or tooling.

2. **Build minimum features and add more later if necessary**

   Apply YAGNI (You Aint’t Gonna Need). Design and build for what is needed now, not for what you think you may need in the future. There are many different perspectives in API creation and people ask for a lot of features. Starting with MVP allow us to reach the root of the issues without being derailed by noises and build the absolutely necessary features as the core building blocks.  

3. **Developer experience first**

   Always keep in mind that LoopBack are built for developers by developers. Our first priority is to make API developers' life easier. When we design APIs and user interfaces such as CLI or UI, we want to make sure they are intuitive to our users and natural to their thought process.  

Here are the stages we are marching through toward the final version of LoopBack Next as illustrated below.

![loopback-stack](/images/lb4/loopback-stack.png)

1. **Rebase and rewrite the core**
    - Leverage TypeScript for better code quality and productivity
      * Provide optional type system for JavaScript.
      * Provide planned features from future JavaScript editions to current JavaScript engines

    - Unify the asynchronous programming model/style
      * 100% promise-based APIs 
      * Async/Await as first-class async programming style

    - Implement an IoC Container for better visibility and extensibility
      * Universal registry across different modules
      * Dependency injection as a pattern to manage dependencies

    - Introduce Component as packaging model for extensions  
      * Component can be a npm module or a local directory
      * Component encapsulates a list of extensions as a whole 

2. **Validate the core design by implementing an REST/HTTP invocation chain**
    - Add top-down REST API creation which starts with OpenAPI specs

    - Build sequence of actions for inbound http processing
      - Introduce sequence as the composition of actions
      - Implement the most critical actions to fulfill the REST API routing and invocation

    - Introduce controllers as entry points for API related business logic

      In current versions of LoopBack, models are the center pieces of the whole application. They take multiple responsibilities:

        - Data modeling
        - Anchor for API related business logic
        - Persistence or service invocation
        - Mapping to REST HTTP/JSON endpoints

    - Authentication as a component

      Implement the core functionality of authentication as a component, which includes:
      - Decorators to denote authentication requirement
      - `authenticate` action to handle authentication
      - Extension points for various authentication strategies 

3. **Rebuild our integration and composition capabilities**
    - Introduce repositories to represent data access patterns such as CRUD or Key/Value stores
    - Provide a reference implementation of CRUD and KV flavors of repository interfaces using the legacy juggler and connectors
    - Refactor/rewrite the juggler into separate modules
      - Typing system
      - Model and relation definition
      - Validation
      - Query and mutation language
      - DataSource
      - Repository interfaces and implementations for data access
      - Service interfaces and implementations for service invocations
    - Define interfaces and metadata for connectors
    - Rewrite connectors

4. **Declarative metadata and bootstrapping**

    LoopBack manages a set of artifacts, such as models, relations, datasources, connectors, ACLs, controllers, repositories, actions, sequences, components, utility functions, and OpenAPI specs. In addition to the programmatic approach to describe these artifacts by code (apis and decorators), we would like to add declarative support so that they can be declared in JSON/YAML files.

    - Define a LB Next definition language (DSL) in JSON/YAML format and corresponding templates
    - Define the project layout to organize LB next artifacts
    - Leverage the IoC Context to manage metadata/instances of such artifacts following the extension point/extension pattern
    - Define the life cycle and serialization/de-serialization requirements for each type of artifact
    - Add a boot component to discover/load/resolve/activate the artifacts. The boot process can be tailored for both tooling and runtime

5. **Enable cloud native experience**
    - Allow controllers to be exposed as gRPC services
    - Allow interaction with other gRPC services
    - Integration with microservices deployment infrastructure such as Docker and Kubernetes
    - Integration with service mesh  

6. **Tooling (CLI & UI)**
    - Add CLI and UI  tools to:
      - Scaffold LoopBack Next applications
      - Manage artifacts such as sequences, actions, controllers, repositories, services, datasources and models  

## A new core foundation

### Key ingredients for an ideal core

LoopBack itself is very modular. For example, a typical LoopBack application's dependency graph will have the following npm modules:

- loopback
- strong-remoting
- loopback-datasource-juggler
- loopback-connector-*
- loopback-component-explorer

Management of these artifacts is a key part of LoopBack but it's beyond the scope of Express and we have to introduce our own ways to manage them as they are added to LoopBack. Express helped LoopBack quickly got off the ground and grew with good success. But LoopBack needs a more flexible foundation to further build out its capabilities and ecosystem.

LoopBack manages more and more artifacts across different modules. The following are a list of built-in types of artifacts that LoopBack support out of box:

- Model definitions/relations: describes data models and their relations
- Validation: validates model instances and properties
- Model configurations: configures models and attaches them to data sources
- Datasources: configures connectivity to the backend
- Connectors: implements interactions with the underlying backend
- Components: wraps a module
- Remoting: maps JavaScript methods to REST API operations
- ACLs
- Express middleware like actions
- Utility functions
- Built-in models
- Remote hooks
- CRUD operation hooks
- Connector hooks

- Hooks/interceptors
  - Express middleware
  - remote hooks
  - CRUD operation hooks
  - connector hooks

- Security integration
  - Identity and token management
  - Authentication schemes
    - Passport component for various authentication strategies
  - Authorization (ACL)

- Storage component for various local/cloud object storage systems
- Push component for IOS and Android push notifications

- Different api styles
  - jsonapi.org
  - gRPC
  - GraphQL

Metadata for these artifacts form the knowledge base for LoopBack to glue all the pieces together and build some magic to deal with API use cases.

Representation of such knowledge base is the most critical construct for LoopBack. This knowledge base will bring all internal and external build blocks together by providing a consistent way to contribute and consume.     

- A consistent registry to provide visibility and addressability for all artifacts
- Extensibility
- Ability to compose with dependency resolution

- Visibility

  Each artifact has a unique address and can be accessed via a uri or key. Artifacts can also be visible at different scopes.

- Extensibility

  LoopBack artifacts are managed by types. New artifact types can be introduced. Instances for a given type can be added, removed, or replaced.

- Composability

  It's common that one artifact has dependencies on other artifacts.

- Pluggability

  Extensions can be organized and contributed as a whole. We need to have a packaging model so that extension developers can create their own modules as bundles and plug into a LoopBack application.


### Why not Express?

Do we need to build our own core foundation? Can we continue to use Express? Our conclusion is no. Here are the gaps between what Express and LoopBack's need.

- **Lack of extensibility**.  

  Express is a routing and middleware web framework with minimal functionality of its own: An Express application is essentially a series of middleware function calls. For details, see [Using middleware](http://expressjs.com/en/guide/using-middleware.html).

  Express is only extensibile via middleware.  It neither exposes a registry nor provides APIs to manage artifacts such as middleware or routers. For its purpose, Express only deals with middleware-like extensions that intercept http requests/responses. LoopBack needs much more extension points and extensions.

- **Lack of composability**.

  Express is not composable. For example, `app.use()` is the only way to register a middleware. The order of middleware is determined by the order of `app.use`. This simplistic approach works for a single monolithic application where all middleware are known and arranged ahead of time. But it does not support the case when middleware from other components need to be added between existing ones. LoopBack has to introduce a phase-based extension and hack Express to provide this capability.

  Express doesn't provide any way to manage dependencies between artifact instances either.

- **Lack of declarative support**.  

  In Express, everything is done by JavaScript code as it works exactly as the web site claims: `Fast, unopinionated, minimalist web framework for Node.js`. In contrast, LoopBack is designed to facilitate API creation and composition by conventions and patterns as best practices. More types of constructs are introduced.   

## Deep-diving into the building blocks

### Service registry/container

![loopback-ioc](/images/lb4/loopback-ioc.png)

### Dependency injection

See [Dependency injection](Dependency-injection.html)

### Component as the packaging model for extensions

![loopback-component](/images/lb4/loopback-component.png)

## Rebuilding LoopBack on top of the new core

With the extensible foundation in place, we start to rebuild the LoopBack REST API experience by "eating your own dog food".

### Sequence and actions

See [Sequence and actions](Sequence.html).

### Controllers

### Repositories

## Example for application developers

Before we go further, let's try to build a 'hello world' application with LoopBack next.

### Hello-World

## Example for extension developers

LoopBack next
### Hello-Extension

## References

- https://strongloop.com/strongblog/announcing-loopback-next/
- https://github.com/strongloop/loopback-next-hello-world
- https://www.infoq.com/articles/driving-architectural-simplicity
- https://strongloop.com/strongblog/creating-a-multi-tenant-connector-microservice-using-loopback/
- https://strongloop.com/strongblog/loopback-as-an-event-publisher/
- https://strongloop.com/strongblog/loopback-as-a-service-using-openwhisk/
