`.specs/project/ROADMAP.md` documenta o plano de evolução do projeto a médio/longo prazo — útil para um agente de IA avaliar se uma mudança proposta está alinhada com a direção futura do projeto, ou se conflita com algo já planejado.

## Uso prático com agentes

Diferente de `AGENTS.md` (regras operacionais sempre carregadas) e de `STATE.md` (snapshot do presente, mudando com frequência), `ROADMAP.md` é consultado pontualmente — quando o agente precisa decidir entre duas abordagens de implementação e uma delas se alinha melhor com uma direção futura já registrada. Vale a pena referenciá-lo explicitamente no prompt ("considere @.specs/project/ROADMAP.md antes de propor a arquitetura") em vez de esperar que seja carregado automaticamente, já que nenhum harness pesquisado trata pastas dentro de `.specs/` como memória sempre-ativa por padrão.

## Fontes

- https://code.claude.com/docs/en/memory
