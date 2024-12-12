Quick reference on kubectl

kubectl api-resources

kubectl api-resources --namespaced=true      # All namespaced resources

kubectl api-resources --namespaced=false     # All non-namespaced resources

kubectl api-resources -o name                # All resources with simple output (only the resource name)

kubectl api-resources -o wide                # All resources with expanded (aka "wide") output

kubectl api-resources --verbs=list,get       # All resources that support the "list" and "get" request verbs

kubectl api-resources --api-group=extensions # All resources in the "extensions" API group

# To get verbs for a specific resource

kubectl api-resources -o wide | grep pv 
