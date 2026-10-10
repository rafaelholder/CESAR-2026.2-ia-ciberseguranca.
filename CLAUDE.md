# CLAUDE.md — IA para Cibersegurança (CESAR School, 2026.2)

> Contexto raiz deste repositório. Leia antes de mover, renomear, editar ou executar qualquer arquivo.

---

## 1. Identidade

| Item | Valor |
|---|---|
| Disciplina | IA para Cibersegurança (no Projeto 4 aparece como "Segurança em IA") |
| Curso | Tecnológico em Segurança da Informação, CESAR School |
| Semestre | 2026.2 · 60h · currículo CS2025.1 |
| Docente | Prof. Raphael Crespo Pereira — rcp@cesar.school |
| Aluno | Rafael Holder — **trabalho individual, sem dupla** |
| Repositório | `rafaelholder/CESAR-2026.2-ia-ciberseguranca` |
| Pasta local | `Praticas` |

**Objetivo da disciplina:** usar ML para defender sistemas (Etapa 1: construir detectores) e entender como esses modelos são atacados (Etapa 2: atacar e defender). Tudo registrado em notebooks reprodutíveis, que viram peça de portfólio.

---

## 2. Regras do professor (não negociáveis)

**Canais**
- **Google Classroom** é o canal oficial: plano de ensino, materiais, avisos formais e **submissão de tudo que vale nota**.
- **Slack** (`#ia-para-ciber-cs20262_4a`) é para conversa, dúvidas rápidas e troca de arquivos.
- Regra prática: conversa → Slack; entrega ou algo que vale nota → Classroom.

**Rotina semanal (segundas-feiras, sala BRUM-206)**
- 18h–19h: atividade assíncrona (trilha, leitura ou lab) — **vale nota**.
- 19h–22h: encontro presencial/síncrono.
- Entregas até **23h59** do dia indicado, sempre pelo Classroom.

**Avaliação**
- Média final = (AV1 + AV2) / 2. Cada AV vale 10 pontos.
- Cada AV = **50% notebook da etapa** + **30% atividades assíncronas** + **20% exercícios e estudos de caso em aula**.
- Assíncronas são pontuadas pela realização: completa (100%), parcial (60%), não entregue (0%). Evidência aceita: print de conclusão, resposta curta à pergunta-guia ou célula no notebook. A menor nota de assíncrona de cada etapa é descartada. Atraso de até 48h conta como parcial.
- **Reprodutibilidade conta na nota:** `requirements.txt` com versões realmente instaladas e dados versionados (no mínimo, a origem documentada).
- Apresentação com **arguição individual**: o aluno precisa saber defender cada célula.

**Uso de IA:** permitido como ferramenta de apoio, **com curadoria e análise crítica**, respeitando a LGPD e a política de governança de IA do CESAR. O uso deve ser declarado no README.

**Protocolo de avaliação de modelos (padrão de todo experimento, definido na aula 4)**
1. Split estratificado feito **antes** de qualquer ajuste (sem vazamento).
2. Métrica principal: **recall da classe positiva** (não deixar o ataque passar).
3. Métrica global: **PR-AUC** (honesta no desbalanceamento).
4. Limiar escolhido pelo **custo dos erros**, nunca fixo em 0,5, e escolhido **só na validação**.
5. Robustez: validação cruzada de 5 divisões.
6. O teste é aberto **uma única vez**, no fim.

---

## 3. Inventário das aulas e entregáveis

Os prazos reais divergiram do plano de ensino (o professor estendeu vários e a AV1 foi adiada uma semana). Os prazos abaixo são os efetivamente comunicados.

| Aula | Tema | Notebook | Entregável | Prazo |
|---|---|---|---|---|
| 1 | Setup do ambiente | `setup_aula1_ia_ciberseguranca.ipynb` | Drive montado, estrutura de pastas, `requirements.txt` gerado, célula-teste rodada, notebook versionado no GitHub, nome preenchido no topo | 17/08 |
| 2 | Fundamentos de ML (Teachable Machine) | `aula2_fundamentos_ml_teachable_machine.ipynb` | Modelo treinado no Teachable Machine, avaliado em teste independente, com respostas da discussão no notebook | 17/08 |
| 3 | Dados e features (CIC-IDS2017) | `aula3_ml_ciberseguranca_cicids2017.ipynb` | Carregamento, limpeza e EDA rodados + `respostas_atividade3.txt` (5 respostas curtas, 3–5 linhas cada). Vale como assíncrona **e** como notebook | 26/08 |
| 4 | Métricas, validação e phishing | `aulas4_metricas_validacao_phishing_alunos.ipynb` | Notebook rodado com as 4 respostas (no próprio Colab ou em txt) | — |
| 5 | Detector de malware + Splunk | `aula5_modelagem_detector_malware_uci_splunk.ipynb` | Origem dos dados documentada, features justificadas por família, baseline × RF, limiar na validação, teste final, riscos de leakage, respostas das discussões | — |
| 6 | Anomalias, UEBA e SOC | `aula6_ueba_anomalias_process_mining.ipynb` | Notebook executado + comentário da fadiga de alertas + 4 respostas da discussão | 25/09 |
| AV1 | Experimento com os notebooks | `aula5_...` (seção nova) | Notebook executável + relatório (máx. 2 páginas) + apresentação oral | 05/10 |

**Material de apoio do professor (não é entrega):** `Notebook_referencia_trocar_dataset.ipynb`, `Bases_de_dados_-_compativeis_Notebook_3.xlsx`, `perguntas_atividade_3.txt`, slides em PDF e plano de ensino.

### Assíncronas da Etapa 1
Cisco NetAcad (Introdução à Ciência de Dados, módulo 3) · AWS Academy ML Foundations (introdução, preparação de dados, avaliação de modelos, pipeline com SageMaker) · Cisco CyberOps Associate (conceitos de segurança) · Huawei HCIA-AI (aprendizado não supervisionado). Evidências ficam fora do repositório (prints enviados no Classroom).

---

## 4. AV1 — o que foi pedido e o que foi feito

**Enunciado:** escolher um notebook (aulas 3 a 6), fazer **pelo menos duas alterações**, comparar antes × depois e explicar as decisões.

| Critério | O que o professor avalia |
|---|---|
| O QUE FEZ | Identificação precisa das duas alterações e do resultado observado |
| COMO FEZ | Explicação das células, etapas, valores e comparações |
| POR QUE FEZ | Justificativa e relação com o problema de cibersegurança |

A nota **não depende** de a alteração melhorar o sistema. Categorias permitidas: Dados e Features · Algoritmo · Parâmetros e Decisão · Avaliação.

**Escolha:** notebook da aula 5. Seção nova `## AV1 — Experimentos (antes × depois)` no final, antes de "Entregável da aula". As células originais ficam intactas como o "antes".

- **Alteração 1 (Dados e Features):** 1a remove as 18 features zeradas; 1b remove também as 5 que podem denunciar a origem (`image_base`, `file_alignment`, `section_alignment`, `size_of_headers`, `size_of_optional_header`). Pergunta: o modelo detecta malware ou detecta de onde o arquivo veio?
- **Alteração 2 (Parâmetros e Decisão):** limiar escolhido pelo custo mínimo na validação (FN = 10 × FP), no lugar da regra "recall ≥ 95%", com sensibilidade para razões 1, 2, 5, 10 e 20.

Mesmo split, seed e hiperparâmetros em todas as variantes. Limiar sempre na validação; o teste só reporta.

**Arquivos da AV1:** `relatorio_av1.md` (máx. 2 páginas, estrutura O QUE / COMO / POR QUE por alteração, tabela antes × depois, limitações).

---

## 5. Achados documentados (não "corrigir" sem pedido)

Estes pontos já foram identificados e respondidos nas entregas. **Não alterar as células originais para escondê-los** — eles são parte da análise.

- **Aula 2:** as classes do Teachable Machine vinham com maiúscula (`Rock`) e as pastas de teste em minúscula (`rock`), o que deu 0% de acurácia na primeira execução. Foi adicionada uma célula `class_names = [c.lower() for c in class_names]` antes da avaliação. Resultado final: 95,6% no teste contra 100% na validação do Teachable Machine. Base: Rock Paper Scissors do TensorFlow (70 imagens de treino e 15 de teste por classe, seed 42). O `modelo_aula2.h5` é **o alvo dos ataques da aula 10** e não pode se perder.
- **Aula 3:** 2.830.743 fluxos, 308.381 duplicatas, 1.358 NaN, infinitos em `Flow Bytes/s` e `Flow Packets/s`, valores negativos impossíveis (`Flow Duration = -2`). Após a limpeza, 83,1% BENIGN.
- **Aula 4:** o enunciado cita números da base PhiUSIIL, mas o notebook carregou o fallback **UCI Phishing Websites (id 327, 11.055 sites)**. As respostas usam os números da execução real (recall 96,19% → 99,25%; FN 56 → 11; FP 48 → 161 com limiar 0,5 → 0,2) e explicam a diferença.
- **Aula 5:** as 18 colunas de entropia, imports e `number_of_sections` têm apenas 2 valores diferentes de zero em cerca de 112 mil células. Todo PE tem pelo menos uma seção, então é dado ausente na base original. Há uma célula de auditoria e uma nota explicando isso após a seção 2.
- **Aula 6:** ensemble quase no nível do acaso (top-10 com 1 de 47 insiders; base de 5,8%). A célula dos `traces` usa `log` em vez de `log_proc`, então a redução de `web_browse` não foi aplicada. A matriz do grafo lista `file_read` e `email_send`, que não existem nos dados.

---

## 6. Estrutura-alvo do repositório

```
CESAR-2026.2-ia-ciberseguranca/
├── CLAUDE.md
├── README.md                 # visão geral, como reproduzir, origem dos dados, declaração de uso de IA
├── requirements.txt          # gerado no Colab (versões reais), nunca escrito à mão
├── .gitignore
├── notebooks/
│   ├── setup_aula1_ia_ciberseguranca.ipynb
│   ├── aula2_fundamentos_ml_teachable_machine.ipynb
│   ├── aula3_ml_ciberseguranca_cicids2017.ipynb
│   ├── aulas4_metricas_validacao_phishing_alunos.ipynb
│   ├── aula5_modelagem_detector_malware_uci_splunk.ipynb
│   └── aula6_ueba_anomalias_process_mining.ipynb
├── respostas/
│   └── respostas_atividade3.txt
├── av1/
│   └── relatorio_av1.md
├── models/
│   ├── modelo_aula2.h5       # versionado (poucos MB, alvo da aula 10)
│   └── labels_aula2.txt
├── data/
│   └── test/                 # imagens de teste da aula 2 (rock/paper/scissors)
└── materiais/                # slides, plano de ensino, material de apoio — IGNORADO pelo Git
```

**Convenções:**
- Manter os nomes originais dos notebooks (o professor os reconhece). Apenas remover sufixos de download como `(1)`, `__1_`, `__2_` ou prefixos numéricos longos (`1790551701279_...`).
- Notebooks sobem **com as saídas visíveis**: elas são a evidência da execução.

---

## 7. O que NÃO vai para o GitHub

- Bases de dados: `*.csv`, `*.parquet`, `*.zip`, pastas `MachineLearningCVE/`, `cse2018/`, `uci_malware_541/`, `rps/`, `rps-test-set/`, `data/raw/`, `data/processed/`, `data/tm_treino/`. O GitHub recusa arquivos acima de 100 MB, e a origem fica documentada no README.
- Exports gerados: `malware_features.csv` e similares.
- Modelos serializados regeneráveis: `*.pkl`, `*.joblib`, `converted_keras.zip`. **Exceção:** `models/modelo_aula2.h5`.
- Material do professor: slides em PDF, plano de ensino, planilhas e notebooks de referência (direitos do autor). Ficam em `materiais/`, ignorada.
- Segredos: `.env`, tokens, `credentials*.json`, chaves. Nunca colar token em notebook.
- Fotos de pessoas: se houver imagem com rosto em `data/`, não versionar.

---

## 8. Como trabalhar neste repositório

**Ao organizar a pasta local:**
1. Fazer inventário completo antes de mover qualquer coisa (`git status`, listagem com tamanhos).
2. Propor o mapeamento origem → destino e **aguardar confirmação** antes de mover ou apagar.
3. Usar `git mv` para arquivos já versionados (preserva o histórico).
4. Validar cada notebook após mover: `python -c "import nbformat; nbformat.validate(nbformat.read('arquivo.ipynb', 4))"`.
5. Conferir que nenhum arquivo passa de 50 MB e que nada da seção 7 entrou em stage (`git status`, `git diff --cached --stat`).
6. Procurar segredos antes do commit: `git grep -nIiE "(token|api[_-]?key|password|secret)"`.

**Ao editar notebooks:**
- Usar a ferramenta de edição de notebooks ou `nbformat`. Nunca editar o JSON como texto livre.
- **Não apagar nem reescrever células originais ou respostas já entregues.** Adicionar células novas.
- **Não reexecutar notebooks sem pedido explícito.** Eles foram executados no Colab (com Drive, `google.colab`, caminhos `/content/...` e downloads da internet). Reexecutar localmente pode falhar ou apagar as saídas, que são a evidência da entrega.

**Ao escrever texto:**
- Português do Brasil, **primeira pessoa do singular** (sem dupla).
- Respostas curtas e diretas: 3–5 linhas por pergunta, como o professor pede.
- Usar os números da execução real; nunca inventar resultado. Se faltar dado, deixar `[__]`.
- Toda alteração experimental traz O QUE / COMO / POR QUE e a relação com cibersegurança.

**Git (lições aprendidas):**
- O repositório recebe commits do Colab ("Salvar uma cópia no GitHub") e do terminal. Sempre `git pull --rebase` antes de commitar. Configurar `git config pull.rebase true`.
- Nunca `git push --force`.
- Mensagem de commit com aspas retas: `git commit -m "mensagem"`. O acento agudo `´` do teclado ABNT2 quebra o comando.
- Conferir `git remote -v`: o nome do repositório no remoto aparece terminando com ponto (`...ia-ciberseguranca.`). Funciona, mas vale saber ao montar links.

---

## 9. Próximos passos (Etapa 2 — fecha na AV2, 30/11 pelo plano)

Datas do plano de ensino, sujeitas a ajuste como aconteceu na Etapa 1.

| Aula | Tema | Entregável |
|---|---|---|
| 9 | Introdução ao adversarial ML (taxonomia, NIST AI 100-2, MITRE ATLAS) | Mapa das superfícies de ataque do próprio projeto |
| 10 | Ataques de evasão (FGSM, PGD, transferabilidade) | Relatório do ataque de evasão, com a queda de desempenho medida — alvo: `modelo_aula2.h5` |
| 11 | Envenenamento de dados (label flipping, backdoors, sanitização) | Experimento de envenenamento com a defesa aplicada |
| 12 | Segurança e governança de IA (NIST AI RMF: Govern, Map, Measure, Manage) | Matriz de risco do projeto estruturada pelas funções do AI RMF |
| 13 | LLMs na segurança (triagem, análise de logs, regras) | Protótipo de apoio à triagem com modelo de linguagem |
| 14 | Segurança de LLMs (OWASP Top 10 for LLM, prompt injection, jailbreak) | Notebook da Etapa 2 com relatório de ataques e defesas |
| AV2 | Apresentação e arguição dos notebooks da Etapa 2 | — |

**Bibliografia básica:** NIST AI RMF 1.0 · Chio & Freeman, *Machine Learning and Security* · Joseph et al., *Adversarial Machine Learning* · NIST AI 100-2. **Complementar:** MITRE ATLAS · OWASP Top 10 for LLM Applications · MITRE ATT&CK · Biggio & Roli (2018) · Géron, *Hands-on ML*.
