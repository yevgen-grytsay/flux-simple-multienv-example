# Flux simple multi-environment example

// TODO

```sh
# PAT with permissions "Contents: Read and Write"

flux bootstrap github \
 --owner=yevgen-grytsay \
 --repository=flux-simple-multienv-example \
 --personal \
 --path=cluster \
 --token-auth
```

Перевірити правильність маніфестів можна за допомогою наступних команд:

```sh
kubectl kustomize ./app/dev

kubectl kustomize ./app/prod
```

Перевіряємо, що наш додаток встановлено:

```
# kubectl get all -n prod
NAME                                        READY   STATUS    RESTARTS   AGE
pod/hello-app-deployment-58d97c88d4-cxj7p   1/1     Running   0          38s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-app-deployment   1/1     1            1           38s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-app-deployment-58d97c88d4   1         1         1       38s


# kubectl get all -n dev
NAME                                        READY   STATUS    RESTARTS   AGE
pod/hello-app-deployment-6d5c5f9f67-q9r4q   1/1     Running   0          70s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-app-deployment   1/1     1            1           70s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-app-deployment-6d5c5f9f67   1         1         1       70s
```

Перевіряємо, що у `prod` і `dev` неймспейси були встановлені відповідні версії додатку.

Спочатку для `dev`:

```
# kubectl port-forward -n dev pod/hello-app-deployment-6d5c5f9f67-q9r4q 8080
Forwarding from 127.0.0.1:8080 -> 8080

# curl localhost:8080
Hello, world!
Version: 2.0.0
Hostname: hello-app-deployment-6d5c5f9f67-q9r4q
```

Тепер для `prod`:

```
# kubectl port-forward -n prod pod/hello-app-deployment-58d97c88d4-cxj7p 8080
Forwarding from 127.0.0.1:8080 -> 8080

# curl localhost:8080
Hello, world!
Version: 1.0.0
Hostname: hello-app-deployment-58d97c88d4-cxj7p
```
