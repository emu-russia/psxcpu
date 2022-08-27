# PSXCPU Cells Library

PSX CPU is based on standard cells.

PSX CPU built on CMOS technology. P and N channel transistors differ in size (P-doping is 1.3-1.5 times thicker). Diffusion is shown in yellow, and the polysilicon as purple. P-type diffusion is usually thickier rather diffusion of N-type, and is closer to the power supply. Accordingly, the diffusion of N-type is usually close to the ground.

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

## Cells Addressing

Чтобы определиться с соединением между двумя блоками необходимо разработать механизм адресации на топологии процессора.

Как мы уже ранее решили - процессор делится на 4 больших части: 00, 01, 02, 03.

Поэтому адресация между ячейками будет базироваться на этих 4 частях.

Ряды стандартных ячеек будут нумероваться слева-направо, внутри каждой из частей, начиная с `00`.

Внутри ряда ячейки будет нумероваться сверху-вниз, начиная с `00`.

Количество рядов в каждой из частей:

![Psxcpu-cell-rows](/imgstore/cells/Psxcpu-cell-rows.jpg)

- Part 00: 82 rows
- Part 01: 81 rows
- Part 02: 66 rows (counting from 16)
- Part 03: 81 rows

## Cells Map

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

:warning: Топология и схемы ячеек получались в разное время и в разной последовательности. Иногда например находилась ячейка, а потом её усиленная версия. Но оказывалась что есть ещё "слабее" версия первой ячейки, поэтому обозначения их "мощности" (цифры после названия) весьма хаотичны.

:warning: Некоторые ячейки вообще не понятно как называть, большая часть таких вынесены в категорию "композитных".

Transistor schematic sources for all cells can be found here: https://drive.google.com/drive/u/2/folders/1ZomLORsXA5cFluM0va30vhQP0l_kxcaR

### Inverters

Тут будут инверторы.

Чем больше _параллельно соединенных_ транзисторов в инверторе, тем больше его drive strength.

Чем больше _последовательно соединенных_ транзисторов в инверторе, тем дольше его propagation delay.

#### Мини-версия инвертора (NOT)

![Not_mini](/imgstore/cells/Not_mini.jpg)

Этот инвертор мы обнаружили после более крупного собрата (NOT1), поэтому NOT пришлось переименовать в NOT1, а новым NOT стал этот мелкий )))

#### Инвертор (NOT1)

![Cell000](/imgstore/cells/Cell000.jpg)

Ничего особенного))

#### Инвертор, 2 пары транзисторов (NOT2)

![Not2](/imgstore/cells/Not2.jpg)

:warning: Топологически очень похож на NAND2X:

![Nand2x](/imgstore/cells/Nand2x.jpg)

Поэтому будь очень внимательным, не накосячь. NAND2X короче, чем NOT2, а также лапки с правой стороны идут под другими углами.

#### Инвертор, 3 пары транзисторов (NOT3)

![Not3](/imgstore/cells/Not3.jpg)

Разводка M1 позволяет делать альтернативный роутинг сверху. Также на вход может приходить с М2.

#### Инвертор, 4 пары транзисторов (NOT4)

![Not4](/imgstore/cells/Not4.jpg)

#### Strong инвертор, 8 пар транзисторов (NOT8)

![Pushpull](/imgstore/cells/Pushpull.jpg)

Эти инверторы легко обнаружить по довольно внушительному металлическому экрану (это выход) :

![Pushpull_M1](/imgstore/cells/Pushpull_M1.jpg)

Особенностью топологии является то, что вход и выход могут идти на М2, а могут альтернативными путями идти на М1.

Альтернативный роутинг через М1. Дополнительный М1 показан голубым цветом, когда вход/выход идут на M2 - этого дополнительного М1 нет.

![Pushpull_alternate](/imgstore/cells/Pushpull_alternate.jpg)

#### Биг-мак (12 пар транзисторов)

![Not12](/imgstore/cells/Not12.jpg)

Эту хрень ни с чем не спутаешь :)

### Buffers

Тут будут усилительные буферы.

Чем больше _параллельно соединенных_ транзисторов в буфере, тем больше его drive strength.

Чем больше _последовательно соединенных_ транзисторов в буфере, тем дольше его propagation delay.

#### Буфер обычный, 2X

![Buf2x_circuit](/imgstore/cells/Buf2x_circuit.jpg)

#### Буфер обычный, 4 пары транзисторов

![Buf3x_circuit](/imgstore/cells/Buf3x_circuit.jpg)

#### Буфер обычный, 4 пары транзисторов, усиленная диффузия

![Buf3x2_circuit](/imgstore/cells/Buf3x2_circuit.jpg)

#### Двойной буфер

Двойной буфер отличается от обычного тем, что делает out = not(not(not(not(in)))), в то время как обычный буфер делает просто out = not(not(in)).

Следовательно задержка распространения у двойного буфера будет дольше, чем у обычного.

![Buf](/imgstore/cells/Buf.jpg)

out = in

:warning: Верхняя часть этой ячейки очень похожа на NOR2X:

![Nor2x](/imgstore/cells/Nor2x.jpg)

Не перепутай!!

#### Двойной буфер, 4 пары транзисторов

![Buf2x](/imgstore/cells/Buf2x.jpg)

### Multiplexers

Тут будут всякие мультиплексоры.

#### Обычный мультиплексор (MUX)

Мультиплексор реализует конструкцию if-else в железном варианте. Если a = 0, то выход будет b, иначе с.

Для упрощения обозначения мультиплексор 2-в-1 на схемах будет обозначаться просто MUX (мультиплексоры большей разрядности достаточно редки).

![21-mux](/imgstore/cells/21-mux.jpg)

```
abc | x
000 | 0
001 | 1
010 | 0 
011 | 1
100 | 0
101 | 0
110 | 1
111 | 1
```

Функция: '''x = a ? b : c'''

В классическом варианте вход a называется s (select), а входы c и b - i0 и i1 соответственно. То есть если select = 0, то выбираем 0й вход (i0), а значение select = 1 выбирает 1й вход (i1). Поэтому классическая таблица истинности выглядит немного по другому. Мы ещё не решили какой вариант обозначения входов использовать, но скорее всего это будет классический вариант :

'''x = (s == 0) ? i0 : i1'''

Все транзитные состояния схемы предлагаем рассмотреть читателю самостоятельно ололо.

На картинке видно, что сбоку идёт какой-то левый провод, который ни к чему не присоединен. Дело в том, что он используется для альтернативного роутинга:
- Вход a / s (select) может приходить как с M2, так и через М1 по этому альтернативному маршруту
- Этот маршрут может использоваться просто для прокладки другого роутинга, то есть М1 проходит по всей ячейке, никак на неё не влияя.

Выход с мультиплексора дополнительно нагружается спаренным push/pull инвертором, то есть эта ячейка даёт дополнительный запас по мощности на выходе, чтобы к ней можно было подключать длинные шланги.

Существует также "усиленная" версия мультиплексора:

![Mux_strong](/imgstore/cells/Mux_strong.jpg)

#### Инвертирующий мультиплексор (IMUX)

Отличается от обычного тем, что инвертирует входы.

![IMUX](/imgstore/cells/IMUX.jpg)

Также есть более мощная версия, с усиленным инвертором на выходе:

![Imux_strong](/imgstore/cells/Imux_strong.jpg)

Таблица истинности:
```
abc | out
000 | 1
001 | 1
010 | 0
011 | 0
100 | 1
101 | 0
110 | 1
111 | 0
```

Нижний контакт '''c''' имеет специальную площадку, чтобы альтернативно приходить с M2. Но чаще вход приходит по М1 сверху или снизу.

Вход '''b''' всегда приходит с M2.

Кстати входы '''b''' и '''c''' идут на диффузию.

#### Мультиплексор 3 в 1

![3-mux](/imgstore/cells/3-mux.jpg)

Если вход s1 == 1, то на выход идёт in2.

Иначе (s1 == 0) проверяется следующее условие:
- Если s0 == 0 на выход идёт in0.
- Если s0 == 1 на выход идёт in1.

Или короче:

```
sel:
00: out = in0
01: out = in1
1X: out = in2
```

#### Мультиплексор 4 в 1

![4mux](/imgstore/cells/4mux.jpg)

Выбирает один провод из 4-х.

```
s1 s0 | какой входной контакт подать на выход
0  0  | 0
0  1  | 1
1  0  | 2
1  1  | 3
```

- Иногда входные контакты 0-3 прикрепляются к земле/питанию (то есть хардкорно устанавливаются в 0/1).
- Слева и справа есть место для транзитного прохода по М1

#### Редуцированный инвертирующий мультиплексор (IMUXR)

Иногда встречаются особенные мультиплескоры, у которых вход SEL реализован двумя контактами:

![IMUXR1_trans1](/imgstore/cells/IMUXR1_trans1.jpg)

Предполагается что sel1 != sel2 (иначе схема будет работать непредсказуемо)

Такая компоновка называется Dual-Pass Logic:

![DPL](/imgstore/cells/DPL.jpg)

Как указано, такой MUX не может выдавать сигнал на большую нагрузку, поэтому его использование ограничено локальным доменом ячеек.

### Logic elements OR

#### OR, OR2X, OR3X

|OR|OR2X|OR3X|OR4|
|---|---|---|---|
|![Or_small](/imgstore/cells/Or_small.jpg)|![Or2x](/imgstore/cells/Or2x.jpg)|![Or3x](/imgstore/cells/Or3x.jpg)|![OR4X](/imgstore/cells/OR4X.jpg)|

#### 3-OR, 3-OR2X

|3-OR|3-OR2X|
|---|---|
|![3or](/imgstore/cells/3or.jpg)|![3or2x](/imgstore/cells/3or2x.jpg)|

### Logic elements NOR

#### NOR, NOR2X, NOR3X

|NOR|NOR2X|NOR3X|
|---|---|---|
|![Nor](/imgstore/cells/Nor.jpg)|![Nor2x](/imgstore/cells/Nor2x.jpg)|![Nor3x_trans](/imgstore/cells/Nor3x_trans.jpg)|

#### 3-NOR, 3-NOR2X, 3-NOR4X

|3-NOR|3-NOR2X|3-NOR4X|
|---|---|---|
|![3-nor](/imgstore/cells/3-nor.jpg)|![3-nor-2x](/imgstore/cells/3-nor-2x.jpg)|![3-nor-4x](/imgstore/cells/3-nor-4x.png)|

#### 5-NOR

![5-nor](/imgstore/cells/5-nor.jpg)

#### 6-NOR

![6-nor](/imgstore/cells/6-nor.jpg)

### Logic elements AND

#### AND

![And](/imgstore/cells/And.jpg)

#### 3-AND

![3-and](/imgstore/cells/3-and.jpg)

### Logic elements NAND

#### NAND, NAND2X, NAND3X, NAND4X

|NAND|NAND2X|NAND3X|NAND4X|
|---|---|---|---|
|![Nand](/imgstore/cells/Nand.jpg)|![Nand2x](/imgstore/cells/Nand2x.jpg)|![Nand3x](/imgstore/cells/Nand3x.jpg)|![Nand-2](/imgstore/cells/Nand-2.jpg)|

#### 3-NAND, 3-NAND2X, 3-NAND4X

|3-NAND|3-NAND2X|3-NAND4X|
|---|---|---|
|![3-nand](/imgstore/cells/3-nand.jpg)|![3nand_2x](/imgstore/cells/3nand_2x.jpg)|![3-nand_4x](/imgstore/cells/3-nand_4x.jpg)|

#### 5-NAND

![5-nand](/imgstore/cells/5-nand.jpg)

#### 6-NAND, 6-NAND3X

|6-NAND|6-NAND3X|
|---|---|
|![6-nand](/imgstore/cells/6-nand.jpg)|![6-nand3x](/imgstore/cells/6-nand3x.png)|

### Logic elements XOR/XNOR

![Xnor](/imgstore/cells/Xnor.jpg)

### Composite logic (AO, OA, NAND+NOR etc)

#### 22-OAI

![22oai](/imgstore/cells/22oai.jpg)

22-OAI реализует функцию Y = NAND(a|b, c|d).

Также ещё такой вентиль называется 2-2 OR/NAND MUX. Практическая польза от такого вентиля пока не ясна. Скорее всего такое сочетание часто используется, поэтому разработчики добавили специальную ячейку для этого.

#### 22-AOI

![22aoi](/imgstore/cells/22aoi.jpg)

Реализует функцию Y = NOR(a&b, c&d)  (2-2 AND/NOR MUX).

#### NOR & NAND

![Nor_nand](/imgstore/cells/Nor_nand.jpg)

Y = NOR(a,b) & NAND(c,d)

Если A или B = 1, то выход 0. Если C и D = 1, то выход 0. В остальных случаях выход 1.

```
abcd
0000	1			
0001    1
0010    1
0011    0
0100    0
0101    0
0110    0
0111    0
1000    0
1001    0
1010    0
1011    0
1100	0    
1101    0
1110    0
1111    0
```

#### NAND | NOR

![Nand_or_nor](/imgstore/cells/Nand_or_nor.jpg)

Y = NAND(a,b) | NOR(c,d)

Если A или B = 0, то выход 1. Если C и D = 0, то выход 1. В остальных случаях выход 0.

```
abcd
0000	1
0001    1
0010    1
0011    1
0100    1
0101    1
0110    1
0111	1
1000    1
1001    1
1010    1
1011    1
1100    1
1101    0
1110    0
1111	0
```

#### 12-OAI

![12oai](/imgstore/cells/12oai.jpg)

y = ~ (a & (b|c));

#### 12-AOI

![12-aoi](/imgstore/cells/12-aoi.png)

y = ~ (a | (b&c));

#### 31-AOI

![31-AOI](/imgstore/cells/31-AOI.jpg)

```
abcd
0000	1
0001    0
0010    1
0011    0
0100    1
0101    0
0110    1
0111	0
1000    1
1001    0
1010    1
1011    0
1100    1
1101    0
1110	0    
1111	0

nand(a,b,c) & ~d
```

#### NAND & XOR

![Nand_and_xor](/imgstore/cells/Nand_and_xor.jpg)

Используется в счётчиках, в роли элемента XOR.

Вверху находится операция NAND(a,b). Когда a и b = 1, на выходе 0. Иначе выход идёт с нижней половины.

Нижняя половина это операция XOR(c,d)

![Counter2](/imgstore/cells/Counter2.jpg)

#### NOR | AND

![Nor_or_and](/imgstore/cells/Nor_or_and.png)

Используется в счётчиках, в роли элемента XNOR.

![Counter1](/imgstore/cells/Counter1.jpg)

### Triggers (synchronous memory elements)

Edge-triggered схемы срабатывают не по уровню, а по факту смены уровня с 0 на 1 (задний фронт, raising edge, posedge в Verilog) или с 1 на 0 (передний фронт, falling edge, negedge в Verilog).

![Edges](/imgstore/cells/Edges.jpg)

Смысл этого в том, чтобы "открыть" триггер только в первую половину '''полутакта''' или в последнюю. То есть мы можем по идее умножить частоту, если использовать два триггера, один из которых настроен на задний фронт, а другой на передний.

Если на входе "z" (отсоединен), то триггер не меняет своего значения. Такой способ позволяет сэкономить и не делать отдельный вход Enable.

Либо мы можем за 1 полутакт сделать сразу два действия, опять же используя этот трюк. Первое действие мы вешаем на триггер по заднему фронту, а второе - на триггер по переднему фронту. Память DDR так и работает.

Активная логика отличается от пассивной тем, что умеет запоминать некоторые состояния (в данном случае триггеры запоминают входное значение).

Семейство триггеров по нарастающему фронту выглядит примерно так (в большинстве это довольно крупные ячейки, состоящие из нескольких частей):

![DFFs](/imgstore/cells/DFFs.jpg)

Устройство современных DFF:

![Modern_dff](/imgstore/cells/Modern_dff.jpg)

Master flip-flop открывается первым, т.к. CLK=0 заведен на PMOS. Slave открывается вторым.

В стандартных ячейках PSXCPU используется немного другая схема. Вместо FF на паре инверторов+MOSFET применяется инверторы+MUX.

Если на CLK=0 первый MUX открывается для приема D, то это posedge DFF. Если на CLK=0 открывается второй MUX, а первый остается закрытым, то это negedge DFF.

Я не очень понимаю как это работает, но скорее всего вся фишка в propagation delay. Эту магию знать не обязательно, будем отталкиваться от того, что если вход DFF заходит в MUX, который открывается при CLK=0 MOSFET-ом P-типа, то это posedge DFF.

#### DFF срабатывающий по нарастающему фронту (posedge, смена CLK 0->1)

![DFF](/imgstore/cells/DFF.jpg)

Схемка для симуляции в Logisim лежит в SVN.

![DFF_logisim](/imgstore/cells/DFF_logisim.jpg)

#### DFF по нарастающему фронту, со сбросом

Взяв за основу DFF, разработчики стали плодить его модификации.

Одна из них - DFF с дополнительным входом для сброса (причем вход в инверсной логике, как это обычно принято делать для сигналов сброса).

![DFFR_circuit](/imgstore/cells/DFFR_circuit.jpg)

- Если /res (in2) равен нулю (сброс), то на выходе всегда будет 0
- Иначе схема работает как обычный DFF, устанавливая значение по нарастающему фронту

На следующей схеме не отражается тонкая механика работы мультиплексоров с задержкой:

![DFFR_logic](/imgstore/cells/DFFR_logic.png)

(схема приведена чтобы примерно понимать какие логические элементы используются)

DFF со сбросом применяются например в счётчиках, а /RES используется когда нужно сбросить счётчик.

#### DFF по нарастающему фронту, селектируемый по MUX, со сбросом

Данный вариант ячейки является гибридным: вместо обычного входа (D) на ней установлен мультиплексируемый вход.

Вероятнее всего комбинация MUX + DFF встречалась настолько часто, что разработчики решили добавить гибридную ячейку.

Схема не особо отличается от DFFR.

Топология:

![DFFR_MUX_topo](/imgstore/cells/DFFR_MUX_topo.jpg)

Транзисторная схема:

![DFFR_MUX_trans](/imgstore/cells/DFFR_MUX_trans.jpg)

Логическая схема (опять здесь не учитываются "медленные" MUXы):

![DFFR_MUX_logic](/imgstore/cells/DFFR_MUX_logic.jpg)

#### DFF по спадающему фронту (negedge)

![NDFF_trans](/imgstore/cells/NDFF_trans.jpg)

Как видно, когда CLK=0, то нижний MUX (куда приходит вход D) остается закрытым, а верхний открывается. Как уже было сказано выше - такая магия формирует negedge DFF.

### Latches (asynchronous memory elements)

Тут будут всякие level-triggered девайсы 

#### D latches

![Dlatches](/imgstore/cells/Dlatches.jpg)

/DLATCH запоминает значение когда CLK=0, DLATCH запоминает значение когда CLK=1.

Если на входе "z" (отсоединен), то защелка не меняет своего значения. Такой способ позволяет сэкономить и не делать отдельный вход Enable.

Транзисторная схема на примере /DLATCH :

![D1](/imgstore/cells/D1.jpg)

Flow на примере /DLATCH:

![Ndlatch_flow](/imgstore/cells/Ndlatch_flow.jpg)

При CLK=0 старое значение отсекается, а новое с входа in поступает на защелку. Там оно и хранится.

При CLK=1 значение на защелке циркулирует туда-сюда и поступает на выход.

Выход кстати не инвертированный (Q=in).

Защелка по CLK=1 отличается просто расположением боковых лапок.

![NDLATCH_logisim](/imgstore/cells/NDLATCH_logisim.jpg)

### Cells for the multiplier

#### Full Adder

![Full_adder](/imgstore/cells/Full_adder.jpg)

```
in1 = a
in2 = b
in3 = carry_in
out1 = carry_out
out2 = sum
```

#### Half Adder

![Half_adder](/imgstore/cells/Half_adder.jpg)

```
in1 = a
in2 = b
out1 = carry_out
out2 = sum
```

#### Carry Adder

Этот элемент по функционалу похож на Full Adder, за исключением того, что его Carry In всегда равен 1.

![Xnor_or](/imgstore/cells/Xnor_or.jpg)

```
out1 = XNOR (a,b) = sum
out2 = OR (a,b) = carry out
```

#### Weighted Sum

|WS1|WS2|
|---|---|
|![WS1_circuit](/imgstore/cells/WS1_circuit.jpg)|![WS2_circuit](/imgstore/cells/WS2_circuit.jpg)|

Не до конца понятно назначение этих ячеек, но они обычно находятся на последних стадиях умножителей. Так что скорее всего они занимаются вычислением взвешенной суммы промежуточных результатов.

Поэтому кодовое обозначение их будет "взвешенные суммы" (weighted sum, WS).

- В верхней части находится инвертирующий мультиплексор (IMUX)
- Чуть ниже два усиляющих инвертора для выходов out2/out3
- Ещё ниже 3 мультиплексора, 2 используются для входов out2/out3 и ещё один для выходного IMUX
- В самом низу находится селектирующая операция XNOR

Логические схемы:

![WS_logic](/imgstore/cells/WS_logic.jpg)

Отличия между двумя ячейками незначительные:
- Переставляется инвертор после in2/in3 мультиплексора
- Прямой/инвертированный вход in5 для out2/out3

Селектирующая операция XNOR определяет 2 поведения схемы.

WS1:

```
x = XNOR (in4, in5)

if (x = 0):
out1 = !MUX (in1, in2, in3);
out2 = !in2;
out3 = !in3;

if (x = 1):
out1 = MUX (in1, in2, in3);
out2 = out3 = !in5;
```

WS2:

```
x = XNOR(in4, in5);

if (x = 0):
out1 = MUX (in1, in2, in3);
out2 = !in2
out3 = !in3

if (x = 1):
out1 = !MUX (in1, in2, in3);
out2 = out3 = in5
```

Впервые встретились в схеме умножения матриц [MDEC IDCT](mdec.md), где они образуют цепочку:

![WS_circuit](/imgstore/cells/WS_circuit.jpg)

- WS1 и WS2 попеременно чередуются, для организации inverted carry-chain (трюк для уменьшения задержки распространения)
- На входы in4/in5 подаются

#### MUX Array

:warning: Название этой ячейки претерпело много вариаций, изначально мы решили называть её "Пайплайн", но потом упростили до "MUX Array".

Эти ячейки находятся на начальных стадиях умножителей. Ячейка состоит из 4-х частей, каждая часть представляет собой 2 связанных мультиплексора.

Входной мультиплексор по сигналу C выбирает между двумя входами, а выходной мультиплексор по сигналу D выдает наружу либо предыдущее значение, либо текущее (то есть организует своеобразную цепочку сдвига на 1 разряд).

![Pipeline](/imgstore/cells/Pipeline.jpg)

Генерализованный конвеер операций, для разделния по тактам очередности действий (4-stage). Может образовывать более длинные цепочки.

Ячейка состоит из 4 этапов (stage) конвеера, каждый этап имеет два входа (1,2) и один выход (3). Управляется схема контрольными сигналами C1/C2 и D1/D2 (соединяются они между собой или нет - неизвестно).

Также схема имеет "стартовый" вход - in (для запуска конвеера) и "финиш" (сигнализирующий о том, что конвеер остановился) - out.

В основе работы каждого из этапов лежит 2 мультиплексора :

|![Pipeline_C1_C2_flow](/imgstore/cells/Pipeline_C1_C2_flow.jpg)|![Pipeline_D1_D2_flow](/imgstore/cells/Pipeline_D1_D2_flow.jpg)|
|---|---|

Контрольные сигналы C1/C2 управляют входами 1 и 2. Контрольные сигналы D1/D2 выбирают что подавать на выход 3.

Когда C1 = 1, то на вход текущего этапа подается значение с контакта 1. Когда C2 = 1, то на вход текущего этапа подается значение с контакта 2.

Когда D1 = 1, то на выход 3 идёт выбранный вход с контактов 1/2 текущего этапа.
Когда D2 = 1, то на выход 3 идёт выход с предыдущего этапа.

По идее C1/C2 и D1/D2 должны работать согласованно и представлять собой один и тот же тактовый сигнал (CLK). Но пока это не подтверждено.
Точно известно что C1 != C2 и D1 != D2 (они не могут быть одновременно 0 0 или 1 1).

В общем и целом логика всей "колбасы" будет выглядеть следующим образом:

![Pipeline_logic](/imgstore/cells/Pipeline_logic.jpg)

:warning: Логика выхода 3 с каждого этапа инвертирована по отношению к входам 1/2 или значению с предыдущего этапа.

Выходной сигнал `out` (stop) дополнительно подтягивается небольшим буфером.

Какой-то особой логики искать тут бессмысленно, равно как и искать аналогов в стандартных схемах умножителей.

Наряду с ячейками Weight sum это просто "ячейка-помощник", которая заменяет собой 8 мультиплексоров, для оптимизации места на кристалле.

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
