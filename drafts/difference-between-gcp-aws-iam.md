---
title: Difference between GCP IAM and AWS IAM
created: 2020-02-28T13:48:11+05:30
author: ashishdoneriya
description: This article will teach you how to create your own static site generator from scratch in any language
permalink: /difference-between-gcp-aws-iam.html
categories:
  - big-data
tags:
  - gcp
  - aws
---

### GCP Roles

* In GCP, **a role is collection of permissions**.

* We cannot assign direct permissions to user instead we can assign role to a user, so we can grant all the permissions that the role contain.

#### Types of Roles

* Primitive Roles (Owner, Editor, Viewer)
* Predefined Roles (More granular level access)
* Custom Roles (As per user requirement)

##### Primitive Roles

* **Viewer** - View Data (read-only action)
* **Editor** - Contain all Viewer permissions and can change the things (modify gcp resources)
* **Owner** - Viewer + Editor. Can manage the GCP project with all the resources in GCP and setup billing for project.

### IAM Policy

**Who(identity)** can do **what (role)** to **which things (resources)**.

<iframe width="560" height="315" src="https://www.youtube.com/embed/vaylQUuIANk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Difference between AWS & GCP IAM



| Concept                       | AWS                                                    | Google Cloud                                                 |
| ----------------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| Permission collection         | Policy                                                 | Role                                                         |
| Predefined set of permissions | Managed policies                                       | Predefined roles                                             |
| Policy                        | A document that explicitly lists permissions.          | A list of bindings. A binding binds a list of members to a role. |
| Policy attachment             | Policy attached to an IAM user or group or a resource. | Policy attached to resource.                                 |

### Roles in AWS

In GCP Role is a bundle of permission while in AWS it is not a thing but it is a way to grant permissions to entities like (other iam users or code in ec2 instance or an aws service). In AWS a bundle of permissions is called policy.


<iframe width="560" height="315" src="https://www.youtube.com/embed/_S-x4ZIYHkQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
