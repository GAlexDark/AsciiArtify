# Модуль 4. Основи Kubernetes, Задача "Розгортагння Argo CD"

## 1. Розгортагння та первинне налаштування Argo CD
0. Рекомендується встановити **Kubernetes Dashboard** та/або **Lens** для контролю та вирішення можливих проблем. Також необхідно завантажити Argo CD CLI **argocd**.
1. Створення namespace та встановлення Argo CD
    ```
    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```
2. За необхідності, замеміть тип служби argocd-server на LoadBalancer:
    ```
    kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
    ```
	Якщо команда поверне помилку, можна використати **Kubernetes Dashboard** або **Lens** для заміни.
    Перевірити заміну можна за допомогою команди:
	```
	kubectl get svc -n argocd
	```

3. Налаштування переадресації портів.
    ```
    kubectl port-forward svc/argocd-server -n argocd 8080:443
    ```
4. Відкрити посилання у браузері:
    ```
	https://127.0.0.1:8080
	```
5. Отримати перший пароль можна за допомогою команди:
    ```
	argocd admin initial-password -n argocd
	```
6. Увійти до інтерфейсу Argo CD та змінити пароль.
7. Видалити секрет argocd-initial-admin-secret командою:
    ```
	kubectl delete secret argocd-initial-admin-secret -n argocd
	```
### Демонстрація розгортання Argo CD
![](argo_install.gif)
## 2. Налаштування синхронизації
### Демонстрація налаштування синхронизації в Argo CD
![](argo_create_app.gif)

***Зауваження.*** Оскільки go-demo-app не зовсім адаптований для запуску в середовищі minikube, то в процесі деплою може виникнути помилка:
```
1:C 26 May 2025 12:46:16.124 * oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 26 May 2025 12:46:16.124 * Redis version=7.4.0, bits=64, commit=00000000, modified=1, pid=1, just started
1:C 26 May 2025 12:46:16.124 * Configuration loaded
1:M 26 May 2025 12:46:16.124 * monotonic clock: POSIX clock_gettime
1:M 26 May 2025 12:46:16.198 * Running mode=standalone, port=6379.
1:M 26 May 2025 12:46:16.199 * Server initialized
1:M 26 May 2025 12:46:16.199 # Can't open or create append-only dir appendonlydir: Permission denied
```
Рішення:
1. Швидке.  
	1.0. (Не тестувалось) Додати в розділ redis файлу values.yaml:
	```
	volumePermissions: enabled: true
	```
	1.1. Виконати команду:
    ```
    kubectl get pod demo-redis-master-0 -o yaml -n demo | grep runAsUser
    ```  
    Будуть отримані значення runAsUser та fsGroup (наприклад, 1001 та 1001).  
	1.2. На кожній ноді кластеру minikube, підключившись до нього за допомогою ssh, виконати команди від root (значення runAsUser та fsGroup взяти з результатів попередньої команди):
    ```
    cd /tmp/hostpath-provisioner/demo
    chown -R 1001:1001 ./redis-data-demo-redis-master-0
    ```
2. Коректне. Використовуючи NFS або Ceph як джерело дискового простору для redis, створити відповідні Storage Classes та Persistent Volumes.

### Демонстрація автоматичної синхронизації
В репозиторій внесено та відалено помилковий коміт ТЕСТ
![](argo_autosync_app.gif)
### Демонстрація застосунку go-demo-app
![](demo_app.gif)