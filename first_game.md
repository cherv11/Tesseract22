# Первая игра на Pygame

Итак, мы немного разобрались в программировании и устройстве Питона, скачали arcade и хотим делать игры! Но с чего начать?  
С шаблона кода arcade! Когда вы начинаете любой новый проект, качайте шаблон здесь (файл называется ex.py): [ссылка](https://disk.yandex.ru/d/lQnxpveYyEQbCQ)  
Давайте разберём его по кусочкам.  
```py
import arcade
from collections import defaultdict

WHITE = (255, 255, 255)
YELLOW = (255, 255, 0)
YELLANGE = (255, 192, 0)
ORANGE = (255, 128, 0)
BLUE = (0, 0, 225)
LIGHT_BLUE = (135, 208, 250)
RED = (255, 0, 0)
DARK_RED = (128, 0, 0)
GREEN = (64, 255, 64)
LIGHT_GREEN = (128, 255, 128)
BLACK = (0, 0, 0)
BROWN = (96, 38, 0)
GREY = (128, 128, 128)

WINDOW_WIDTH = 1920
WINDOW_HEIGHT = 1080
WINDOW_TITLE = "Test Game v0.0.1"
```
Здесь мы импортируем библиотеку arcade и defaultdict из библиотеки collections. defaultdict — это такой словарь, который не будет ругаться на эту ошибку:
```py
a = {1: "Первый"}
print(a[2])

>>> KeyError: 2
```
Далее мы определяем много разных цветов (в arcade есть свои цвета, например, ниже мы используем `arcade.csscolor.CORNFLOWER_BLUE`, но это традиция))  
После мы задаём размеры и заголовок окна. Вы можете заметить, что пока это обычные переменные, мы поместим их в функцию позже.  
Параметры в начале файла называются глобальными переменными. Рекомендую выносить все важные числа и строки таким образом, чтобы с ними было удобно работать.

## Ивенты
В arcade игра — это класс. Когда мы пишем игру, мы наследуем класс от класса из библиотеки. Это значит, что в него уже встроено множество функций, а нам нужно дописать функционал игры:

```py
class GameView(arcade.View):
    def __init__(self):
        super().__init__()
        self.mouse_pos = (0, 0)
        self.mouse_rel = (0, 0)
        self.keys = defaultdict(bool)
```
В этом классе есть функции, называемые ивентами. Ивент, он же событие — это функция, которая будет вызываться сама, когда происходит определённое действие:
```py
    def on_key_press(self, key, modifiers):
        self.keys[key] = True
        if self.keys[arcade.key.Q] or self.keys[arcade.key.ESCAPE]:
            arcade.close_window()
        if self.keys[arcade.key.F]:
            if not os.path.exists('screenshots'):
                os.mkdir('screenshots')
            arcade.get_image().save(time.strftime("screenshots/%d.%m.%Y_%H-%M.png", time.localtime()), 'PNG')
```
Ивент on_key_press срабатывает тогда, когда нажимается клавиша. Важно знать: нажатие и отпускание клавиши — разные ивенты.  
Здесь мы записываем нажатую клавишу в словарь, чтобы потом использовать список нажатых в данный момент клавиш (при отпускании клавиши нужно будет выключить её), а также определяем логику окна: выход по нажатию `Q` или `Esc`, а ещё функция скриншота на клавишу `F`, который сохраняется в папку `screenshots`.  
Аналогичным образом в других ивентах мы сохраняем позицию мыши и её кнопок.  
А вот эти два ивента самые важные: в первом мы обновляем логику (физику, счётчики) игры, а во втором рисуем на экране. Они оба вызываются в таком порядке каждый кадр, 60 раз в секунду.
```py
    def on_update(self, delta_time):
        pass

    def on_draw(self):
        self.clear()
        arcade.draw_text(str(self.mouse_pos), WINDOW_WIDTH-125, WINDOW_HEIGHT-30, DARK_RED, 16)
        arcade.draw_text(str(int(arcade.get_fps())), WINDOW_WIDTH-160, WINDOW_HEIGHT-30, DARK_RED, 16)
```
Последним в нашем файле будет функция `main()`, которая находится уже вне класса. В ней создаётся окно с нашими параметрами, задаётся цвет фона и запускается игра. Ниже эта функция вызывается:
```py
def main():
    window = arcade.Window(WINDOW_WIDTH, WINDOW_HEIGHT, WINDOW_TITLE, fullscreen=True)
    # self.window.set_mouse_visible(False)
    arcade.set_background_color(arcade.csscolor.CORNFLOWER_BLUE)
    arcade.enable_timings()
    view = GameView()
    window.show_view(view)
    arcade.run()

if __name__ == "__main__":
    main()
```
## Итак, давайте напишем что-нибудь простое. Крестики-нолики подойдут!
### Как они устроены?
Сама игра в крестики-нолики проста. Нужно просто создать двухмерный массив нулей (все переменные задаются в конструкторе):
```py
self.table = [[0, 0, 0], 
             [0, 0, 0], 
             [0, 0, 0]]
```
Теперь давайте скажем, что 0 — это пустая клетка, 1 — крестик, 2 — нолик. Когда кто-то делает ход, мы меняем 0 на 1 или 2 в нужном месте:
```py
self.table[1][2] = 2
```
Ещё нам нужна переменная, которая помнит, чей сейчас ход (пусть первым ходит нолик), и переменная, которая запишет победителя (пока что None):
```py
self.turn = 2
self.winner = None
```
И последнее: когда кто-то делает ход, нужно проверять, победил он в игре, или ещё нет:
```py
def check_win():
    for i in range(3):
        if self.table[i][0] != 0 and self.table[i][0] == self.table[i][1] and self.table[i][1] == self.table[i][2]:
            return True
        if self.table[0][i] != 0 and self.table[0][i] == self.table[1][i] and self.table[1][i] == self.table[2][i]:
            return True
    if self.table[0][0] != 0 and self.table[0][0] == self.table[1][1] and self.table[1][1] == self.table[2][2]:
        return True
    if self.table[0][2] != 0 and self.table[0][2] == self.table[1][1] and self.table[1][1] == self.table[2][0]:
        return True
    return False
```
# Attention
Сорри, я не доделал лекцию, здесь появится продолжение, а пока можете посмотреть, как это было в прошлом году на pygame:) Как видно по первым строчкам, функционал не сильно отличается от arcade.

### Простая геометрия
Теперь займёмся рисованием. Сегодня мы воспользуемся простой геометрией — функционалом для создания фигур, а на следующей паре заменим это на картинки повеселее:) Вот все функции, которые могут нам понадобиться:
```py
# Прямоугольник (вторая функция создаёт заполненный)
arcade.create_rectangle(<x центра>, <y центра>, <размер по x>, <размер по y>, <цвет>, [толщина])
arcade.create_rectangle_filled(<x центра>, <y центра>, <размер по x>, <размер по y>, <цвет>)

# Нарисуем квадрат со стороной 100 посередине экрана:
arcade.create_rectangle(sc, BLACK, (450, 250, 100, 100))

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
Полный код можно посмотреть на [Диске](https://disk.yandex.ru/d/lQnxpveYyEQbCQ) (файл tictactoe.py)  
Теперь у нас есть первая полноценная игра!
