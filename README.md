# IA para Cibersegurança — Projeto da dupla

CESAR School · Tecnológico em Segurança da Informação · 2026.2
Prof. Raphael Crespo Pereira

**Dupla:** `Rafael Holder` 

Projeto do semestre: escolher um problema de segurança, construir um detector com ML (Etapa 1, AV1) e depois atacá-lo e defendê-lo (Etapa 2, AV2).

---

## Estrutura

```
ia-ciberseguranca/
├── data/
│   ├── raw/          # dados brutos baixados (não versionados — ver "Dados")
│   └── processed/    # dados tratados gerados pelos notebooks (não versionados)
├── notebooks/        # um notebook por aula/entregável
├── src/              # funções reutilizadas entre notebooks
├── models/           # modelos treinados (grandes ficam fora do Git)
├── reports/          # figuras e relatórios
└── requirements.txt  # versões realmente instaladas no Colab
```

## Como reproduzir

1. Abrir o notebook desejado em `notebooks/` pelo link "Open in Colab".
2. Montar o Google Drive quando solicitado. O projeto vive em `MyDrive/ia-ciberseguranca`.
3. Instalar as dependências fixadas:
   ```
   pip install -r requirements.txt
   ```
4. Rodar as células em ordem. Todo experimento chama `set_seed(42)` no início.

## Dados

As bases usadas passam do limite de arquivo do GitHub (100 MB), por isso os CSVs brutos **não** são versionados. O que se versiona é a origem, para que qualquer pessoa obtenha exatamente a mesma base:

| Base | Fonte | Como é obtida |
|---|---|---|
| CIC-IDS2017 | https://www.unb.ca/cic/datasets/ids-2017.html | `gdown` na célula de carregamento do notebook |
| *(base do projeto — definir)* | | |

## Entregas

| Aula | Entregável | Notebook |
|---|---|---|
| 1 | Ambiente configurado, repositório criado, notebook inicial versionado | `notebooks/setup_aula1_ia_ciberseguranca.ipynb` |

## Uso de IA

Uso de assistentes de IA como ferramenta de apoio, com curadoria e análise crítica dos resultados, conforme o plano de ensino e a política de governança de IA do CESAR.
