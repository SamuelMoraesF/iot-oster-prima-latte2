# Atuadores

Detalhamento dos atuadores e elementos de controle do projeto.

---

## 1. SSR — Controle PID da Caldeira

### Propósito
Controlar a temperatura da caldeira via PID, substituindo o termostato bimetálico original (que causa oscilações de ±10°C).

### Especificações
- **Tipo**: SSR-40DA (Solid State Relay)
- **Entrada**: 3-32V DC
- **Saída**: 24-380V AC, 40A
- **Chaveamento**: Zero-crossing

### Funcionamento
- O ESP32 gera um sinal PWM de baixa frequência (~1-2Hz)
- O duty cycle é controlado pelo algoritmo PID
- O SSR liga/desliga a resistência da caldeira proporcionalmente
- Zero-crossing minimiza interferência eletromagnética

### PID
- **Setpoint**: Temperatura alvo (ex: 93°C para espresso)
- **Entrada**: Leitura do PT100 via MAX31865
- **Saída**: PWM para o SSR (0-100% duty)
- **Parâmetros**: Kp, Ki, Kd — calibrar experimentalmente
- Usar window-based PID (período de ~1-2s)

### Biblioteca
- `PID` by Brett Beauregard (Arduino PID Library)
- Ou implementação custom com anti-windup

### Segurança
- Manter o termostato bimetálico original como **backup de segurança** (em série com o SSR)
- Limite de temperatura máxima no firmware (ex: 130°C → desliga tudo)
- Watchdog no ESP32 para desligar SSR em caso de travamento

---

## 2. Dimmer AC — Controle da Bomba (Pré-infusão)

### Propósito
Controlar a potência da bomba vibratória para pré-infusão suave e perfis de pressão customizados.

### Especificações
- **Módulo**: RobotDyn AC Dimmer (ou equivalente com detecção zero-crossing)
- **Capacidade**: 3.3A / 600W (suficiente para bomba de ~50W)
- **Controle**: Fase (0-100%)
- **Zero-crossing**: Detecção integrada

### Funcionamento
1. O módulo detecta o zero-crossing da rede AC
2. O ESP32 recebe a interrupção de zero-crossing
3. Após um delay programável, o ESP32 envia um pulso de gate ao TRIAC
4. Maior delay = menor potência = menor pressão

### Perfis de Pré-infusão

```
Pressão
(bar)
  9 ┤                    ┌──────────────
    │                   /
  6 ┤               ───┘
    │              /
  3 ┤         ────┘
    │        /
  0 ┤───────┘
    └──────┬──────┬──────┬──────┬─────→ tempo (s)
           0      5     10     15

    Exemplo: Rampa suave de pré-infusão
```

### Perfis Programáveis
- **Rampa linear**: Pressão sobe gradualmente de 0 a 9 bar
- **Pré-infusão + extração**: 3 bar por 5s → 9 bar
- **Blooming**: Pulsação a baixa pressão por 10s → 9 bar
- **Pressure profiling**: Curva custom definida pelo usuário

### Biblioteca
- `RBDdimmer` (para módulos RobotDyn)
- Ou controle manual via interrupts (mais flexível)

---

## 3. Módulo Relé 4 Canais — Botões e Liga/Desliga

### Propósito
Substituir os botões físicos do painel por triggers digitais e adicionar controle de liga/desliga remoto.

### Especificações
- **Módulo**: Relé 4 canais, 5V, optoacoplador isolado
- **Capacidade**: 10A/250V AC por canal
- **Trigger**: LOW level (ativo em LOW)

### Mapeamento dos Canais

| Canal | Função | Descrição |
|---|---|---|
| Relé 1 | Botão Café | Simula pressionar o botão de café/espresso |
| Relé 2 | Botão Vapor | Simula pressionar o botão de vapor |
| Relé 3 | Botão Leite | Simula pressionar o botão de leite |
| Relé 4 | Liga/Desliga | Controle geral de energia da máquina |

### Instalação
- Soldar fios em paralelo aos botões originais no painel
- O relé "simula" o pressionamento do botão fechando o contato
- Para o liga/desliga: relé na alimentação principal (antes da fonte da máquina)

### Lógica de Automação

Exemplo de sequência automatizada:
1. Relé 4: liga a máquina
2. Aguarda aquecimento (PID atinge setpoint)
3. Relé 1: inicia extração
4. Monitora peso na balança
5. Quando atinge ratio, desliga a bomba (via dimmer ou relé)

---

## 4. OPV (Over Pressure Valve) — Ajuste Mecânico

### Propósito
Limitar a pressão máxima da bomba a 9 bar (padrão para espresso).

### Contexto
A bomba vibratória original opera a ~15 bar. O OPV regula a pressão máxima que chega ao grupo. Na máquina original, geralmente está ajustado para ~12-15 bar.

### Ajuste
1. Identificar a válvula OPV na máquina (geralmente na saída da bomba)
2. Ajustar a mola interna ou parafuso para limitar a 9 bar
3. Validar com o manômetro digital instalado
4. Usar blind basket (filtro cego) para teste de pressão estática

### Validação
- Com o manômetro digital conectado, a pressão deve estabilizar em ~9 bar com filtro cego
- Se a máquina tiver OPV ajustável por parafuso, usar chave Allen
- Se for mola, pode ser necessário cortar espiras ou substituir a mola
