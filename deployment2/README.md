# Deployment2

Deploy version 1 and expose the service via an ingress:

    minikube kubectl -- apply -f ./app-v1.yaml -f ./ingress-v1.yaml

Deploy version 2:

    minikube kubectl -- apply -f ./app-v2.yaml

In a different terminal you can check that requests are responding with version 1

    minikube service ingress-nginx --url

Copy the url, Open a third terminal window and run:

    while sleep 0.1; do curl "http://127.0.0.1:52008" -H "Host: my-app.com"; done

Go to the first terminal window, deploy version 2 of ingress:

    minikube kubectl -- apply -f ./ingress-v2.yaml

Now you should see that the traffic is changing (in the 3rd terminal e.g curl)

When you are happy, delete the ingress:

    minikube kubectl -- delete -f ./ingress-v2.yaml

Then finish the rollout, set traffic to version 2:

    minikube kubectl -- apply -f ./ingress-v3.yaml

Finished, clean up:

    minikube kubectl -- delete all -l app=my-app