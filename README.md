# Weather API chaos test

    0. Исходные данные
    
    Настроенный и штатно функционирующий кластер postgres на базе patroni.

    Конфигурация:    
        - Master-нода: 10.0.10.5  
        - Slave-нода:  10.0.10.6
    
        <pic>

## Отключение узла базы данных.

    1. Описание эксперимента

        Попробуем остановить сервис patroni на master-ноде, сымитировав таким образов выход master-узла из строя.
        Это должно вызвать запуск процесса перевыбора master'а в кластере.
        Убедимся, что master изменился.
        Запустим сервис patroni, убедимся, что в кластере снова две ноды и Master не вернулся к первой ноде.

    2. Ожидаемые результаты  

        После выключения сервиса patroni на master-ноде происходит кратковременная недоступность кластера, т.к. запускается процесс выбора нового master. Через некоторое время (0-5сек) должен определиться новый master (ранее наш slave).
        После включения сервиса patroni на ноде старого master (который сейчас slave), ничего не  должно произойти.

    3. Реальные результаты

        После выключения сервиса patroni на master-ноде начинается процедура выбора нового мастера и кластер переходит снова в рабочее состояние.

        Обнаружил интересный эффект: если вырубить master и slave базы и дать нагрузку 100рпс - приложение падает в кубере. 

        ```k8s
        > kubectl get pods
        
        NAME                          READY   STATUS    RESTARTS          AGE
        weather-api-fbf499ff5-5pd4r   1/1     Running   133 (105s ago)    36d
        weather-api-fbf499ff5-b9csn   1/1     Running   140 (111s ago)    36d
        weather-api-fbf499ff5-fmt2m   1/1     Running   135 (2m11s ago)   36d
        ```

        <screenshot с потреблением ресурсов>

    4. Анализ результатов

        Единственное отклонение - поведение weather-api приложение при недоступности базы. Очень странно, что под нагрузкой в 100рпс и без подключения к базе оно валиться.

## Имитация частичной потери сети

    1. Описание эксперимента
    2. Ожидаемые результаты
    3. Реальные результаты
    4. Анализ результатов

## Высокая нагрузка на CPU или I/O

    1. Описание эксперимента
    2. Ожидаемые результаты
    3. Реальные результаты
    4. Анализ результатов

## Тестирование систем мониторинга и оповещения

    1. Описание эксперимента
    2. Ожидаемые результаты
    3. Реальные результаты
    4. Анализ результатов