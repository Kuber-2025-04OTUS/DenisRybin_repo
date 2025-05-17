Создаем сервис-акаунт monitoring - service-account.yaml
Создаем кластерную роль с нужными правами - cluster-role.yaml
Связываем - cluster-role-binding.yaml
В манифест деплоймента добавляем от какого сервис аканта запускать - deployment.yaml
проверка
kubectl -n homework describe pod homework-deployment-6ffb9958-hxd85 | grep "Service Account"
Service Account:  monitoring

Создаем сервис-акаунт cd - service-account-cd.yaml
Связываем его с ролью - role-binding.yaml
Получаем токен для cd - kubectl create token cd -n homework --duration=24h
Создаем cd-kubeconfig.yaml используя токен cd строкой выше, а certificate-authority-data берем из
cat ~/.minikube/ca.crt | base64 -w0
Проверка
kubectl --kubeconfig=cd-kubeconfig.yaml get pods -n homework
NAME                                 READY   STATUS    RESTARTS   AGE
homework-deployment-6ffb9958-hxd85   1/1     Running   0          55m

*
добавляем в deployment формирование файла metrics.html
добавляем симлинк, чтоб открывалось согласно дз просто по url metrics.html
проверка в броузере по ссылке http://homework.otus:8010/metrics.html
