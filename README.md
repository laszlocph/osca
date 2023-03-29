# OSCA - Open Service Catalog Annotations

This is a design document for using Kubernetes annotations to store metadata alongside deployed services.

## Goal

The goal is to curate an industry wide taxonomy that can serve as the basis of service catalog implementations.

## Scope

The metadata can range across all aspects of the software delivery lifecycle:

- Service owner: person, team, company
- Service owner contact information: email, IM group, etc
- Version control information: git url, owner, repository
- Observability references: links to external sites, dashboards
- Service dependencies, interrelations

## Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: cart-backend
  name: cart-backend
  annotations:
    osca.dev/v0.1.0/name: cart-backend
    osca.dev/v0.1.0/description: "Backend to manage shopping cart state, written in Go"
    osca.dev/v0.1.0/owner-and-repository: "octocat/Hello-World"
    osca.dev/v0.0.0/sha: 7fd1a60b01f91b314f59955a4e4d4e80d8edf11d
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cart-backend
  template:
    metadata:
      labels:
        app: cart-backend
    spec:
      containers:
        - image: nginx:latest
          name: nginx
```

## Taxonomy

### Service information

| Annotation                  | Description                                    | Example                                                | Format                                                                                                                                                                                                                                      |
| --------------------------- | ---------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| osca.dev/v0.0.0/name        | The service name for unique identification     | `cart-backend`                                         | [RFC 1035](https://tools.ietf.org/html/rfc1035) Label Names <br>- contain at most 63 characters <br>- contain only lowercase alphanumeric characters or '-'<br>- start with an alphabetic character<br>- end with an alphanumeric character |
| osca.dev/v0.0.0/description | Human readable text description of the service | `Backend to manage shopping cart state, written in Go` | unstructured                                                                                                                                                                                                                                |

### Version control

| Annotation                           | Description                                                        | Example                                  | Format                                                    |
| ------------------------------------ | ------------------------------------------------------------------ | ---------------------------------------- | --------------------------------------------------------- |
| osca.dev/v0.0.0/vcs                  | Host name of the version control system                            | `github.com`                             | host name without protocol                                |
| osca.dev/v0.0.0/owner                | Onwer user/organization/group handle in the version control system | `octocat`                                | any valid owner in the version control system             |
| osca.dev/v0.0.0/repository           | Repository name in the version control system                      | `Hello-World`                            | any valid repository name in the version control system   |
| osca.dev/v0.0.0/owner-and-repository | right-aligned                                                      | `octocat/Hello-World`                    | owner and repository separated with a slash               |
| osca.dev/v0.0.0/repository-link      | right-aligned                                                      | `https://github.com/octocat/Hello-World` | full link of the repository                               |
| osca.dev/v0.0.0/sha                  | right-aligned                                                      | `7fd1a60b`                               | a valid git hash identifier in the version control system |
| osca.dev/v0.0.0/tag                  | right-aligned                                                      | `v1.0.0`                                 | a valid tag identifier in the version control system      |

## Conformity

Parties can adopt the OSCA taxonomy in part or in full. No requirement is imposed on the adopter in the completeness sense, but the adopted taxonomy elements must not be misused deliberately.

## Curation process

- Contributing new taxonomy fields happen in pull requests.
- The approach towards new taxonomy additions is generous. If the proposal follows the pull request template and the proposer is available to make her case, it is likely that OSCA adopts the suggestion.
- OSCA uses semver in the strictest form.

## Prior work

- OSCA was inspired by https://ambassadorlabs.github.io/k8s-for-humans/
- Komodor uses a similar approach to link deployed services to git repositories https://docs.komodor.com/Learn/komodor-source-control.html
- ?
