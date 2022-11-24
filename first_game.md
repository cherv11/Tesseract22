# Первая игра на Pygame
Привет, если ты это читаешь, статья недопилена:)

Итак, мы немного разобрались в программировании и устройстве Питона, скачали arcade и хотим делать игры! Но с чего начать?  
С шаблона кода arcade! Когда вы начинаете любой новый проект, качайте шаблон здесь (файл называется ex.py): [ссылка]()  
Давайте разберём его по кусочкам.  
## Шаблон ex.py
![image](https://user-images.githubusercontent.com/56085790/140838149-6b60ef30-13c0-4013-ac1d-a79e167b805a.png)   
![image](https://user-images.githubusercontent.com/56085790/140841380-f0db1c02-24a6-4d6a-81d1-fca70f674cb7.png)  

## Функции и полезности
Код на pygame состоит из трёх частей:  
- Определение переменных (цвета, разрешение, экран, шрифты и т.п.)  
- Ивенты, управление, просчитывание игрового процесса  
- Рисование нового кадра, то есть всё, что связано с графикой  

Вот так делится на эти три части наш код:  
![image](https://user-images.githubusercontent.com/56085790/140828746-074f2613-dcd3-41e9-a6ac-025be8863233.png)  

В конце кода, помимо прочего, есть две новые функции:  
```py
font.render(str(pos), True, BLACK)  # Рендерит текст `str(pos)`, используя шрифт `font` чёрного цвета
```
Рендерить — означает "делать что-то готовым (к помещению на экран в нашем случае)". Следующая функция:
```py
sc.blit(picture, position)
```
Помещает на экран изображение `picture`, ставя его верхний левый угол на координаты `position`. Например:
```py
name = font.render('Дима', True, RED)
sc.blit(name, (0,0))
```
Сделает нам текст в верхней левой части экрана:  
![image](https://user-images.githubusercontent.com/56085790/140817476-ba9d949a-5667-40cb-b6ab-5235b12ca74f.png)  
Но это выглядит некрасиво, поэтому лучше сделать отступ:)
## Итак, давайте напишем что-нибудь простое. Крестики-нолики подойдут!
### Как они устроены?
Сама игра в крестики-нолики проста. Нужно просто создать двухмерный массив (определение):
```py
table = [[0, 0, 0], 
         [0, 0, 0], 
         [0, 0, 0]]
```
Теперь давайте скажем, что 0 — это пустая клетка, 1 — крестик, 2 — нолик. Писать в массиве ничего не нужно, просто когда кто-то делает ход, мы меняем 0 на 1 или 2 в нужном месте (управление):
```py
table[1][2] = 2
```
Ещё нам нужна переменная, которая помнит, чей сейчас ход (пусть первым ходит нолик), и переменная, которая запишет победителя (пока что None):
```py
turn = 2
winner = None
```
И последнее: когда кто-то делает ход, нужно проверять, победил он в игре, или ещё нет:
```py
def check_win():
    for i in range(3):
        if table[i][0] != 0 and table[i][0] == table[i][1] and table[i][1] == table[i][2]:
            return True
        if table[0][i] != 0 and table[0][i] == table[1][i] and table[1][i] == table[2][i]:
            return True
    if table[0][0] != 0 and table[0][0] == table[1][1] and table[1][1] == table[2][2]:
        return True
    if table[0][2] != 0 and table[0][2] == table[1][1] and table[1][1] == table[2][0]:
        return True
    return False
```
### Простая геометрия
Теперь займёмся рисованием. Сегодня мы воспользуемся простой геометрией — функционалом для создания фигур, а на следующей паре заменим это на картинки повеселее:) Вот все функции, которые могут нам понадобиться:
```py
# Прямоугольник, если не указать толщину, будет применена заливка
pygame.draw.rect(sc, <цвет>, (<x угла>, <y угла>, <размер по x>, <размер по y>), [толщина])
# Нарисуем квадрат со стороной 100 посередине экрана:
pygame.draw.rect(sc, BLACK, (450, 250, 100, 100))

# Круг, толщина так же не обязательна
pygame.draw.circle(sc, <цвет>, <координаты центра>, <радиус>, [толщина])
# Нариусем красный бублик радиусом 50
pygame.draw.circle(sc, RED, (200, 100), 50, 10)

# Линия заданной толщины
pygame.draw.line(sc, <цвет>, <координаты точки 1>, <координаты точки 2>, <толщина>)
pygame.draw.line(sc, GREEN, (50, 500), (500, 100), 3)

# Линия толщины 1, на которую применяется сглаживание 
# Лучше всего использовать их для создания тонких линий, чтобы было красиво
pygame.draw.aaline(sc, <цвет>, <координаты точки 1>, <координаты точки 2>)
pygame.draw.aaline(sc, LIGHT_GREEN, (50, 520), (500, 120))
```
Это не все фигуры, есть ещё эллипсы и дуги, о которых можно почитать [здесь](https://younglinux.info/pygame/draw). Вот, что у нас получилось:  
![image](https://user-images.githubusercontent.com/56085790/140987978-b41f9e9b-3418-42a3-886b-ba528798d2ca.png)  
### Рисуем поле
Воспользуемся этим функционалом для создания игрового поля из квадрата и линий почти на весь экран. Для этого нам нужно найти такой размер одной клетки нашего поля 3х3, чтобы всё красиво влезоло в экран. Я выбрал сторону квадрата 170 (тогда размер поля 170\*3 = 510) и записал в переменную TILE_SIZE:
```py
TILE_SIZE = 170 # В самом начале (определение)

# А вот это в части рисования
pygame.draw.rect(sc, BLACK, (455, 60, 510, 510), 10)
for i in range(1,3):
    pygame.draw.line(sc, BLACK, (455+i*TILE_SIZE, 60), (455+i*TILE_SIZE, 60+510), 10)
    pygame.draw.line(sc, BLACK, (455, 60+i*TILE_SIZE), (455+510, 60+i*TILE_SIZE), 10)
```
![image](https://user-images.githubusercontent.com/56085790/140987995-4f87444b-42d7-406a-b0cf-ff815f1a15d2.png)  
### Определяем ход игрока
Чтобы сделать ход в крестиках-ноликах, нужно нажать левой кнопкой мыши на нужное место на экране. Давайте сделаем двойной цикл, который показывает нам координаты каждой клетки:
```py
# Управление
for event in pygame.event.get():
    if event.type == pygame.MOUSEBUTTONUP and event.button == 1:
        for i in range(3):
            for j in range(3):           # Проверяем каждую координату на позиции мыши
                if 455+j*TILE_SIZE < pos[0] < 455+(j+1)*TILE_SIZE and 60+i*TILE_SIZE < pos[1] < 60+(i+1)*TILE_SIZE:
                    print(i, j)
```
Теперь мы можем найти клетку, в которую тыкает игрок. Давайте сделаем ход! Вместо `print()` в конце предыдущего кода пишем следующее:
```py
if table[i][j] == 0:  # Мы же не можем поставить фигуру на клетку, где уже есть крестик или нолик?
    table[i][j] = turn  # Здесь у нас записано, чей сейчас ход
    if check_win():  # Проверяем, победил ли игрок, сделавший ход 
        winner = turn   # Тот, кто ходил последний, и будет победителем
    else:
        turn = 1 if turn == 2 else 2  # И только после этого передаём ход следующему игроку 
```
### Рисование фигур
Мы уже можем выиграть в крестики-нолики, не видя ничего на экране xD. Теперь нужно нарисовать сами фигуры!
Алгоритм такой же, как с определением клетки:
```py
for i in range(3):
    for j in range(3):
        if table[i][j] == 1:
            pygame.draw.line(sc, RED, (455+j*TILE_SIZE+10, 60+i*TILE_SIZE+10), (455+(j+1)*TILE_SIZE-10, 60+(i+1)*TILE_SIZE-10), 10)
            pygame.draw.line(sc, RED, (455+(j+1)*TILE_SIZE-10, 60+i*TILE_SIZE+10), (455+j*TILE_SIZE+10, 60+(i+1)*TILE_SIZE-10), 10)
        elif table[i][j] == 2:
            pygame.draw.circle(sc, LIGHT_GREEN, (455+(j+0.5)*TILE_SIZE, 60+(i+0.5)*TILE_SIZE), 75, 10)
```
Да, определять координаты каждого элемента на экране — одна из самых сложных задач в нашем деле. Чаще всего это получается методом проб и ошибок:  
![image](https://user-images.githubusercontent.com/56085790/141008235-1136723a-eb31-463d-8cb0-70d3ec8ede12.png)  
### Последние штрихи
Сейчас при победе одного из игроков или ничьей ничего не происходит. Исправим это, добавив надпись!
```py
# Снова добавляем новый код в определение
font2 = pygame.font.SysFont('times_new_roman', 72)
wintext = font.render('Победа', True, BLACK)
tietext = font2.render('Ничья', True, BLACK)
xtext = font.render('X', True, RED)
otext = font.render('O', True, LIGHT_GREEN)

# И небольшой алгоритм проверки победителя и ничьей в конце:
if winner:
    sc.blit(wintext, (100, 250))
    if winner == 1:
        sc.blit(xtext, (340, 250))
    else:
        sc.blit(otext, (340, 250))
elif sum(table, []).count(0) == 0:  # Проверяет, есть ли пустые клетки в игре
    sc.blit(tietext, (100, 250))

```
![image](https://user-images.githubusercontent.com/56085790/141016673-36dc83cd-3999-44b4-b8bd-8742d5447ba1.png)  
Полный код можно посмотреть на [Диске](https://disk.yandex.ru/d/wueDsYmkqlHs2A) (файл tictactoe.py)  
Теперь у нас есть первая полноценная игра!