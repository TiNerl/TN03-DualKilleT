# 🚀 Projeto TN03 — Codinome: DualKiller

Documentação técnica, especificação de hardware, estimativas de consumo energético e decisões de arquitetura de software para o nó de processamento **TN03 (DualKiller)**.

---

## 📌 Visão Geral do Projeto

O nó **TN03 "DualKiller"** é um servidor de alta densidade de processamento projetado para atuar como homelab principal, ambiente de desenvolvimento distribuído, hospedagem de microserviços e suporte a projetos acadêmicos e de conclusão de curso (TCC).

* **Objetivo Principal:** Execução de virtualização (Proxmox VE), containers Docker, servidores de jogos e pipelines de teste.
* **Orçamento Total Aprovado:** R$ 1.100,00 (parcelado em 10x).
* **Fator de Forma:** ATX (Placa-mãe de servidor em gabinete convencional).

---

## 🛠️ Especificações do Hardware

| Componente | Modelo / Descrição | Status | Observações Técnicas |
| :--- | :--- | :--- | :--- |
| **Placa-Mãe** | Supermicro MBD-X9DBL-IF-B | Comprado | Soquete duplo LGA 1356, Chipset C602, Formato ATX |
| **Processadores** | 2x Intel Xeon E5-2470 v2 | Comprado | 20 Núcleos / 40 Threads (2.4 GHz Base / 3.2 GHz Turbo, 95W TDP cada) |
| **Cooler CPU** | 2x Maxcool Air Cooler | Comprado | TDP suportado até 115W, compatíveis com soquete LGA 1356 |
| **Memória RAM** | 20GB DDR3 Non-ECC (4x 4GB + 2x 2GB) | Reaproveitado | Configuração Triple-Channel por CPU (Limite físico da placa em Non-ECC: 48GB) |
| **Placa de Vídeo** | NVIDIA GeForce GTX 1060 5GB | Reaproveitada | Conexão PCIe 3.0 para suporte leve a aceleração/video transcoding |
| **Armazenamento** | 6x HDD Seagate 500GB (ST500D002) | Reaproveitado | Arranjo de armazenamento primário para dados e backups |
| **Fonte de Energia**| Corsair VS550 (550W 80 Plus) | Reaproveitada | Dimensionada para a carga estimada de ~400W a 450W sob estresse |
| **Gabinete & Ar** | Gabinete ATX Mid-Tower + Ventoinhas 120mm | Comprado | R$ 150 (gabinete) + R$ 100 (ventoinhas) para fluxo de ar contínuo |

---

## ⚡ Análise de Viabilidade Energética & Custos

Com a redefinição do escopo (remoção das GPUs de inteligência artificial de alto consumo), o consumo elétrico e a exigência de energia foram significativamente reduzidos, viabilizando o uso da fonte Corsair VS550.

### Estimativa de Carga Elétrica
* **Idle / Carga Leve:** ~140W a 170W
* **Carga Moderada / Pico:** ~400W a 450W
* **Média Estimada de Operação:** **180W/h**

### Projeção Financeira Mensal (24/7 no RN)
* **Consumo Diário:** $180\text{W} \times 24\text{h} = 4,32\text{ kWh/dia}$
* **Consumo Mensal (30 dias):** $4,32\text{ kWh} \times 30 = 129,6\text{ kWh/mês}$
* **Tarifa Média Estimada (Neoenergia Cosern - RN):** ~R$ 1,05 / kWh
* **Custo Adicional Estimado:** **~R$ 136,00 / mês**

---

## 🚦 Tabela Kanban de Status (GitHub Projects Format)

| 📋 TO DO | ⏳ IN PROGRESS | ✅ DONE |
| :--- | :--- | :--- |
| • Montagem física do hardware | • Acompanhar entrega dos pedidos | • Seleção dos processadores |
| • Formatação com Proxmox VE | • Validação do arranjo de RAM | • Viabilidade da fonte VS550 |
| • Configuração de Linux Bridges | _(Aguardando entregas)_ | • Descarte do projeto de M40 GPUs |
| • Migração dos containers Docker | | • Compra do Kit Hardware (R$ 1.100) |

---

### Detalhamento dos Cards de Decisão

#### 📋 TO DO
- [ ] **Montagem Física:** Acomodar a placa Supermicro no gabinete ATX, instalar os dois coolers Maxcool com pasta térmica e posicionar as ventoinhas de 120mm para exaustão.
- [ ] **Instalação do Proxmox VE:** Realizar o boot via pendrive, formatar o disco de sistema e configurar o IP estático do nó.
- [ ] **Rede Virtual (Linux Bridges):** Configurar interfaces virtuais para comunicação interna entre containers e integração com o gateway de rede (ZeroTier / OpenWrt).
- [ ] **Migração de Serviços:** Mover as Stacks Docker do ZimaOS antigo para o novo ambiente em VMs/LXC no Proxmox.

#### ⏳ IN PROGRESS
- [ ] **Logística de Entregas:** Acompanhar o envio dos processadores Xeon E5-2470 v2, coolers, gabinete e ventoinhas adicionais.
- [ ] **Mapeamento de Memória:** Testar a inicialização com os pentes de 4GB e 2GB Non-ECC em Triple-Channel para garantir estabilidade do POST.

#### ✅ DONE
- [x] **Escolha do Processador:** Seleção do Xeon E5-2470 v2 (Melhor opção do soquete LGA 1356, descartando a linha "L" por limitação de clock base).
- [x] **Redimensionamento da Fonte:** Cancelamento da compra de duas GPUs NVIDIA Tesla M40 (250W cada) para manter a fonte Corsair VS550 de 550W em operação segura.
- [x] **Análise de Durabilidade e Overclock:** Confirmação de que chips Xeon de servidor operam sem histórico de overclock e possuem alta longevidade em ambientes de laboratório.
- [x] **Definição da Distribuição:** Escolha do **Proxmox VE** como Hypervisor principal (substituindo o ZimaOS) visando o aprendizado avançado de virtualização e preparação para o TCC.

---

## 🎯 Arquitetura de Software & Proxmox VE

A escolha do **Proxmox VE** como sistema operacional definitivo fundamenta-se nos seguintes pilares:

1. **Virtualização de Alta Performance (Hypervisor Tipo-1):** Permite isolar o ambiente do sistema em Máquinas Virtuais (VMs) dedicadas para desenvolvimento de software (NestJS, Vue.js, C#, Python).
2. **Containers LXC (Linux Containers):** Ideal para hospedagem de servidores de jogos (Minecraft, Project Zomboid, Garry's Mod), garantindo consumo mínimo de memória RAM e acesso direto à CPU.
3. **Gerenciamento de Snapshots:** Capacidade de criar pontos de restauração instantâneos antes de alterações críticas de infraestrutura e código.
4. **Isolamento de Redes:** Criação de interfaces virtuais para simulação de topologias de rede corporativa, integração com VPNs/Mesh (ZeroTier) e alinhamento com os requisitos do TCC acadêmico.
