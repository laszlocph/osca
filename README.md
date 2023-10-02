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
    opensca.dev/v0.1.0/name: cart-backend
    opensca.dev/v0.1.0/description: "Backend to manage shopping cart state, written in Go"
    opensca.dev/v0.1.0/vcs/owner: "octocat"
    opensca.dev/v0.1.0/vcs/name: "Hello-World"
    opensca.dev/v0.1.0/version/sha: 7fd1a60b01f91b314f59955a4e4d4e80d8edf11d
    opensca.dev/v0.1.0/owner/name: "backend-team"
    opensca.dev/v0.1.0/owner/im: "#backend-team"
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
| opensca.dev/v0.1.0/name        | The service name for unique identification     | `cart-backend`                                         | [RFC 1035](https://tools.ietf.org/html/rfc1035) Label Names <br>- contain at most 63 characters <br>- contain only lowercase alphanumeric characters or '-'<br>- start with an alphabetic character<br>- end with an alphanumeric character |
| opensca.dev/v0.1.0/description | Human readable text description of the service | `Backend to manage shopping cart state, written in Go` | unstructured                                                                                                                                                                                                                                |

### Version control

| Annotation                   | Description                                                        | Example                                                                                  | Format                                                    |
| ---------------------------- | ------------------------------------------------------------------ | ---------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| opensca.dev/v0.1.0/vcs/host     | Host name of the version control system                            | `github.com`                                                                             | host name without protocol                                |
| opensca.dev/v0.1.0/vcs/owner    | Onwer user/organization/group handle in the version control system | `octocat`                                                                                | any valid owner in the version control system             |
| opensca.dev/v0.1.0/vcs/name     | Repository name in the version control system                      | `Hello-World`                                                                            | any valid repository name in the version control system   |
| opensca.dev/v0.1.0/vcs/link     |                                                                    | `https://github.com/octocat/Hello-World`                                                 | full link of the repository                               |
| opensca.dev/v0.1.0/version/sha  |                                                                    | `7fd1a60b`                                                                               | a valid git hash identifier in the version control system |
| opensca.dev/v0.1.0/version/tag  |                                                                    | `v1.0.0`                                                                                 | a valid tag identifier in the version control system      |
| opensca.dev/v0.1.0/version/link |                                                                    | `https://github.com/octocat/Hello-World/commit/7fd1a60b01f91b314f59955a4e4d4e80d8edf11d` | full link to the version                                  |

### External references

| Annotation                    | Description                                                                                         | Example                                         | Format         |
| ----------------------------- | --------------------------------------------------------------------------------------------------- | ----------------------------------------------- | -------------- |
| opensca.dev/v0.1.0/documentation | Link to documentation. The link's preferred usage is in a `href` html tag.                          | `https://confluence.mycompany.com/cart-backend` | any valid link |
| opensca.dev/v0.1.0/logs          | Link to log aggregation platform. The link's preferred usage is in a `href` html tag.               | `https://grafana.mycompany.com/logs`            | any valid link |
| opensca.dev/v0.1.0/metrics       | Link to metrics dashboard. The link's preferred usage is in a `href` html tag.                      | `https://grafana.mycompany.com/cart-dashboard`  | any valid link |
| opensca.dev/v0.1.0/issues        | Link to issue tracker. The link's preferred usage is in a `href` html tag.                          | `https://jira.mycompany.com/cart-backend`       | any valid link |
| opensca.dev/v0.1.0/ci            | Link to the CI pipeline run that built the app. The link's preferred usage is in a `href` html tag. | `https://github.com/laszlocph/osca/actions`     | any valid link |
| opensca.dev/v0.1.0/traces        | Link to application traces. The link's preferred usage is in a `href` html tag.                     |                                                 | any valid link |
| opensca.dev/v0.1.0/runbooks      | Link to runbooks. The link's preferred usage is in a `href` html tag.                               |                                                 | any valid link |
| opensca.dev/v0.1.0/dev-portal    | Link to general information about the service. The link's preferred usage is in a `href` html tag.  |                                                 | any valid link |

### Service ownership

| Annotation                  | Description                                                      | Example                     | Format                                                        |
| --------------------------- | ---------------------------------------------------------------- | --------------------------- | ------------------------------------------------------------- |
| opensca.dev/v0.1.0/owner/name  | The person or team who is responsible for this service           | `Jane Doe`, `Platform team` | unstructured                                                  |
| opensca.dev/v0.1.0/owner/im    | Instant messaging channel, or direct message handle of the owner | `@jane`, `#platform-team`   | prefix with # for channels, prefix with @ for direct messages |
| opensca.dev/v0.1.0/owner/email | e-mail address of the owner                                      | `jane@coolcompany.com`      | A valid email address                                         |

## Conformity

Parties can adopt the OSCA taxonomy in part or in full. No requirement is imposed on the adopter in the completeness sense, but the adopted taxonomy elements must not be misused deliberately.

## Curation process

- Contributing new taxonomy fields happen in pull requests.
- The approach towards new taxonomy additions is generous. If the proposal follows the pull request template and the proposer is available to make her case, it is likely that OSCA adopts the suggestion.
- OSCA uses semver in the strictest form.

## Prior work

- OSCA was inspired by https://ambassadorlabs.github.io/k8s-for-humans/
- Komodor uses a similar approach to link deployed services to git repositories https://docs.komodor.com/Learn/komodor-source-control.html
- [Giant Swarm annotations](https://github.com/giantswarm/fmt/blob/master/kubernetes/annotations_and_labels.md#labels-set-on-objects-installed-by-charts)
- [Openshift](https://github.com/redhat-developer/app-labels/blob/master/labels-annotation-for-openshift.adoc#annotations)
- [ArgoCD's external URL feature](https://argo-cd.readthedocs.io/en/stable/user-guide/external-url/)
