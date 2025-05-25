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
Вказаний при налаштуванні синхронизації namespace повинен спивпадати із namespace в коді.

### Демонстрація автоматичної синхронизації
В репозиторій внесено та відалено помилковий коміт ТЕСТ
![](argo_autosync_app.gif)
