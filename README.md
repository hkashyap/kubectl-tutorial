# kubectl-tutorial
A brief tutorial on using kubectl tool to run applications on kubernetes clusters.

## Set up to start using kubectl on CHASE-CI clusters:

1. Install kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl/. To know more about it, the documentation is available [here](https://kubernetes.io/docs/reference/kubectl/overview/).
2. Gain access to CHASE-CI cluster. Go to http://nautilus.optiputer.net
   - Click on login (upper right corner)
   - Choose UCI (or your institution) from the list and use your credentials. Now you should have user access.
   - Click the "Get Config" link on top right and get your config file.
   - Put the file to your $home/.kube directory.
   - (option 1) Register at https://rocket.nautilus.optiputer.net/ chat app and request the owners there to give you admin access to create a namespace. Recommended as the owners are active on the chat and help you fast, even if you don't need admin access.
   - (option 2) If someone from your lab already has admin access (like me), ask to add as a user to his/her namespaces(s).
3. If you use a different kubernetes cluster, contact its admin on creating namespaces and gaining access.
  
## Using kubectl:

1. 
