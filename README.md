# IA para Cibersegurança — Projeto do semestre

CESAR School · Tecnológico em Segurança da Informação · **2026.2**
Prof. Raphael Crespo Pereira · **Aluno:** Rafael Holder (trabalho individual)

Projeto do semestre: escolho um problema de segurança, construo um detector com Machine Learning (**Etapa 1**, AV1) e depois o ataco e defendo (**Etapa 2**, AV2). Cada aula vira um notebook reprodutível, com as saídas versionadas como evidência da execução.

---

## Estrutura

```
.
├── notebooks/            # um notebook por aula (sobem com as saídas visíveis)
│   ├── setup_aula1_ia_ciberseguranca.ipynb
│   ├── aula2_fundamentos_ml_teachable_machine.ipynb
│   ├── aula3_ml_ciberseguranca_cicids2017.ipynb
│   ├── aulas4_metricas_validacao_phishing_alunos.ipynb
│   ├── aula5_modelagem_detector_malware_uci_splunk.ipynb
│   └── aula6_ueba_anomalias_process_mining.ipynb
├── av1/                  # relatório, guia, roteiro e apresentação da AV1
│   └── Entregas/         # pacote de entrega: relatório (PDF) + notebook + apresentação
├── respostas/            # respostas das atividades + PDFs de evidência
├── models/               # modelo_aula2.h5 (alvo dos ataques da aula 10) + labels
├── data/
│   ├── test/             # imagens de teste da aula 2 (rock/paper/scissors)
│   ├── raw/              # dados brutos baixados (não versionados)
│   └── processed/        # dados gerados pelos notebooks (não versionados)
├── requirements.txt
└── CLAUDE.md             # contexto do repositório
```

As bases de dados são grandes demais para o GitHub, então **não** são versionadas (ver [Dados](#dados)); os notebooks as baixam sozinhos. Só a origem fica documentada.

---

## Como reproduzir

Todos os experimentos usam **seed 42** (`RANDOM_SEED = 42` / `set_seed(42)`), e o split é feito **antes** de qualquer ajuste.

### Opção A — Google Colab (recomendada)

Os notebooks foram escritos para o Colab (caminhos `/content`, download automático dos dados, `google.colab`).

1. Abra o notebook desejado em `notebooks/` pelo botão **“Open in Colab”** (ou suba o arquivo no Colab).
2. Em *Ambiente de execução*, monte o Google Drive quando a célula pedir (aulas 1 e 2).
3. Rode as células **em ordem, de cima para baixo**. A célula de carregamento baixa a base automaticamente.

### Opção B — Local

Funciona bem para as aulas **3, 4, 5 e 6** (a aula 5 cai automaticamente de `/content` para a pasta atual). As aulas **1 e 2** usam `google.colab` (`drive.mount`, `files.download`) e rodam melhor no Colab.

```bash
git clone https://github.com/rafaelholder/CESAR-2026.2-ia-ciberseguranca.git
cd CESAR-2026.2-ia-ciberseguranca

python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt

jupyter lab        # abra os arquivos em notebooks/ e rode de cima para baixo
```

> As dependências pesadas (`tensorflow`, `torch`) estão sem versão fixa no `requirements.txt` — instale-as só se for rodar as aulas 1/2 localmente; o canônico é o Colab (`pip freeze`).

---

## Dados

As bases passam do limite de 100 MB do GitHub, então versiono **a origem**, não os arquivos — qualquer pessoa obtém exatamente a mesma base.

| Notebook | Base | Origem | Como é obtida |
|---|---|---|---|
| aula 2 | Rock-Paper-Scissors | TensorFlow (`storage.googleapis.com`) | download automático (`rps.zip`, `rps-test-set.zip`); modelo em `models/` |
| aula 3 | CIC-IDS2017 | CIC / UNB | `gdown` (espelho) ou `MachineLearningCSV.zip` do site do CIC |
| aula 4 | UCI Phishing Websites (id 327) | UCI ML Repository | `ucimlrepo.fetch_ucirepo(id=327)` |
| aula 5 | Malware Static/Dynamic VxHeaven+VirusTotal (id 541) | UCI ML Repository | download automático (`archive.ics.uci.edu/.../541`) |
| aula 6 | UEBA / insider (logs) | Google Drive (material da disciplina) | `gdown` |

O modelo `models/modelo_aula2.h5` é versionado (poucos MB) por ser o **alvo dos ataques de evasão da aula 10**.

---

## AV1 — Etapa 1 (detector de malware)

Notebook escolhido: **aula 5** (Random Forest sobre a base UCI 541). Fiz duas alterações, comparando antes × depois:

1. **Dados e Features** — removo as 18 features zeradas e, depois, as 5 que denunciam a origem da coleta.
2. **Avaliação** — troco o split aleatório por um **split por procedência do malware** (treino numa fonte, teste na outra), que mede a generalização sem vazamento de fonte.

O relatório, o roteiro, o guia e a apresentação estão em [`av1/`](av1/); o pacote de entrega (relatório em PDF + notebook executado + apresentação) está em [`av1/Entregas/`](av1/Entregas/).

### Protocolo de avaliação (padrão de todo experimento)

Split estratificado antes de tudo · métrica principal **recall da classe positiva** · métrica global **PR-AUC** · limiar escolhido pelo **custo dos erros, só na validação** · validação cruzada de 5 divisões · **teste aberto uma única vez**, no fim.

---

## Uso de IA

Usei assistentes de IA como ferramenta de apoio (organização do repositório, redação e geração de artefatos), sempre com **curadoria e análise crítica** dos resultados, respeitando a LGPD e a política de governança de IA do CESAR. Os números dos relatórios vêm sempre da execução real dos notebooks.
