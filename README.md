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
    v1alpha1.opensca.dev/name: cart-backend
    v1alpha1.opensca.dev/description: "Backend to manage shopping cart state, written in Go"
    v1alpha1.opensca.dev/vcs.owner: "octocat"
    v1alpha1.opensca.dev/vcs.name: "Hello-World"
    v1alpha1.opensca.dev/version.sha: 7fd1a60b01f91b314f59955a4e4d4e80d8edf11d
    v1alpha1.opensca.dev/owner.name: "backend-team"
    v1alpha1.opensca.dev/owner.im: "#backend-team"
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
| v1alpha1.opensca.dev/name        | The service name for unique identification     | `cart-backend`                                         | [RFC 1035](https://tools.ietf.org/html/rfc1035) Label Names <br>- contain at most 63 characters <br>- contain only lowercase alphanumeric characters or '-'<br>- start with an alphabetic character<br>- end with an alphanumeric character |
| v1alpha1.opensca.dev/description | Human readable text description of the service | `Backend to manage shopping cart state, written in Go` | unstructured                                                                                                                                                                                                                                |

### Version control

| Annotation                   | Description                                                        | Example                                                                                  | Format                                                    |
| ---------------------------- | ------------------------------------------------------------------ | ---------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| v1alpha1.opensca.dev/vcs/host     | Host name of the version control system                            | `github.com`                                                                             | host name without protocol                                |
| v1alpha1.opensca.dev/vcs.owner    | Onwer user/organization/group handle in the version control system | `octocat`                                                                                | any valid owner in the version control system             |
| v1alpha1.opensca.dev/vcs.name     | Repository name in the version control system                      | `Hello-World`                                                                            | any valid repository name in the version control system   |
| v1alpha1.opensca.dev/vcs.link     |                                                                    | `https://github.com/octocat/Hello-World`                                                 | full link of the repository                               |
| v1alpha1.opensca.dev/version.sha  |                                                                    | `7fd1a60b`                                                                               | a valid git hash identifier in the version control system |
| v1alpha1.opensca.dev/version.tag  |                                                                    | `v1.0.0`                                                                                 | a valid tag identifier in the version control system      |
| v1alpha1.opensca.dev/version.link |                                                                    | `https://github.com/octocat/Hello-World/commit/7fd1a60b01f91b314f59955a4e4d4e80d8edf11d` | full link to the version                                  |

### External references

| Annotation                    | Description                                                                                         | Example                                         | Format         |
| ----------------------------- | --------------------------------------------------------------------------------------------------- | ----------------------------------------------- | -------------- |
| v1alpha1.opensca.dev/documentation | Link to documentation. The link's preferred usage is in a `href` html tag.                          | `https://confluence.mycompany.com/cart-backend` | any valid link |
| v1alpha1.opensca.dev/logs          | Link to log aggregation platform. The link's preferred usage is in a `href` html tag.               | `https://grafana.mycompany.com/logs`            | any valid link |
| v1alpha1.opensca.dev/metrics       | Link to metrics dashboard. The link's preferred usage is in a `href` html tag.                      | `https://grafana.mycompany.com/cart-dashboard`  | any valid link |
| v1alpha1.opensca.dev/issues        | Link to issue tracker. The link's preferred usage is in a `href` html tag.                          | `https://jira.mycompany.com/cart-backend`       | any valid link |
| v1alpha1.opensca.dev/ci            | Link to the CI pipeline run that built the app. The link's preferred usage is in a `href` html tag. | `https://github.com/laszlocph/osca/actions`     | any valid link |
| v1alpha1.opensca.dev/traces        | Link to application traces. The link's preferred usage is in a `href` html tag.                     |                                                 | any valid link |
| v1alpha1.opensca.dev/runbooks      | Link to runbooks. The link's preferred usage is in a `href` html tag.                               |                                                 | any valid link |
| v1alpha1.opensca.dev/dev-portal    | Link to general information about the service. The link's preferred usage is in a `href` html tag.  |                                                 | any valid link |

### Service ownership

| Annotation                  | Description                                                      | Example                     | Format                                                        |
| --------------------------- | ---------------------------------------------------------------- | --------------------------- | ------------------------------------------------------------- |
| v1alpha1.opensca.dev/owner.name  | The person or team who is responsible for this service           | `Jane Doe`, `Platform team` | unstructured                                                  |
| v1alpha1.opensca.dev/owner.im    | Instant messaging channel, or direct message handle of the owner | `@jane`, `#platform-team`   | prefix with # for channels, prefix with @ for direct messages |
| v1alpha1.opensca.dev/owner.email | e-mail address of the owner                                      | `jane@coolcompany.com`      | A valid email address                                         |

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
