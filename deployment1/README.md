# Deployment 1

First:

    cd deployment1:

Deploy the first application:

    minikube kubectl -- apply -f app-v1.yaml

Test if the deployment was successful:

    minikube service my-app --url

open a webbrowser with the same ip.

It should say something like 2202-01-28T00:22:04+01:00 - Host: host-1, Version: v1.0.0
You can now close the minikube service call

(optional)To see the deployment in action, open a new terminal and run the following command:

    minikube kubectl -- get pods -w

Then deploy version 2 of the application:
    
    minikube kubectl -- apply -f app-v2.yaml

Wait for all the version 2 pods to be running:

    minikube kubectl -- rollout status deploy my-app-v2 -w 
    
deployment "my-app-v2" successfully rolled out

Side by side, 3 pods are now running with version 2 but the service still send traffic to the first deployment.

Once your pods are ready, you can switch the traffic to the new version by patching the service to send traffic to all pods with label version=v2.0.0, like this:

    minikube kubectl -- patch service my-app -p '{"spec":{"selector":{"version":"v2.0.0"}}}'

Test if the second deployment was successful:

    minikube service my-app --url

open a webbrowser with the same ip.

In case you need to rollback to the previous version:

    minikube kubectl -- patch service my-app -p '{"spec":{"selector":{"version":"v1.0.0"}}}'

If everything is working as expected, you can then delete the v1.0.0 deployment:

    minikube kubectl -- delete deploy my-app-v1

Check if it has been deleted:

    minikube kubectl -- get pods -w

Finished! You can now clean up by deleting everything:

    minikube kubectl -- delete all -l app=my-app

