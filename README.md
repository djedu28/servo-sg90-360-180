# servo-sg90-360-180

Um estudo para o servo motor sg90 para alcançar 360. Spoiler consegui um servo hibrido.

Sim, um servo que é giro continuo e ao mesmo tempo 180° em modo posição!!!. E sem modificar nada no servo.
> Yes, a sg90 with continuous rotation and at the same time 180° in position mode!!!

abaixo minha jornada até essa conquista

## Em um passado distante comprei um servo motor sg90 que não tinha travas internas

Apesar de não possuir travas internas, e girar manualmente 360, o mesmo tinha o comportamento padrão de giro 180º dos servos motores sg90

Testando coisas aleatórias, resolvi colocar o `BLINK` padrão no pino do servo. e ele começou a girar para o sentido horário, girava e parava, depois girava de novo.

Fiquei feliz, mas queria que ele girasse continuo e não consegui alterando os tempos do delay.

## Não consegui, e passei pro método gambiarra

cortei a conexão das engrenagens com o tinpot e usei o servo como 360. 4
Colocando angulo em 0º era giro máximo pro sentido horário; Colocando em 90º o servo ficava parado. E com angulo 180º era diro máximo no sentido anti-horário.

## Anos se passaram e acabei encontrando novamente um servo sg-90 que era sem travas.

Já sabendo mais de como o servo funcionava. Onde não é a frequência que importa, mas sim o pulso.

* sendo a frequência de 40hz até 60hz. (padrão em 50hz).
* E o pulso variando de 1500μs até 2500μs.


### lembrei do `blink` que fez o meu antigo servo girar continuo, testei nesse e funcionou como o anterior.

### Dai pensei em usar o `FADE` que usando o `analogWrire` conseguiria pulsos em um range maior.

Com o **FADE**, o servo começou girando continuo sentido horário, a partir do `analogWrire(pino, 1);`. Vou chamar de **1 PWM** (pq não sei o termo correto).

* com o aumentar do PWM, o servo girava mais devagar.
* com o PWM em 24, o servo começou a girar com um delay em cada rotação.
* até chegar em um ponto que o servo entrava no **modo posição**, o modo padrão alcançado com `meuservo.write(90);`

## frustrado. Pq no pwm 255 (o máximo do analogWrire), o servo continuava em modo posição.

Testei muitas gambiarras, mexendo na frequência e no pulso. E voltando aos estudos, recordei que o servo é PDM e não PWM, então deixei de mexer no analogWrire. E comecei a usar registradores, pois a precisão é maior com eles.

Codigo base:

```cpp
const int PINO_SERVO = 9;

void setup() {
  pinMode(PINO_SERVO, OUTPUT);
  // Configuração específica para 50Hz
  TCCR1A = _BV(WGM11) | _BV(WGM10);
  TCCR1B = _BV(WGM13) | _BV(CS11) | _BV(CS10); // Prescaler 64
}

void loop() {
  // Frequência exata de 50Hz
  OCR1A = 40000 / 50; // Período em ciclos
  
  // Pulso de 1ms (posição mínima)
  digitalWrite(PINO_SERVO, HIGH);
  delayMicroseconds(1000);
  digitalWrite(PINO_SERVO, LOW);
  
  delay(10); // Completa o ciclo de 20ms
}
```

Com o código base, comecei a mexer no máximo que o pulso poderia ir, até que em um momento pensei em duplicar a base do pulso, de 20ms para 40ms, e foi então que meus olhos brilharam ao ver o servo mudar de modo posição para modo giro continuo em sentido anti-horário.

## Testando o clico de 40ms

```cpp
void loop() {
  OCR1A = 40000 / 40; // Período em ciclos
  // envia os pulsos
  for (int pulso = 0; pulso <= 4000; pulso += 100) {
    Serial.print("\nTestando pulso: ");
    Serial.print(pulso);
    Serial.println("us");
    // repete o envio do pulso umas 100vz para que o feedback seja perceptível por um humano
    for (int repetir = 0; repetir <= 100; repetir += 1) {
      digitalWrite(PINO_SERVO, HIGH);
      delayMicroseconds(pulso);
      digitalWrite(PINO_SERVO, LOW);
      delayMicroseconds(4000-pulso); // Completa o ciclo de 40ms
    }
  }
}
```

Estava aumentando gradualmente com passo de 100us, mas quando chegou em 30ms o servo não aumentava a velocidade. e quando chegou em 40ms o loop foi redefinido e o giro foi pro máximo horário.
coloquei o passo em 1, e fui observando a alteração pelo serial, e encontrei esses dados:

* em 2700 gira continuo anti-horário (novo comportamento COMEMORAÇÃO atingimos o resultado esperado)
* em 2800 ele atinge o máximo continuo anti-horário

Com esse código (versão final dos estudos) cheguei nos dados da presente pesquisa.

```cpp
/* 
 * Notas de Estudos anteriores:
 * no pwm de `analogWrite(9, 1)` ele girava máximo para um lado 
 * e quando tem analogWrite(9, 24) o servo começa a girar cada ciclo mais devagar
 * e apos isso ele começa a se mover a posições, comportamento padrão do servo.
 * 
 * Com esse código pretendo analisar pulsos/frequências maiores 
 * e tentar fazer o servo girar para o sentido anti-horário continuamente.
 *
 */

const int PINO_SERVO = 9;

void setup() {
  pinMode(PINO_SERVO, OUTPUT);
  Serial.begin(9600);
}


// Resultado dos estudos 3 - com registradores
// em 1 tem o giro máximo giro continuo sentido-horário (comportamento já esperado  anterior)
// de 0 até 230 gira continuo sentido horário (comportamento já esperado pelo estudo anterior) 
// em 240 o servo para de girar continuamente (comportamento já esperado pelo estudo anterior)
// em 2700 gira continuo anti-horário (novo comportamento COMEMORAÇÃO atingimos o resultado esperado)
// em 2800 ele atinge o máximo continuo anti-horário
// aumentar o pulso não tem efeito.
// inicialmente tentei de 0us até 4000us, e frequências acima não percebi diferença
// O servo começou a aquecer. 
//   Não sei se foi pelo tempo de teste (umas 2 horas até pensar em almentar a largura do pulso acima de 20ms)
//   ou se foi por ter usado pulsos altos (acima dos 3000 Microseconds).

void loop() {
  Serial.println("Teste de Pulso Mínimo e máximo do Servo");
  Serial.println("--------------------------------");
  // Frequência exata de 50Hz ==> 40000 / 50;
  OCR1A = 40000 / 50; // Período em ciclos
  // envia os pulsos em cilos de 30ms
  for (
      int pulso = 10; // Pulso de 10 Milissegundos (posição mínima)
      pulso <= 3000; // Pulso de 30 Milissegundos (posição maxima)
      pulso += 10
  ) {
    Serial.print("\nTestando pulso: ");
    Serial.print(pulso);
    Serial.println("us");
    // repete o envio do pulso umas 100vezes para que o feedback seja perceptível por um humano
    for (int repetir = 0; repetir <= 100; repetir += 1) {
      digitalWrite(PINO_SERVO, HIGH);
      delayMicroseconds(pulso);
      digitalWrite(PINO_SERVO, LOW);
      delayMicroseconds(3000-pulso); // Completa o ciclo de 30ms
    }
  }
}
```

