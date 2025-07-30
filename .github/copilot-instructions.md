<!-- Use this file to provide workspace-specific custom instructions to Copilot. For more details, visit https://code.visualstudio.com/docs/copilot/copilot-customization#_use-a-githubcopilotinstructionsmd-file -->

# **Authoring OpenFGA Models**

This guide provides a comprehensive overview of authoring OpenFGA authorization models. It covers core concepts, the modeling language, relationship definitions, and testing methodologies, drawing insights from the openfga/sample-stores repository for practical examples.

## **1. Introduction to OpenFGA and Authorization Modeling**

OpenFGA is an open-source authorization solution that empowers developers to implement fine-grained access control within their applications through an intuitive modeling language.

It functions as a flexible authorization engine, simplifying the process of defining application permissions.

Inspired by Google's Zanzibar paper, OpenFGA primarily champions Relationship-Based Access Control (ReBAC), while also effectively addressing use cases for Role-Based Access Control (RBAC) and Attribute-Based Access Control (ABAC). Its "developer-first" philosophy is evident in its Domain Specific Language (DSL) and supporting tools, which lower the barrier to entry for developers.

The core purpose of an authorization model is to define a system's permission structure, answering questions like, "Can user U perform action A on object O?". By externalizing authorization logic from application code, OpenFGA provides a robust mechanism for managing complex access policies, especially in large-scale systems. The modeling language is designed to be powerful for engineers yet accessible to other team stakeholders, fostering collaborative policy development.

## **2. OpenFGA Core Concepts: The Building Blocks of Your Model**

To effectively model authorization in OpenFGA, it is essential to understand its core building blocks:

* **Authorization Model:** A static blueprint that combines one or more type definitions to precisely define the permission structure of a system. It represents the  
  *possible* relations between users and objects. Models are immutable; each modification creates a new version with a unique ID. Models aren't expected change often - only when new product features or change in functionality is introduced. They're also generally expected to be backward compatible, but can break backward compatibility once the system has completely moved off the older relations in it.
* **Type:** A string that defines a class of objects sharing similar characteristics (e.g., user, document, folder, organization, team, repo).
* **Object:** A specific instance of a defined Type (e.g., `document:roadmap`, `user:anne`, `organization:acme`). An object's relationships are defined through relationship tuples and the authorization model.
* **User:** An entity that can be related to an object. A user can be a specific individual (e.g., user:anne), a wildcard representing everyone of a certain type (e.g. `user:*` which means all users), or a userset (e.g., `team:product#member`), which denotes a group of users.  
* **Relation:** A string defined within a type definition in the authorization model. It specifies the *possibility* of a relationship existing between an object of that type and a user. Relation names are arbitrary (e.g., owner, editor, viewer, member, admin), but must be defined on the object type in the model.
* **Relationship Tuple:** Dynamic data elements representing the *facts* about relationships between users and objects (e.g., {"user": "user:anne", "relation": "editor", "object": "document:new-roadmap"}).3 Without these, authorization checks will fail, as the model only defines what is *possible*, not what *currently exists*.

The clear separation between the static authorization model (schema) and dynamic relationship tuples (data) is a fundamental design principle. This enables efficient permission evaluation and decouples core logic changes from specific user permission modifications. The immutability of models supports robust versioning, allowing for controlled rollouts and managing complex migrations.

The following table summarizes these core concepts:

| Concept | Description | Example |
| :---- | :---- | :---- |
| **Type** | A class of objects that share similar characteristics. | user, document, folder, organization |
| **Object** | A specific instance of a defined type, an entity in the system. | `user:anne`, `document:report_2023`, `folder:marketing_docs` |
| **User** | An entity that can be related to an object. Can be a specific object, a wildcard, or a userset. | `user:bob`, `user:*` (everyone), `team:product#member` |
| **Relation** | A string defined within a type definition that specifies the possibility of a relationship between an object of that type and a user. | owner, editor, viewer, member, admin |
| **Relationship Tuple** | A grouping of a user, a relation, and an object, representing a factual relationship in the system. | `{"user": "user:anne", "relation": "viewer", "object": "document:roadmap"}` |
| **Authorization Model** | A static definition combining type definitions to define the entire permission structure of a system. | ```type document relations define viewer: [user]``` |

## **3. The OpenFGA Modeling Language: DSL**

OpenFGA's Configuration Language is fundamental to constructing a system's authorization model, informing OpenFGA about object types and their relationships. It describes all possible relations for an object of a given type and the conditions under which one entity is related to that object. The language is primarily expressed in DSL (Domain Specific Language).

### **DSL: Developer-Friendly Syntax for Readability**

The DSL provides syntactic sugar over the underlying JSON, designed for ease of use and improved readability.10 It is the preferred syntax for developers using the Playground, CLI, and IDE extensions (like Visual Studio Code), offering features like syntax highlighting and validation.5 DSL models are compiled to JSON before being sent to OpenFGA's API.5

An example of an OpenFGA model in DSL:

```dsl.openfga
model  
  schema 1.1  
type user  
type document  
  relations  
  define viewer: [user] or editor  
  define editor: [user]
```

## **4  Defining Relationships: Crafting Your Authorization Logic**

OpenFGA provides a rich set of constructs for defining relationships, enabling the modeling of complex authorization policies.

### **Direct Relationships: Explicit Access Grants**

A direct relationship is established when a specific relationship tuple (e.g., user=X, relation=R, object=Y) is explicitly stored. The authorization model must explicitly permit this through direct relationship type restrictions.5 These restrictions define which types of users can be directly associated with an object for a given relation, using formats like

`[<type>`], `[<type:*>]`, or `[<type\>#<relation\>]`. 

For example,

```
define owner: [user] 
```

means only individual users can be directly assigned as owners.

A tuple like:

```
{"user": "user:anne", "relation": "user", "object": "document:1"} 
```

is a direct relationship.

### **Concentric Relationships: Inheriting Permissions**

Concentric relationships represent nested or implied relations, where one relation automatically confers another (e.g., "all editors are viewers").This is implemented using the or keyword within a relation definition.

For example,

```
define viewer: [user] or editor
```

means a user is a viewer if directly assigned OR if they are an editor. user:anne is an editor of `document:new-roadmap`, she implicitly has viewer access, reducing the number of required tuples.

### **Indirect Relationships with 'X from Y': Scalable Hierarchical and Group-Based Access**

The X from Y syntax is crucial for scalability, allowing a user to acquire a relation (X) to an object through an intermediary object (Y) and a defined relation on Y. This avoids individual tuple creation for every permission, enabling higher-level abstraction. It is highly effective for hierarchical and group-based access control. For example,

```
define admin: [user] or repo_admin from organization 
```

on a repo type means a user is an admin if directly assigned, or if they have the repo_admin relation to an org that owns the repo.

This simplifies management; revoking access can be done by deleting a single tuple linking the intermediary.

### **Contextual Authorization with Conditions: Dynamic Permissions**

Conditions introduce dynamic, contextual authorization. A condition is a function using Google's Common Expression Language (CEL), with parameters and a boolean expression.

Example:
```
condition less_than_hundred(x: int) { 
  x < 100 
}
```

Conditions are need to be added at the end of the model, and are instantiated using conditional relationship tuples.

### **Leveraging Usersets for Group-Based Access Control**

A userset represents a set or collection of users, denoted by object#relation (e.g., `company:xy#employee`). Usersets are fundamental for assigning permissions to groups, reducing tuple count and providing flexibility for bulk access management. They can be used in direct relationship type restrictions, such as:

```
define editor: [user, team#member]
```

OpenFGA computes implied relationships based on userset membership, and usersets are integral to defining complex access rules involving union, intersection, or exclusion of groups.

Note that specifying `team#member` means "all members from a specific team". It does not mean that "you need to be a team member to be an editor". Only use it when you need to assign a relation to a set of users from specific object.

OpenFGA's strength lies in its capacity to construct complex authorization logic from foundational elements: direct relationships, concentric relationships (or), and indirect relationships (X from Y). This compositional approach 10 enables modeling intricate real-world permission structures efficiently.

The following table summarizes the key relationship definition patterns in OpenFGA:

| Pattern Name | Description | DSL Syntax Example | Explanation of Effect |
| :---- | :---- | :---- | :---- |
| **Direct Relationship** | Explicitly grants a user a relation to an object via a stored tuple, subject to type restrictions. | `define owner: [user]` | Only individual users can be directly assigned as owner. |
| **Concentric Relationship** | Defines that having one relation implies having another relation to the same object (e.g., editors are viewers). | `define viewer: [user] or editor` | A user is a viewer if directly assigned as viewer OR if they are an editor. |
| **Indirect Relationship ('X from Y')** | A user gains a relation (X) to an object through another object (Y) and a specific relation on Y. | `define admin: [user] or repo_admin from owner `| A user is admin of a repo if directly assigned OR if they are repo_admin of an org that owns the repo. |
| **Conditional Relationship** | A relationship is permissible only if a specified condition, evaluated at runtime, is true. | `define admin: [user with non_expired_grant]` | A user is admin only if the non_expired_grant condition evaluates to true for their context. |
| **Usersets** | Represents a collection of users (e.g., a group or a set of users related by a specific relation). | `define editor: [user, team#member]` | An editor can be a direct user OR any member of a specified team. |

## **5. Step-by-Step: Authoring Your First OpenFGA Model**

The process of authoring an OpenFGA model is iterative, starting with critical features and systematically translating authorization requirements into a structured model.

The central question guiding this process is: "Why could user U perform an action A on object O?" This encourages a relational perspective.

The iterative nature of model design, including "Test the model" and "Iterate" steps, is fundamental. Dedicated testing tools like the CLI, Playground, and .fga.yaml configuration support this workflow, enabling rapid feedback and refinement.

The recommended steps for defining an authorization model are:

1. **Pick the most important feature:** Focus on a high-priority use case to establish a foundational model.
2. **List the object types:** Identify all relevant entities (e.g., user, document, folder, organization).
3. **List relations for those types:** For each type, determine relationships users or other objects can have (e.g., owner, editor, viewer, member).
4. **Define relations:** Translate these relationships into OpenFGA DSL, specifying direct, concentric, and indirect relationships as needed.6  
5. **Test the model:** Validate your model against expected behaviors using assertions and comprehensive test cases.
6. **Iterate:** Refine the model based on testing and evolving requirements.

### **Illustrative Example: Building a Document Management Authorization Model**

Consider a document management system with these requirements:

* Documents can be created by users.  
* Documents can be shared, granting editor or viewer roles.  
* A document's creator inherently has delete, share, edit, and view permissions.  
* Editors can edit and view.  
* Viewers can only view.  
* Documents can belong to folders, with permissions inherited from the parent.

Let's walk through the modeling process:

#### Step 1: Identify Types  

Core types: user, document, folder. An organization type can represent groups.

#### Step 2: Define Relations for organization  

An organization can define membership:

```dsl.openfga
type organization  
  relations  
     define member: [user] # Users can be members.
```

#### Step 3: Define Relations for document  
The document type defines ownership, editing, viewing, sharing, and deleting:

```dsl.openfga
type document  
  relations  
    define organization : [organization] # A document belongs to an organization.
    define parent_folder: [folder] # A document can have a parent folder.

    define owner: [user]  # A direct user 
    define editor: [user] or owner or editor from parent_folder # A direct user or editor or anyone who is an owner.  
    define viewer: [user] or editor or viewer from parent_folder or member from organization # A direct user, or anyone who is an editor.  
    define can_share: owner # Example of a permission: owner
    define can_delete: owner or editor # Example of a permission: owner or editor
```

Step 4: Consider Folder Inheritance (Parent-Child Objects)  

Permissions can be inherited from parent folders. This requires defining relations on the folder type and using X from Y to propagate permissions:

```dsl.openfga
type folder  
  relations  
    define parent_folder: [folder] 
    define owner: [user]  
    define editor: [user] or owner or editor from parent_folder  
    define viewer: [user] or editor or viewer from parent_folder  
```

In this extension, an editor of a document could be someone directly assigned, an owner, or an editor of its parent_folder, leveraging X from Y for hierarchical permissions.3

## **6. Adding permissions

It's a common pratice to define specific permissions, that can't be directly assigned, using `can_<permission>` relations, for example:

```dsl.openfga
type folder  
  relations  
    define parent_folder: [folder] 
    define owner: [user]  
    define editor: [user] or owner or editor from parent_folder  
    define viewer: [user] or editor or viewer from parent_folder  

    define can_view: viewer
    define can_edit: editor
    define can_delete: editor
    define can_share: owner
```

Always define permissions in the authorization models.

## **7. Testing and Validating Your OpenFGA Models**

Thorough testing and validation are indispensable. OpenFGA provides tools for rapid prototyping and automated testing to ensure your authorization model is correctly designed before deployment.

### **OpenFGA CLI: Command-Line Model Management and Testing**

The OpenFGA CLI serves as a cross-platform command-line tool for interacting with an OpenFGA server. It provides a robust set of commands for various tasks, including:

* Reading, writing, validating, and transforming authorization models.
* Running tests on an authorization model, which is crucial for verifying its correctness.
* Managing OpenFGA stores, encompassing operations such as creation, listing, retrieval, deletion, import, and expor

To execute tests defined within a .fga.yaml file, the fga model test command is utilized: fga model test \--tests \<filename\>.fga.yaml.18

### **Automated Testing with .fga.yaml**

The .fga.yaml file is central to defining and testing OpenFGA authorization models, providing a structured approach for validation.18 This file can include:

* name (optional): A descriptive name for the test file.18  
* model or model_file: The authorization model can be defined inline or referenced from an external .fga, .json, or .mod file.18  
* tuples or tuple_file or tuple_files (optional): Relationship tuples can be defined inline or referenced from external JSON, YAML, or CSV files, and are considered for all tests.18

#### Defining the Model and Tuples in .fga.yaml  

You can define your authorization model and relationship tuples directly within the .fga.yaml file. This allows for a self-contained test definition.  
Example of defining a model and tuples inline 18:


```yaml
name: Model Tests # optional  
model: |  
  model  
  schema 1.1  
  type user  
  type organization  
    relations  
    define member : [user]  
    define admin : [user with non_expired_grant]  
  condition non\_expired_grant(current_time: timestamp, grant_time: timestamp, grant_duration: duration) {  
    current_time < grant_time + grant_duration  
  }  
tuples: # Inline tuple definitions go here  
  # Anne is a member of the Acme organization  
  - user: user:anne  
    relation: member  
    object: organization:acme  
  # Peter has the admin role from February 2nd 2024 0AM to 1AM  
  - user: user:peter  
    relation: admin  
    object: organization:acme  
    condition:  
      name: non_expired_grant  
      context:  
        grant_time : "2024-02-01T00:00:00Z"  
        grant_duration : 1h

```

#### Writing Tests in .fga.yaml  

The tests section within the .fga.yaml file is where you define specific test cases for OpenFGA API calls, including check, list_objects, and list_users. 

* **Check Tests:** Verify whether a user U has a relation R with an object O. These tests include the user, object, context (for evaluating conditions), and assertions (pairs of relation: expected-result).

  Example of Check tests 
```yaml
  tests:  
    - name: Test  
      check:  
        - user: user:peter  
          object: organization:acme  
          context:  
            current_time : "2024-02-01T00:10:00Z"  
          assertions:  
            member: false  
            admin: true
```

* **List Objects Tests:** Validate the expected results when querying which objects a user has a specific relation with (e.g., user:anne is a member of organization:acme). These tests include the user, type of object, context, and assertions.18  Example of List Objects tests:

```yaml
  list_objects:  
    - user: user:anne  
      type: organization  
      assertions:  
        member:  
          - organization:acme  
        admin:

    - user: user:peter  
      type: organization  
      context:  
        current_time : "2024-02-01T00:10:00Z"  
      assertions:  
        member:  
        admin:  
          - organization:acme
```

* **List Users Tests:** Confirm which users possess access to a given object. These tests specify the object, a user_filter (by type or userset), context, and assertions for the users for any number of relations. The users field within assertions supports specific syntax for different userset types: `<type\>:<id\>` for a user, `<type\>:<id\>#<relation\>` for a relation on a type, and `<type\>:*` for public access.

Example of List Users tests:
```yaml
  list_users:  
    - object: organization:acme  
      user_filter:  
        - type: user  
      context:  
        current_time : "2024-02-02T00:10:00Z"  
      assertions:  
        member:  
          users:  
            - user:anne  
        admin:  
          users:
```

.fga.yaml format actively promotes a test-driven development methodology for authorization logic.

## **8. Modeling Custom Roles**

Many applications require the flexibility for end-users to define their own custom roles, in addition to any pre-defined roles. This approach enables organizations to tailor permissions to their specific needs.

Only consider this if you are asked to implement custom roles.

### Simple User-Defined Roles

With the following model, your application can support both static roles and user-defined roles, with user-defined roles at the top_level object (in this case 'organization').

```dsl.openfga
model
  schema 1.1

type user

type role
  relations
    define assignee: [user]

type organization
  relations
    define admin: [user]  # static role

    # permissions can be assigned to custom roles or static roles
    define can_create_projectexport: [role#assignee] or admin 
    define can_edit_project: [role#assignee] or admin 
```

#### Setting Up Custom Roles

1. **Define role permissions** by creating tuples that grant the role-specific permissions:

```yaml
- user: role:acme-project-admin#assignee
  relation: can_create_project
  object: organization:acme

- user: role:acme-project-admin#assignee
  relation: can_edit_project
  object: organization:acme
```

2. **Assign users to the role**:

```yaml
- user: user:anne
  relation: assignee
  object: role:acme-project-admin
```

#### Adding New Permissions

When you add new permissions to your model, existing roles don't automatically receive them:

```dsl.openfga
model
  schema 1.1

type user

type role
  relations
    define assignee: [user]

type organization
  relations
    define admin: [user]
    define can_create_project: [role#assignee] or admin 
    define can_edit_project: [role#assignee] or admin 
    define can_delete_project: [role#assignee] or admin  # new permission
```

To grant the new permission to existing roles, create additional tuples:

```yaml
- user: role:acme-project-admin#assignee
  relation: can_delete_project
  object: organization:acme
```

You do not need to add these tuples when adding the new permission. End-users will add the new permission to their custom roles when they find it appropriate.

### Custom roles with role Assignments

The previous approach works well when custom roles are global for the organization. However, if you need roles that can be attached to different object instances with different members for each instance, you need role assignments. DO NOT USE this approach for defining custom roles for the top-level type (e.g. 'organization').

#### Example: Project-Specific Admin Roles

Let's say you want a "Project Admin" role where each project can have different admins, but the role permissions remain consistent.

##### Step 1: Define the Role and its Permissions

Define a `role` type where you list all the permissions that any role can have:

```dsl.openfga
model
  schema 1.1

type role
  relations
    define can_view_project: [user:*]
    define can_edit_project: [user:*]
```

A "Project Admin" role can have `can_view_project` and `can_edit_project`:

```yaml
# Project Admin role has both the can_view_project and can_edit_project assigned
- user: user:*
  relation: can_view_project
  object: role:project-admin

- user: user:*
  relation: can_edit_project
  object: role:project-admin
```

##### Step 2: Assign Users to a Role on an Entity

Add a `role_assignment` type to assign users to the role:

```dsl.openfga
type role_assignment
  relations
    define assignee: [user]
    define role: [role]

    define can_view_project: assignee and can_view_project from role
    define can_edit_project: assignee and can_edit_project from role
```

##### Step 3: Connect to Your Objects

Define an `organization` type with an `admin` role. Then, define a `project` type that links to an `organization` and a `role_assignment`. Note that we are combining a static `admin` role with custom role assignments. We recommend to always use static roles when they are known in advance.

```dsl.openfga
type organization 
  relations
    define admin: [user]

type project
  relations
    define organization: [organization]
    define role_assignment: [role_assignment]
    
    # combine role assignments and static roles
    define can_edit_project: can_edit_project from role_assignment or admin from organization
    define can_view_project: can_view_project from role_assignment or admin from organization
```

#### Setting Up Role Assignments

1. **Create the role assignment instance**:

```yaml
- user: user:anne
  relation: assignee
  object: role_assignment:project-admin-openfga

- user: role:project-admin  
  relation: role
  object: role_assignment:project-admin-openfga
```

2. **Link the role assignment to the project**:

```yaml
- user: role_assignment:project-admin-openfga
  relation: role_assignment
  object: project:openfga
```
3. **Link the project to an organization**:

```yaml
- user: organization:acme
  relation: organization
  object: project:openfga
```
---

### **When to Use Each Custom Role Pattern**

| Pattern | Use Case | Pros | Cons |
|---------|----------|------|------|
| **Global Custom Roles** | Organization-wide roles with consistent permissions | Simple, efficient | Less flexible for per-resource customization |
| **Role Assignments** | Resource-specific roles with different members per resource | Highly flexible | More complex, potential performance impact |

### **Migration Strategies**
When evolving from static roles to custom roles:
1. **Additive approach**: Introduce custom roles alongside existing static roles
2. **Gradual migration**: Move permissions one at a time to custom roles
3. **Backwards compatibility**: Maintain existing static role behavior during transition


## **9. Testing Models
Always run the `fga model test --tests <.fga.yaml>` command to ensure your model and tests are behaving as expected.

## **10. Simplify Models

After generating model and tests, remove from the model all types and relations that are not referenced in the model or the tests.

