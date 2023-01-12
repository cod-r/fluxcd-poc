Flux advantages:
- Operators oriented
- Permissions use native k8s RBAC
- Easier cluster bootstrapping

Flux disadvantages:
- UI is a plugin and has guarded features behind enterprise license
- You need access to the k8s cluster


Argo advantages:
- Developer experience oriented
    - nicer UI
    - view container logs
    - exec into terminal for pods
- Multi cluster management
- Has its own user management
- Developers need access only to argocd UI
- Higher development velocity in github stats

Argo disadvantages:
- Installing helm charts is better in Flux