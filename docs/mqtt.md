# MQTT — Tópicos e Métricas

## Configuração

- **Broker**: Configurável (ex: Mosquitto local, HiveMQ cloud)
- **Tópico base**: `espresso/oster6701/` (configurável)
- **QoS**: 0 para sensores em tempo real, 1 para comandos
- **Retain**: Sim para estado, não para sensores

## Tópicos de Estado (publicação)

### Sensores (tempo real)

| Tópico | Payload | Frequência | Descrição |
|---|---|---|---|
| `espresso/oster6701/temperature` | `93.2` | 1 Hz | Temperatura da caldeira (°C) |
| `espresso/oster6701/pressure` | `9.1` | 1 Hz (10 Hz durante extração) | Pressão da bomba (bar) |
| `espresso/oster6701/weight` | `24.5` | 1 Hz (10 Hz durante extração) | Peso na balança (g) |
| `espresso/oster6701/flow_rate` | `1.8` | 1 Hz durante extração | Flow rate (g/s) |
| `espresso/oster6701/water_level` | `72` | 0.1 Hz | Nível do reservatório (%) |

### Energia

| Tópico | Payload | Frequência | Descrição |
|---|---|---|---|
| `espresso/oster6701/energy/voltage` | `127.3` | 0.5 Hz | Tensão (V) |
| `espresso/oster6701/energy/current` | `9.4` | 0.5 Hz | Corrente (A) |
| `espresso/oster6701/energy/power` | `1196` | 0.5 Hz | Potência (W) |
| `espresso/oster6701/energy/energy` | `2.45` | 0.5 Hz | Energia acumulada (kWh) |
| `espresso/oster6701/energy/frequency` | `60.0` | 0.5 Hz | Frequência (Hz) |
| `espresso/oster6701/energy/pf` | `0.98` | 0.5 Hz | Fator de potência |

### Estado da Máquina

| Tópico | Payload | Retain | Descrição |
|---|---|---|---|
| `espresso/oster6701/state` | `idle` / `heating` / `ready` / `extracting` / `steaming` / `error` | Sim | Estado atual |
| `espresso/oster6701/power` | `on` / `off` | Sim | Ligada/desligada |
| `espresso/oster6701/pid/setpoint` | `93.0` | Sim | Setpoint do PID |
| `espresso/oster6701/pid/kp` | `2.0` | Sim | Parâmetro Kp |
| `espresso/oster6701/pid/ki` | `5.0` | Sim | Parâmetro Ki |
| `espresso/oster6701/pid/kd` | `1.0` | Sim | Parâmetro Kd |

### Extração

| Tópico | Payload | Descrição |
|---|---|---|
| `espresso/oster6701/shot/active` | `true` / `false` | Extração em andamento |
| `espresso/oster6701/shot/profile` | `"espresso_default"` | Perfil ativo |
| `espresso/oster6701/shot/timer` | `18.5` | Timer da extração (s) |
| `espresso/oster6701/shot/target_weight` | `36.0` | Peso alvo (g) |
| `espresso/oster6701/shot/result` | JSON (ver abaixo) | Resultado da última extração |

#### Payload de resultado da extração
```json
{
  "timestamp": "2026-04-30T23:00:00-03:00",
  "profile": "espresso_default",
  "dose": 18.0,
  "yield": 36.2,
  "ratio": 2.01,
  "time_total": 28.3,
  "time_preinfusion": 5.0,
  "temp_avg": 93.1,
  "pressure_avg": 9.0,
  "flow_rate_avg": 1.3,
  "pressure_curve": [0, 2.1, 3.0, 3.0, 8.5, 9.0, 9.1, 9.0, ...],
  "weight_curve": [0, 0, 0.5, 1.2, 3.5, 6.0, 9.2, ...]
}
```

## Tópicos de Comando (subscrição)

| Tópico | Payload | Descrição |
|---|---|---|
| `espresso/oster6701/cmd/power` | `on` / `off` | Liga/desliga a máquina |
| `espresso/oster6701/cmd/extract` | `start` / `stop` | Iniciar/parar extração |
| `espresso/oster6701/cmd/profile` | `"nome_perfil"` | Selecionar perfil |
| `espresso/oster6701/cmd/pid/setpoint` | `93.0` | Alterar setpoint |
| `espresso/oster6701/cmd/pid/kp` | `2.0` | Alterar Kp |
| `espresso/oster6701/cmd/pid/ki` | `5.0` | Alterar Ki |
| `espresso/oster6701/cmd/pid/kd` | `1.0` | Alterar Kd |
| `espresso/oster6701/cmd/tare` | (qualquer) | Tarar balança |
| `espresso/oster6701/cmd/dimmer` | `0-100` | Ajustar dimmer manualmente |

## Home Assistant — Auto Discovery

O firmware publica mensagens de auto-discovery no tópico `homeassistant/` para que o Home Assistant detecte automaticamente os sensores e controles.

### Exemplo de discovery (sensor de temperatura)
```json
{
  "name": "Espresso Temperature",
  "unique_id": "oster6701_temperature",
  "state_topic": "espresso/oster6701/temperature",
  "unit_of_measurement": "°C",
  "device_class": "temperature",
  "device": {
    "identifiers": ["oster6701"],
    "name": "Oster Prima Latte II",
    "model": "BVSTEM6701",
    "manufacturer": "Oster (modded)"
  }
}
```

## Grafana / InfluxDB

Para histórico de longo prazo, configurar um bridge MQTT → InfluxDB (via Telegraf ou Node-RED) e visualizar no Grafana.

### Dashboards sugeridos
- **Temperatura ao longo do dia** — estabilidade do PID
- **Pressão por extração** — consistência
- **Consumo de energia diário/mensal**
- **Shots por dia/semana**
- **Comparação entre perfis de extração**
