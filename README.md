# **Сетевые пакеты. VLAN'ы. Bonding.**

## **HomeWork**

Cтроим бонды и вланы:

- в Office1 в тестовой подсети появляется сервера с доп интерфесами и адресамив internal сети testLAN
- testClient1 - 10.10.10.254
- testClient2 - 10.10.10.254
- testServer1- 10.10.10.1
- testServer2- 10.10.10.1
- равести вланами
- testClient1 <-> testServer1
- testClient2 <-> testServer2
- между centralRouter и inetRouter:
- "пробросить" 2 линка (общая inernal сеть) и объединить их в бонд актив-актив
- проверить работу если выбрать интерфейсы в бонде по очереди

В Vagrantfile добавила конфигурацию виртуальных машин: testServer1, testServer2, testClient1, testClient2 как указано в дз.
Корректность выполненного дз можно проверить с помощью ping:

![alt-текст](https://github.com/natali1701/OtusHW18/blob/master/Screenshot%20from%202019-04-20%2014-16-36.png)

Внесла в конфигурацию все что требовалось по дз, также внесено, чтобы с клиента можно попасть ssh на сервер без пароля.

