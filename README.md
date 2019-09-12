# Upgrade-Kubernetes-Clusters
This note shows you how to in-place upgrade your Kubernetes cluster to the latest version. 

I finished upgrade my Kubernetes cluster from 1.9.x to the latest one 1.14.6. Following https://docs.microsoft.com/en-us/azure/aks/upgrade-cluster , I upgraded from 1.9->1.10->1.11->1.12->1.13->1.14.6. Yes. I did FIVE upgrades to bring the cluster to the lasted version.

Before and after each upgrade you can test your web services deployed and they should work as before, and the URL should be unchanged. For me, I have the following stuff deployed:

C:\Users\xinxue>kubectl get service

| NAME                  | TYPE           | CLUSTER-IP    | EXTERNAL-IP    | PORT(S)                      |
|:---------------------:|:--------------:|:-------------:|:--------------:|:----------------------------:|
| area-calc             | NodePort       | 10.0.41.222   | <none>         | 80:31353/TCP                 | 
| azureml-fe            | LoadBalancer   | 10.0.31.83    | 13.77.70.202   | 80:31018/TCP,443:32187/TCP   | 
| azureml-fe-int-http   | ClusterIP      | 10.0.205.53   | <none>         | 9001/TCP                     | 
| jackrwebservice       | LoadBalancer   | 10.0.42.136   | 40.84.20.166   | 80:31171/TCP                 | 
| kubernetes            | ClusterIP      | 10.0.0.1      | <none>         | 443/TCP                      | 
| mnist-svc             | NodePort       | 10.0.15.103   | <none>         | 80:31773/TCP                 | 

For all the services having external IPs, I should be able to test them. For example, hitting url http://40.84.20.166/weight?height=100, I see the response:

```json
{
  "response":[181.3303]
}
```


Per Marc Wolfson, behind scene, we basically spin up a new node with the target version then drain one of the older nodes and let aks re deploy the containers that were running on the mode that was drained.
Depending on the number of containers and nodes aks can get pretty busy moving things around during the upgrade.

For me: a one-node cluster and just three web services, each upgrade runs about 5 minutes.
