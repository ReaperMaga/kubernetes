# How to install Kubernetes

## Step 1: Microk8s


We will use microk8s as our wrapper to simplify the kubernetes installation.

First of all you need the snap package manager:

```
sudo apt install snapd
sudo snap install core
```

After snap you can install microk8s with this simple command:

```
sudo snap install microk8s --classic --channel=1.24
```

Why 1.24 and not the newer version? I already tested and tried with the newer version but there were some bugs that I encountered that I couldn't fix, thats why we will choose 1.24 for this installation.

Feel free to try this installation with a newer version.
I would be thankful if you could tell me if you succeeded.

<br>

Make sure to check if everything is working:
```
microk8s status
```

Enable following addons:

```
microk8s enable dashboard dns helm3
microk8s helm3 init
```

That should be everything for microk8s and your kubernetes should be running.

## Step 2: Traefik with Cloudflare SSL Provider

We will create a Secret to store our Cloudflare credentials.

Download this <a href="/installation/files/cloudflare-secret.yaml">file</a>
and replace the placeholders with your credentials.

After replacing apply this file to your kubernetes cluster with:
```
microk8s kubectl apply -f cloudflare-secret.yaml
```

<br>

Now we will install traefik by using the official helm chart:


First comes the helm repo:
```
microk8s helm3 repo add traefik https://helm.traefik.io/traefik
microk8s helm3 repo update
```

Download this <a href="/installation/files/traefik-helm-values.yaml">file</a>
and replace all placeholders.

Switch your context namespace to traefik:
```
kubectl config set-context --current --namespace=traefik
```
Now install traefik:

```
microk8s helm3 install traefik traefik/traefik --values=traefik-helm-values.yaml
```

Now traefik should be installed. Check by executing this command and see if everything is running.

```
microk8s kubectl get all --namespace=traefik
```

## Step 3: Let kubernetes dashboard run behind traefik

It's actually simple just download this <a href="/installation/files/k8s-dashboard-ingress.yaml">file</a> and replace `<domain>` with your choosen domain name.

After replacing apply it your kubernetes cluster with:
```
microk8s kubectl apply -f k8s-dashboard-ingress.yaml
```





