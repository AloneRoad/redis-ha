# Redis HA

This is a customized Redis image determines whether the pod executes it will be a Redis Sentinel, Master, or Slave and launches the appropriate service. This Helm chart signals Sentinel status with environment variables. If not set, the newly launched pod will query K8S for an active master. If none exists, it uses a deterministic means of sensing whether it should launch as master then writes "master" or "slave" to the label called redis-role as appropriate. It's this label that determines which LB a pod can be seen through.

The redis-role=master pod is the key for the cluster to get started. Sentinels will wait for it to appear in the LB before they finish launching. All other pods wait for the Sentinels to ID the master. Running Pods also set the labels podIP and runID. runID is the first few characters of the unique run_id value generated by each Redis sever.

During normal operation, there should be only one redis-role=master pod. If it fails, the Sentinels will nominate a new master and change all the redis-role values appropriately.

To see the pod roles, run the following:

```bash
$ kubectl get pods -L redis-role
```

## License

The code in this repository, unless otherwise noted, is BSD licensed. See the `LICENSE` file in this repository.