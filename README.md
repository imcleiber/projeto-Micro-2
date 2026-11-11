# projeto-Micro-2
Repositório para o projeto 2 da disciplina de Microcontroladores e Aplicações, lecionada pelo professor Erick Barboza no semestre 26.2
## Integrantes: Levi Farias, Lucas Beelen e Cleiber de Meireles

# Sistema de Triagem de Objetos por Cor

## Descrição

O projeto é um sistema embarcado de **triagem automática de objetos por cor**, controlado por Arduino e programado em C com as bibliotecas do Arduino. Ele simula, em pequena escala, uma linha de produção. Uma esteira transporta os objetos até um ponto de inspeção, o sistema identifica se o objeto é vermelho e um servo motor o desvia para o destino correto.

A identificação da cor usa um **LDR combinado com um LED RGB**. O LED ilumina o objeto com luz vermelha, depois verde e depois azul, e o LDR mede quanto de luz é refletida em cada caso. Como cada cor reflete as componentes de luz de forma diferente, as três leituras formam uma "assinatura" do objeto.

Cada leitura passa por três etapas:

- é filtrada com várias amostragens no conversor analógico-digital;
- é normalizada para a faixa de 0 a 255, com uma calibração feita a partir das cores branca e preta;
- é classificada por uma **árvore de decisão** simples, baseada em limiares das componentes vermelha e azul.

A aferição é repetida três vezes, e o resultado final é decidido por **votação por maioria**.

### Ciclo de funcionamento

1. A esteira, representada por um motor CC, transporta o objeto.
2. Um sensor de presença detecta o objeto no ponto de inspeção, e a esteira para.
3. O sistema faz as leituras de cor e aplica a árvore de decisão e a votação.
4. O resultado aparece no display LCD.
5. O servo motor, acionado por PWM, posiciona o desviador para a área de itens válidos (vermelhos) ou inválidos (outras cores).
6. A esteira volta a funcionar e leva o objeto ao destino.

### Origem do projeto

Este projeto é uma versão simplificada de um sistema de detecção de cor desenvolvido anteriormente para a Raspberry Pi Pico W, com a Pico SDK. Desse sistema:

- **foram removidos** a comunicação Wi-Fi/TCP e o processamento em dois núcleos;
- **foi mantido** o processo de leitura, decisão e exibição, portado para Arduino;
- **foram acrescentados** o controle da esteira e o mecanismo de separação com servo motor.

### Problema real

A separação de itens é uma tarefa comum em linhas de produção. Exemplos são a classificação de frutas por grau de maturação, a separação de materiais em centros de reciclagem e o descarte de peças ou embalagens fora do padrão no controle de qualidade. Feita manualmente, essa triagem é lenta, repetitiva e sujeita a erros por fadiga. Automatizar a inspeção e o desvio aumenta a produtividade e padroniza o critério de aceitação. O projeto demonstra os elementos centrais dessa automação: sensoriamento, tomada de decisão, atuação e interface com o operador.

## Objetivos

Ao final do projeto, o grupo terá um protótipo funcional que separa automaticamente os objetos vermelhos dos demais. Os objetivos específicos são:

- Identificar se um objeto é vermelho usando apenas um LDR e um LED RGB, com filtragem das leituras, calibração e árvore de decisão.
- Exibir o resultado de cada aferição em um display LCD.
- Separar fisicamente os objetos com um servo motor controlado por PWM.
- Controlar a esteira para que pare durante a aferição e volte a funcionar ao final.
- Portar a lógica de leitura e decisão, originalmente escrita com a Pico SDK, para a plataforma Arduino.
- Validar o sistema primeiro em simulação (Tinkercad) e depois no circuito montado em protoboard.

## Requisitos

| ID | Requisito | Descrição |
| --- | --- | --- |
| R1 | Identificação de cor | O sistema deve identificar uma cor específica arbitrária; neste projeto, a cor vermelha. |
| R2 | Exibição no LCD | O sistema deve mostrar em um display LCD o resultado da aferição do objeto. |
| R3 | Separação por servo motor | O sistema deve acionar um servo motor, utilizando PWM, separando o destino dos objetos da cor correta e das cores incorretas. |
| R4 | Controle da esteira | O sistema deve ativar e desativar a esteira de modo a realizar a aferição corretamente. |

## Validação

Cada requisito será validado com um teste prático, primeiro no Tinkercad e depois na protoboard.

| Requisito | Procedimento de teste | Critério de aprovação |
| --- | --- | --- |
| R1 – Identificação de cor | Aproximar do sensor um objeto vermelho e, em seguida, objetos de outras cores. | O objeto vermelho é classificado como válido: aparece no display e vai para a área válida. Os demais são classificados como inválidos. |
| R2 – Exibição no LCD | Comparar o texto do display com a cor de cada objeto aferido. | Objeto vermelho: o display mostra `Eh vermelho`. Objeto de outra cor: o display mostra `Outra cor`. |
| R3 – Separação por servo motor | Passar uma sequência de objetos vermelhos e de outras cores pela esteira. | Todos os objetos vermelhos são separados das demais cores pelo servo motor. |
| R4 – Controle da esteira | Observar a esteira durante a chegada e a aferição de cada objeto. | A esteira para ao detectar a presença de um objeto e volta a funcionar após o fim da aferição. |

> **Observação:** no Tinkercad, o LDR não enxerga cores reais, e a luminosidade é ajustada manualmente. Por isso, a simulação valida a **lógica** do sistema: sequência de leitura, decisão, display, servo e esteira. A detecção de cor com objetos reais e a calibração são validadas na protoboard.

## Equipamentos

O projeto será **simulado no Tinkercad** e, em seguida, **montado em protoboard** com componentes físicos.

| Componente | Função no projeto |
| --- | --- |
| Arduino Uno | Controle de todo o sistema |
| LDR + resistor (divisor de tensão) | Medição da luz refletida pelo objeto, lida pelo ADC |
| LED RGB + resistores | Iluminação do objeto com luz vermelha, verde e azul |
| Display LCD 16x2 com módulo I²C | Exibição do resultado da aferição |
| Servo motor (ex.: SG90) | Desvio dos objetos para a área válida ou inválida, controlado por PWM |
| Motor CC pequeno | Simulação do movimento da esteira |
| Transistor NPN (ex.: BC337 ou TIP120) + resistor de ~1 kΩ + diodo 1N4007 | Acionamento do motor: o pino do Arduino liga e desliga o transistor, e a corrente do motor vem da alimentação |
| Sensor infravermelho de obstáculo | Detecção da presença do objeto no ponto de inspeção. No Tinkercad, é simulado por um sensor ultrassônico ou por um botão. |
| Protoboard, jumpers e fonte externa | Montagem do circuito e alimentação dos motores |

