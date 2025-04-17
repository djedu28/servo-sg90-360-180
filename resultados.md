Agradeço por ler até aqui, acredito que as descobertas são extremamente valiosas para nossa base de conhecimento. 
Vou organizar os resultados em um formato estruturado para facilitar o entendimento e análise futura.

## Mapa de Comportamentos do SG90

| Range de Pulso (μs) | Comportamento | Observações |
|-----------------|-----------------------|-----------------|
| 1               | Giro Max Horário | Limite inferior - Velocidade máxima |
| 1-239           | Giro Horário  | Velocidade decressente |
| 240-2699        | Modo Posição  | Transição gradual |
| 2700-2799       | Giro Anti-Horário | Velocidade crescente |
| ≥2800           | Giro Max Anti-Horário | Limite superior |

[Notas de Segurança]
- Aquecimento observado após 2h de teste
- Frequências >3500μs sem diferença perceptível
- Período de teste: 30ms (50Hz)

## Análise dos Resultados

1. **Transições Críticas**:
          - Ponto de inflexão em 240μs: fim do giro horário
  - Limiar de 2700μs: início do giro anti-horário
  - Zona intermediária (240-2699μs): comportamento de posição


2. **Considerações de Segurança**:
          - Monitoramento de temperatura recomendado após 2h
  - Limite superior estabelecido em 2800μs
  - Período de teste otimizado em 20ms



## Recomendações para Uso

1. **Modo Contínuo Horário**:
          - Use valores entre 1-239μs
  - Ajuste fino com incrementos de 10μs
  - Monitorar temperatura em longos períodos


2. **Modo Contínuo Anti-Horário**:
          - Iniciar em 2700μs
  - Ajustar até 2800μs para velocidade máxima
  - Evitar saltos grandes entre valores


3. **Zona de Transição**:
          - Para modo continuo, evitar operação prolongada entre 240-2699μs
  - Usar apenas para calibração ou pausa de rotação.
  - Documentar pontos específicos de interesse


As descobertas são particularmente valiosas porque:

- Estabelecem limites precisos para operação segura
- Identificam pontos críticos de transição
- Validam o comportamento bidirecional do SG90 sem precisar de Hack no hardware
- permite ter um servo 180° posicional e um 360° giro continuo ao mesmo tempo.


## Proximos testes sugeridos

* Recomendo testarem em um servo sg90 180 motor com trava, removendo a trava, mas mantendo a comunicação com o trinpot
* Você poderia compartilhar mais detalhes sobre como o servo se comportou durante as transições entre os modos? Isso ajudaria a entender melhor o comportamento em diferentes modelos.

* recomendo tabem testar em outras marcas e modelos, exemplo no mg90, no MG955.
* E até nos mais brutos e profissionais como os futaba S3003, ou A700. Não que eu acredique que esses mais caros estilo o A700 vão ter giro continuo só por mudar a largura do pulso, mas vale o teste
