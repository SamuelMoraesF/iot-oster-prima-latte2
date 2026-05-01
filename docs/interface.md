# Interface Web e Display

## Interface Web

### Tecnologias
- **Backend**: ESPAsyncWebServer rodando no ESP32
- **Frontend**: HTML5 + CSS3 + JavaScript (vanilla ou Alpine.js)
- **Comunicação**: WebSocket para tempo real, REST para config
- **Armazenamento**: LittleFS no ESP32

### Páginas

#### Dashboard (Home)
- Temperatura atual vs setpoint (gauge)
- Pressão atual (gauge)
- Peso na balança
- Status da máquina (idle, aquecendo, extraindo, vapor)
- Nível do reservatório (barra)
- Consumo de energia atual (W)
- Botão de início de extração

#### Extração (Shot View)
- Gráfico em tempo real: Pressão × Tempo
- Gráfico em tempo real: Peso × Tempo
- Gráfico em tempo real: Temperatura × Tempo
- Timer
- Peso atual / peso alvo
- Flow rate calculado (g/s)

#### Histórico
- Lista de extrações anteriores
- Para cada extração:
  - Data/hora
  - Dose, yield, ratio
  - Tempo total e tempo de pré-infusão
  - Curvas (pressão, peso, temperatura)
  - Notas (campo de texto)
- Filtros por data
- Exportação CSV

#### Perfis
- Criar/editar perfis de extração
- Campos:
  - Nome
  - Dose (g)
  - Ratio alvo
  - Temperatura (°C)
  - Pré-infusão (duração, pressão)
  - Perfil de pressão (curva custom)
  - Tempo máximo de extração

#### Configurações
- **PID**: Kp, Ki, Kd, setpoint
- **Balança**: calibração, tara
- **WiFi**: SSID, senha
- **MQTT**: broker, porta, usuário, tópico base
- **OTA**: upload de firmware
- **Sistema**: reset, backup config

### WebSocket Events

```json
// Server → Client (tempo real, ~10Hz durante extração)
{
  "type": "sensors",
  "data": {
    "temperature": 93.2,
    "pressure": 9.1,
    "weight": 24.5,
    "flow_rate": 1.8,
    "water_level": 72,
    "power_watts": 1200,
    "state": "extracting",
    "timer": 18.5
  }
}

// Client → Server
{
  "type": "command",
  "action": "start_extraction",
  "profile": "espresso_default"
}

{
  "type": "command",
  "action": "set_pid",
  "setpoint": 93.0
}

{
  "type": "command",
  "action": "tare_scale"
}

{
  "type": "command",
  "action": "power",
  "state": "on"
}
```

---

## Display Touch (TFT ILI9341)

### Especificações
- **Tamanho**: 2.8" (320×240 pixels)
- **Interface**: SPI
- **Touch**: Resistivo, XPT2046 (SPI)
- **Framework UI**: LVGL (Light and Versatile Graphics Library) ou TFT_eSPI + custom

### Telas

#### Tela Principal
```
┌─────────────────────────┐
│  93.2°C    ☕ Pronto     │
│  ████████░░  72% água   │
│                         │
│  Perfil: Espresso 18g   │
│  Ratio: 1:2 → 36g      │
│                         │
│  ┌─────────────────┐    │
│  │   ▶ EXTRAIR     │    │
│  └─────────────────┘    │
│                         │
│  ⚡ 5W    🕐 14:32      │
└─────────────────────────┘
```

#### Tela de Extração (em andamento)
```
┌─────────────────────────┐
│  T:93.1°C  P:8.9bar     │
│                         │
│  Pressão ──────────     │
│  ╭─────╮               │
│  │     ╰────────        │
│  ╰──────────────        │
│                         │
│  24.5g / 36.0g          │
│  ██████████░░░  68%     │
│  ⏱ 18.5s   💧 1.8g/s   │
│                         │
│  [ ■ PARAR ]            │
└─────────────────────────┘
```

#### Tela de Resultado
```
┌─────────────────────────┐
│     ✅ Extração OK      │
│                         │
│  Dose:  18.0g           │
│  Yield: 36.2g           │
│  Ratio: 1:2.01          │
│  Tempo: 28.3s           │
│  T avg: 93.1°C          │
│  P avg: 9.0 bar         │
│                         │
│  [Salvar] [Nova] [Menu] │
└─────────────────────────┘
```

#### Menu de Configuração
- Seleção de perfil
- Ajuste de temperatura
- Calibração da balança
- Liga/desliga máquina
- Info do sistema (IP, uptime, versão)

### Biblioteca de Display
- **TFT_eSPI** — driver rápido para ILI9341
- **LVGL** — framework de UI completo (widgets, gráficos, animações)
- **lv_port_esp32** — port do LVGL para ESP32
