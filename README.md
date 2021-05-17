# Технопарк Mail.ru, Разработчик децентрализованных систем, Домашнее задание №6
## Уймин Максим Сергеевич

Сделать программу - консольное приложение, используя языки: С/C++, Rust, Python, Go, Node.JS.
Код должен быть оформлен в  отдельный репозитарий на GitHub c инструкцией в README.md как собрать и запустить код в консоли под Ubuntu 18.04.
К заданию должен быть приложен скриншот или текст вывода одного или нескольких прогонов программы(!).

Программа разделяет случайную строку в hex формате на N частей по схеме Шамира и восстанавливает её при предъявлении T любых частей

## Как запустить
`go build -o bin/shamir cmd/main.go` - сборка исполняемого файла. Требуется компилятор Go версии >= 1.15, при необходимости версию можно понизить в файле go.mod, должно собираться начиная с версии 1.13

`./bin/shamir keygen|split|recover` - выполнить программу

- `keygen` - генерирует пару ключей на эллептической кривой `secp256r1`
- `split` - читает из `stdin` 2 строки: в первой приватный ключ, во второй 2 числа: `N` (на сколько частей разделить ключ) и `T` (сколько частей нужно для восстановления)
- `recover` - читает из `stdin` строки до `EOF`, восстанавливает приватный ключ на основе полученных значений

## Демонстрация работы
![image](https://user-images.githubusercontent.com/22894650/118446651-8ab20800-b6f8-11eb-91ac-d1f52e54f150.png)

`go build -o bin/shamir cmd/main.go` - собираем программу

`./bin/shamir keygen` - генерируем пару ключей

`./bin/shamir split | tee /tmp/shares.txt` - разбиваем ключ на 4 части, для восстановления необходимо предъявить как минимум 3;
все части печатаем в `stdout` и файл `/tmp/shares.txt`

`tail -3 /tmp/shares.txt | ./bin/shamir recover` - проверяем, что можем восстановить ключ из последних 3-ёх частей

`head -3 /tmp/shares.txt | ./bin/shamir recover` - проверяем, что можем восстановить ключ из первых 3-ёх частей

`cat /tmp/shares.txt | ./bin/shamir recover` - проверяем, что можем восстановить ключ из 4-ёх частей

`tail -2 /tmp/shares.txt | ./bin/shamir recover` - проверяем, что не можем восстановить ключ из 2-ух частей

## Детали реализации
Для реализации рандома во всей программе используется источник энтропии, предоставленный операционной системой. Из документации к golang:

> On Linux and FreeBSD, Reader uses getrandom(2) if available, /dev/urandom otherwise. On OpenBSD, Reader uses getentropy(2). On other Unix-like systems, Reader reads from /dev/urandom.
> On Windows systems, Reader uses the RtlGenRandom API. On Wasm, Reader uses the Web Crypto API.
