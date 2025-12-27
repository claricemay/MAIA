# MAIA
MAIA (from pt-br: Mecanismo auxiliar inteligente para animais), is a ADAS system that is being created to help blind dogs to have a better quality of life!


## By Clarice May and Vinicius May (@vrmay23)

# 🐕‍🦺 Sistema de Navegação Tátil para Cachorro Cego

> **Projeto de assistência tecnológica para animais com deficiência visual**  
> Versão 1.0 | Última atualização: Novembro 2025

---

## 📋 Índice

- [Visão Geral](#-visão-geral)
- [Parte 1: Arquitetura do Sistema](#-parte-1--arquitetura-do-sistema-e-posicionamento-de-sensores)
- [Parte 2: Planejamento em Sprints](#-parte-2--planejamento-em-sprints)
- [Backlog Adicional](#-backlog-adicional-tapete-com-sensores-de-peso)

---

## 🎯 Visão Geral

### Objetivos do Sistema

| Funcionalidade | Descrição |
|----------------|-----------|
| 📳 **Feedback Tátil** | Vibrações direcionais para alertar sobre obstáculos |
| 🔊 **Feedback Auditivo** | Voz do tutor identificando cada cômodo |
| 🏠 **Modos Contextuais** | Comportamentos adaptados por ambiente (casa, rua, brincadeira) |
| 🛡️ **Segurança** | Design impermeável, leve e com bateria protegida |

### ⚡ Especificações Técnicas Resumidas

```
🔋 Bateria: LiPo com PCM (proteção integrada)
🧠 Processador: ESP32 (WiFi + BLE)
📡 Sensores: 2x LIDAR (VL53L1X/VL53L5CX)
📳 Atuadores: 4x Motores Haptic
🔊 Áudio: Módulo MP3 + Speaker
⚖️ Peso: < 10% do peso do cachorro
```

---

## 🏗️ Parte 1 – Arquitetura do Sistema e Posicionamento de Sensores

### 🔧 Arquitetura de Alto Nível

```
┌─────────────────────────────────────────────┐
│     🔊 SPEAKER + MP3 MODULE (Voz Tutor)     │
└──────────────────┬──────────────────────────┘
                   │
                   ↓
┌─────────────────────────────────────────────┐
│   🧠 ESP32 COLEIRA CENTRAL                  │
│   • Processamento                           │
│   • 2x LIDAR (frontal + traseiro)           │
│   • BLE/WiFi                                │
│   • Lógica de controle                      │
└───────┬─────────────────────┬───────────────┘
        │                     │
        ↓                     ↓
┌───────────────┐    ┌───────────────────┐
│ 📳 4x MOTORES │    │ 🔋 BATERIA LiPo   │
│    HAPTIC     │    │    + PCM          │
└───────────────┘    └───────────────────┘
```

#### 🔹 Componentes Principais

| Componente | Função | Especificações |
|------------|--------|----------------|
| 🧠 **ESP32** | Processamento central | WiFi, BLE, GPIO múltiplos |
| 📡 **LIDAR Frontal** | Detecção de obstáculos à frente | VL53L1X/VL53L5CX, alcance 4m |
| 📡 **LIDAR Traseiro** | Cobertura lateral/traseira | VL53L1X/VL53L5CX, alcance 4m |
| 📳 **Motor Frontal** | Vibração - alerta frontal | ERM/LRA, 3.3V |
| 📳 **Motor Traseiro** | Vibração - alerta traseiro | ERM/LRA, 3.3V |
| 📳 **Motor Esquerdo** | Vibração - alerta lateral esq. | ERM/LRA, 3.3V |
| 📳 **Motor Direito** | Vibração - alerta lateral dir. | ERM/LRA, 3.3V |
| 🔊 **Speaker MP3** | Reprodução de áudio do tutor | DFPlayer Mini, 3W |
| 🔋 **Bateria** | Alimentação | LiPo 3.7V 2000mAh + PCM |

---

### 🗺️ Posicionamento Físico no Cachorro

```
                    🐕 CABEÇA
                       ⬆️
                       │
              📳 MOTOR FRONTAL
                       │
                   ╭───┴───╮
                   │       │
          📡 LIDAR │   🧠  │ LIDAR 📡
           FRONTAL │  ESP32│ TRASEIRO
                   │       │
                   ╰───┬───╯
                       │
        📳 ──────────  │  ────────── 📳
    MOTOR ESQUERDO     │      MOTOR DIREITO
     (Pata Esq.)       │       (Pata Dir.)
                       │
              📳 MOTOR TRASEIRO
                       │
                    🐕 BUNDA
```

#### 📍 Mapeamento de Posições

| Dispositivo | Localização | Função |
|-------------|-------------|---------|
| 📳 **Motor Frontal** | Peito/pescoço | Alerta obstáculo à frente |
| 📳 **Motor Traseiro** | Base da cauda | Alerta obstáculo atrás (opcional) |
| 📳 **Motor Esquerdo** | Pata esquerda/lateral | Alerta obstáculo à esquerda |
| 📳 **Motor Direito** | Pata direita/lateral | Alerta obstáculo à direita |
| 📡 **LIDAR Frontal** | Frente da coleira | Scan frontal 0-4m |
| 📡 **LIDAR Traseiro** | Lateral/traseira | Cobertura lateral |
| 📷 **Câmera (opcional)** | Frontal | Reconhecimento de degraus/pessoas |

---

### ⚖️ Comparação de Configurações de Sensores

| 🔧 Configuração | ✅ Vantagens | ⚠️ Desvantagens |
|-----------------|--------------|-----------------|
| **📡 Somente LIDAR** | • Medição precisa<br>• Resposta rápida<br>• Baixo consumo | • Não detecta degraus<br>• Não reconhece objetos móveis |
| **📷 Somente Câmera** | • Reconhecimento inteligente<br>• Identifica pessoas/animais<br>• Detecta degraus | • Processamento pesado<br>• Alto consumo<br>• Sensível à luminosidade |
| **📡 1 LIDAR + 📷 Câmera** | • Distância precisa + reconhecimento<br>• Boa versatilidade | • Mais complexo<br>• Consumo moderado |
| **📡 2 LIDARs + 📷 Câmera** | • Cobertura 360°<br>• Máxima inteligência | • Mais caro<br>• Alto consumo<br>• Peso aumentado |
| **📡 2 LIDARs (escolhido)** | • Boa cobertura<br>• Rápido<br>• Consumo otimizado | • Requer posicionamento estratégico |

🎯 **Configuração Escolhida:** `2 LIDARs + 4 Motores Haptic + 1 Speaker MP3`

---

### 🛡️ Considerações de Design e Segurança

#### 🔒 Segurança

- ✅ Bateria LiPo com PCM (proteção contra sobrecarga/descarga)
- ✅ Encapsulamento em silicone (IP65+)
- ✅ Fusível térmico integrado
- ✅ Monitoramento de temperatura em tempo real
- ✅ Alerta de bateria fraca

#### 💡 Conforto

- ✅ Peso total < 10% do peso do cachorro
- ✅ Motores haptic com intensidade ajustável (PWM)
- ✅ Vibração progressiva (mais longe = menos intenso)
- ✅ Desativação em zonas seguras (comedouro, cama)

#### 🔧 Manutenibilidade

- ✅ Módulos removíveis para lavagem
- ✅ Bateria substituível
- ✅ Conexões à prova d'água (conectores IP67)
- ✅ Firmware atualizável via OTA (WiFi)

#### 🌍 Modos de Operação

| Modo | 🏠 Casa | 🚶 Rua | 🎾 Brincar |
|------|---------|--------|-----------|
| **LIDAR** | ✅ Ativo | ✅ Ativo | ⚠️ Reduzido |
| **Haptic** | ✅ Normal | ✅ Intenso | ⚠️ Suave |
| **Áudio** | ✅ Por cômodo | ❌ Desligado | ✅ Comandos |
| **Zonas** | ✅ Ativo | ❌ N/A | ❌ Desligado |

---

## 🗓️ Parte 2 – Planejamento em Sprints

### 📊 Estrutura do Projeto

```
🎯 Duração Total: 10 semanas (5 sprints × 2 semanas)
📈 Entrega: Incremental (funcionalidade por sprint)
⚡ Prioridades: Segurança → Conforto → Performance → Bateria
```

---

### 🔷 Sprint 1 – Arquitetura e Prototipagem Física
**⏱️ Duração:** 2 semanas

#### 🎯 Objetivos
- Definir arquitetura física e lógica completa
- Selecionar e adquirir todos os componentes
- Criar protótipo inicial funcional

#### ✅ Tarefas

| # | Tarefa | Responsável | Prioridade |
|---|--------|-------------|------------|
| 1 | Selecionar ESP32 + periféricos | Hardware | 🔴 Alta |
| 2 | Escolher LIDARs (VL53L1X vs VL53L5CX) | Hardware | 🔴 Alta |
| 3 | Definir motores haptic (ERM vs LRA) | Hardware | 🔴 Alta |
| 4 | Selecionar módulo MP3 (DFPlayer Mini) | Hardware | 🟡 Média |
| 5 | Projetar layout 3D da coleira | Design | 🔴 Alta |
| 6 | Calcular distribuição de peso | Design | 🔴 Alta |
| 7 | Escolher bateria LiPo + PCM | Hardware | 🔴 Alta |
| 8 | Prototipar em breadboard | Prototipagem | 🟡 Média |
| 9 | Testar conforto em manequim | Testes | 🟡 Média |

#### 📌 Lembretes
- 🎯 Meta de peso: < 200g para cachorro de 10kg
- 🔧 Planejar encaixes modulares para manutenção
- 🧪 Testar alergias a materiais (silicone hipoalergênico)

#### 📦 Entregas
- [ ] Lista de componentes aprovada
- [ ] Protótipo físico (não funcional)
- [ ] Esquemático elétrico v1.0
- [ ] Modelo 3D da coleira

---

### 🔷 Sprint 2 – Feedback Haptic
**⏱️ Duração:** 2 semanas

#### 🎯 Objetivos
- Implementar sistema de vibração tátil
- Calibrar padrões de vibração por distância
- Testar conforto e eficácia

#### ✅ Tarefas

| # | Tarefa | Responsável | Prioridade |
|---|--------|-------------|------------|
| 1 | Conectar 4 motores haptic ao ESP32 | Hardware | 🔴 Alta |
| 2 | Implementar PWM para controle de intensidade | Software | 🔴 Alta |
| 3 | Mapear distância → intensidade de vibração | Software | 🔴 Alta |
| 4 | Criar padrões de vibração (pulsos, contínuo) | Software | 🟡 Média |
| 5 | Testar com obstáculos simulados | Testes | 🔴 Alta |
| 6 | Ajustar timing (latência < 50ms) | Software | 🔴 Alta |
| 7 | Calibrar para não assustar o cachorro | Testes | 🟡 Média |

#### 📐 Tabela de Calibração

| Distância | Intensidade | Padrão | Frequência |
|-----------|-------------|--------|------------|
| **< 30cm** | 🔴 100% | Contínuo | - |
| **30-60cm** | 🟠 70% | Pulso rápido | 5Hz |
| **60-100cm** | 🟡 50% | Pulso médio | 2Hz |
| **100-200cm** | 🟢 30% | Pulso lento | 1Hz |
| **> 200cm** | ⚪ 0% | Desligado | - |

#### 📌 Lembretes
- ⚠️ Evitar vibração simultânea de múltiplos motores (confuso)
- 🎚️ Implementar modo "sensibilidade" (baixa/média/alta)
- 🧪 Testar com diferentes raças/portes

#### 📦 Entregas
- [ ] Firmware de controle haptic v1.0
- [ ] Tabela de calibração finalizada
- [ ] Vídeo de teste com manequim
- [ ] Relatório de conforto

---

### 🔷 Sprint 3 – Áudio e Zonas
**⏱️ Duração:** 2 semanas

#### 🎯 Objetivos
- Implementar feedback auditivo por cômodo
- Criar sistema de zonas (BLE/IR)
- Integrar áudio + haptic

#### ✅ Tarefas

| # | Tarefa | Responsável | Prioridade |
|---|--------|-------------|------------|
| 1 | Conectar DFPlayer Mini ao ESP32 | Hardware | 🔴 Alta |
| 2 | Gravar áudios do tutor por cômodo | Conteúdo | 🔴 Alta |
| 3 | Implementar reprodução de áudio por trigger | Software | 🔴 Alta |
| 4 | Configurar beacons BLE por cômodo | Hardware | 🟡 Média |
| 5 | Criar lógica de detecção de entrada/saída | Software | 🔴 Alta |
| 6 | Implementar zonas de exceção (desativa sensores) | Software | 🔴 Alta |
| 7 | Testar integração áudio + vibração | Testes | 🟡 Média |
| 8 | Ajustar volume por ambiente | Software | 🟢 Baixa |

#### 🏠 Mapeamento de Zonas

```
┌─────────────────────────────────────┐
│  🏠 CASA                            │
│                                     │
│  🚪 Entrada  → 🔊 "Você está na    │
│               entrada"              │
│                                     │
│  🍽️ Cozinha → 🔊 "Cozinha" +       │
│               📳 Desliga sensores   │
│                                     │
│  🛋️ Sala    → 🔊 "Sala de estar"   │
│                                     │
│  🛏️ Quarto   → 🔊 "Seu quarto" +    │
│               🔇 Volume reduzido    │
│                                     │
│  🌳 Quintal  → 🔊 "Quintal" +       │
│               📳 Modo rua ativo     │
└─────────────────────────────────────┘
```

#### 🎵 Modos de Áudio

| Modo | Cômodo | Rua | Brincadeira |
|------|--------|-----|-------------|
| **Volume** | 🔊 Médio | 🔇 Mudo | 🔊 Alto |
| **Comandos** | ✅ Sim | ❌ Não | ✅ Sim |
| **Frequência** | Entrada | - | Sob demanda |

#### 📌 Lembretes
- 🎤 Áudios devem ser curtos (< 3 segundos)
- 🔊 Volume ajustável via botão físico
- 🚨 Implementar comando de emergência ("Vem aqui!")

#### 📦 Entregas
- [ ] Sistema de áudio funcional
- [ ] 5+ áudios gravados e testados
- [ ] Beacons BLE configurados
- [ ] Lógica de zonas implementada

---

### 🔷 Sprint 4 – Integração dos LIDARs
**⏱️ Duração:** 2 semanas

#### 🎯 Objetivos
- Conectar e calibrar 2 LIDARs
- Integrar leitura LIDAR → vibração haptic
- Otimizar tempo de resposta

#### ✅ Tarefas

| # | Tarefa | Responsável | Prioridade |
|---|--------|-------------|------------|
| 1 | Conectar LIDARs ao ESP32 (I2C) | Hardware | 🔴 Alta |
| 2 | Configurar endereços I2C diferentes | Software | 🔴 Alta |
| 3 | Implementar leitura assíncrona | Software | 🔴 Alta |
| 4 | Calibrar alcance e precisão | Software | 🔴 Alta |
| 5 | Mapear LIDAR → motor haptic correspondente | Software | 🔴 Alta |
| 6 | Implementar fusão de dados (2 LIDARs) | Software | 🟡 Média |
| 7 | Testar com obstáculos móveis | Testes | 🔴 Alta |
| 8 | Otimizar latência (< 100ms total) | Software | 🟡 Média |

#### 📡 Configuração dos LIDARs

```
LIDAR Frontal (0x29)  →  Motor Frontal
       ↓
   [ESP32]  ←→  [I2C Bus]
       ↑
LIDAR Traseiro (0x30) →  Motores Laterais
```

#### ⚙️ Parâmetros de Calibração

| Parâmetro | Valor | Unidade |
|-----------|-------|---------|
| **Frequência de leitura** | 20 | Hz |
| **Alcance máximo** | 4 | metros |
| **Zona de segurança** | 30 | cm |
| **Latência máxima** | 100 | ms |
| **Filtro de ruído** | Média móvel (n=3) | - |

#### 📌 Lembretes
- ⚠️ Evitar falsos positivos ao comer/beber (zona de exceção)
- 🎯 Priorizar obstáculos frontais sobre laterais
- 🧪 Testar em diferentes ambientes (luz/escuro)

#### 📦 Entregas
- [ ] 2 LIDARs calibrados e funcionais
- [ ] Sistema integrado LIDAR → Haptic
- [ ] Latência < 100ms confirmada
- [ ] Testes em cenário real

---

### 🔷 Sprint 5 – Testes Finais, Segurança e Ajustes
**⏱️ Duração:** 2 semanas

#### 🎯 Objetivos
- Sistema completo e pronto para uso diário
- Validação de segurança e conforto
- Documentação final

#### ✅ Tarefas

| # | Tarefa | Responsável | Prioridade |
|---|--------|-------------|------------|
| 1 | Testar impermeabilidade (IP65+) | Testes | 🔴 Alta |
| 2 | Validar encapsulamento da bateria | Testes | 🔴 Alta |
| 3 | Monitorar temperatura em uso prolongado | Testes | 🔴 Alta |
| 4 | Testar autonomia de bateria (meta: 8h) | Testes | 🔴 Alta |
| 5 | Validar todos os modos (casa/rua/brincar) | Testes | 🔴 Alta |
| 6 | Ajustar peso e distribuição final | Design | 🟡 Média |
| 7 | Implementar alertas de falha/bateria | Software | 🔴 Alta |
| 8 | Criar manual do usuário | Documentação | 🟡 Média |
| 9 | Realizar testes com cachorro real | Testes | 🔴 Alta |

#### 🧪 Checklist de Testes

- [ ] ✅ Peso total < 10% do peso do cachorro
- [ ] ✅ Autonomia ≥ 8 horas de uso contínuo
- [ ] ✅ Resistência à água (chuva leve, 15 min)
- [ ] ✅ Conforto: cachorro não tenta remover
- [ ] ✅ Latência total < 100ms
- [ ] ✅ Todos os modos funcionais
- [ ] ✅ Áudio claro e audível
- [ ] ✅ Vibração eficaz mas não incômoda
- [ ] ✅ Temperatura < 40°C em uso prolongado
- [ ] ✅ Sem falsos positivos ao comer/beber

#### 🛡️ Testes de Segurança

| Teste | Critério de Sucesso | Status |
|-------|---------------------|--------|
| **Queda de 1m** | Sem danos estruturais | ⬜ |
| **Submersão 30s** | Sem entrada de água | ⬜ |
| **Mordida (simulada)** | Componentes protegidos | ⬜ |
| **Temperatura extrema** | Funcional 0-40°C | ⬜ |
| **Vibração contínua** | Motor não superaquece | ⬜ |

#### 📌 Lembretes
- 📱 Configurar alerta de bateria fraca (< 20%)
- 🔄 Testar atualização de firmware OTA
- 📸 Documentar com fotos/vídeos

#### 📦 Entregas
- [ ] Sistema 100% funcional
- [ ] Relatório de testes completo
- [ ] Manual do usuário (PT-BR)
- [ ] Vídeo demonstrativo
- [ ] Código-fonte documentado

---

## 🎁 Backlog Adicional: Tapete com Sensores de Peso

### 🎯 Objetivo

Criar tapetes inteligentes para detectar quando o cachorro cruza divisas entre cômodos, usando sensores de pressão para reforçar a localização espacial através de vibração.

---

### 🔧 Componentes Necessários

| Componente | Especificação | Quantidade | Custo (est.) |
|------------|---------------|------------|--------------|
| 🎛️ **Sensores FSR** | Force Sensing Resistor 5kg+ | 4-8 por tapete | $5-10 |
| 🧠 **Microcontrolador** | ESP32 Nano ou Arduino Nano | 1 por tapete | $8-15 |
| 📳 **Motor Haptic** | ERM 3V (opcional) | 1 por tapete | $3-5 |
| 🔋 **Bateria** | LiPo 500mAh ou USB | 1 por tapete | $5-10 |
| 📡 **Módulo BLE** | Integrado no ESP32 | - | - |
| 🧵 **Material tapete** | Borracha/silicone antiderrapante | 1m² | $10-20 |

---

### 🔄 Fluxo de Funcionamento

```
┌─────────────────────────────────────────────┐
│  1️⃣ Cachorro pisa no tapete                │
└──────────────┬──────────────────────────────┘
               ↓
┌─────────────────────────────────────────────┐
│  2️⃣ Sensores FSR detectam peso (> 5kg)     │
└──────────────┬──────────────────────────────┘
               ↓
┌─────────────────────────────────────────────┐
│  3️⃣ Microcontrolador processa sinal        │
│     (filtra ruído, valida peso)             │
└──────────────┬──────────────────────────────┘
               ↓
┌─────────────────────────────────────────────┐
│  4️⃣ Envia comando via BLE para coleira     │
│     Payload: {room: "sala", action: "enter"}│
└──────────────┬──────────────────────────────┘
               ↓
┌─────────────────────────────────────────────┐
│  5️⃣ Coleira ativa:                          │
│     • 📳 Vibração direcional                │
│     • 🔊 Áudio do cômodo (opcional)         │
└─────────────────────────────────────────────┘
```

---

### 🗺️ Layout do Tapete

```
┌────────────────────────────────────┐
│   🚪 PORTA / DIVISA               │
│                                    │
│   [FSR1]  [FSR2]  [FSR3]  [FSR4]  │
│     🔴      🔴      🔴      🔴     │
│      ↓       ↓       ↓       ↓    │
│   ┌─────────────────────────────┐ │
│   │    🧠 ESP32 + BLE          │ │
│   └─────────────────────────────┘ │
│                                    │
│         📳 Motor (opcional)        │
└────────────────────────────────────┘

Dimensões sugeridas: 60cm × 20cm
```

---

### ✅ Vantagens

| Vantagem | Descrição |
|----------|-----------|
| 🎯 **Precisão física** | Detecção por peso real, sem dependência de GPS/BLE |
| 🔗 **Integração** | Complementa sensores da coleira |
| 🏠 **Delimitação clara** | Define zonas sem necessidade de beacons complexos |
| 💰 **Baixo custo** | Componentes acessíveis (~$30-50 por tapete) |
| 🔋 **Autonomia** | Modo sleep quando não detecta peso (semanas de bateria) |

---

### ⚠️ Desafios

| Desafio | Solução |
|---------|---------|
| 🐾 **Falsos positivos** | Filtrar por peso mínimo (> 5kg) e tempo (> 1s) |
| 🧹 **Manutenção** | Design removível para limpeza |
| 🔌 **Alimentação** | Bateria LiPo + carregamento USB ou fonte fixa |
| 📡 **Alcance BLE** | Posicionar tapetes próximos à área de uso |

---

### 🚀 Próximos Passos

##### LEMBRAR ####
- Calibrar a distancia de acordo com o tamanho do cao
- Calibrar a distancia minima no qual o cachorro irá comer (para nao pertubrar o cachorro nessa atividade)

#### Fase 1: Prototipagem (1 semana)
- [ ] Adquirir sensores FSR e ESP32 Nano
- [ ] Testar detecção de peso com diferentes objetos
- [ ] Validar comunicação BLE com coleira

#### Fase 2: Integração (1 semana)
- [ ] Criar protocolo de comunicação tapete → coleira
- [ ] Implementar lógica de zona no firmware da coleira
- [ ] Testar latência de resposta (< 500ms)

#### Fase 3: Produção (2 semanas)
- [ ] Fabricar 3-5 tapetes para cômodos principais
- [ ] Encapsular eletrônica em silicone
- [ ] Testar durabilidade (pisadas repetidas)

#### Fase 4: Validação (1 semana)
- [ ] Instalar tapetes na casa do tutor
- [ ] Testar com cachorro real por 7 dias
- [ ] Coletar feedback e ajustar sensibilidade

---

## 📚 Apêndices

### 🔗 Referências Técnicas

- [ESP32 Datasheet](https://www.espressif.com/en/products/socs/esp32)
- [VL53L1X LIDAR Sensor](https://www.st.com/en/imaging-and-photonics-solutions/vl53l1x.html)
- [DFPlayer Mini MP3 Module](https://wiki.dfrobot.com/DFPlayer_Mini_SKU_DFR0299)
- [Force Sensing Resistors (FSR)](https://www.interlinkelec.com/fsr)

### 📧 Contato

Para dúvidas ou contribuições, entre em contato através dos canais do projeto.

---

<div align="center">

**🐕‍🦺 Projeto desenvolvido com ❤️ para melhorar a qualidade de vida de pets com deficiência visual**

*Versão 1.0 | Última atualização: Novembro 2025*

</div>
