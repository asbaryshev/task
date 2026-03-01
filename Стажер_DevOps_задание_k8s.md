
## **Задание для стажера DevOps (Kubernetes + Helm)**  
**Цель:** Освоить базовые концепции Kubernetes, научиться работать с подами, деплойментами, сервисами, установкой Helm-чартов, Ingress-ресурсами.

DoD не фиксирован. Даже если какая-то часть задания не выполнена, результат будет оценен и по решению проверяющего зачтен.

### **Базовая часть** 

#### **1. Установка и настройка локального кластера**  
- Установить **`kind`** (Kubernetes in Docker) или **`minikube`**.  
- Запустить кластер и проверить подключение через **`kubectl`**.  
- Убедиться, что `kubectl get nodes` показывает рабочую ноду. 
- Выполнить `kubectl get ns`, почитать о концепции namespace в k8s
- Выполнить `kubectl get po -n kube-system`, ознакомиться с контейнерами запущенными в неймспейсе (что это за контенеры и зачем они нужны).

#### **2. Запуск пода с одним и несколькими контейнерами**  
- Создать **Pod** (`kubectl run`) взяв за основу образ https://hub.docker.com/r/jkaninda/simple-api
- Проверить логи (`kubectl logs <pod> -c <container>`).
- Получить доступ к api через `kubectl port-forward`

#### **3. Переход на ReplicaSet и Deployment**  
- Заменить **Pod** на **Deployment** (указать `replicas: 2`) (https://hub.docker.com/r/jkaninda/simple-api#:~:text=simple%2Dapi%3Alatest%20.-,Run%20on%20Kubernetes,-Prerequisites)
- Проверить, что при удалении пода он восстанавливается. 

#### **4. Настройка доступа через Service**  
- Создать **Service** типа **ClusterIP** для доступа к поду внутри кластера.   

#### **5. Развертывание Ingress-контроллера через Helm**  
- Установить **Helm**.  
- Добавить репозиторий `ingress-nginx` https://kubernetes.github.io/ingress-nginx (`helm repo add`)
- Установить `ingress-nginx` с **NodePort** (если нет LoadBalancer) через (`helm install`)
- Создать **Ingress**-ресурс для маршрутизации трафика на сервис c simple-api.  

### **Продвинутая часть часть** 

#### **6. Установка VictoriaMetrics и Grafana через Helm**
- Добавить репозиторий `vm` https://victoriametrics.github.io/helm-charts/ (`helm repo add`)
- Установить `victoria-metrics-single` через (`helm install`)
- Установить `victoria-metrics-operator` через (`helm install`)
- Установить `victoria-metrics-agent` через (`helm install`) или через CRD
- Добавить репозиторий `grafana` https://grafana.github.io/helm-charts (`helm repo add`)
- Установить `grafana` через (`helm install`)

#### **7. Сконфигурировать доступ снаружи до /targets VMAgent'а и ui Grafana**
- Создать **Ingress**-ресурс для маршрутизации трафика на сервис c Grafana.  
- Создать **Service** типа **ClusterIP** для доступа к поду vmagent.
- Создать **Ingress**-ресурс для маршрутизации трафика на сервис c vmagent. 

#### **8. Добавить сбор метрки с сервиса simple-api и ingress-nginx**
- Убедиться что метрики simple-api доступны через **Service**
- Создать **VMServiceScrape**-ресурс указывающий на точку сбора метрик simple-api
- Убедиться что **vmagent** собирает метрики с simple-api и отправляет их в **VictoriaMetrics**
- Сконфигурировать публикацию метрик ingress-nginx'a
- Создать **VMServiceScrape**-ресурс указывающий на точку сбора метрик ingress-nginx
- Убедиться что **vmagent** собирает метрики с ingress-nginx и отправляет их в **VictoriaMetrics**

#### **9. Добавить дашборды для визуализации получаемых метрик**
- Импортировать или создать дашборд по визуализации метрик simple-api
- Импортировать или создать дашборд по визуализации метрик ingress-nginx
- Импортировать или создать дашборд по визуализации метрик kind
---

### **Ожидаемый результат:**  
Базовая часть:
- Локальный кластер (kind/minikube) с работающим `kubectl`.  
- Поды с simple-api под управлением Deployment с 2 репликами. 
- Service и доступ к simple-api внутри кластера.
- Ingress-контроллер, развернутый через Helm.
- **Ingress**-ресурс и доступ к приложению снаружи.

Продвинутая часть:
- Установлен стек мониторинга:
	- VictoriaMetrics (Single-node) через Helm.
	- VictoriaMetrics Operator для работы с CRD-ресурсами.
	- VictoriaMetrics Agent для сбора метрик и отправки их в VictoriaMetrics.
	- Grafana для визуализации метрик.
- Настроен внешний доступ к интерфейсам:
	- Grafana UI
	- VMAgent targets (/targets)
- Настроен автоматический сбор метрик через CRD-ресурсы:
	- Метрики собираются с приложения simple-api (доступны в VictoriaMetrics и Grafana).
	- Метрики собираются с ingress-nginx (доступны в VictoriaMetrics и Grafana).
	- Метрики компонентов kind собираются и доступны в VictoriaMetrics и Grafana.
- В Grafana импортированы и настроены дашборды для визуализации:
	- Метрик simple-api
	- Метрик ingress-nginx
	- Метрик кластера kind (состояние узлов, подов, контейнеров)

### **Проверка знаний:**  
Базовая часть:
- Понимание разницы между **Pod**, **ReplicaSet**, **Deployment**.  
- Умение настраивать **Service** и **Ingress**.  
- Опыт работы с **Helm** (базовые команды, установка чартов).  

Продвинутая часть:
- Понимание основных концепций мониторинга в Kubernetes (метрики, endpoint'ы, scrape-интервалы).
- Умение устанавливать и настраивать стек мониторинга (VictoriaMetrics, VMAgent, Grafana) через Helm.
- Понимание роли VictoriaMetrics Operator и умение использовать CRD-ресурсы (VMServiceScrape) для декларативного управления мониторингом.
- Навыки настройки внешнего доступа к сервисам мониторинга (Grafana, VMAgent) через Ingress.
- Умение интегрировать метрики приложений (simple-api, ingress-контроллер) в систему мониторинга.
- Умение импортировать и создавать дашборды Grafana для визуализации различных метрик.
