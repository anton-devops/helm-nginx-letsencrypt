Назначение репозитория:

	Данный helm чарт деплоит в кубернетис "hello world" приложение (можно указать свой образ)
	и выставляет его "наружу" и автоматически получает ssl сертификат от letsencrypt

Структура проекта:

	├── helm
	│   ├── Chart.yaml
	│   ├── templates
	│   │   ├── deployment.yaml
	│   │   ├── ingress.yaml
	│   │   ├── issuer.yaml
	│   │   └── service.yaml
	│   └── values.yaml
	└── README.md

Запуск деплоя из чарта:

- клонипуем репозиторий и заходим в директорию helm-nginx-letsencrypt:

		git clone https://github.com/manbojack/helm-nginx-letsencrypt.git
		cd helm-nginx-letsencrypt/

- В файле ./helm/values.yaml меняем доменное имя example.com на своё. Также меняем image и appPort на свои, если необходимо

- install ingress controller:
	
		helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
		helm repo update
		helm install ingress-nginx ingress-nginx/ingress-nginx --version 3.33.0

- install cert-manager:

		helm repo add jetstack https://charts.jetstack.io
		helm repo update
		helm install cert-manager jetstack/cert-manager --version v1.2.0 --set installCRDs=true

- install app (hello world!):

		helm install app helm/

Запускаем цикл:

		while ! kubectl get ingress|grep -E -o "([0-9]{1,3}[\.]){3}[0-9]{1,3}";do sleep 2;done 2> /dev/null

Ожидаем появления IP-адреса, копируем IP-адрес и добавляем его в качестве А-записи в DNS.

Проверяем результат работы:

		curl https://example.com
