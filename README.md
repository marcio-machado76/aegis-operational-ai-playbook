# Playbook de IA operacional — Aegis

Biblioteca de prompts de engenharia da Aegis, **versionada, testada e tratada como código**,
para substituir o uso ad-hoc de prompts soltos no histórico de chat. Cada item do playbook é
parametrizável, documentado, curado e coberto por avaliação automatizada, para que qualquer
engenheiro do time possa reutilizá-lo com confiança.

Os prompts desta biblioteca recebem os dados variáveis na própria entrada (snapshot, alerta,
artefatos, cenário, manifesto, provedor), sem agentes de codificação nem tools externas. A
entrega registra o **prompt parametrizável final** com sua curadoria; o meta-prompting foi usado
como método de criação e refino, mas o meta-prompt em si não faz parte da biblioteca.

## Origem e mapeamento (Checkpoints 01–06)

Os seis prompts nasceram dos checkpoints do desafio e foram organizados nas convenções desta
biblioteca:

- **Categoria → pasta na raiz:** todos os prompts são operacionais, então caem em
  [`devops/`](./devops/) — sem aninhar categorias.
- **Um prompt por pasta, nomeada pelo resultado:** `triagem-de-pods/`, `causa-raiz/`,
  `networkpolicy-sentinel/` etc. — nunca pela técnica (não existe `chain-of-thought/`).
- **`{{placeholders}}` = `inputs` do frontmatter:** os parâmetros de cada prompt (o snapshot,
  o alerta, os artefatos, o cenário) aparecem como `{{variavel}}` no `prompt.md` e estão
  documentados no campo `inputs`. É onde "todo prompt é parametrizável" encontra a estrutura.
- **Desvio consciente — cadeia:** `migracao-batch-para-streaming/` é uma cadeia de prompts
  (CP05), então usa uma subpasta `prompts/` com os elos numerados, em vez de um único
  `prompt.md`. O frontmatter da cadeia fica no `README.md` da pasta.

## Mapeamento da entrega ao desafio

| Checkpoint | Evidência no repositório |
|---|---|
| **CP01** | [`devops/triagem-de-pods/`](./devops/triagem-de-pods/) — prompt parametrizável + README + testes determinísticos |
| **CP02** | [`devops/nota-de-triagem/`](./devops/nota-de-triagem/) — prompt parametrizável + README + testes determinísticos |
| **CP03** | [`devops/causa-raiz/`](./devops/causa-raiz/) — prompt de saída aberta + gate LLM-as-judge + calibração |
| **CP04** | [`devops/estrategia-de-backpressure/`](./devops/estrategia-de-backpressure/) — comparação de estratégias com avaliação informativa |
| **CP05** | [`devops/migracao-batch-para-streaming/`](./devops/migracao-batch-para-streaming/) — cadeia diagnóstico → plano → runbook |
| **CP06** | [`devops/networkpolicy-sentinel/`](./devops/networkpolicy-sentinel/) — endurecimento de manifesto com saída em YAML |
| **CP07** | Estrutura da biblioteca descrita neste README e no índice de [`devops/`](./devops/) |
| **CP08** | `promptfooconfig.yaml` acoplados aos prompts de saída estruturada |
| **CP09** | Rubrica calibrada em [`devops/causa-raiz/calibracao.yaml`](./devops/causa-raiz/calibracao.yaml) |
| **CP10** | Pipeline em [`.github/workflows/promptfoo.yml`](./.github/workflows/promptfoo.yml) + justificativa em [`.github/workflows/JUSTIFICATIVA.md`](./.github/workflows/JUSTIFICATIVA.md) |

## Estrutura e convenções

```
<categoria>/
  <nome-do-prompt>/
    prompt.md            # frontmatter + texto puro do prompt (placeholders {{variavel}})
    README.md            # mesmo frontmatter + doc humana (objetivo, uso, exemplo, limitações)
    promptfooconfig.yaml # testes do prompt (onde aplicável) — viajam junto com o prompt
```

- **Categoria** = pasta na raiz, em `kebab-case`, uma por domínio, sem aninhar.
- **Prompt** = subpasta nomeada pelo **resultado**, não pela técnica.
- **`prompt.md`** = frontmatter YAML + o texto do prompt, com os parâmetros como `{{variavel}}`.
- **`README.md`** = o mesmo frontmatter + documentação humana (objetivo, quando usar, exemplo,
  limitações, curadoria).
- **Frontmatter** (idêntico nos dois arquivos): `nome`, `descricao`, `versao` (semver, inicia
  em `1.0.0`), `tags`, `inputs` (lista de parâmetros, cada um com `nome` e `descricao`).
- **Versionamento:** o campo `versao` de cada prompt + **commits semânticos** com escopo na
  categoria (ex.: `feat(devops): adiciona prompt de triagem de pods`). Os índices (este README
  e o da categoria) são atualizados junto com a mudança.

## Categorias

### [DevOps](./devops/)

Pipelines de CI/CD, containers, orquestração, infraestrutura como código, observabilidade,
SRE e segurança operacional.

- [triagem-de-pods](./devops/triagem-de-pods/) — triagem de pods Kubernetes a partir de um snapshot do cluster.
- [nota-de-triagem](./devops/nota-de-triagem/) — nota padronizada de alerta a partir de um alerta cru.
- [causa-raiz](./devops/causa-raiz/) — análise de causa-raiz cruzando config, métricas e logs.
- [estrategia-de-backpressure](./devops/estrategia-de-backpressure/) — comparação de estratégias de backpressure num barramento.
- [migracao-batch-para-streaming](./devops/migracao-batch-para-streaming/) — cadeia de prompts para migrar um pipeline batch → streaming.
- [networkpolicy-sentinel](./devops/networkpolicy-sentinel/) — endurecimento de uma NetworkPolicy permissiva (default-deny).

## Testes e integração contínua

O playbook é testado, não só guardado:

- **Determinístico (CP08):** `triagem-de-pods`, `nota-de-triagem` e `networkpolicy-sentinel`
  têm `promptfooconfig.yaml` com asserts de formato + limites de latência e custo.
- **LLM-as-judge (CP09):** `causa-raiz` (saída aberta) tem um gate de qualidade por juiz LLM,
  calibrado contra pontuação humana (ver `devops/causa-raiz/`).
- **Pipeline (CP10):** [`.github/workflows/promptfoo.yml`](./.github/workflows/promptfoo.yml)
  roda a suíte a cada pull request — o recorte determinístico em **OpenAI** barra o build, e o
  juiz LLM roda como camada informativa para as saídas abertas. O requisito de usar ao menos dois
  provedores ao longo do desafio é atendido pelo uso de **Anthropic** nos prompts abertos
  avaliados nos CP09/CP10. A justificativa do desenho do gate está em
  [`.github/workflows/JUSTIFICATIVA.md`](./.github/workflows/JUSTIFICATIVA.md).

## Como usar

1. Abrir o `README.md` do prompt para entender objetivo, parâmetros e limitações.
2. Copiar o conteúdo do `prompt.md` e substituir os placeholders `{{variavel}}` pelos valores
   reais (o snapshot, o alerta, os artefatos…).
3. Rodar no modelo de sua escolha (chat, playground ou API).

## Como adicionar ou alterar um prompt

1. Escolher a categoria existente que melhor se encaixa (ou criar uma nova pasta na raiz, em
   `kebab-case`, com seu próprio `README.md` de escopo).
2. Criar a pasta do prompt nomeada pelo **resultado** e escrever `prompt.md` + `README.md` com
   o **mesmo frontmatter** (`versao: 1.0.0` no nascimento).
3. Adicionar `promptfooconfig.yaml` quando o prompt for testável.
4. Atualizar os índices (este README e o da categoria) e commitar com mensagem semântica
   (`feat(<categoria>): ...`). Evoluções posteriores incrementam o campo `versao`.
