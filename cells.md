# PSXCPU Cells Library

PSX CPU is based on standard cells.

Standard cells are placed by rows. Connections between cells are made using two layers of metal. (Channel Router)

Thus M1 runs parallel to the rows of cells, and M2 (the top layer) - perpendicularly.

Quite often automatic router place route directly on the area of the cell, if there exist free space for it:

![row_m1](/imgstore/cells/row_m1.jpg)

Ground and power supply are also by M1.

M1 is also sometimes used for the connection between adjacent cells, just inside the row of cells, without going beyond its boundaries.

Cells under a microscope look like this:

![Cells](/imgstore/cells/Cells.jpg)

Standard cell patterns may be rotated by 180<sup>o</sup>, as the power and ground in alternating rows changes, due to the fact that the cells are stacked zigzag.

In some cases, the cell is also mirrored relative to a vertical axis. It seems that fitter tool has a feedback from wire router. Wire routrer (if it is more convenient) can give fitter signal flip cell to wire it easier to get to its input/outputs.

## PSX CPU Standard cell library

PSX CPU built on CMOS technology. P and N channel transistors differ in size (P-doping is 1.3-1.5 times thicker). Diffusion is shown in yellow, and the polysilicon as purple. P-type diffusion is usually thickier rather diffusion of N-type, and is closer to the power supply. Accordingly, the diffusion of N-type is usually close to the ground.

In addition to standard NAND, NOR, and so forth, modern logic includes such elements as AO, AOI, OA and the OAI. This variation of And / Or operation with an additional result inversion (I). examples:
- 22-AOI: Y = ~ (AB | CD)
- 33-OA: Y = ( (A|B|C) & (D|E|F) )

That is, the first letter indicates what operation is used within the groups, and the second letter indicates what operation is used between groups. Before operation, the number of items placed into each group.

For convenience we have divided the types of categories and assigned them a cell color:

![Cell_types](/imgstore/cells/Cell_types.jpg)

At the moment, we made the identification of almost all cells. PSX CPU contains about 37,600 cells of 150 different types.

Cells Heatmap:

![Psxcpu_cells_map_sm](/imgstore/cells/Psxcpu_cells_map_sm.jpg)

## Cells By Category

Further description of the cell is divided into categories for easier reference.

### Inverters

Тут будут инверторы.

Чем больше ''параллельно соединенных'' транзисторов в инверторе, тем больше его drive strength.

Чем больше ''последовательно соединенных'' транзисторов в инверторе, тем дольше его propagation delay.

= Мини-версия инвертора (NOT) =

[[File:Not mini.jpg]]

Этот инвертор мы обнаружили после более крупного собрата (NOT1), поэтому NOT пришлось переименовать в NOT1, а новым NOT стал этот мелкий )))

= Инвертор (NOT1) =

[[File:Cell000.jpg]]

Ничего особенного))

= Инвертор, 2 пары транзисторов (NOT2) =

[[File:Not2.jpg]]

[[File:Not2.tif]]

{{warning}} Топологически очень похож на NAND2X:

[[File:Nand2x.jpg]]

Поэтому будь очень внимательным, не накосячь. NAND2X короче, чем NOT2, а также лапки с правой стороны идут под другими углами.

= Инвертор, 3 пары транзисторов (NOT3) =

[[File:Not3.jpg]]

Разводка M1 позволяет делать альтернативный роутинг сверху. Также на вход может приходить с М2.

[[File:Not3.tif]]

= Инвертор, 4 пары транзисторов (NOT4) =

[[File:Not4.jpg]]

= Strong инвертор, 8 пар транзисторов (NOT8) =

[[File:Pushpull.jpg]]

Эти инверторы легко обнаружить по довольно внушительному металлическому экрану (это выход) :

[[File:Pushpull M1.jpg|150px]]

Особенностью топологии является то, что вход и выход могут идти на М2, а могут альтернативными путями идти на М1.

Альтернативный роутинг через М1. Дополнительный М1 показан голубым цветом, когда вход/выход идут на M2 - этого дополнительного М1 нет.

[[File:Pushpull alternate.jpg|150px]]

Исходники : [[File:Pushpull circuit.tif]]

= Биг-мак (12 пар транзисторов) =

[[File:Not12.jpg]]

Эту хрень ни с чем не спутаешь)))

### Buffers

Тут будут усилительные буферы.

Чем больше ''параллельно соединенных'' транзисторов в буфере, тем больше его drive strength.

Чем больше ''последовательно соединенных'' транзисторов в буфере, тем дольше его propagation delay.

= Буфер обычный, 2X =

[[File:Buf2x circuit.jpg|500px]]

= Буфер обычный, 4 пары транзисторов =

[[File:Buf3x circuit.jpg|500px]]

= Буфер обычный, 4 пары транзисторов, усиленная диффузия =

[[File:Buf3x2 circuit.jpg]]

= Двойной буфер =

Двойной буфер отличается от обычного тем, что делает out = not(not(not(not(in)))), в то время как обычный буфер делает просто out = not(not(in)).

Следовательно задержка распространения у двойного буфера будет дольше, чем у обычного.

[[File:Buf.jpg]]

out = in

[[File:Buf circuit.tif]]

{{warning}} Верхняя часть этой ячейки очень похожа на NOR2X:

[[File:Nor2x.jpg]]

Не перепутай!!

= Двойной буфер, 4 пары транзисторов =

[[File:Buf2x.jpg]]

[[File:Buf2x circuit.tif]]

### Multiplexers

Тут будут всякие мультиплексоры.

= Обычный мультиплексор (MUX) =

Мультиплексор реализует конструкцию if-else в железном варианте. Если a = 0, то выход будет b, иначе с.

Для упрощения обозначения мультиплексор 2-в-1 на схемах будет обозначаться просто MUX (мультиплексоры большей разрядности достаточно редки).

[[File:21-mux.jpg]]

<syntaxhighlight lang="asm">
abc | x
000 | 0
001 | 1
010 | 0 
011 | 1
100 | 0
101 | 0
110 | 1
111 | 1
</syntaxhighlight>

Функция : '''x = a ? b : c'''

В классическом варианте вход a называется s (select), а входы c и b - i0 и i1 соответственно. То есть если select = 0, то выбираем 0й вход (i0), а значение select = 1 выбирает 1й вход (i1). Поэтому классическая таблица истинности выглядит немного по другому. Мы ещё не решили какой вариант обозначения входов использовать, но скорее всего это будет классический вариант :

'''x = (s == 0) ? i0 : i1'''

Скачать исходники стандартной ячейки со схемой : [[File:21-mux circuit.tif]]

== Flow ==

Все транзитные состояния схемы предлагаем рассмотреть читателю самостоятельно ололо

== Заметки по топологии ==

На картинке видно, что сбоку идёт какой-то левый провод, который ни к чему не присоединен. Дело в том, что он используется для альтернативного роутинга :
* Вход a / s (select) может приходить как с M2, так и через М1 по этому альтернативному маршруту
* Этот маршрут может использоваться просто для прокладки другого роутинга, то есть М1 проходит по всей ячейке, никак на неё не влияя.

Выход с мультиплексора дополнительно нагружается спаренным push/pull инвертором, то есть эта ячейка даёт дополнительный запас по мощности на выходе, чтобы к ней можно было подключать длинные шланги.

Существует также "усиленная" версия мультиплексора:

[[File:Mux strong.jpg]]

= Инвертирующий мультиплексор (IMUX) =

Отличается от обычного тем, что инвертирует входы.

[[File:IMUX.jpg]]

Также есть более мощная версия, с усиленным инвертором на выходе :

[[File:Imux strong.jpg]]

Таблица истинности:
<syntaxhighlight lang="asm">
abc | out
000 | 1
001 | 1
010 | 0
011 | 0
100 | 1
101 | 0
110 | 1
111 | 0
</syntaxhighlight>

== Заметки по топологии ==

Нижний контакт '''c''' имеет специальную площадку, чтобы альтернативно приходить с M2. Но чаще вход приходит по М1 сверху или снизу.

Вход '''b''' всегда приходит с M2.

Кстати входы '''b''' и '''c''' идут на диффузию.

[[File:Imux circuit.tif]]

= Мультиплексор 3 в 1 =

[[File:3-mux.jpg|400px]]

Если вход s1 == 1, то на выход идёт in2.

Иначе (s1 == 0) проверяется следующее условие:
* Если s0 == 0 на выход идёт in0.
* Если s0 == 1 на выход идёт in1.

Или короче:
<pre>
sel:
00: out = in0
01: out = in1
1X: out = in2
</pre>

= Мультиплексор 4 в 1 =

[[File:4mux.jpg]]

Выбирает один провод из 4-х.

<syntaxhighlight lang="asm">
s1 s0 | какой входной контакт подать на выход
0  0  | 0
0  1  | 1
1  0  | 2
1  1  | 3
</syntaxhighlight>

== Заметки по топологии ==

* Иногда входные контакты 0-3 прикрепляются к земле/питанию (то есть хардкорно устанавливаются в 0/1).
* Слева и справа есть место для транзитного прохода по М1

[[File:4mux circuit.tif]]

= Редуцированный инвертирующий мультиплексор (IMUXR) =

Иногда встречаются особенные мультиплескоры, у которых вход SEL реализован двумя контактами:

[[File:IMUXR1 trans1.jpg]]

Предполагается что sel1 != sel2 (иначе схема будет работать непредсказуемо)

Такая компоновка называется Dual-Pass Logic:

[[File:DPL.jpg]]

Как указано, такой MUX не может выдавать сигнал на большую нагрузку, поэтому его использование ограничено локальным доменом ячеек.

### Logic elements OR

### Logic elements NOR

### Logic elements AND

### Logic elements NAND

### Logic elements XOR/XNOR

### Composite logic (AO, OA, NAND+NOR etc)

### Triggers (synchronous memory elements)

### Latches (asynchronous memory elements)

### Cells for the multiplier

### Cells for use with buses (Bus keeper, Tri-states)

#### Bus Keeper

|![BUS_KEEPER_1](/imgstore/cells/poly/BUS_KEEPER_1.jpg) ![BUS_KEEPER_2](/imgstore/cells/poly/BUS_KEEPER_2.jpg)|![BUS_KEEPER](/imgstore/cells/m1/BUS_KEEPER.jpg)|
|---|---|

BUS keeper is to keep last state on the bus, basically, it's flipflop constructed by two inverter, but its output drive ability is limited. its output is connected on bus. in normal operation, it take no effect on normal logic level, but when bus is into tristate, the last logic level is keeped by bus keeper to prevent bus from floating.

![bus_keeper](/cells/cells_circuits/bus_keeper.png)

## Lambda cell parameters

Each standard cell library have so-called lambda parameters. They define in units of cells ratio of areas: how much place will take P-diffusion, how much takes the gap between the P- and N- diffusion regions, what size of wires and how they can pass through the cell when installing routing and so forth.

The unit of lambda adopted thickness of the thin element, in our case - is the thickness of polysilicon tracks.

Calculate lambda parameters of some cell.

First, we find the thickness of the polysilicon, as the average sum:

![Lambda_avg_poly](/imgstore/cells/Lambda_avg_poly.jpg)

Then we measure the width of the different parts of the cell and divide by the thickness of the polysilicon. We get the lambda values:

![Lamda_parts](/imgstore/cells/Lamda_parts.jpg)

- The width of the P-diffusion 13 lambda
- The width of the gap 7 lambda
- The width of the N-diffusion 11 lambda

Lambda parameters allow you to accurately describe the cell and can be used for example when zooming cells in specific cell research programs.

:warning: Does this section even make sense? Anyone who studies standard cells first learns about the lambda parameter.

## Pockets

PSXCPU uses P-pockets. For a long time it was not clear at all whether they are there (obviously they should be). But they are practically not visible and appear only when lapping the chip:

![pocket](/imgstore/cells/pocket.jpg)

On the domestic side this knowledge is of little use when studying the cells (it is already clear where the P-MOS FETs are), but when studying [Custom Blocks](custom.md) it is very important, because there is a whole mess of N and P. Somewhere you can guess, but there are places where without visible pockets is very difficult.
