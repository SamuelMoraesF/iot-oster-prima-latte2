# Componentes (BOM - Bill of Materials)

## Microcontrolador

| Componente | Qtd | Observações |
|---|---|---|
| ESP32-WROOM-32 DevKit | 1 | Controlador principal. 38 pinos, WiFi+BT |

## Sensores

| Componente | Qtd | Uso | Observações |
|---|---|---|---|
| Célula de carga 1kg | 1 | Balança de extração | Tipo barra, sob a base da xícara |
| HX711 | 1 | ADC para célula de carga | Módulo amplificador 24-bit |
| Transdutor de pressão 0-1.6MPa | 1 | Manômetro digital | Rosca 1/8" BSP, saída 0.5-4.5V |
| Termopar tipo K / PT100 | 1 | Temperatura da caldeira | PT100 preferível pela precisão |
| MAX31865 | 1 | ADC para PT100 | Conversor RTD para SPI |
| Sensor de nível capacitivo | 1 | Nível do reservatório | Sem contato, externo ao reservatório |
| PZEM-004T v3 | 1 | Medição de energia | Tensão, corrente, potência, energia, frequência, fator potência |

## Atuadores

| Componente | Qtd | Uso | Observações |
|---|---|---|---|
| SSR 40A (ex: SSR-40DA) | 1 | Controle PID da caldeira | Entrada 3-32V DC, saída 24-380V AC |
| Dimmer AC (RobotDyn) | 1 | Controle da bomba (pré-infusão) | Detecção de zero-crossing integrada |
| Módulo relé 4 canais 5V | 1 | Botões + liga/desliga | Optoacoplador isolado |

## Interface

| Componente | Qtd | Uso | Observações |
|---|---|---|---|
| Display TFT 2.8" ILI9341 | 1 | Display touch | 320x240, SPI, touch XPT2046 |

## Alimentação

| Componente | Qtd | Uso | Observações |
|---|---|---|---|
| Fonte Hi-Link HLK-PM05 | 1 | 5V DC dentro da máquina | AC-DC, 5V/3W, encapsulada |
| Regulador 3.3V (AMS1117) | 1 | 3.3V para ESP32 e sensores | Se necessário (ESP32 DevKit já tem) |

## Conectores e Cabos

| Componente | Qtd | Uso | Observações |
|---|---|---|---|
| Conector aviação GX16-8 ou GX20 | 2 (macho+fêmea) | Conexão máquina ↔ case externo | Escolher com pinos suficientes |
| Cabo multi-vias blindado | 1 | Conexão dos sinais | Comprimento conforme necessidade |

## Case Externo

| Componente | Qtd | Uso | Observações |
|---|---|---|---|
| Case impresso 3D / caixa projeto | 1 | Abrigar ESP32, display, balança | Projetar em CAD |
| Placa perfurada / PCB custom | 1 | Conexões dos módulos | Pode evoluir para PCB |

## Outros

| Componente | Qtd | Uso | Observações |
|---|---|---|---|
| Fios AWG 22-26 | - | Conexões de sinal | Cores variadas |
| Fios AWG 14-16 | - | Conexões AC de potência | Adequados para corrente da máquina |
| Terminais/bornes | - | Conexões seguras | Tipo Wago ou barra de terminais |
| Pasta térmica | 1 | Fixação do sensor de temp | Para contato térmico na caldeira |
| OPV spring (mola ajustável) | 1 | Ajuste de pressão máxima | Consultar compatibilidade com a válvula |

## Estimativa de Pinos ESP32

> Detalhes completos em [pinagem.md](pinagem.md)

- **SPI**: Display TFT (4 pinos) + Touch (1 CS) + MAX31865 (1 CS) = ~6 pinos
- **I2C/GPIO**: HX711 (2 pinos), PZEM (2 pinos UART), Sensor nível (1 pino)
- **GPIO saída**: SSR (1), Dimmer (1), Relés (4)
- **ADC**: Transdutor pressão (1 pino)
- **Total estimado**: ~18-20 pinos → ESP32 suporta tranquilamente
