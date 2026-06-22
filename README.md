# Playbook de IA operacional — Aegis

Biblioteca de prompts operacionais da Aegis, organizada para substituir o uso ad-hoc de
prompts soltos no histórico de chat. Cada item do playbook é versionado, parametrizável e
documentado para reuso pelo time de engenharia.

## Escopo atual

O repositório está sendo construído checkpoint a checkpoint a partir do desafio da Aegis.
Neste momento, o playbook contém os primeiros prompts operacionais da biblioteca.

## Estrutura

```text
devops/
  nota-de-triagem/
    prompt.md
    README.md
  triagem-de-pods/
    prompt.md
    README.md
```

## Prompts

- [causa-raiz](./devops/causa-raiz/) — análise de causa-raiz cruzando config, métricas e logs.
- [estrategia-de-backpressure](./devops/estrategia-de-backpressure/) — comparação de estratégias de backpressure num barramento.
- [migracao-batch-para-streaming](./devops/migracao-batch-para-streaming/) — cadeia de prompts para migrar um pipeline batch → streaming.
- [networkpolicy-sentinel](./devops/networkpolicy-sentinel/) — endurecimento de uma NetworkPolicy permissiva (default-deny).
- [nota-de-triagem](./devops/nota-de-triagem/) — nota padronizada de alerta a partir de um alerta cru.
- [triagem-de-pods](./devops/triagem-de-pods/) — triagem de pods Kubernetes a partir de um snapshot do cluster.
