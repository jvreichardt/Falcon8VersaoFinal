# Falcon8VersaoFinal

Processador RISC de 8 bits inspirado no RISC-V, desenvolvido do zero no Logisim-Evolution como projeto acadêmico. Inclui pipeline de 3 estágios e dois módulos de controle para veículos autônomos.

🎥 [Vídeo demonstrativo](https://www.youtube.com/watch?v=KGEStgCfw6g)

---

## Arquitetura

- **Largura de dados:** 8 bits
- **Instruções:** 16 bits
- **Registradores:** 8 (R0–R7), R0 fixo em 0
- **Memória:** ROM 256×8 (instruções) + RAM 256×8 (dados)
- **Pipeline:** 3 estágios — Fetch / Decode+Execute / Writeback+Memory

### Estágios do Pipeline

| Estágio | O que faz |
|---|---|
| IF | Busca instrução na ROM, incrementa PC |
| ID/EX | Decodifica, lê registradores, executa na ALU |
| MEM/WB | Acessa RAM, grava resultado nos registradores |

### Hazards
- **RAW:** resolvido por forwarding
- **Load-Use:** eliminado por design — com 3 estágios o LW já gravou quando a instrução seguinte chega no decode
- **Controle (branch/jump):** resolvido por flush do pipeline
- **Stall:** não necessário

---

## Conjunto de Instruções

| Tipo | Instruções |
|---|---|
| R | ADD, SUB, AND, OR, SLT, SLL, SRL |
| I | ADDI, LW, SLLI |
| S | SW |
| B | BEQ, BNE, BLT |
| J | JAL |

---

## Módulos Implementados

### Módulo 1 — Sistema de Frenagem Inteligente (SFI)
Lê sensores de distância e velocidade da memória e decide o comando de freio:

| Condição | Saída Mem[0x20] | Status Mem[0x21] |
|---|---|---|
| Distância OK | 0 (desativado) | 0 (normal) |
| Dist. crítica, vel. OK | 1 (freio normal) | 1 (alerta) |
| Dist. crítica + vel. alta | 2 (emergência) | 2 (emergência) |

### Módulo 2 — Controle de Velocidade Adaptativa (CVA)
Ajusta a velocidade alvo com base na distância do veículo à frente:

| Condição | Ação |
|---|---|
| dist < 10m | vel_alvo = vel_atual >> 1 (÷2) |
| 10m ≤ dist ≤ 20m | vel_alvo = vel_frente (manter) |
| dist > 20m | vel_alvo = vel_atual << 1 (×2, máx 120) |

---

## Arquivos

```
FalconPipeline.circ          → Circuito completo no Logisim-Evolution
codigosassembly.txt          → Código Assembly dos dois módulos
InstructionsCasos1_5.hex     → Hexadecimal do Módulo 1 (SFI)
InstructionsCasos6_10.hex    → Hexadecimal do Módulo 2 (CVA)
falcon8_assembler_v3.html    → Montador Falcon-8 (abre no navegador)
```

---

## Montador

O Falcon-8 usa formato de instrução próprio — incompatível com o RARS. Para gerar o `.hex`:

1. Abra `falcon8_assembler_v3.html` no navegador
2. Cole o código Assembly
3. Clique em **Montar** e depois **Baixar .hex**
4. Carregue o arquivo na ROM do Logisim

---

## Como executar

1. Abra `FalconPipeline.circ` no **Logisim-Evolution 4.1.0**
2. Carregue o `.hex` desejado na ROM (botão direito → Load image)
3. Carregue os valores de entrada na RAM nos endereços 0x10–0x15
4. Ative a simulação e observe as saídas em 0x20–0x23

---

## Tecnologias

- [Logisim-Evolution 4.1.0](https://github.com/logisim-evolution/logisim-evolution)
- Assembly Falcon-8 (montador customizado em HTML/JS)
