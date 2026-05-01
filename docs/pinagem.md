# Pinagem do ESP32

Mapeamento de pinos do ESP32-WROOM-32 DevKit para os componentes do projeto.

## Diagrama de Pinos

```
                    ┌──────────────┐
                    │   ESP32      │
                    │   DevKit     │
                    │              │
         3V3  ─────┤ 3V3    VIN  ├───── 5V
         GND  ─────┤ GND    GND  ├───── GND
              ─────┤ GPIO15 GPIO13├───── TFT_MOSI (SPI: HSPI)
              ─────┤ GPIO2  GPIO12├───── TFT_MISO (SPI: HSPI)
              ─────┤ GPIO4  GPIO14├───── TFT_CLK  (SPI: HSPI)
  HX711_DOUT ─────┤ GPIO16 GPIO27├───── TFT_CS
  HX711_SCK  ─────┤ GPIO17 GPIO26├───── TFT_DC
   PZEM_TX   ─────┤ GPIO5  GPIO25├───── TFT_RST
   PZEM_RX   ─────┤ GPIO18 GPIO33├───── TOUCH_CS (XPT2046)
  RELE_1     ─────┤ GPIO19 GPIO32├───── MAX31865_CS
  RELE_2     ─────┤ GPIO21 GPIO35├───── PRESSAO_ADC (input only)
  RELE_3     ─────┤ GPIO22 GPIO34├───── NIVEL_ADC (input only)
  RELE_4     ─────┤ GPIO23 GPIO39├───── (input only, reserva)
  SSR_PID    ─────┤ GPIO0  GPIO36├───── (input only, reserva)
  DIMMER     ─────┤ GPIO15       ├
  DIMMER_ZC  ─────┤ GPIO2        ├
                    └──────────────┘
```

> **Nota**: O diagrama acima é uma referência simplificada. A pinagem final será definida durante a montagem e pode sofrer ajustes.

## Tabela de Pinos

### SPI (barramento compartilhado — HSPI)

| Função | Pino ESP32 | GPIO | Observações |
|---|---|---|---|
| SPI MOSI | HSPI_MOSI | GPIO13 | Display + MAX31865 |
| SPI MISO | HSPI_MISO | GPIO12 | Display + MAX31865 |
| SPI CLK | HSPI_CLK | GPIO14 | Display + MAX31865 |
| TFT CS | - | GPIO27 | Chip select do display |
| TFT DC | - | GPIO26 | Data/Command do display |
| TFT RST | - | GPIO25 | Reset do display |
| Touch CS | - | GPIO33 | Chip select do touch (XPT2046) |
| MAX31865 CS | - | GPIO32 | Chip select do RTD |

### Balança (HX711)

| Função | Pino ESP32 | GPIO | Observações |
|---|---|---|---|
| HX711 DOUT | - | GPIO16 | Dados da célula de carga |
| HX711 SCK | - | GPIO17 | Clock da célula de carga |

### Medição de Energia (PZEM-004T)

| Função | Pino ESP32 | GPIO | Observações |
|---|---|---|---|
| PZEM TX | UART | GPIO5 | Software Serial TX |
| PZEM RX | UART | GPIO18 | Software Serial RX |

### Atuadores

| Função | Pino ESP32 | GPIO | Observações |
|---|---|---|---|
| SSR (PID caldeira) | - | GPIO0 | PWM para controle PID |
| Dimmer (bomba) | - | GPIO15 | Sinal de controle do dimmer |
| Dimmer Zero-Cross | - | GPIO2 | Detecção zero-crossing (input) |
| Relé 1 (botão café) | - | GPIO19 | Simula botão do painel |
| Relé 2 (botão vapor) | - | GPIO21 | Simula botão do painel |
| Relé 3 (botão leite) | - | GPIO22 | Simula botão do painel |
| Relé 4 (liga/desliga) | - | GPIO23 | Relé principal da máquina |

### Entradas Analógicas

| Função | Pino ESP32 | GPIO | Observações |
|---|---|---|---|
| Transdutor pressão | ADC1_CH7 | GPIO35 | 0.5-4.5V → 0-16 bar (input only) |
| Sensor nível | ADC1_CH6 | GPIO34 | Sinal analógico (input only) |

## Observações

### Pinos a evitar no ESP32

- **GPIO6-11**: Usados pelo flash SPI interno — **não usar**
- **GPIO34, 35, 36, 39**: Apenas entrada (input only) — ok para sensores analógicos
- **GPIO0**: Puxado para HIGH internamente; LOW durante boot entra em modo flash. Usar com cuidado para SSR (HIGH = desligado no boot)
- **GPIO2**: LED onboard em alguns DevKits; evitar conflito com dimmer ZC
- **GPIO15**: Deve ser HIGH durante boot para log via UART

### Tensões

- Sensores analógicos: usar divisor de tensão se sinal > 3.3V
- Transdutor de pressão (0.5-4.5V): precisa de divisor de tensão para ADC do ESP32 (máx 3.3V)
- HX711: alimentar com 3.3V ou 5V conforme módulo
- PZEM-004T: alimentar com 5V, lógica 3.3V compatível

### Contagem Total de Pinos

| Tipo | Quantidade |
|---|---|
| SPI (compartilhado) | 3 + 4 CS/DC/RST = 7 |
| GPIO digital (saída) | 5 (SSR + Dimmer + 4 relés) |
| GPIO digital (entrada) | 3 (HX711 x2 + Dimmer ZC) |
| UART (Software Serial) | 2 (PZEM) |
| ADC (entrada analógica) | 2 (pressão + nível) |
| **Total** | **~20 pinos** |

O ESP32 possui ~25 GPIOs utilizáveis, então a pinagem está confortável.
