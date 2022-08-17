---
layout: default
---

<h2 id="intro">Introdução</h2>

Neste curso introdutório eu vou ensinar os primeiros passos para escrever em linguagem assembly 6502. Tudo o que você vê aqui existe graças ao [Nick Morgam](http://skilldrick.co.uk). Dentro da licença deste material, eu traduzi e adaptei o [original](https://github.com/skilldrick/easy6502) para o contexto deste curso.

O processador 6502 foi um gigante dos anos 70 e 80, presente em máquinas como:

[BBC Micro](http://en.wikipedia.org/wiki/BBC_Micro),
[Atari 2600](http://en.wikipedia.org/wiki/Atari_2600),
[Commodore 64](http://en.wikipedia.org/wiki/Commodore_64),
[Apple II](http://en.wikipedia.org/wiki/Apple_II), e o [Nintendo Entertainment
System](http://en.wikipedia.org/wiki/Nintendo_Entertainment_System). 

O Bender do Futurama [tem um 6502 como cérebro](http://www.transbyte.org/SID/SID-files/Bender_6502.jpg).

Até o Exterminador do Futuro [foi programado em 6502](http://www.pagetable.com/docs/terminator/00-37-23.jpg).


Então, mesmo assim por que você iria querer aprender 6502?

Com a ascensão do fenômeno retro-gaming, fazer novos jogos para máquinas antigas está em moda. 
Mesmo com as ferramentas mais modernas, o conhecimento da linguagem fundamental da máquina é essencial para extrair todo o suco que esses chips de 40 anos têm a oferecer. (em 2025 o 6502 faz 50 anos!) 

Além disso, o 6502 ainda é fabricado [Western Design Center](http://www.westerndesigncenter.com/wdc/w65c02s-chip.cfm) e pode ser comprado em praticamente qualquer lugar, o que é muito conveniente.

Nas próximas horas veremos as instruções básicas do 6502 em uma plataforma fictícia, na forma de um simulador desenvolvido por [Stian Søreng](http://www.6502asm.com). Nele você pode simular quase toda a lógica do 6502 (exceto interrupções) e produzir gráficos numa tela de 32x32 pixels com 16 cores (a mesma paleta do Comodore 64). Isto possibilita aprender a lidar com a CPU sem ter que preocupar com chips periféricos, como *PPU*s, *APU*s e *mappers*, por exemplo. Se depois de terminar esta aula você estiver se sentindo aventureiro, [acesse o simulador](http://www.6502asm.com/index.html) ou quem sabe a [versão beta](http://www.6502asm.com/beta/index.html), ambos com diversos exemplos para explorar.

Este curso **não é** para ensinar programação em jogos de NES/Famicom. Porém, é uma intrudução imperdível caso seja este o seu objetivo. Pense assim: para um dia ser um programador de jogos para NES/Famicom você terá que saber não só assembly 6502 como também conhecer a arquitetura do console e dos cartuchos. Hoje é o proverbial **primeiro passo** dessa jornada.

Mas a melhor razão eu guardei por último: no mundo da programação de jogos modernos, há quem use as comodidades do Unity, UE, Godot, entre muitos outros. Estes motores democratizaram a criação de publicação de jogos, o que é sensacional. Mas neste meio saber programação te da o poder de torcer e espremer estas plataformas à sua vontade. Mas se você tem uma compreensão razoável de assembly você passa a ver estas ferramentas com outros olhos pois você entende a organização do silício onde seu programa está rodando. Não há exagero em dizer que um pouquinho de assembly vai te transformar num verdadeiro mago dos jogos. Neste sentido o 6502 é um bom começo pois de um assembly antigo para um moderno o que muda é a complexidade das CPUs, mas a filosofia continua a mesma.

Um último comentário antes de começarmos: eu poderia ter traduzido os comando do simulador, como Assemble para Montar, Run para Rodar, Step para Passo, etc. Mas isso não ajuda tanto quanto possa parecer para um leigo. No mundo da computação é comum usarmos estes termos como eles chegam do inglês, como mouse, notebook ou pendrive. É mais fácil aprender o que Assemble e Assembly significam na prática do que ficar tentando traduzir tudo.

<h2 id="first-program">Nosso primeiro programa</h2>

Opa, então vamos mergulhar nessa de cabeça! Essa coisinha aí em baixo é um pequeno [Simulador e montador 6502 em JavaScript](https://github.com/skilldrick/6502js) que o grande [Nick Morgan](http://skilldrick.co.uk/) adaptou para este curso.
Clique **Assemble** e depois **Run** para montar e rodar o pedaço de código assembly.

{% include start.html %}
LDA #$01
STA $0200
LDA #$05
STA $0201
LDA #$08
STA $0202
{% include end.html %}

Com sorte, a área preta à direita (representando um monitor) tem agora 3 pixels coloridos no canto superior esquerdo.
(Se não tiver, talvez você precise atualizar seu browser para algo mais moderno como Chrome, Edge ou Firefox)

Certo, então o que este programa está realmente fazendo? Vamos fazer um passo-a-passo no depurador (*debugger*).
Pressione **Reset**, depois acione o **Debugger** para dar início à depuração. Clique em **Step** uma única vez e preste bastante atenção às mudanças: o programa vai avançar uma instrução e `A=` mudou de `$00` para `$01`, enquanto `PC=` mudou de `$0600` para `$0602`.

Qualquer número precedido por `$` estão no **formato hexadecimal**. Se você não conhece, eu recomendo uma lida com mais atenção na [Wikipedia](https://pt.wikipedia.org/wiki/Sistema_de_numera%C3%A7%C3%A3o_hexadecimal). Além disso, valores precedidos de `#`são valores literais, enquanto que aqueles que não tem `#` se referem à endereços de memória.

De posse desse conhecimento, você deve ser capaz de identificar a instrução 
`LDA #$01` carregou o valor `$01` (lembre-se, `#` indica um valor literal) no registro `A`. Eu vou dar mais detalhes sobre o que são registros na próxima seção. Por hora, pense que é uma caixinha onde a CPU guarda valores temporariamente.

Pressione **Step** mais uma vez para exeecutar a segunda intrução. O canto superior esquerdo da nossa telinha deve estar mostrando um pixel branco. Neste computador de faz-de-conta, os endereços de memória localizados entre `$0200` à `$05ff` estão mapeados com os 1024 pixels da tela (32x32). Pense nesta região da memória como sendo a **memória de vídeo** do nosso computador/console. Inicialmente todos estão pretos (valem `$00`) e cada valor consecutivo de `$01` até `$0f` representa uma das cores de uma paleta pré-definida (`$00` é preto e `$01` é branco, e assim vai). Os pixels são mapeados em sequência, então `$0200` + `$20` = `$0220` representa o primeiro pixel da seguna linha (`$20` é 32 em hexa). Esta arquitetura é muito mais simples do que um computador real (mesmo naquela época), mas não se engane! Este exemplo é muito mais importante do que possa parecer: se cada pixel é um endereço de memória e cada cor é um valor, podemos explorar este recurso para aprender como manipular a memória e nos acostumar com aritimética hexadecimal. 

Então, a instrução `STA $0200` guarda o valor que estava temporariamente no registro `A` no endereço de memória `$0200` (repare que `$0200` não tem `#`, então não é um valor, mas sim um endereço). Clique em **Step** mais quatro vezes para executar o resto das instruções. Fique de olho em `A` e observe como ele muda a cada passo.

### Exercícios ###

1. Explore a paleta de 16 cores e tente mudar as cores dos pixels.

2. Mude alguns pixel em outra parte da tela. Digamos, no canto inferior direito? (endereço `$05ff`).

3. Adicione mais instruções para desenhar mais pixels.

4. Para pensar: para uma tela de 256x240 pixel (uma resolução comum nos anos 80 época), quanto de memória seria necessário para manter este mesmo esquema de mapeamento de vídeo? Quando chegar na resposta, reflita sobre isso: o 6502 consegue endereçar somente 65536 valores diferentes (também conhecido como 64k).

<h2 id='registers'>Registros e flags</h2>

Nós já vimos um pouco do que há por dentro do processador, aquela parte sobre 
`A`, `PC`, etc.), mas o que isso significa?

A primeira linha mostra os **registros** `A`, `X` e `Y`. Pense no significado da palavra registro: uma anotação, uma informação guardada. O 6502 tem apenas três registros, `A`, `X` e `Y`, que são três espaços para guardar valores temporariamente. Cada um deles é capaz de guardar 1 byte de informação (um valor hexa entre `$00` e `$ff`) e estes valores são utilizados em nossos programas. O `A` é chamado **acumulador**, enquanto `X` e `Y` são contadores. Eles podem ser úteis em diversas situações mas não pense que são idênticos. Cada um têm suas próprias características e domniná-las será essencial.

`SP` significa *stack pointer*, ou ponteiro da pilha. Eu não vou falar da pilha ainda, mas por hora basta dizer que é uma região da memória e que o `SP`
indica uma posição nesta memória, aumentando ou diminuindo a cada byte que é adicionado ou removido dela.

`PC` é o contador de programa. É a forma como o processador sabe em que ponto do programa ele está. O programa é executado em sequência, uma instrução de cada vez, com `PC`sempre indicando o endereço da instrução a ser executada. Nós temos certo controle sobre `PC`, o que significa que podemos pular instruções, voltar, ou ir para outra parte completamente diferente do programa. Em nosso computador de faz-de-conta, `PC` sempre começa no endereço `$0600`. Este é o endereço onde o 6502 espera encontrar o início do programa. O endereço inicial é diferente em outras máquinas, mas ele sempre existe. Quando for aplicar esta lição ao NES ou ao C64, lembre-se de pesquisar onde o `PC` começa naquela máquina.

Por último, vamos falar sobre *flags*. Imagine os bandeirinhas do fotebol, prontos a levantar uma bandeira para mostrar ao árbitro de campo algo que ele pode não ter visto. É a mesma coisa: as flags, ou bandeiras, são sinais de que algo aconteceu dentro da CPU, fora do nosso campo de visão. O 6502 tem 7 flags e cada uma é apenas um bit (0 ou 1). Ou seja, todas vivem dentro de um único byte. Veremos mais sobre flags em alguns minutos.


<h2 id='instructions'>Instructions</h2>

Instructions in assembly language are like a small set of predefined functions.
All instructions take zero or one arguments. Here's some annotated
source code to introduce a few different instructions:

{% include start.html %}
LDA #$c0  ;Load the hex value $c0 into the A register
TAX       ;Transfer the value in the A register to X
INX       ;Increment the value in the X register
ADC #$c4  ;Add the hex value $c4 to the A register
BRK       ;Break - we're done
{% include end.html %}

Assemble the code, then turn on the debugger and step through the code, watching
the `A` and `X` registers. Something slightly odd happens on the line `ADC #$c4`.
You might expect that adding `$c4` to `$c0` would give `$184`, but this
processor gives the result as `$84`. What's up with that?

The problem is, `$184` is too big to fit in a single byte (the max is `$FF`),
and the registers can only hold a single byte.  It's OK though; the processor
isn't actually dumb. If you were looking carefully enough, you'll have noticed
that the carry flag was set to `1` after this operation. So that's how you
know.

In the simulator below **type** (don't paste) the following code:

    LDA #$80
    STA $01
    ADC $01

{% include widget.html %}

An important thing to notice here is the distinction between `ADC #$01` and
`ADC $01`. The first one adds the value `$01` to the `A` register, but the
second adds the value stored at memory location `$01` to the `A` register.

Assemble, check the **Monitor** checkbox, then step through these three
instructions. The monitor shows a section of memory, and can be helpful to
visualise the execution of programs. `STA $01` stores the value of the `A`
register at memory location `$01`, and `ADC $01` adds the value stored at the
memory location `$01` to the `A` register. `$80 + $80` should equal `$100`, but
because this is bigger than a byte, the `A` register is set to `$00` and the
carry flag is set. As well as this though, the zero flag is set. The zero flag
is set by all instructions where the result is zero.

A full list of the 6502 instruction set is [available
here](http://www.6502.org/tutorials/6502opcodes.html) and
[here](http://www.obelisk.me.uk/6502/reference.html) (I usually refer to
both pages as they have their strengths and weaknesses). These pages detail the
arguments to each instruction, which registers they use, and which flags they
set. They are your bible.

### Exercises ###

1. You've seen `TAX`. You can probably guess what `TAY`, `TXA` and `TYA` do,
   but write some code to test your assumptions.
2. Rewrite the first example in this section to use the `Y` register instead of
   the `X` register.
3. The opposite of `ADC` is `SBC` (subtract with carry). Write a program that
   uses this instruction.


<h2 id='branching'>Branching</h2>

So far we're only able to write basic programs without any branching logic.
Let's change that.

6502 assembly language has a bunch of branching instructions, all of which
branch based on whether certain flags are set or not. In this example we'll be
looking at `BNE`: "Branch on not equal".

{% include start.html %}
  LDX #$08
decrement:
  DEX
  STX $0200
  CPX #$03
  BNE decrement
  STX $0201
  BRK
{% include end.html %}

First we load the value `$08` into the `X` register. The next line is a label.
Labels just mark certain points in a program so we can return to them later.
After the label we decrement `X`, store it to `$0200` (the top-left pixel), and
then compare it to the value `$03`.
[`CPX`](http://www.obelisk.me.uk/6502/reference.html#CPX) compares the
value in the `X` register with another value. If the two values are equal, the
`Z` flag is set to `1`, otherwise it is set to `0`.

The next line, `BNE decrement`, will shift execution to the decrement label if
the `Z` flag is set to `0` (meaning that the two values in the `CPX` comparison
were not equal), otherwise it does nothing and we store `X` to `$0201`, then
finish the program.

In assembly language, you'll usually use labels with branch instructions. When
assembled though, this label is converted to a single-byte relative offset (a
number of bytes to go backwards or forwards from the next instruction) so
branch instructions can only go forward and back around 256 bytes. This means
they can only be used to move around local code. For moving further you'll need
to use the jumping instructions.

### Exercises ###

1. The opposite of `BNE` is `BEQ`. Try writing a program that uses `BEQ`.
2. `BCC` and `BCS` ("branch on carry clear" and "branch on carry set") are used
   to branch on the carry flag. Write a program that uses one of these two.


<h2 id='addressing'>Addressing modes</h2>

The 6502 uses a 16-bit address bus, meaning that there are 65536 bytes of
memory available to the processor. Remember that a byte is represented by two
hex characters, so the memory locations are generally represented as `$0000 -
$ffff`. There are various ways to refer to these memory locations, as detailed below.

With all these examples you might find it helpful to use the memory monitor to
watch the memory change. The monitor takes a starting memory location and a
number of bytes to display from that location. Both of these are hex values.
For example, to display 16 bytes of memory from `$c000`, enter `c000` and `10`
into **Start** and **Length**, respectively.

### Absolute: `$c000` ###

With absolute addressing, the full memory location is used as the argument to the instruction. For example:

    STA $c000 ;Store the value in the accumulator at memory location $c000

### Zero page: `$c0` ###

All instructions that support absolute addressing (with the exception of the jump
instructions) also have the option to take a single-byte address. This type of
addressing is called "zero page" - only the first page (the first 256 bytes) of
memory is accessible. This is faster, as only one byte needs to be looked up,
and takes up less space in the assembled code as well.

### Zero page,X: `$c0,X` ###

This is where addressing gets interesting. In this mode, a zero page address is given, and then the value of the `X` register is added. Here is an example:

    LDX #$01   ;X is $01
    LDA #$aa   ;A is $aa
    STA $a0,X ;Store the value of A at memory location $a1
    INX        ;Increment X
    STA $a0,X ;Store the value of A at memory location $a2

If the result of the addition is larger than a single byte, the address wraps around. For example:

    LDX #$05
    STA $ff,X ;Store the value of A at memory location $04

### Zero page,Y: `$c0,Y` ###

This is the equivalent of zero page,X, but can only be used with `LDX` and `STX`.

### Absolute,X and absolute,Y: `$c000,X` and `$c000,Y` ###

These are the absolute addressing versions of zero page,X and zero page,Y. For example:

    LDX #$01
    STA $0200,X ;Store the value of A at memory location $0201

Unlike zero page,Y, absolute,Y can't be used with `STX` but can be used with `LDA` and `STA`.

### Immediate: `#$c0` ###

Immediate addressing doesn't strictly deal with memory addresses - this is the
mode where actual values are used. For example, `LDX #$01` loads the value
`$01` into the `X` register. This is very different to the zero page
instruction `LDX $01` which loads the value at memory location `$01` into the
`X` register.

### Relative: `$c0` (or label) ###

Relative addressing is used for branching instructions. These instructions take
a single byte, which is used as an offset from the following instruction.

Assemble the following code, then click the **Hexdump** button to see the assembled code.

{% include start.html %}
  LDA #$01
  CMP #$02
  BNE notequal
  STA $22
notequal:
  BRK
{% include end.html %}

The hex should look something like this:

    a9 01 c9 02 d0 02 85 22 00

`a9` and `c9` are the processor opcodes for immediate-addressed `LDA` and `CMP`
respectively. `01` and `02` are the arguments to these instructions. `d0` is
the opcode for `BNE`, and its argument is `02`. This means "skip over the next
two bytes" (`85 22`, the assembled version of `STA $22`). Try editing the code
so `STA` takes a two-byte absolute address rather than a single-byte zero page
address (e.g. change `STA $22` to `STA $2222`). Reassemble the code and look at
the hexdump again - the argument to `BNE` should now be `03`, because the
instruction the processor is skipping past is now three bytes long.

### Implicit ###

Some instructions don't deal with memory locations (e.g. `INX` - increment the
`X` register). These are said to have implicit addressing - the argument is
implied by the instruction.

### Indirect: `($c000)` ###

Indirect addressing uses an absolute address to look up another address. The
first address gives the least significant byte of the address, and the
following byte gives the most significant byte. That can be hard to wrap your
head around, so here's an example:

{% include start.html %}
LDA #$01
STA $f0
LDA #$cc
STA $f1
JMP ($00f0) ;dereferences to $cc01
{% include end.html %}

In this example, `$f0` contains the value `$01` and `$f1` contains the value
`$cc`. The instruction `JMP ($f0)` causes the processor to look up the two
bytes at `$f0` and `$f1` (`$01` and `$cc`) and put them together to form the
address `$cc01`, which becomes the new program counter. Assemble and step
through the program above to see what happens. I'll talk more about `JMP` in
the section on [Jumping](#jumping).

### Indexed indirect: `($c0,X)` ###

This one's kinda weird. It's like a cross between zero page,X and indirect.
Basically, you take the zero page address, add the value of the `X` register to
it, then use that to look up a two-byte address. For example:

{% include start.html %}
LDX #$01
LDA #$05
STA $01
LDA #$07
STA $02
LDY #$0a
STY $0705
LDA ($00,X)
{% include end.html %}

Memory locations `$01` and `$02` contain the values `$05` and `$07`
respectively. Think of `($00,X)` as `($00 + X)`. In this case `X` is `$01`, so
this simplifies to `($01)`. From here things proceed like standard indirect
addressing - the two bytes at `$01` and `$02` (`$05` and `$07`) are looked up
to form the address `$0705`.  This is the address that the `Y` register was
stored into in the previous instruction, so the `A` register gets the same
value as `Y`, albeit through a much more circuitous route. You won't see this
much.


### Indirect indexed: `($c0),Y` ###

Indirect indexed is like indexed indirect but less insane. Instead of adding
the `X` register to the address *before* dereferencing, the zero page address
is dereferenced, and the `Y` register is added to the resulting address.

{% include start.html %}
LDY #$01
LDA #$03
STA $01
LDA #$07
STA $02
LDX #$0a
STX $0704
LDA ($01),Y
{% include end.html %}

In this case, `($01)` looks up the two bytes at `$01` and `$02`: `$03` and
`$07`. These form the address `$0703`. The value of the `Y` register is added
to this address to give the final address `$0704`.

### Exercise ###

1. Try to write code snippets that use each of the 6502 addressing modes.
   Remember, you can use the monitor to watch a section of memory.


<h2 id='stack'>The stack</h2>

The stack in a 6502 processor is just like any other stack - values are pushed
onto it and popped ("pulled" in 6502 parlance) off it. The current depth of the
stack is measured by the stack pointer, a special register. The stack lives in
memory between `$0100` and `$01ff`. The stack pointer is initially `$ff`, which
points to memory location `$01ff`. When a byte is pushed onto the stack, the
stack pointer becomes `$fe`, or memory location `$01fe`, and so on.

Two of the stack instructions are `PHA` and `PLA`, "push accumulator" and "pull
accumulator". Below is an example of these two in action.

{% include start.html %}
  LDX #$00
  LDY #$00
firstloop:
  TXA
  STA $0200,Y
  PHA
  INX
  INY
  CPY #$10
  BNE firstloop ;loop until Y is $10
secondloop:
  PLA
  STA $0200,Y
  INY
  CPY #$20      ;loop until Y is $20
  BNE secondloop
{% include end.html %}

`X` holds the pixel colour, and `Y` holds the position of the current pixel.
The first loop draws the current colour as a pixel (via the `A` register),
pushes the colour to the stack, then increments the colour and position.  The
second loop pops the stack, draws the popped colour as a pixel, then increments
the position. As should be expected, this creates a mirrored pattern.


<h2 id='jumping'>Jumping</h2>

Jumping is like branching with two main differences. First, jumps are not
conditionally executed, and second, they take a two-byte absolute address. For
small programs, this second detail isn't very important, as you'll mostly be
using labels, and the assembler works out the correct memory location from the
label. For larger programs though, jumping is the only way to move from one
section of the code to another.

### JMP ###

`JMP` is an unconditional jump. Here's a really simple example to show it in action:

{% include start.html %}
  LDA #$03
  JMP there
  BRK
  BRK
  BRK
there:
  STA $0200
{% include end.html %}


### JSR/RTS ###

`JSR` and `RTS` ("jump to subroutine" and "return from subroutine") are a
dynamic duo that you'll usually see used together. `JSR` is used to jump from
the current location to another part of the code. `RTS` returns to the previous
position. This is basically like calling a function and returning.

The processor knows where to return to because `JSR` pushes the address minus
one of the next instruction onto the stack before jumping to the given
location. `RTS` pops this location, adds one to it, and jumps to that location.
An example:

{% include start.html %}
  JSR init
  JSR loop
  JSR end

init:
  LDX #$00
  RTS

loop:
  INX
  CPX #$05
  BNE loop
  RTS

end:
  BRK
{% include end.html %}

The first instruction causes execution to jump to the `init` label. This sets
`X`, then returns to the next instruction, `JSR loop`. This jumps to the `loop`
label, which increments `X` until it is equal to `$05`. After that we return to
the next instruction, `JSR end`, which jumps to the end of the file. This
illustrates how `JSR` and `RTS` can be used together to create modular code.


<h2 id='snake'>Creating a game</h2>

Now, let's put all this knowledge to good use, and make a game! We're going to
be making a really simple version of the classic game 'Snake'.

Even though this will be a simple version, the code will be substantially larger
than all the previous examples. We will need to keep track of several memory
locations together for the various aspects of the game. We can still do
the necessary bookkeeping throughout the program ourselves, as before, but
on a larger scale that quickly becomes tedious and can also lead to bugs that
are difficult to spot. Instead we'll now let the assembler do some of the
mundane work for us.

In this assembler, we can define descriptive constants (or symbols) that represent
numbers. The rest of the code can then simply use the constants instead of the
literal number, which immediately makes it obvious what we're dealing with.
You can use letters, digits and underscores in a name.

Here's an example. Note that immediate operands are still prefixed with a `#`.
{% include start.html %}
  define  sysRandom  $fe ; an address
  define  a_dozen    $0c ; a constant
 
  LDA sysRandom  ; equivalent to "LDA $fe"

  LDX #a_dozen   ; equivalent to "LDX #$0c"
{% include end.html %}

The simulator widget below contains the entire source code of the game. I'll
explain how it works in the following sections.

[Willem van der Jagt](https://twitter.com/wkjagt) made a [fully annotated gist
of this source code](https://gist.github.com/wkjagt/9043907), so follow along
with that for more details.

{% include snake.html %}


### Overall structure ###

After the initial block of comments (lines starting with semicolons), the first
two lines are:

    jsr init
    jsr loop

`init` and `loop` are both subroutines. `init` initializes the game state, and
`loop` is the main game loop.

The `loop` subroutine itself just calls a number of subroutines sequentially,
before looping back on itself:

    loop:
      jsr readkeys
      jsr checkCollision
      jsr updateSnake
      jsr drawApple
      jsr drawSnake
      jsr spinwheels
      jmp loop

First, `readkeys` checks to see if one of the direction keys (W, A, S, D) was
pressed, and if so, sets the direction of the snake accordingly. Then,
`checkCollision` checks to see if the snake collided with itself or the apple.
`updateSnake` updates the internal representation of the snake, based on its
direction. Next, the apple and snake are drawn. Finally, `spinWheels` makes the
processor do some busy work, to stop the game from running too quickly. Think
of it like a sleep command. The game keeps running until the snake collides
with the wall or itself.


### Zero page usage ###

The zero page of memory is used to store a number of game state variables, as
noted in the comment block at the top of the game. Everything in `$00`, `$01`
and `$10` upwards is a pair of bytes representing a two-byte memory location
that will be looked up using indirect addressing.  These memory locations will
all be between `$0200` and `$05ff` - the section of memory corresponding to the
simulator display. For example, if `$00` and `$01` contained the values `$01`
and `$02`, they would be referring to the second pixel of the display (
`$0201` - remember, the least significant byte comes first in indirect addressing).

The first two bytes hold the location of the apple. This is updated every time
the snake eats the apple. Byte `$02` contains the current direction. `1` means
up, `2` right, `4` down, and `8` left.  The reasoning behind these numbers will
become clear later.

Finally, byte `$03` contains the current length of the snake, in terms of bytes
in memory (so a length of 4 means 2 pixels).


### Initialization ###

The `init` subroutine defers to two subroutines, `initSnake` and
`generateApplePosition`. `initSnake` sets the snake direction, length, and then
loads the initial memory locations of the snake head and body. The byte pair at
`$10` contains the screen location of the head, the pair at `$12` contains the
location of the single body segment, and `$14` contains the location of the
tail (the tail is the last segment of the body and is drawn in black to keep
the snake moving). This happens in the following code:

    lda #$11
    sta $10
    lda #$10
    sta $12
    lda #$0f
    sta $14
    lda #$04
    sta $11
    sta $13
    sta $15

This loads the value `$11` into the memory location `$10`, the value `$10` into
`$12`, and `$0f` into `$14`. It then loads the value `$04` into `$11`, `$13`
and `$15`. This leads to memory like this:

    0010: 11 04 10 04 0f 04

which represents the indirectly-addressed memory locations `$0411`, `$0410` and
`$040f` (three pixels in the middle of the display). I'm labouring this point,
but it's important to fully grok how indirect addressing works.

The next subroutine, `generateApplePosition`, sets the apple location to a
random position on the display. First, it loads a random byte into the
accumulator (`$fe` is a random number generator in this simulator). This is
stored into `$00`. Next, a different random byte is loaded into the
accumulator, which is then `AND`-ed with the value `$03`. This part requires a
bit of a detour.

The hex value `$03` is represented in binary as `00000011`. The `AND` opcode
performs a bitwise AND of the argument with the accumulator. For example, if
the accumulator contains the binary value `10101010`, then the result of `AND`
with `00000011` will be `00000010`.

The effect of this is to mask out the least significant two bits of the
accumulator, setting the others to zero. This converts a number in the range of
0&ndash;255 to a number in the range of 0&ndash;3.

After this, the value `2` is added to the accumulator, to create a final random
number in the range 2&ndash;5.

The result of this subroutine is to load a random byte into `$00`, and a random
number between 2 and 5 into `$01`. Because the least significant byte comes
first with indirect addressing, this translates into a memory address between
`$0200` and `$05ff`: the exact range used to draw the display.


### The game loop ###

Nearly all games have at their heart a game loop. All game loops have the same
basic form: accept user input, update the game state, and render the game
state. This loop is no different.


#### Reading the input ####

The first subroutine, `readKeys`, takes the job of accepting user input. The
memory location `$ff` holds the ascii code of the most recent key press in this
simulator. The value is loaded into the accumulator, then compared to `$77`
(the hex code for W), `$64` (D), `$73` (S) and `$61` (A). If any of these
comparisons are successful, the program branches to the appropriate section.
Each section (`upKey`, `rightKey`, etc.) first checks to see if the current
direction is the opposite of the new direction. This requires another little detour.

As stated before, the four directions are represented internally by the numbers
1, 2, 4 and 8. Each of these numbers is a power of 2, thus they are represented
by a binary number with a single `1`:

    1 => 0001 (up)
    2 => 0010 (right)
    4 => 0100 (down)
    8 => 1000 (left)

The `BIT` opcode is similar to `AND`, but the calculation is only used to set
the zero flag - the actual result is discarded. The zero flag is set only if the
result of AND-ing the accumulator with argument is zero. When we're looking at
powers of two, the zero flag will only be set if the two numbers are not the
same. For example, `0001 AND 0001` is not zero, but `0001 AND 0010` is zero.

So, looking at `upKey`, if the current direction is down (4), the bit test will
be zero. `BNE` means "branch if the zero flag is clear", so in this case we'll
branch to `illegalMove`, which just returns from the subroutine. Otherwise, the
new direction (1 in this case) is stored in the appropriate memory location.


#### Updating the game state ####

The next subroutine, `checkCollision`, defers to `checkAppleCollision` and
`checkSnakeCollision`. `checkAppleCollision` just checks to see if the two
bytes holding the location of the apple match the two bytes holding the
location of the head. If they do, the length is increased and a new apple
position is generated.

`checkSnakeCollision` loops through the snake's body segments, checking each
byte pair against the head pair. If there is a match, then game over.

After collision detection, we update the snake's location. This is done at a
high level like so: First, move each byte pair of the body up one position in
memory. Second, update the head according to the current direction. Finally, if
the head is out of bounds, handle it as a collision. I'll illustrate this with
some ascii art. Each pair of brackets contains an x,y coordinate rather than a
pair of bytes for simplicity.

      0    1    2    3    4
    Head                 Tail
    
    [1,5][1,4][1,3][1,2][2,2]    Starting position

    [1,5][1,4][1,3][1,2][1,2]    Value of (3) is copied into (4)
    
    [1,5][1,4][1,3][1,3][1,2]    Value of (2) is copied into (3)
    
    [1,5][1,4][1,4][1,3][1,2]    Value of (1) is copied into (2)
    
    [1,5][1,5][1,4][1,3][1,2]    Value of (0) is copied into (1)
    
    [0,5][1,5][1,4][1,3][1,2]    Value of (0) is updated based on direction

At a low level, this subroutine is slightly more complex. First, the length is
loaded into the `X` register, which is then decremented. The snippet below
shows the starting memory for the snake.

    Memory location: $10 $11 $12 $13 $14 $15

    Value:           $11 $04 $10 $04 $0f $04

The length is initialized to `4`, so `X` starts off as `3`. `LDA $10,x` loads the
value of `$13` into `A`, then `STA $12,x` stores this value into `$15`. `X` is
decremented, and we loop. Now `X` is `2`, so we load `$12` and store it into
`$14`. This loops while `X` is positive (`BPL` means "branch if positive").

Once the values have been shifted down the snake, we have to work out what to
do with the head. The direction is first loaded into `A`. `LSR` means "logical
shift right", or "shift all the bits one position to the right". The least
significant bit is shifted into the carry flag, so if the accumulator is `1`,
after `LSR` it is `0`, with the carry flag set.

To test whether the direction is `1`, `2`, `4` or `8`, the code continually
shifts right until the carry is set. One `LSR` means "up", two means "right",
and so on.

The next bit updates the head of the snake depending on the direction. This is
probably the most complicated part of the code, and it's all reliant on how
memory locations map to the screen, so let's look at that in more detail.

You can think of the screen as four horizontal strips of 32 &times; 8 pixels.
These strips map to `$0200-$02ff`, `$0300-$03ff`, `$0400-$04ff` and `$0500-$05ff`.
The first rows of pixels are `$0200-$021f`, `$0220-$023f`, `$0240-$025f`, etc.

As long as you're moving within one of these horizontal strips, things are
simple. For example, to move right, just increment the least significant byte
(e.g. `$0200` becomes `$0201`). To go down, add `$20` (e.g. `$0200` becomes
`$0220`). Left and up are the reverse.

Going between sections is more complicated, as we have to take into account the
most significant byte as well. For example, going down from `$02e1` should lead
to `$0301`. Luckily, this is fairly easy to accomplish. Adding `$20` to `$e1`
results in `$01` and sets the carry bit. If the carry bit was set, we know we
also need to increment the most significant byte.

After a move in each direction, we also need to check to see if the head
would become out of bounds. This is handled differently for each direction. For
left and right, we can check to see if the head has effectively "wrapped
around". Going right from `$021f` by incrementing the least significant byte
would lead to `$0220`, but this is actually jumping from the last pixel of the
first row to the first pixel of the second row. So, every time we move right,
we need to check if the new least significant byte is a multiple of `$20`. This
is done using a bit check against the mask `$1f`. Hopefully the illustration
below will show you how masking out the lowest 5 bits reveals whether a number
is a multiple of `$20` or not.

    $20: 0010 0000
    $40: 0100 0000
    $60: 0110 0000

    $1f: 0001 1111

I won't explain in depth how each of the directions work, but the above
explanation should give you enough to work it out with a bit of study.


#### Rendering the game ####

Because the game state is stored in terms of pixel locations, rendering the
game is very straightforward. The first subroutine, `drawApple`, is extremely
simple. It sets `Y` to zero, loads a random colour into the accumulator, then
stores this value into `($00),y`. `$00` is where the location of the apple is
stored, so `($00),y` dereferences to this memory location. Read the "Indirect
indexed" section in [Addressing modes](#addressing) for more details.

Next comes `drawSnake`. This is pretty simple too - we first undraw the tail
and then draw the head. `X` is set to the length of the snake, so we can index
to the right pixel, and we set `A` to zero then perform the write using the
indexed indirect addressing mode. Then we reload `X` to index to the head, set
`A` to one and store it at `($10,x)`. `$10` stores the two-byte location of
the head, so this draws a white pixel at the current head position. As only
the head and the tail of the snake move, this is enough to keep the snake
moving.

The last subroutine, `spinWheels`, is just there because the game would run too
fast otherwise. All `spinWheels` does is count `X` down from zero until it hits
zero again. The first `dex` wraps, making `X` `#$ff`.
