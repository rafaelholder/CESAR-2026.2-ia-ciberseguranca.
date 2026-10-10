# AV1 — IA para Cibersegurança · Experimento no Notebook 5 (malware)

> Arquivo de contexto para o Claude Code. Leia inteiro antes de editar qualquer coisa.
> Idioma de tudo que for entregue: **português do Brasil**, na **primeira pessoa do singular** (trabalho individual, sem dupla). Identificadores de código em inglês ou português, como já estão no notebook.

---

## 1. Contexto

- **Disciplina:** IA para Cibersegurança — CESAR School, 2026.2 — Prof. Raphael Crespo Pereira (rcp@cesar.school)
- **Aluno:** Rafael Holder (individual, sem dupla — confirmar com o professor no tira-dúvidas de 28/09)
- **Repositório:** `rafaelholder/CESAR-2026.2-ia-ciberseguranca`
- **Notebook-alvo:** `aula5_modelagem_detector_malware_uci_splunk.ipynb`
- **Data da AV1:** 05/10/2026 (apresentação oral + arguição). Tira-dúvidas: 28/09/2026.

### Enunciado da AV1

Escolher um notebook (aulas 3 a 6) e fazer **pelo menos duas alterações**, comparar antes e depois e explicar as decisões.

Critérios avaliados:
- **O QUE FEZ:** identificação precisa das duas alterações e do resultado observado.
- **COMO FEZ:** explicação das células, etapas, valores e comparações utilizadas.
- **POR QUE FEZ:** justificativa da escolha e relação com o problema de cibersegurança.

A nota **não depende** de a alteração melhorar o sistema.

Categorias permitidas: Dados e Features · Algoritmo · Parâmetros e Decisão · Avaliação.

**Entregável:** notebook executável + relatório (máx. 2 páginas) + apresentação oral.

---

## 2. O que o notebook faz hoje (estado "antes")

Base: **UCI Malware Static and Dynamic Features VxHeaven and VirusTotal** (id 541, CC BY 4.0). Três CSVs: `staDynBenignLab` (595 benignos), `staDynVt2955Lab` (2.955 malwares), `staDynVxHeaven2698Lab` (2.698 malwares). Total 6.248 amostras, 1.085 colunas comuns, **90,5% malware**.

Pipeline: 28 features em três famílias (`features_estrutura` com 11, `features_entropia` com 7, `features_imports` com 10) → split estratificado 60/20/20 (`X_treino`, `X_valid`, `X_teste`) → Dummy → Random Forest (`modelo`, 250 árvores, `min_samples_leaf=2`, `class_weight='balanced'`, `RANDOM_SEED=42`) → limiar escolhido na validação (`LIMIAR`, regra: recall ≥ 95% com maior precisão) → teste único → importâncias → ablação por família (CV 5 folds) → export para Splunk.

### Resultados da execução original (para citar no relatório)

**Validação, limiar 0,50:** Benigno precision 0,61 / recall 0,83 · Malware 0,98 / 0,94 · ROC-AUC 0,960 · PR-AUC 0,995.

**Limiar escolhido na validação:** 0,46.

**Teste final (limiar 0,46):** Benigno precision 0,72 / recall 0,82 (119 amostras) · Malware precision 0,98 / recall 0,97 (1.131 amostras) · acurácia 0,95 · ROC-AUC 0,972 · PR-AUC 0,997.

**Importâncias (top):** AddressOfEntryPoint 0,188 · size_of_headers 0,181 · filesize 0,170 · size_init_data 0,146 · size_code 0,117 · file_alignment 0,066 · size_uninit_data 0,055 · image_base 0,045 · section_alignment 0,030 · size_of_optional_header 0,003 · **todas as features de entropia e imports = 0,000**.

**Ablação (CV 5 folds em treino+validação):**

| Família | recall | F1 | PR-AUC |
|---|---|---|---|
| Estrutura | 0,966 | 0,970 | 0,997 |
| Entropia/seções | 0,600 | 0,570 | 0,905 |
| Imports/capacidades | 0,600 | 0,570 | 0,905 |
| Todas | 0,951 | 0,964 | 0,996 |

### Achado central (já verificado)

As 18 colunas de entropia, imports e `number_of_sections` são `int64` no CSV, a conversão com `errors='coerce'` não gerou nenhum NaN, e **apenas 2 das ~112 mil células são diferentes de zero**. Todo PE tem pelo menos uma seção, então esses zeros são **dado ausente na base original**, não medição. Consequências:

- PR-AUC 0,905 nessas famílias = proporção de malware = desempenho de um chute;
- o modelo decide **só com features de estrutura** (tamanhos, endereços, alinhamentos), justamente as mais sujeitas ao **viés de origem** (benignos de *Program Files* do Windows 7/8; malwares de VxHeaven e VirusTotal 2018);
- essas features são baratas de alterar por um atacante (padding, recompilação), ligando o resultado à **evasão** (aula 10).

Já existe no notebook (inserida pelo aluno, após a seção 2) uma célula de auditoria que imprime `Tipos`, `Viraram NaN na conversão` e `Valores diferentes de 0`, seguida de uma célula de texto "Auditoria das features de entropia, seções e imports". **Não remover.**

---

## 3. Tarefa — as duas alterações

Criar uma seção nova **no final do notebook, imediatamente antes da célula "## Entregável da aula"**, com o título `## AV1 — Experimentos (antes × depois)`.

**Regras invioláveis:**
- **Não editar nem apagar** nenhuma célula original. O notebook original é o "antes".
- Todo limiar é escolhido **somente na validação**. O teste só reporta. Nenhuma decisão olha o teste.
- Mesmo `RANDOM_SEED`, mesmos hiperparâmetros do RF original, mesmo split. A única coisa que muda em cada experimento é a variável sob teste.
- Cada célula de código vem precedida de uma célula de texto curta (2 a 4 linhas) dizendo o que ela faz e por quê.

### Alteração 1 — Dados e Features

Remover as features zeradas e, numa segunda variante, também as que podem denunciar a origem da coleta.

- **1a:** sem `features_zeradas` = `features_entropia + features_imports + ['number_of_sections']`
- **1b:** sem `features_zeradas` e sem `features_origem` = `['image_base', 'file_alignment', 'section_alignment', 'size_of_headers', 'size_of_optional_header']`

Pergunta: o modelo detecta malware ou detecta de onde o arquivo veio? A expectativa é que 1a ≈ original (a ablação já indicava isso) e que 1b caia. Se cair muito, o modelo dependia da assinatura da fonte.

### Alteração 2 — Avaliação (split por procedência)

Trocar o split aleatório por um **split por procedência do malware**: treinar o mesmo Random Forest com o malware de uma fonte (`staDynVxHeaven2698Lab`) e testar no da outra (`staDynVt2955Lab`), e vice-versa; o benigno (`staDynBenignLab`, fonte única) é dividido ao meio. Comparar PR-AUC/ROC-AUC com o split aleatório original. Sem validação separada aqui, não se calibra limiar: usa-se 0,50 fixo só para ilustrar o recall. É o teste de estresse direto do viés de origem da Alteração 1.

### Código das células (usar exatamente esta lógica)

**Célula 1 — preparação e linha de base**

```python
# === AV1 — Experimentos (antes × depois) ===
LIMS = np.arange(0.05, 0.96, 0.01)

def escolher_limiar(y, p):
    """Mesma regra da seção 9: recall >= 95% com maior precisão; senão, maior F1."""
    tab = pd.DataFrame([{'limiar': l,
                         'precisao': precision_score(y, (p >= l).astype(int), zero_division=0),
                         'recall': recall_score(y, (p >= l).astype(int)),
                         'f1': f1_score(y, (p >= l).astype(int))} for l in LIMS])
    cand = tab[tab.recall >= 0.95]
    if len(cand):
        return float(cand.sort_values(['precisao', 'limiar'], ascending=[False, False]).iloc[0].limiar)
    return float(tab.loc[tab.f1.idxmax(), 'limiar'])

def resumo(nome, y, p, limiar, n_feat):
    tn, fp, fn, tp = confusion_matrix(y, (p >= limiar).astype(int)).ravel()
    return {'experimento': nome, 'n_features': n_feat, 'limiar': round(limiar, 2),
            'recall_malware': round(tp / (tp + fn), 3), 'recall_benigno': round(tn / (tn + fp), 3),
            'FN (malware liberado)': fn, 'FP (benigno bloqueado)': fp,
            'PR-AUC': round(average_precision_score(y, p), 3), 'ROC-AUC': round(roc_auc_score(y, p), 3)}

proba_teste = modelo.predict_proba(X_teste)[:, 1]
resultados_av1 = [resumo('Original (28 features)', y_teste, proba_teste, LIMIAR, len(features))]
pd.DataFrame(resultados_av1)
```

**Célula 2 — Alteração 1**

```python
features_zeradas = features_entropia + features_imports + ['number_of_sections']
features_origem  = ['image_base', 'file_alignment', 'section_alignment',
                    'size_of_headers', 'size_of_optional_header']

variantes = {
    'Alt. 1a: sem zeradas':              [c for c in features if c not in features_zeradas],
    'Alt. 1b: sem zeradas e sem origem': [c for c in features if c not in features_zeradas + features_origem],
}

for nome, cols in variantes.items():
    m = RandomForestClassifier(n_estimators=250, min_samples_leaf=2, class_weight='balanced',
                               n_jobs=-1, random_state=RANDOM_SEED).fit(X_treino[cols], y_treino)
    lim = escolher_limiar(y_valid, m.predict_proba(X_valid[cols])[:, 1])   # limiar só na validação
    resultados_av1.append(resumo(nome, y_teste, m.predict_proba(X_teste[cols])[:, 1], lim, len(cols)))
    print(nome, '->', cols)

pd.DataFrame(resultados_av1)
```

**Célula 3 — Alteração 2 (split por procedência)**

```python
# X preserva o índice de df (concat com ignore_index), então recupero a fonte de cada amostra:
fonte = df.loc[X.index, 'source_dataset']
BENIGNO    = 'staDynBenignLab'
FONTES_MAL = {'VirusTotal': 'staDynVt2955Lab', 'VxHeaven': 'staDynVxHeaven2698Lab'}

def split_por_fonte(tag_teste):
    """Benigno dividido 50/50; malware da fonte-alvo vai p/ teste e o da outra fonte p/ treino."""
    ben = X.index[fonte == BENIGNO]
    ben_tr, ben_te = train_test_split(ben, test_size=0.5, random_state=RANDOM_SEED)
    tr = X.index[(fonte != BENIGNO) & (fonte != tag_teste)].union(ben_tr)
    te = X.index[fonte == tag_teste].union(ben_te)
    return tr, te

resultados_proc = []
for nome, tag in FONTES_MAL.items():
    tr, te = split_por_fonte(tag)
    m = RandomForestClassifier(n_estimators=250, min_samples_leaf=2, class_weight='balanced',
                               n_jobs=-1, random_state=RANDOM_SEED).fit(X.loc[tr], y.loc[tr])
    p = m.predict_proba(X.loc[te])[:, 1]
    resultados_proc.append(resumo(f'Malware de teste: {nome} (treino na outra fonte)',
                                  y.loc[te], p, 0.50, len(features)))
    print(f'{nome:>11}: treino {len(tr)}  teste {len(te)}  (malware no teste: {int(y.loc[te].sum())})')

tabela_proc = pd.DataFrame(resultados_proc)
tabela_proc
```

**Célula 4 — comparação (antes × depois)**

```python
cols_cmp = ['experimento', 'PR-AUC', 'ROC-AUC', 'recall_malware', 'recall_benigno',
            'FN (malware liberado)', 'FP (benigno bloqueado)']
comparacao_proc = pd.DataFrame([resultados_av1[0]] + resultados_proc)[cols_cmp]
comparacao_proc
```

**Célula final de texto — "Conclusão da AV1":** deixar um esqueleto com lacunas `[__]` a preencher depois da execução, cobrindo: resultado da 1a vs original, resultado da 1b vs original (e o que isso diz sobre viés de origem), e o split por procedência vs split aleatório (PR-AUC/ROC-AUC das duas direções e o que a queda, ou a ausência dela, indica). Não inventar números.

---

## 4. Execução

O notebook foi escrito para o **Google Colab**. Ao executar localmente:

- A célula de download usa `PASTA = Path('/content/uci_malware_541')` e `ZIP = Path('/content/uci_malware_541.zip')`. Fora do Colab, `/content` não existe e o `mkdir` pode falhar por permissão. **Não alterar a célula original.** Se for executar localmente, criar o diretório antes ou rodar via Colab.
- A célula de export para o Splunk usa `files.download` dentro de `try/except ImportError` e funciona fora do Colab.
- O download do UCI exige internet.

Se houver Python com as dependências e rede, executar e validar com:

```bash
jupyter nbconvert --to notebook --execute --inplace aula5_modelagem_detector_malware_uci_splunk.ipynb
```

Se não for possível executar localmente, **apenas editar** o notebook, garantir que o JSON continua válido (`python -c "import nbformat; nbformat.validate(nbformat.read('aula5_modelagem_detector_malware_uci_splunk.ipynb', 4))"`) e avisar que a execução será feita no Colab.

Para editar o `.ipynb`, usar a ferramenta de edição de notebook do Claude Code ou um script com `nbformat`. Nunca editar o JSON do notebook como texto livre.

---

## 5. Depois da execução — relatório e apresentação

Com a `tabela_av1`, a `comparacao_proc` e as métricas por procedência em mãos:

1. **Preencher** a célula "Conclusão da AV1" com os números reais.
2. **Gerar `relatorio_av1.md`** (máx. 2 páginas, ~900 palavras), com esta estrutura:
   - Contexto em 3 linhas: base, problema, achado das features zeradas.
   - **Alteração 1:** O QUE (features removidas, resultado 1a e 1b) · COMO (células, mesmo split/seed/hiperparâmetros, limiar na validação) · POR QUE (viés de origem, evasão por features baratas).
   - **Alteração 2:** O QUE (split por procedência, PR-AUC/ROC-AUC das duas direções) · COMO (recupera `source_dataset`, treina numa fonte e testa na outra, mesmo seed/hiperparâmetros) · POR QUE (vazamento de fonte e viés de origem).
   - Tabela antes × depois (a própria `tabela_av1`).
   - Limitações: viés de origem, idade da base, split aleatório, 119 benignos no teste (cada FP pesa ~0,8 ponto no recall de benigno).
3. **Gerar `roteiro_apresentacao_av1.md`**: roteiro de 5 minutos com 4 a 6 blocos e as 3 perguntas mais prováveis da arguição, com resposta curta.

Não inventar resultados. Se algo não foi executado, deixar `[__]`.

---

## 6. Git

O repositório recebe commits tanto do Colab ("Salvar uma cópia no GitHub") quanto do terminal. Antes de commitar:

```bash
git pull --rebase
```

Mensagem de commit sugerida: `AV1: experimentos no notebook 5 (features e split por procedência)`. Não versionar CSVs, zips ou `.pkl` (já cobertos pelo `.gitignore`).
