# Домашняя работа №36

За основу взят набор сервисов из домашней работы №35, описание по ссылке https://github.com/vmironov/hw35/
Изменения:
 - Сервис выполняющий сценарий заказа orderingService переименован в orderingCoreService.
 - Добавлен фасадный сервис orderingFacedeService, который реализует существующий интерфейс orderingService/order
 - Интерфейс orderingService/order расширен доп.параметром - uuid, для того, чтобы понимать какие запросы на создание заказа повторяются, а какие - новые.
 - Новый фасадный сервис orderingFacedeService осуществляет проксирование всех запрсов на создание заказа, при необходимости перенаправляет запросы в сторону настоящего orderingService

ПОведение orderingFacedeService:
1. Получает запрос на создание заказа, по UUID ищет в БД, таблице cache, были ли ранее запрос с таким uuid.
2. Если запрос был, то достаёт сохранённый ранее ответ и транслирует его в качестве HTTP Response.
3. Если запроса не было, то вызывает orderingCoreService для обработки заказа.
4. Если orderingCoreService успешно обработал заказ (вернул 200 ок), то orderingFacedeService сохраняет ответ в БД, в таблице cache, и возвращет ответ в качестве HTTP Response
5. Если orderingCoreService неуспешно обработал заказ (вернул не 200 ok), то orderingFacedeService просто транслирует ответ в HTTP Response.

## Архитектура системы

![Архитектура системы](https://github.com/vmironov/hw36/blob/main/img/4.%20otus%20-HW36.%20%D0%9A%D0%BE%D0%BC%D0%BF%D0%BE%D0%BD%D0%B5%D0%BD%D1%82%D0%BD%D0%B0%D1%8F%20%D1%81%D1%85%D0%B5%D0%BC%D0%B0.png?raw=true)


## Инструкция по установке:

1. Установить основную систему
- $ cd helm
- $ helm install hw36 ./

2. Установить в этот же неймспейс кафку
- $ helm repo add bitnami https://charts.bitnami.com/bitnami
- $ helm install kfk bitnami/kafka --version 28.0.0 --namespace hw36-in-helm -f ./values-kafka.yaml 

>[!IMPORTANT]
>Необходимо дождаться пока не запустится кластер kafka. Он запускается не сразу.
>
>После того как будет запущен kafka подождать пока автоматически восстановится notify-consumer-deployment

3. Запустить тесты на newman
- $ cd ../
- $ newman run HW36.postman_collection.json

