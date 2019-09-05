---
title: Component model
---

Before doing any work, the Ekara engine must build a **model of the environment** that is worked on. This model is aggregated from multiple descriptors, located in **components** that must be discovered, fetched et parsed. A component:

* Lives in a source repository which is fetched dynamically when necessary. 
* Can optionally contain a descriptor file, named `ekara.yaml` by default.

{{% notice note %}}
The first component to be fetched is either specified in the command-line (in the case of a new environment) or already known (in the case of an existing environment).
{{% /notice %}}

## Example

Consider the following components:

![Component model](/img/components_order.png)

* The starting point of component discovery is called the **main component (1)**.
* The main component declares a **parent component (2)**.
* The parent itself declares its own parent component, forming an **inheritance chain (3)**.
* At each level in the inheritance chain, additional components can also be **referenced (1b, 2b, 3b)**.

{{% notice tip %}}
Typically, parent components are used to regroup parameters common to set of environments. For instance, multi-level parents can be used to regroup settings in organizational layers.
{{% /notice %}}

## Aggregation

After all components have been discovered, the effective model aggregation occurs:

1. Components referenced by the top-level parent **(3b)** are parsed in their declaration order, defining the initial model.
2. Then the top-level parent **(3)** is merged.
3. Then components referenced by the intermediate parent **(2b)** are merged.
4. Then the intermediate parent **2** is merged.
5. Then components referenced by the main component **(1b)** are merged.
6. Then the main component **(1)** is merged.

Each step can extend or override values defined in previous ones.

## Declaration

To declare a component (parent or referenced), use the following attributes:

* A `repository` attribute denoting the source repository holding component code.
* A `ref` attribute denoting the branch, tag or commit of the component to fetch.
* An `auth` attribute only required when authentication is required to access the source repository.

### Repository

The `repository` attribute allows to specify the location of a component:

```yaml
ekara:
  components:
    aPublicComponent:
      repository: https://github.com/myOrganisation/my-public-rep
```

{{% notice warning %}}
Currently `git` is the only source control management system supported for repositories.
{{% /notice %}}

### Ref

The `ref` attibutes identifies the branch, tag or commit to fetch:

```yaml
components:
  aPublicComponent:
    repository: https://github.com/myOrganisation/my-public-rep
    ref: aGivenBranch  
```

When a `ref` attribute is specified, Ekara will:

1. Try to find a matching **tag**. 
2. If no tag is found, it will try to find a matching **branch**.
3. If no branch is found, it will try to find a matching **commit**.

{{% notice tip %}}
The default value for `ref` is `master`.
{{% /notice %}}

### Authentication

The `auth` attribute specifies the authentication information required to connect to a private repository.

Example: 
```yaml
ekara:
  components:
    aPrivateComponent:
      repository: https://github.com/myOrganisation/my-private-rep
      auth:
        method: basic
        user: yourUserName
        password: yourUserPassword
```

{{% notice warning %}}
Currently `basic` is the only authentication method supported.
{{% /notice %}}

{{% notice tip %}}
You can externalize data like credentials from descriptors by using [templating]({{< ref "templating.md" >}}).
{{% /notice %}}
