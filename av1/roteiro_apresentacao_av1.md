# Roteiro da apresentação — AV1 (5 minutos)

**Notebook:** `aula5_modelagem_detector_malware_uci_splunk.ipynb` · **Alterações:** (1) Dados e Features · (2) Avaliação (split por procedência)

**Mensagem central (se só der tempo de dizer uma frase):**
> "O modelo parecia ótimo, mas boa parte do acerto vinha de reconhecer *de onde* o arquivo veio, não *se* ele é malicioso. Quando separo as fontes de malware entre treino e teste, dá para medir quanto disso era vazamento."

## Antes de começar (checklist)

- [ ] Notebook aberto e **já executado no Colab** (não rodar ao vivo: o download do UCI depende de internet).
- [ ] Abas/posições prontas: célula de auditoria (após a seção 2), seção 11 (importâncias), seção 12 (ablação), seção "AV1 — Experimentos" (tabela da Alt 1 e comparação por procedência da Alt 2).
- [ ] Relatório `relatorio_av1.md` aberto como plano B, com a tabela antes × depois.

---

## Bloco 1 — Problema e base (0:00 → 0:45)

**Tela:** topo do notebook e a tabela da seção 2 (origem × rótulo).

**Fala:**
"Escolhi o notebook 5, que treina um Random Forest para decidir se um executável Windows é malware **sem executá-lo**, só com números tirados do arquivo PE: tamanhos, endereços, entropia e funções importadas.
A base é a UCI 541: 6.248 arquivos, 90,5% malware. Um detalhe que guiou todo o meu trabalho: cada CSV tem um rótulo só. Todo benigno veio de *Program Files* do Windows 7/8, e todo malware veio do VxHeaven ou do VirusTotal. Ou seja, **origem e rótulo são praticamente a mesma coisa**."

## Bloco 2 — O achado que motivou as alterações (0:45 → 1:30)

**Tela:** célula de auditoria → seção 11 (importâncias) → seção 12 (ablação).

**Fala:**
"Antes de alterar, auditei as features. Das 28, 18 — entropia, imports e `number_of_sections` — têm **só 2 valores diferentes de zero em cerca de 112 mil células**. Todo PE tem pelo menos uma seção, então isso é dado ausente, não medição.
As importâncias confirmam: entropia e imports valem zero. E na ablação essas famílias dão PR-AUC 0,905, que é exatamente a proporção de malware, ou seja, um chute.
Conclusão: o modelo decide **só com tamanho e layout do arquivo**. Isso me levou a duas perguntas, que viraram minhas duas alterações."

## Bloco 3 — Regras do experimento (1:30 → 1:50)

**Tela:** início da seção "AV1 — Experimentos" (funções `escolher_limiar` e `resumo`).

**Fala:**
"Não editei nenhuma célula original; elas são o 'antes'. Criei uma seção nova no fim. Mantive a mesma seed 42 e os mesmos hiperparâmetros. Em cada experimento muda uma coisa só. Na Alteração 1 o limiar é escolhido **só na validação**; na Alteração 2, que mexe no split, comparo por PR-AUC e ROC-AUC, que não dependem de limiar."

## Bloco 4 — Alteração 1: Dados e Features (1:50 → 3:10)

**Tela:** célula da Alteração 1 e `tabela_av1` (linhas Original, 1a, 1b).

**O QUE:**
"Fiz duas variantes. Na **1a** tirei as 18 features zeradas e sobraram 10. Na **1b** tirei também 5 features de layout que dependem do compilador e da época — `image_base`, os dois alinhamentos e os tamanhos de cabeçalho — e sobraram 5."

**COMO:**
"Retreinei o mesmo Random Forest só trocando a lista de colunas. Cada variante escolheu o próprio limiar na validação com a regra da aula: 0,40 na 1a e 0,41 na 1b."

**RESULTADO:**
"A 1a ficou igual ao original: recall de benigno 0,832 nos dois e PR-AUC 0,997 nos dois. Confirma que as zeradas eram ruído.
Na 1b, o recall de benigno caiu de **0,832 para 0,588**, os falsos positivos foram de **20 para 49** e o ROC-AUC caiu de 0,973 para 0,944. O recall de malware ficou estável em 0,959."

**POR QUE (ligação com cibersegurança):**
"A pergunta era: o modelo detecta malware ou detecta de onde o arquivo veio? Sem as marcas de layout, ele deixa de reconhecer a 'cara' de software instalado do Windows 7/8. Então parte do desempenho era **viés de origem**. E tem um segundo problema: tamanho, alinhamento e endereço são **baratos de alterar** com padding ou recompilação, o que abre espaço para **evasão**."

## Bloco 5 — Alteração 2: Avaliação por procedência (3:10 → 4:25)

**Tela:** célula da Alteração 2 e a tabela `comparacao_proc` (linha original + duas direções por fonte).

**O QUE:**
"Em vez de split aleatório, separei o malware **por procedência**: treino o mesmo Random Forest com o malware de uma fonte e testo no da outra. O benigno, que vem de uma fonte só, divido ao meio. Faço as duas direções — treino em VxHeaven e teste em VirusTotal, e o inverso."

**COMO:**
"Recuperei a coluna `source_dataset` de cada amostra — o `df` preserva o índice que sobrevive ao split — e montei treino e teste por fonte, com a mesma seed e os mesmos hiperparâmetros. Como aqui não tenho validação separada para calibrar limiar, comparo **PR-AUC e ROC-AUC**, que não dependem de limiar, e mostro o recall a 0,50 só como ilustração."

**RESULTADO:**
"No split aleatório o modelo tem PR-AUC 0,997, ROC-AUC 0,973 e recall de malware 0,963. Cruzando as fontes, o PR-AUC cai pouco — 0,982 e 0,984 — porque o teste ainda é 90% malware. Mas o **ROC-AUC cai para 0,871 e 0,901**, e o **recall de malware a 0,50 desaba**: treinando no VxHeaven e testando no VirusTotal, vai de 0,963 para **0,606** — o modelo deixa passar 39% do malware. No sentido inverso fica em 0,867."

**POR QUE:**
"O split aleatório deixa variantes próximas da mesma coleção caírem em treino e teste, o que infla a métrica — é vazamento. Separar por procedência responde à pergunta que importa para cibersegurança: o detector generaliza para malware de outra fonte, ou só reconhece a assinatura da coleção onde treinou? É o teste de estresse direto do viés de origem da Alteração 1."

## Bloco 6 — Limitações e fechamento (4:25 → 5:00)

**Tela:** tabela antes × depois completa.

**Fala:**
"Limitações: o viés de origem continua (o benigno é de uma fonte só), pode haver vazamento residual por família dentro de cada fonte, a base é antiga e há poucos benignos.
Fechando: o número alto do notebook original é otimista. Para produção, eu usaria uma base com divisão temporal, como EMBER ou BODMAS, split por família de malware, features de comportamento que não estejam vazias e um custo de erro definido junto com o SOC."

---

## Cola de números (teste)

| Experimento | feat. | limiar | recall malware | recall benigno | FN | FP | PR-AUC | ROC-AUC |
|---|---|---|---|---|---|---|---|---|
| Original (split aleatório) | 28 | 0,37 | 0,963 | 0,832 | 42 | 20 | 0,997 | 0,973 |
| 1a: sem zeradas | 10 | 0,40 | 0,958 | 0,832 | 48 | 20 | 0,997 | 0,974 |
| 1b: sem zeradas e origem | 5 | 0,41 | 0,959 | 0,588 | 46 | 49 | 0,994 | 0,944 |
| 2: treino VxHeaven / teste VirusTotal | 28 | 0,50 | 0,606 | 0,943 | 1163 | 17 | 0,982 | 0,871 |
| 2: treino VirusTotal / teste VxHeaven | 28 | 0,50 | 0,867 | 0,799 | 360 | 60 | 0,984 | 0,901 |

---

## Perguntas prováveis da arguição

**1. Por que a 1b piorou o benigno e não o malware?**
Porque as features que removi descrevem o padrão dos programas instalados (alinhamento, base de imagem, cabeçalho). Sem elas, o modelo deixa de reconhecer "cara de software legítimo". Como malware é 90% da base, o recall de malware continua alto quase por padrão.

**2. Por que no split por procedência você não calibrou o limiar?**
Porque ali o objetivo é medir **generalização**, não escolher um ponto de operação. PR-AUC e ROC-AUC independem de limiar, então são a comparação justa. Mostro o recall a 0,50 só como ilustração.

**3. Você usou o teste para escolher alguma coisa?**
Não. Na Alteração 1 os limiares (0,40 e 0,41) saíram só de `y_valid`/`proba_valid`. Na Alteração 2 não calibro limiar: uso 0,50 fixo e comparo PR-AUC/ROC-AUC. O teste aparece só na função `resumo`, depois de tudo decidido.

**4. Por que dividir por fonte e não por família de malware?**
O ideal seria por família ou por hash, mas a base não traz a família pronta. Ela traz a **fonte** (VxHeaven e VirusTotal), que já é um proxy forte de procedência. Dividir por fonte é o corte mais honesto disponível sem rotular família manualmente.

**5. A 1a teve 48 FN contra 42 do original. Ela piorou?**
Não de forma relevante. São 6 malwares em 1.131 (cerca de 0,5 ponto de recall), com limiares diferentes (0,40 contra 0,37). Está dentro do ruído; PR-AUC e recall de benigno ficaram idênticos.

**6. Por que PR-AUC 0,905 significa chute?**
Na curva precisão × recall, um modelo aleatório tem precisão igual à proporção de positivos. Como 90,5% da base é malware, PR-AUC 0,905 é exatamente o valor de quem não aprendeu nada.

**7. Por que você olha o recall de benigno e não a acurácia?**
Com 90,5% de malware, o Dummy que diz sempre "malware" já tem cerca de 90% de acurácia e bloqueia todos os benignos. O recall de benigno mostra se o modelo realmente separa as classes.

**8. No Colab o limiar da aula deu 0,46, e você mostra 0,37. Por quê?**
Executei localmente com outra versão do scikit-learn, e o Random Forest gera probabilidades um pouco diferentes. Os números mudam levemente, mas as conclusões são as mesmas nas duas execuções.

**9. Qual a relação com evasão?**
O modelo depende de tamanho, endereço e alinhamento. Um atacante altera isso com padding ou recompilação sem mudar o comportamento malicioso. Um detector que depende dessas features é fácil de enganar.

**10. Se fosse continuar, o que faria?**
Base com divisão temporal (EMBER/BODMAS), split por família de malware, features de comportamento preenchidas (entropia, imports) e limiar por custo com teto de FP definido com o SOC.
