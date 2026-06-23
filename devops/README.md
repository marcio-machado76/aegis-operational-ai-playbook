# DevOps

Prompts voltados a **infraestrutura, automação e operação** de sistemas: pipelines de CI/CD, containers, orquestração, provisionamento, observabilidade, confiabilidade e segurança operacional.

## Escopo

Entram aqui prompts relacionados a:

- Pipelines de CI/CD (GitHub Actions, GitLab CI, Jenkins etc.).
- Containers e orquestração (Docker, Kubernetes, Helm).
- Infraestrutura como código (Terraform, Pulumi, Ansible).
- Provedores de nuvem (AWS, GCP, Azure) e seus recursos.
- Observabilidade (logs, métricas, tracing, alertas, dashboards).
- Confiabilidade, SRE, postmortems e análise de incidentes.
- Segurança operacional (hardening, secrets, políticas de acesso).

## Prompts

| Prompt | Descrição | Versão |
|--------|-----------|--------|
| [triagem-de-pods](triagem-de-pods/) | Triagem de pods Kubernetes a partir de um snapshot | 1.0.0 |
| [nota-de-triagem](nota-de-triagem/) | Nota padronizada de alerta a partir de um alerta cru | 1.0.0 |
| [causa-raiz](causa-raiz/) | Análise de causa-raiz cruzando config, métricas e logs | 1.0.0 |
| [estrategia-de-backpressure](estrategia-de-backpressure/) | Comparação de estratégias de backpressure no barramento | 1.0.0 |
| [migracao-batch-para-streaming](migracao-batch-para-streaming/) | Cadeia de migração de pipeline batch → streaming | 1.0.0 |
| [networkpolicy-sentinel](networkpolicy-sentinel/) | Endurecimento de NetworkPolicy permissiva (default-deny) | 1.0.0 |

## Testes (CP08) — setup e ajustes comuns

Os prompts de saída estruturada têm um `promptfooconfig.yaml` ao lado do `prompt.md`
(o teste viaja junto com o prompt). Rodados com `npx promptfoo@latest eval --no-cache`.

- **Provedor bloqueante no CI:** `openai:chat:gpt-5.4-mini`.
- **Segundo provedor usado ao longo do desafio:** `anthropic:messages:claude-haiku-4-5-20251001`
  nos prompts de saída aberta avaliados nos CP09/CP10.
- **Limites operacionais em todos:** latência ≤ 5s e custo ≤ US$ 0,01.

**Ajustes feitos durante o CP08 (o caminho, não só o destino):**
1. **Ollama/experimentos iniciais → Claude API.** As primeiras iterações testaram Llama local,
   mas o caminho final do CI migrou para Claude Haiku por previsibilidade maior de execução.
2. **Gemini free tier → OpenAI.** O gate chegou a usar Gemini 2.5 Flash, mas a combinação de
   rate limit 429 e backoff do free tier gerou flakes demais para CI bloqueante. O provider
   determinístico foi trocado para OpenAI.
3. **Segundo provedor fora do caminho crítico do CP08.** Depois da migração, ficou claro que
   qualquer 2º provedor no gate determinístico podia reintroduzir falso vermelho por latência ou
   indisponibilidade. A solução final foi: OpenAI barra os prompts estruturados; Anthropic fica
   nos prompts de saída aberta e cumpre a exigência de múltiplos provedores ao longo do desafio.
4. **Assert de custo tolerante.** O `type: cost` nativo do promptfoo pode falhar quando o
   provedor não reporta preço. Reescrevi como `javascript` que valida ≤ US$ 0,01 quando há custo
   e passa quando o provedor não reporta.

**Resultados (`promptfoo eval`):**

| Config | Casos × providers no config | Papel no CI |
|--------|--------------------|-----------|
| triagem-de-pods | 3 × 2 | OpenAI **bloqueante** |
| networkpolicy-sentinel | 1 × 2 | OpenAI **bloqueante** |
| nota-de-triagem | 3 × 2 | OpenAI **bloqueante** |
