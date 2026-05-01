# 🛒 Checklist de Compras

Lista de todos os itens necessários para o mod, organizados por categoria.
Marque com `[x]` conforme for comprando.

---

## 🧠 Microcontrolador

- [ ] ESP32-S3 N16R8 DevKit (16MB Flash, 8MB PSRAM)

## 📡 Sensores

- [ ] Célula de carga 1kg (tipo barra, para base da xícara)
- [ ] Módulo HX711 (amplificador 24-bit para célula de carga)
- [ ] Transdutor de pressão 0-1.6 MPa (rosca 1/8" BSP, saída 0.5-4.5V)
- [ ] PT100 (sensor de temperatura RTD para caldeira)
- [ ] Módulo MAX31865 (conversor RTD → SPI)
- [ ] Sensor de nível capacitivo (sem contato, externo ao reservatório)
- [ ] PZEM-004T v3 (medidor de energia: tensão, corrente, potência, kWh, FP)

## ⚡ Atuadores

- [ ] SSR 40A (SSR-40DA — entrada 3-32V DC, saída 24-380V AC) — PID do thermoblock
- [ ] Dimmer AC RobotDyn (com zero-crossing integrado) — controle da bomba
- [ ] Módulo relé 3 canais 5V (optoacoplador isolado) — botões do painel
- [ ] Relé individual 30A / contator (NC) — kill switch de segurança para corte de potência AC

## 📱 Interface

- [ ] Display TFT 2.8" ILI9341 com touch XPT2046 (320x240, SPI)

## 🔋 Alimentação

- [ ] Fonte Hi-Link HLK-PM05 (AC → 5V DC, 3W, encapsulada)
- [ ] Regulador 3.3V AMS1117 (se necessário — ESP32 DevKit já tem)

## 🔗 Conectores e Cabos

- [ ] Par conector aviação GX20-15 pinos (macho + fêmea)
- [ ] Cabo multi-vias blindado (~15 vias, comprimento a definir)
- [ ] Conectores JST ou Dupont (para conexões internas modulares)

## 📦 Case Externo

- [ ] Caixa de projeto ou impressão 3D (abrigar ESP32, display, balança)
- [ ] Placa perfurada ou PCB custom (conexões dos módulos)
- [ ] Espaçadores e parafusos M3 (fixação dos módulos)

## ☕ Modificações Mecânicas

- [ ] Bico vaporizador convencional (substituir o panarello original)
- [ ] Mola ajustável para OPV (ajuste para limite de 9 bar — verificar compatibilidade)
- [ ] Conexão/adaptador para transdutor de pressão (T ou fitting 1/8" BSP)
- [ ] Shower screen IMS Precision 51mm (substituir a tela de dispersão stock por uma com furos a laser para distribuição mais uniforme)

## 🔌 Material Elétrico

- [ ] Fios AWG 22-26 (sinais de baixa tensão — cores variadas)
- [ ] Fios AWG 14-16 (conexões AC de potência)
- [ ] Terminais Wago 221 (conexões rápidas e seguras)
- [ ] Espaguete termocontrátil (isolamento de emendas)
- [ ] Pasta térmica (contato do PT100 com a caldeira)
- [ ] Abraçadeiras de nylon (organização dos cabos)
- [ ] Fita Kapton (isolamento térmico onde necessário)

## 🛡️ Segurança

- [ ] Fusível ou porta-fusível (proteção do circuito DC)
- [ ] Dissipador de calor para SSR (se não vier incluso)

---

> **Dica**: antes de comprar, conferir [`docs/pinagem.md`](pinagem.md) para garantir que todos os componentes são compatíveis com os pinos disponíveis do ESP32-S3.
