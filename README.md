# HW2: Kubernetes

Манифесты для деплоя сервиса health-service в Kubernetes.

## Состав

- manifests/deployment.yaml — 2 реплики, liveness и readiness probes, образ avelpicasso/health-service:1.0.0
- manifests/service.yaml — ClusterIP, порт 8000
- manifests/ingress-health.yaml — Ingress без rewrite для пути /health
- manifests/ingress-otusapp.yaml — Ingress с rewrite-target /$2 для пути /otusapp/avelpicasso(/|$)(.*)
- health-service.postman_collection.json — Postman-коллекция для проверки

## Предварительные шаги

Установка Minikube и Nginx Ingress Controller:

minikube start --driver=docker --memory=4096 --cpus=2

helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
kubectl create namespace m
helm install nginx ingress-nginx/ingress-nginx --namespace m

Добавить запись в /etc/hosts:

echo "127.0.0.1 arch.homework" | sudo tee -a /etc/hosts

Запустить туннель в отдельном терминале (не закрывать):

minikube tunnel

## Развернуть

kubectl apply -f manifests/

## Проверить

Основной URL:

curl -L http://arch.homework/health

URL со звёздочкой:

curl -L http://arch.homework/otusapp/avelpicasso/health

Оба должны вернуть: {"status":"OK"}

## Проверка через Postman

newman run health-service.postman_collection.json

Ожидаемый результат: 2 requests, 0 failed.

## Удалить

kubectl delete -f manifests/

