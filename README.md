# Formula 1 1995 (PS1) — Mapeamento de memória

Mapeamentos experimentais obtidos com telemetria no BizHawk. Os endereços e interpretações abaixo referem-se ao ambiente da coleta; a compatibilidade com outras versões/regions do jogo ainda não foi estabelecida.

## EXP003 — Contadores por slot (30/10/2026)

**Fontes:** `F1_1995_exp003_samples(1).csv` (306 amostras) e `F1_1995_exp003_events(1).csv` (136 alterações). Experimento realizado com um piloto de outra equipe.

- Foram monitorados **26 slots**, índices **0–25**.
- Endereço do campo candidato no slot `s`: `0x0E18C0 + 0x9C * s` (passo de **156 bytes**).
- Exemplos: slot 0 → `0x0E18C0`; slot 1 → `0x0E195C`; slot 25 → `0x0E27FC`.
- Todos os **136 eventos** respeitam essa fórmula de endereçamento.
- `player_slot_raw` apresentou 7 amostras com slot 0, 271 com slot 25 e 28 com valor inválido `4294967295` (`0xFFFFFFFF`).
- Nas 271 amostras com slot 25, `player_lap_candidate == slot_25` (consistência interna, **não** confirmação independente do significado).
- Entre os eventos do slot 25: `0→1` no frame **154635**, `1→2` no **159679**, `2→3` no **164539**, `3→4` no **169493**. Houve transições de inicialização/reinicialização anteriores.
- Após o reset, todos os 26 slots registraram as transições `1→2` e `2→3`, em frames distintos; somente slot 25 atingiu `3→4` no registro disponível.

### Interpretação e confiança

| Item | Situação |
| --- | --- |
| Fórmula de endereços dos 26 slots | Confirmada nos 136 eventos do EXP003 |
| Campo varia individualmente por slot | Observado |
| Campo é contador de voltas | **Hipótese forte, não validada independentemente** |
| Slot 25 é o carro controlado no experimento | Compatível com a identificação coletada; requer referência independente |
| `0xFFFFFFFF` indica slot inválido | Observado no log; tratar como inválido |
| Layout idêntico para todas as versões do jogo | Não verificado |

**Atenção:** o endereço base representa o **campo observado dentro do registro de cada slot**, não necessariamente o início da estrutura completa do carro. O tipo/tamanho nativo do campo ainda precisa ser confirmado. Dados de pré-corrida contêm valores altos e resets: não interpretar todo incremento como volta completada.

## Próximo experimento: EXP004

1. Registrar uma referência independente do piloto/equipe selecionados e do slot efetivo do jogador.
2. Capturar o estado da corrida (menus, grid, largada, corrida e chegada) para separar inicialização, resets e voltas.
3. Comparar os incrementos do contador com a passagem visual ou uma referência independente da linha de chegada.
4. Validar o tamanho do campo (8/16/32 bits), endianidade, versão/região do jogo e domínio de memória utilizado.
5. Repetir com outra equipe, reinício, mudança de sessão e, se possível, outro modo de jogo.

A versão legível por máquina está em [`mapping/exp003_player_lap_candidate.json`](mapping/exp003_player_lap_candidate.json).
