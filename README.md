# Waypoint Promise

This Promise provides [Waypoint](https://www.waypointproject.io/) access as-a-Service. The Promise installs a Waypoint server which then can vend invite tokens through Resource Requests.

The Promise has 1 field:
- `.spec.username`: This is the username that will be provided an invite token for access to Waypoint

Check the CRD documentation for more information.

To install:

> **Warning**
> 
> **By default Waypoint requires a LoadBalancer Service type**
> 
> **If you are running [KinD](https://kind.sigs.k8s.io/docs/user/quick-start/) or any other cluster without support by default, please either:**
> 1. **Follow cluster software instructions to install LoadBalancers (e.g. [here](https://kind.sigs.k8s.io/docs/user/loadbalancer/))**
> 2. **Create your KinD cluster and this Promise to use NodePort type Services (see: [Developer Readme](./internal/README.md#switch-to-nodeport))**

```
kubectl apply -f https://raw.githubusercontent.com/syntasso/kratix-marketplace/main/waypoint/promise.yaml
```

To verify installation of the service, you should see two healhty statefulsets:
```
$ kubectl get statefulsets.apps -n waypoint
NAME              READY   AGE
waypoint-runner   1/1     10m
waypoint-server   1/1     10m
```

And you should be able to go to the `waypoint-ui` service. If you used a loadbalancer (option 1 above), you can get the IP address using:
```
kubectl get service -n waypoint waypoint-ui -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

If you instead configured your KinD cluster with a NodePort (option 2 above), you can immediately go to `https://localhost:30002`.

To get started with waypoint, you will want to introduce users. You can do this by making a resource request:
```
kubectl apply -f https://raw.githubusercontent.com/syntasso/kratix-marketplace/main/waypoint/resource-request.yaml
```

You can verify this by seeing:
```
$ kubectl get jobs --namespace waypoint                                                                  
NAME                               COMPLETIONS   DURATION   AGE
anne-token-generator               1/1           7s         70m
...
```

And the resulting secret:
```
kubectl get secrets --namespace waypoint waypoint-token-annedeveloper -o jsonpath='{.data.token}'
```

You can then use this secret via the UI if you select `Received an invite? Redeem invite` link.

You are now able to authenticate through either the CLI or the UI to use Waypoint. For more details on how to use Waypoint to build and deploy your applciations, please see their [documentation and tutorials](https://developer.hashicorp.com/waypoint/docs/getting-started).

## Development

For development see [README.md](./internal/README.md)

## Questions? Feedback?

We are always looking for ways to improve Kratix and the Marketplace. If you run into issues or have ideas for us, please let us know. Feel free to [open an issue](https://github.com/syntasso/kratix-marketplace/issues/new/choose) or [put time on our calendar](https://www.syntasso.io/contact-us). We'd love to hear from you.
