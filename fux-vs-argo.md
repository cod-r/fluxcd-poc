Flux advantages:
- Operators oriented
- Easier cluster bootstrapping
- Permissions use native k8s RBAC

Flux disadvantages:
- UI is a plugin and has guarded features behind enterprise license
- You need kubectl for debugging


Argo advantages:
- Developer experience oriented
    - nicer UI
    - view container logs
    - exec into terminal for pods
- Multi cluster management
- Developers need access only to argocd UI
- Higher development velocity in github

Argo disadvantages:
- Installing helm charts is better in Flux
- Server-side apply is incomplete