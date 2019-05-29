---
layout: post
title:  "Operator SDK overview"
categories: Kubernetes
tags:  Kubernetes Operator
---

* content
{:toc}

## Background

Operator concept is introduced to k8s community at [2016](https://coreos.com/blog/introducing-operators.html), 
> An Operator is an application-specific controller that extends the Kubernetes API to create, configure, and manage instances of complex stateful applications on behalf of a Kubernetes user. It builds upon the basic Kubernetes resource and controller concepts but includes domain or application-specific knowledge to automate common tasks.

[operator-sdk](https://github.com/operator-framework/operator-sdk) is a framework to make writing operators easier. Which provides:
>  * High level APIs and abstractions to write the operational logic more intuitively
>  * Tools for scaffolding and code generation to bootstrap a new project fast
>  * Extensions to cover common operator use cases

## SDK CLI installation and app-operator example
Follow the guide from "Quick Start": https://github.com/operator-framework/operator-sdk#quick-start



## Project Scaffolding Layout 

The `operator-sdk` CLI generates a number of packages for each project. The following table describes a basic rundown of each generated file/directory.


| File/Folders   | Purpose                           |
| :---           | :--- |
| cmd       | Contains `manager/main.go` which is the main program of the operator. This instantiates a new manager which registers all custom resource definitions under `pkg/apis/...` and starts all controllers under `pkg/controllers/...`  . |
| pkg/apis | Contains the directory tree that defines the APIs of the Custom Resource Definitions(CRD). Users are expected to edit the `pkg/apis/<group>/<version>/<kind>_types.go` files to define the API for each resource type and import these packages in their controllers to watch for these resource types.|
| pkg/controller | This pkg contains the controller implementations. Users are expected to edit the `pkg/controller/<kind>/<kind>_controller.go` to define the controller's reconcile logic for handling a resource type of the specified `kind`. |
| build | Contains the `Dockerfile` and build scripts used to build the operator. |
| deploy | Contains various YAML manifests for registering CRDs, setting up [RBAC][RBAC], and deploying the operator as a Deployment.
| (Gopkg.toml Gopkg.lock) or (go.mod go.sum) | The [Go mod][go_mod] or [Go Dep][dep] manifests that describe the external dependencies of this operator, depending on the dependency manager chosen when initializing or migrating a project. |
| vendor | The golang [vendor][Vendor] folder that contains the local copies of the external dependencies that satisfy the imports of this project. [Go Dep][dep]/[Go modules][go_mod] manages the vendor directly. |

[RBAC]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[Vendor]: https://golang.org/cmd/go/#hdr-Vendor_Directories
[go_mod]: https://github.com/golang/go/wiki/Modules
[dep]: https://github.com/golang/dep

## Example app-operator code layout

The code layout of example "app-operator"

![alt text](/assets/app-operator-layout.png)

Operator SDK generates lots of files/directories for developers, but most of the generated files/directories are not expected to edit. 

Developers should focus on `pkg/apis/<group>/<version>/<kind>_types.go` and `pkg/controller/<kind>/<kind>_controller.go`. 

Edit `pkg/apis/<group>/<version>/<kind>_types.go` to define the custom resource type, and edit `pkg/controller/<kind>/<kind>_controller.go` to define reconcile logic for custom resource. 

Note: After modifying the `pkg/apis/<group>/<version>/<kind>_types.go`, developer should always run the following command to update the generated code for that resource type:
```sh
$ operator-sdk generate k8s
```

## Reference link
* https://coreos.com/blog/introducing-operators.html
* https://github.com/operator-framework/operator-sdk#quick-start
* https://github.com/operator-framework/operator-sdk/blob/master/doc/user-guide.md
* https://github.com/operator-framework/operator-sdk/blob/master/doc/project_layout.md