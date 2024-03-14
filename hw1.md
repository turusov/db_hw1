1. для дз я использовал Docker Desktop и форкнутый имедж монги.
2. заранил контейнер
![image](https://github.com/turusov/db_hw1/assets/62646493/787e9164-0861-4052-94fe-0f5e8ce72243)
3. подключился к контейнеру
![image](https://github.com/turusov/db_hw1/assets/62646493/04f86d6a-d284-4c95-842d-dba9886b8a3f)
4. прокинул файл датасета в докер
![image](https://github.com/turusov/db_hw1/assets/62646493/8b3c0c50-1a82-45d8-ad99-fef1852546d8)
5. заимпортил датасет в бд
![image](https://github.com/turusov/db_hw1/assets/62646493/42cae9b8-bf69-49be-b023-7542117209b1)
6. проверил что данные импортнулись db.intents.find()
![image](https://github.com/turusov/db_hw1/assets/62646493/f7273b2b-abfc-4f9c-bb52-e8d6c3d6bcae)
7. убедился что ограничения на структуру входных данных в монге действительно нет, заимпортил еще разной синтетики, попробовал что-то прочитать по айдишнику
![image](https://github.com/turusov/db_hw1/assets/62646493/3920c29a-bb85-4217-9a2b-5d06825e0479)
![image](https://github.com/turusov/db_hw1/assets/62646493/b861047e-8768-4f84-b532-1ab27fa16fa5)
8. попробовал удалить две записи с инсерта выше, проверил что удалилось
![image](https://github.com/turusov/db_hw1/assets/62646493/04323ad7-bdef-425b-aa9e-42b68a9a56aa)
![image](https://github.com/turusov/db_hw1/assets/62646493/e38919b2-3a47-49b1-a2cc-dde3ec9e9cd8)
9. протестировал апдейт

![image](https://github.com/turusov/db_hw1/assets/62646493/2afb91d8-7422-4490-82ae-836aab245199)
![image](https://github.com/turusov/db_hw1/assets/62646493/fe7a9f06-4a86-4fde-8aeb-c961c2065d92)

10. перед созданием индекса можно посчитать селективность
![image](https://github.com/turusov/db_hw1/assets/62646493/5c894358-d8b9-45bf-9f85-d33f4c6e3d0f)
11. предположим, что часто будут запросы на date+trips,  селективность trips 348/368= ~0.96, селективность date ~0.16
12. попробуем что-то поискать и посмотреть лог explain()
![image](https://github.com/turusov/db_hw1/assets/62646493/63709777-9e1f-4f8d-ab55-5b894a940923)
13. создадим составной индекс, с учетом селективности сначала по trips 
![image](https://github.com/turusov/db_hw1/assets/62646493/9aa778dc-2621-4a50-8ea4-884996ad4627)
14. глянем сравнение эксплейнов - executionTime увеличился при поиске с индексом, хотя значения переменных works и needTime меньше
![image](https://github.com/turusov/db_hw1/assets/62646493/e698b892-efb0-4538-a3c5-04d6a3d2ec05)
15. попробуем потестить по другому ключу - "active_vehicles"
сравним два эксплейна, до создания индекса и после 
![image](https://github.com/turusov/db_hw1/assets/62646493/6f317802-c5f3-4f56-85d1-f78715575cec)
ситуация схожая.
рискну предположить что записей мало и полный перебор быстрее чем лазить по дереву (по дефолту индекс по бдереву в монге), находить значение в индексе и проваливаться в запись чтобы тянуть остальные поля - все таки индекс не кластеризованный, а запрос не покрывающий.
думаю на большем датасете при таких же запросах разница во времени выполняния была бы весомой в пользу использования индекса
16. можно сделать вывод что нужно покурить индексы в монге.

