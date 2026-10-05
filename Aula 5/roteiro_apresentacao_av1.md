# Roteiro da apresentação — AV1 (5 minutos)

## Bloco 1 — Contexto e achado (≈ 45 s)
- Notebook 5: Random Forest detectando malware em executáveis Windows, base UCI 541, 90,5% malware.
- Mostrar a célula de auditoria: das cerca de 112 mil células de entropia/imports, só 2 são diferentes de zero. Isso é dado ausente.
- Mostrar as importâncias: entropia e imports = 0, e o modelo decide só com tamanho e layout do arquivo.

## Bloco 2 — Regras do experimento (≈ 30 s)
- Não editei nenhuma célula original, que serve como o "antes".
- Mantive o mesmo split, a mesma seed (42) e os mesmos hiperparâmetros. Em cada experimento muda só uma coisa.
- O limiar é sempre escolhido na validação, e o teste apenas reporta.

## Bloco 3 — Alteração 1: features (≈ 1 min 30 s)
- 1a, sem as 18 zeradas: resultado igual ao original (recall de benigno 0,832 nos dois, PR-AUC 0,997). Isso prova que eram ruído.
- 1b, sem zeradas e sem `image_base`, alinhamentos e cabeçalhos: o recall de benigno cai de 0,832 para 0,588, os FP vão de 20 para 49 e o ROC-AUC vai de 0,973 para 0,944.
- Mensagem principal: o modelo dependia da "assinatura" de *Program Files* do Windows 7/8. Ele detecta parte da origem, não só malware. Além disso, essas features são baratas de evadir.

## Bloco 4 — Alteração 2: limiar por custo (≈ 1 min 30 s)
- Malware liberado custa 10, benigno bloqueado custa 1. Escolho o limiar de menor custo na validação.
- Mostrar o gráfico: limiar de 0,37 para 0,07.
- No teste: FN de 42 para 4, FP de 20 para 98, custo de 440 para 138. Mas 82% dos benignos ficam bloqueados.
- Sensibilidade: 1:1 → 0,24; 2:1 → 0,14; 5:1 → 0,09; 10:1 e 20:1 → 0,07. A decisão depende de um custo que vem do negócio.

## Bloco 5 — Limitações e fechamento (≈ 45 s)
- Viés de origem, base antiga, split aleatório, só 119 benignos no teste (cada FP vale cerca de 0,84 ponto).
- Conclusão: o número alto do notebook original é otimista. Para produção, eu usaria uma base temporal (EMBER/BODMAS), features de comportamento que não estejam zeradas e um custo definido com o SOC.

---

## Perguntas prováveis da arguição

**1. Por que a 1b piorou o benigno e não o malware?**
Porque as features que removi descrevem o padrão dos programas instalados (alinhamento, base de imagem, cabeçalho). Sem elas, o modelo deixa de reconhecer "cara de software legítimo". Como malware é 90% da base, o recall de malware continua alto quase por padrão.

**2. O limiar por custo não é pior, já que bloqueia 82% dos benignos?**
Pelo critério que eu defini (10:1) ele é melhor: o custo cai de 440 para 138. Mas o experimento mostra justamente que minimizar só o custo esconde o volume de FP que o SOC vai receber. Na prática eu combinaria o custo com um teto de FP, e a razão entre os custos teria que ser definida com a operação.

**3. Você usou o teste para escolher alguma coisa?**
Não. Todos os limiares (0,40, 0,41 e 0,07) e a sensibilidade foram calculados só com `y_valid`/`proba_valid`. O teste aparece apenas na função `resumo`, depois que tudo já estava decidido.
