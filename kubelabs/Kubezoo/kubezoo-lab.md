# Kubezoo Lab

Now that we have covered what Kubezoo is, let's take a look at how we can set it up in a standard cluster. You could go ahead and use [Minikube](https://minikube.sigs.k8s.io/docs/start/), or you could create a cluster using [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation). You can also use any Kubernetes cluster you have at the ready. Let's start by cloning the [KubeZoo repo](https://github.com/kubewharf/kubezoo.git):

```
git clone https://github.com/kubewharf/kubezoo.git
```

Now, go to the root of the repo you just cloned, and run the `make` command:

```
make local-up
```

This will get Kubezoo up and running on port 6443 as long as the port is free. Check to see if the API resources are up and running:

```
kubectl api-resources --context zoo
```

Now, let's create a sample tenant. For this, we will be using the config/setup/sample_tenant.yamlsample_tenant.yaml provided in the repo. If you take a look at the tenant yaml file, you will notice that this is a custom resource of type "tenant", and contains just a few lines specifying the type of resources this tenant requires. The name of the tenant is "111111". Since this is a regular Kubernetes resource, let's go ahead and deploy this tenant as we would a normal yaml:

```
kubectl apply -f config/setup/sample_tenant.yaml --context zoo
```

Check that the tenant is has been setup:

```
kubectl get tenant 111111 --context zoo
```

Since this tenant is basically a "cluster" in itself, it has it's own kubeconfig that gets created for it. You can extract it using:

```
kubectl get tenant 111111 --context zoo -o jsonpath='{.metadata.annotations.kubezoo\.io\/tenant\.kubeconfig\.base64}' | base64 --decode > 111111.kubeconfig
```

You should now be able to deploy all sorts of resources to the tenant by specifying the kubeconfig. For example, if you were to deploy a file called "application.yaml" into the tenant, you would use:

```
kubectl apply -f application.yaml --kubeconfig 111111.kubeconfig
```

You can check the pod as the tenant by specifying the kubeconfig as before:

```
kubectl get po --kubeconfig 111111.kubeconfig
```

The pod would have been created in the namespace that you assigned to the tenant. If you were to have multiple tenants, you would not be able to see the pods of the other tenants as long as you only have the kubeconfig of the tenant that you are dealing with, which allows for better isolation. Using your regular kubeconfig as a cluster admin, if you were to list all pods with `kubectl get po -A`, you would be able to see all the pods of all the tenants separated by namespace.

# Conclusion

This brings us to the end of the section on Kubezoo. Hopefully, by now, you understand what a multi-tenant system is, what the benefits of such a system are, and what possible challenges you could face when using a system. You also know what Kubezoo can do to help alleviate these challenges, specifically when you have constraints such as a smaller development team and a large number of small clients. We also covered a lab on setting up Kubezoo in a kind cluster and deploying the items to the Kubezoo tenant, as well as showing how to interact with multiple tenants as a cluster admin. This covers the basics of Kubezoo. If you want to learn more on the topic, the official Kubezoo [GitHub page](https://github.com/kubewharf/kubezoo) is the best place to start.