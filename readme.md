# Create the Kubernetes Cluster
The k8s-create.sh script should be in the cloud_user's home directory. You may list the directory contents with:

$ ls -l
To run the script, enter the following command:

Note: Be sure to have a space between the . and the ./ in front of the script. This ensures that environment variables set in the script are then available to the parent shell.

$ . ./k8s-create.sh
Once the cluster configuration has been created, you can apply the configuration with this command:

$ kops update cluster -y
Note: To view the cluster servers as they are being created, you may use the aws console and credentials provided.

You may validate the cluster with the command:

$ kops validate cluster
It will give errors until the cluster is fully configured.

When complete, it should report that the cluster is ready.

Verify the cluster is running with:

$ kubectl get nodes


# Configure the Required Namespace

$ kubectl create ns policy-demo


# Create the Demo Pods

Run two replicas of the nginx service:

$ kubectl run --namespace=policy-demo nginx --replicas=2 --image=nginx
Expose the service on port 80:

$ kubectl expose --namespace=policy-demo deployment nginx --port=80
Run an interactice session in a pod called access using the busybox image:

$ kubectl run --namespace=policy-demo access --rm -ti --image busybox /bin/sh
Once inside the image, type this command to verify access to the nginx server:

/ # wget -q nginx -O -
This should respond with the raw html from nginx.

To exit the interactive container session:

/ # exit


# Enable Isolation

To view that the nginx pods are running:

$ kubectl --namespace=policy-demo get pods
To run an interactive container to test access:

$ kubectl run --namespace=policy-demo access --rm -ti --image busybox /bin/sh
Within the access container, enter:

/ # wget -q --timeout=5 nginx -O -
Note: you should receive a timeout in 5 seconds

To exit the container shell:

/ # exit


# Allow Restricted Access Using a Network Policy

To download the yaml file:

$ wget https://raw.github.com/linuxacademy/content-kubernetes-security-ac/master/access-nginx.yaml
To look at the file:

$ more access-nginx.yaml
To create the policy:

$ kubectl create -f access-nginx.yaml

# Test Isolation

To view that the nginx pods are running:

$ kubectl --namespace=policy-demo get pods
To run an interactive container to test access:

$ kubectl run --namespace=policy-demo access --rm -ti --image busybox /bin/sh
Within the access container, enter:

/ # wget -q --timeout=5 nginx -O -
Note: you should receive a timeout in 5 seconds

To exit the container shell:

/ # exit


# Allow Restricted Access Using a Network Policy
To download the yaml file:

$ wget https://raw.github.com/linuxacademy/content-kubernetes-security-ac/master/access-nginx.yaml
To look at the file:

$ more access-nginx.yaml
To create the policy:

$ kubectl create -f access-nginx.yaml


# Verify that access to nginx is not allowed from another pod

Run a container shell in a pod called 'not-access':

$ kubectl run --namespace=policy-demo not-access --rm -ti --image busybox /bin/sh
Within the container attempt to access nginx:

/ # wget -q --timeout=5 nginx -O -
Note: this command should timeout after 5 seconds.

To exit the container shell:

/ # exit

# Delete the namespace to cleanup

To delete the namespace and thus terminate the running pods and nullify the network policies created, enter:

$ kubectl delete ns policy-demo


