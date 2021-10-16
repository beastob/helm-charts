# Deploy application on EKS

---

## Install Helm Chart

```shell
cd ./charts/url-shortener
helm install url-shortener . -f values.yaml
```

### Check chart deployment status

```shell
helm list
```
```shell
watch -n 5 kubectl get pod
```
Wait until `url-shortener-postgresql` and `url-shortener` are both deployed:
```shell
url-shortener-796f85bddd-kx8tp                 1/1     Running   0          80m
url-shortener-postgresql-0                     1/1     Running   0          81m
```

### Scale application
```shell
kubectl scale deploy/url-shortener --replicas=10
```

## Verify deployment

---

### Get external IP/Hostname

```shell
kubectl get services url-shortener --output jsonpath='{.status.loadBalancer.ingress[0].hostname}'
```

>It might take a minute or so for the external DNS hostname to propagate to public DNS servers.



### Create new shorten URLs

Set external hostname URL as environment variable
```shell
export SERVICE_IP=$(kubectl get svc --namespace default url-shortener --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
```
```shell
curl --location --request POST "http://$SERVICE_IP:80/" \
--header 'Content-Type: application/json' \
--data-raw '{
  "url": "https://youtube.com"
}'
```

### Get all existing shorten URLs

```shell
curl --location --request GET "http://$SERVICE_IP:80"
```

### Use shorten URL for redirect

Replace `<shortenUrl>`
```shell
curl --location --request GET "http://$SERVICE_IP:80/<shortenUrl>"
```

### Get HTTP response code

Replace `<shortenUrl>`
```shell
curl -o /dev/null -s -w "%{http_code}\n" "http://$SERVICE_IP:80/<shortenUrl>"
```
HTTP response code should be `304 NOT_MODIFIED`.  
Browser will not redirect to original URL(e.g. https://google.com) since HTTP response code
is not `302`.