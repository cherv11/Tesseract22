## Домашнее задание 3
Напоминаю, что список изученных нами методов есть в конце вот этой лекции: [тык](https://cherv11.github.io/Tesseract22/libs_and_methods)  

Задачи решаются в общем виде, то есть массив/строка может быть любым/ой, в задаче они даны только для примера!

1. Дан список чисел, длина которого кратна трём. Среди элементов этого списка можно найти локальные минимумы. Например, для данного ниже списка локальный минимум первых трёх элементов — минимум среди `[8, 7, 4]`, то есть 4. Найдите локальные минимумы каждой тройки элементов (1-3, 4-6 и т.д.) и выведите их сумму на экран.
```py
a = [8, 7, 4, 5, 2, 6, 7, 5, 9]
```
2. Дан список со строками. Напишите программу, которая уберёт полностью пустые строки (`''`) из списка:
```py
a = ['При', '', 'вет', ' ', '', 'Рыба карп', '']
```
3. Напишите программу, которая имитирует броски игральной кости. Она принимает число `n` — количество кубиков. Один кубик может выбросить случайное число от 1 до 6. В ответ выведите сумму очков на всех кубиках.
4. Давайте напишем ещё пару консольных команд! Первая вычисляет факториал. С клавиатуры вводится `factorial n`, где n — натуральное число до 20. Вам нужно понять, что написана именно эта команда, а не что-то ещё, при помощи `startswith()`, затем отделить число от команды и вернуть факториал. Функцию вычисления факториала писать не нужно, используйте `math.factorial(n)`. Вторая команда — `add n m k` — принимает неограниченное количество чисел через пробел. Вам нужно вывести сумму этих чисел, реализация аналогичная. Естесственно, обе команды должны быть в одном коде, мы для этого и пишем разделение на команды)) Советую использовать следующий код, чтобы не писать `lower()` несколько раз для каждой команды:
```py
a = input().lower()
```
