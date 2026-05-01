# Sensores

Detalhamento dos sensores utilizados no projeto.

---

## 1. Célula de Carga + HX711 (Balança)

### Propósito
Medir o peso do café extraído em tempo real para controle por ratio e tempo.

### Especificações
- **Célula de carga**: 1kg, tipo barra (single point)
- **Resolução**: ~0.1g com HX711 a 80Hz
- **ADC**: HX711, 24-bit, ganho 128
- **Taxa de amostragem**: 10Hz (padrão) ou 80Hz (RATE pin HIGH)

### Funcionamento
1. A célula de carga é posicionada sob a base da xícara/copo
2. Ao posicionar a xícara, o sistema faz tara automática
3. Durante a extração, o peso é monitorado continuamente
4. Quando atingir o ratio configurado (ex: dose 18g × ratio 2 = 36g), a bomba é desligada
5. Alternativamente, para por tempo (ex: 25s após detectar fluxo de água)

### Detecção de Fluxo
O início do fluxo é detectado pela variação de peso na célula de carga (derivada positiva contínua). A partir desse momento, o timer de extração inicia.

### Calibração
- Calibrar com peso conhecido (ex: 100g)
- Armazenar fator de calibração na NVS (Non-Volatile Storage) do ESP32

### Biblioteca
- `HX711` by bogde (Arduino)

---

## 2. Transdutor de Pressão (Manômetro Digital)

### Propósito
Monitorar a pressão da bomba em tempo real durante a extração.

### Especificações
- **Range**: 0-1.6 MPa (0-16 bar)
- **Saída**: 0.5-4.5V (alimentação 5V)
- **Rosca**: 1/8" BSP (padrão para máquinas de espresso)
- **Precisão**: ±1.5% FS

### Instalação
- Conectar via T-fitting na saída da bomba, antes do grupo
- Pode utilizar adaptador de rosca se necessário

### Conversão de Sinal
```
Pressão (bar) = (V_lido - 0.5) × 16 / (4.5 - 0.5)
Pressão (bar) = (V_lido - 0.5) × 4.0
```

> **Atenção**: O ADC do ESP32 suporta no máximo 3.3V. É necessário um divisor de tensão:
> - R1 = 10kΩ, R2 = 22kΩ → Vout_max = 4.5 × 22/(10+22) = 3.09V ✓

### Biblioteca
- Leitura direta via `analogRead()` com `analogReadResolution(12)`

---

## 3. Sensor de Temperatura (PT100 + MAX31865)

### Propósito
Medir a temperatura da caldeira com precisão para controle PID.

### Especificações
- **Sensor**: PT100 (RTD - Resistance Temperature Detector)
- **Range**: -200°C a +850°C
- **Precisão**: ±0.3°C (classe B)
- **Conversor**: MAX31865 (SPI, 15-bit)
- **Referência**: Rref = 430Ω (para PT100)

### Por que PT100 e não termopar?
- Melhor precisão na faixa de trabalho (85-100°C)
- Mais estável e linear
- Não requer compensação de junta fria
- MAX31865 detecta falhas no sensor automaticamente

### Instalação
- Remover o termostato bimetálico original (ou mantê-lo como segurança backup)
- Fixar o PT100 na caldeira com pasta térmica e abraçadeira metálica
- Isolar termicamente o ponto de contato

### Biblioteca
- `Adafruit MAX31865` (Arduino/PlatformIO)

---

## 4. Sensor de Nível do Reservatório

### Opção A: Sensor Capacitivo Sem Contato

### Propósito
Monitorar o nível de água no reservatório sem contato com a água.

### Especificações
- **Tipo**: Capacitivo, externo (colado no reservatório)
- **Saída**: Analógica ou digital (conforme modelo)
- **Alimentação**: 5V ou 3.3V

### Funcionamento
- Múltiplos sensores em alturas diferentes para discretizar o nível
- Ou um sensor tipo fita capacitiva para leitura contínua

### Opção B: Sensor Ultrassônico (HC-SR04 mini)

- Instalado na tampa do reservatório, apontando para baixo
- Mede distância até a superfície da água
- Cálculo: `nível(%) = (dist_max - dist_atual) / (dist_max - dist_min) × 100`
- Mais preciso, porém requer vedação contra umidade

### Opção C: Sensor de peso

- Célula de carga sob o reservatório
- Outro HX711 dedicado
- Precisão excelente, mas ocupa mais espaço

> **Decisão**: A definir durante a prototipagem. A opção ultrassônica é a mais simples de instalar.

---

## 5. Sensor de Energia (PZEM-004T v3)

### Propósito
Monitorar consumo de energia da máquina.

### Especificações
- **Medições**: Tensão (V), Corrente (A), Potência (W), Energia (kWh), Frequência (Hz), Fator de potência
- **Range corrente**: 0-100A (via transformador de corrente CT)
- **Interface**: UART (9600 baud, Modbus-RTU)
- **Alimentação**: 5V

### Instalação
- O transformador de corrente (CT) abraça o fio fase da alimentação da máquina
- Os fios de tensão são conectados em paralelo com a alimentação

### Biblioteca
- `PZEM004Tv30` (Arduino/PlatformIO)
