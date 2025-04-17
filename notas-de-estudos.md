## para o arduino uno. qual o pulso minimo ele consegue gerar?

O Arduino Uno possui limitações específicas na geração de pulsos, especialmente quando se trata de controle de servos. Vamos analisar detalhadamente essas limitações:

### Limitações de Hardware

- O Arduino Uno utiliza um timer de 16 bits (Timer1) para controlar os servos
- A resolução mínima é determinada pela frequência do cristal interno (16 MHz)
- Com o prescaler padrão de 64, temos uma resolução de aproximadamente 4µs por contador

### Pulsos Mínimos Possíveis

| Configuração | Resolução | Pulso Mínimo | Observação |
| --- | --- | --- | --- |
| Modo Padrão | ~4µs | ~500ns | Usando biblioteca Servo |
| Modo Direto | ~1µs | ~250ns | Controle direto dos registradores |
| PWM Nativo | ~4µs | ~500ns | Usando funções PWM nativas |

### Considerações Práticas

1. **Precisão Real**:
          - Na prática, pulsos menores que 500ns podem ser instáveis
  - Recomenda-se usar pelo menos 1ms como pulso mínimo
  - Variações na fonte de alimentação podem afetar a precisão


2. **Limitações do Servo**:
          - A maioria dos servos não responde bem a pulsos muito curtos
  - Geralmente, o pulso mínimo útil é em torno de 500-600µs
  - Depende do modelo específico do servo motor



### Recomendações para Uso

1. **Para Controle de Servo**:
          - Use como mínimo 500µs para maior estabilidade
  - Faça calibração individual para cada servo
  - Considere usar modulação PWM com prescaler adequado


2. **Para Outros Usos**:
          - Para precisão máxima, use controle direto dos registradores
  - Implemente filtragem se necessário
  - Verifique a documentação específica do seu componente



Importante Esses valores são teóricos baseados nas especificações do hardware. Para aplicações práticas, recomendo fazer testes com seu servo específico usando o código que fornecemos anteriormente, começando com valores maiores e diminuindo gradualmente até encontrar o pulso mínimo funcional.


## PDM O que é? (Modulação por largura de pulso)

Recomendo acessar https://www-realdigital-org.translate.goog/doc/333049590c67cb553fc7f9880b2f79c3?_x_tr_sl=en&_x_tr_tl=pt&_x_tr_hl=pt&_x_tr_pto=wa

ali é explicado com grande riqueza de detalhes.


## Onde descobri que o servo é PDM e não PWM

Ao pesquisar sobre o servo 360, não encontrei nada que falasse da minha descoberta, do servo 180 sem travas. mas achei esse site russo que elucidou o que eu tinha constatado (só não sabia o nome)

Titulo do artigo: "Servos Arduino SG90, MG995, MG996: diagrama de fiação e controle"
Acesso em: https://arduinomaster.ru/motor-dvigatel-privod/servoprivody-arduino-sg90-mg995-shema-podklyuchenie-upravlenie/?ysclid=m9l6hu2ww4636014096

## O hack gambiarra no servo motor, que mensionei

Existem diveras formas de fazer isso, eu prefiro só cortar as travas.
Para isso: colocar o servo em 90° e ainda ligado remover a engrenagem com muito cuidado para não toracionar o trimpot. depois cortar parte da aste que vai no trinpot e remontar o servo.

outra alternativa é remover o trinpot e colocar um divisor de tensão no lugar, aqui um guia de como fazer (eu não recomendo) 
https://www.instructables.com/How-to-Make-a-TowerPro-Micro-Servo-Spin-360/

Venho usando um servo hack feito do jeito que comentei a mais de 2 anos, e ainda alimento ele com 9v para ter mais torque. (mas não recomendo alimentar com mais de 6v, pois vai superaquecer) 
