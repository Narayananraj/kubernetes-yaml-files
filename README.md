# Top 50 kubectl Commands — Daily Reference

Grouped by use case, in the order you'd typically reach for them.

## Cluster & Context

1. `kubectl cluster-info` — show control plane and DNS service endpoints
2. `kubectl config get-contexts` — list all available contexts
3. `kubectl config current-context` — show the active context
4. `kubectl config use-context <name>` — switch cluster context
5. `kubectl config set-context --current --namespace=<ns>` — set default namespace for current context

## Get & Inspect

6. `kubectl get pods -A` — list all Pods across all namespaces
7. `kubectl get pods -o wide` — list Pods with node and IP info
8. `kubectl get pods -w` — watch Pods live as state changes
9. `kubectl get all -n <namespace>` — list all common resources in a namespace
10. `kubectl get pods -l app=<label>` — filter Pods by label
11. `kubectl get pods --field-selector status.phase=Running` — filter by field
12. `kubectl get nodes -o wide` — list nodes with internal/external IPs
13. `kubectl api-resources` — list every resource type the cluster supports
14. `kubectl explain <resource>.<field> --recursive` — inline field documentation

## Describe & Logs

15. `kubectl describe pod <name>` — full details + Events for a Pod
16. `kubectl describe node <name>` — node conditions, capacity, allocated resources
17. `kubectl logs <pod>` — current container logs
18. `kubectl logs <pod> -c <container>` — logs for a specific container in a multi-container Pod
19. `kubectl logs <pod> --previous` — logs from the last crashed instance (CrashLoopBackOff debugging)
20. `kubectl logs <pod> -f` — stream/follow logs live
21. `kubectl logs -l app=<label> --all-containers=true --prefix` — logs from every Pod matching a label

## Apply, Edit, Delete

22. `kubectl apply -f <file.yaml>` — create or update from a manifest
23. `kubectl apply -f ./manifests/` — apply every file in a directory
24. `kubectl apply -k <overlay-dir>/` — apply via Kustomize
25. `kubectl delete -f <file.yaml>` — delete resources defined in a manifest
26. `kubectl delete pod <name> --grace-period=0 --force` — force-delete a stuck Pod
27. `kubectl edit deployment <name>` — live-edit a resource in $EDITOR
28. `kubectl diff -f <file.yaml>` — preview changes before applying

## Scaling & Rollouts

29. `kubectl scale deployment <name> --replicas=5` — manually scale
30. `kubectl autoscale deployment <name> --min=2 --max=10 --cpu-percent=70` — quick HPA creation
31. `kubectl rollout status deployment/<name>` — watch rollout progress
32. `kubectl rollout history deployment/<name>` — list revisions
33. `kubectl rollout undo deployment/<name>` — rollback to previous revision
34. `kubectl rollout undo deployment/<name> --to-revision=<N>` — rollback to a specific revision
35. `kubectl rollout restart deployment/<name>` — force fresh Pods (picks up ConfigMap/Secret changes)
36. `kubectl set image deployment/<name> <container>=<image>:<tag>` — update image without editing YAML

## Exec, Port-Forward, Copy

37. `kubectl exec -it <pod> -- /bin/sh` — open a shell inside a container
38. `kubectl port-forward svc/<name> 8080:80` — forward a local port to a Service
39. `kubectl cp <pod>:/path/to/file ./local-file` — copy a file out of a container
40. `kubectl debug -it <pod> --image=busybox:1.36 --target=<container>` — attach a debug container to a running Pod (for shell-less/distroless images)
41. `kubectl run tmp-pod --image=busybox:1.36 --rm -it --restart=Never -- sh` — spin up a disposable debug Pod

## Debugging & Events

42. `kubectl get events --sort-by='.lastTimestamp' -A` — all recent events, chronological
43. `kubectl get events --field-selector type=Warning -A` — only warnings, fastest triage
44. `kubectl top nodes` — live CPU/memory usage per node
45. `kubectl top pods` — live CPU/memory usage per Pod

## RBAC & Auth

46. `kubectl auth can-i <verb> <resource> --as=<user-or-sa>` — simulate a permission check

## Scaffolding YAML (dry-run technique)

47. `kubectl create deployment <name> --image=<image> --replicas=3 --dry-run=client -o yaml` — generate boilerplate instead of hand-typing
48. `kubectl expose deployment <name> --port=80 --target-port=8080 --dry-run=client -o yaml` — generate a Service manifest

## Node Management

49. `kubectl drain <node> --ignore-daemonsets --delete-emptydir-data` — safely evict Pods before maintenance
50. `kubectl cordon <node>` / `kubectl uncordon <node>` — mark a node unschedulable / schedulable again

---

## The "something's wrong" sequence (memorize this order)

```bash
kubectl get pods -A | grep -v Running
kubectl describe pod <broken-pod>
kubectl logs <broken-pod> --previous
kubectl get events --sort-by='.lastTimestamp' -n <namespace> | tail -20
kubectl get endpoints <related-svc>     # connectivity issue
kubectl top pods                         # performance/OOM issue
```
