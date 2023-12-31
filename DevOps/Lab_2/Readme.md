# Лабораторная работа №2

## Условие (без звездочки)
Написать два Dockerfile – плохой и хороший. Плохой должен запускаться и работать корректно, 
но в нём должно быть не менее 3 “bad practices”. В хорошем Dockerfile они должны быть исправлены. 
В Readme описать все плохие практики из кода Dockerfile и почему они плохие, 
как они были исправлены в хорошем Dockerfile, а также две плохие практики по использованию этого контейнера

## Ход работы
1. В качестве примера возьмем Dockerfile, используемый для лабораторной работы №1

```dockerfile
# !1 Tag latest
FROM ubuntu:latest

# Install ssh server
RUN apt update && apt install openssh-server sudo -y
# !2 Cleaning caches in separete layer
RUN apt clean

# !3 Running multiple RUN commands except of single one
# Create user
RUN useradd -rm -d /home/ubuntu -s /bin/bash -g root -G sudo -u 1000 test
RUN echo 'test:test' | chpasswd

# Run ssh server
RUN service ssh start
EXPOSE 22
CMD ["/usr/sbin/sshd","-D"]
```
2. Рассмотрим содержащиеся в нем “bad practices”:
   - **Использование :latest** - может вдруг сломать сборку после обновления образа. 
   Лучше указывать точную версии используемого образа, таким образом докер-файл остается неизменным и его работоспособность ничто не нарушит.
   - **Запуск связанных команд отдельно** - увеличивает количество слоев и как следствие размер образа.
   - **Очистка кэша в отдельном слое** - это приводит к созданию еще одного слоя, где эти данные удалены, но в предыдущих слоях данные останутся, что увеличивает размер образа и делает команду по очистке кэша бессмысленной.


3. Докер файл с исправленными “bad practices”:
```dockerfile
FROM ubuntu:20.04

# Install ssh server
RUN apt update &&\
apt install openssh-server sudo -y &&\
apt clean

# Create user
RUN useradd -rm -d /home/ubuntu -s /bin/bash -g root -G sudo -u 1000 test &&\
echo 'test:test' | chpasswd

# Run ssh server
RUN service ssh start
EXPOSE 22
CMD ["/usr/sbin/sshd","-D"]

```

4. Работоспособность файлов была проверена в лабораторной №1


5. Практики плохого использования контейнеров:
   - **Подключение к контейнеру для дополнительной настройки.** 
   В случае нашего докерфайла, если не прописывать команду создания пользователя внутри, 
   то придется подключаться вручную к контейнеру и создавать пользователя, 
   чтобы этого избежать и все работало из коробки добавили команду создания пользователя в файл. 
   - **Не обновлять свои образы.** Желательно использовать базовые образы, которые часто обновляются и перестраивать свои 
   на основе последних версий. Так как постоянно находятся новые уязвимости безопасности => хорошей практикой является 
   использование самых последних исправлений безопасности.

Mission complete!🐳
