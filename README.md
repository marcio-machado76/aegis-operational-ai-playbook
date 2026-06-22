# Playbook de IA operacional — Aegis

Biblioteca de prompts operacionais da Aegis, organizada para substituir o uso ad-hoc de
prompts soltos no histórico de chat. Cada item do playbook é versionado, parametrizável e
documentado para reuso pelo time de engenharia.

## Escopo atual

O repositório está sendo construído checkpoint a checkpoint a partir do desafio da Aegis.
Neste momento, o playbook contém o primeiro prompt operacional da biblioteca.

## Estrutura

```text
devops/
  triagem-de-pods/
    prompt.md
    README.md
```

## Prompts

- [triagem-de-pods](./devops/triagem-de-pods/) — triagem de pods Kubernetes a partir de um snapshot do cluster.
