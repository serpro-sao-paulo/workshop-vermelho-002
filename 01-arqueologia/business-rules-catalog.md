<!-- markdownlint-disable MD013 MD025 MD026 MD028 MD029 MD034 MD040 MD051 MD060 -->

# Catálogo de Regras de Negócio — SIFAP Legado

![ESTÁGIO 01 Arqueologia](https://img.shields.io/badge/ESTÁGIO-01%20Arqueologia-F25022?style=for-the-badge) ![TIPO Worksheet](https://img.shields.io/badge/TIPO-Worksheet-1A1A1A?style=for-the-badge) ![PREENCHA Durante S1](https://img.shields.io/badge/PREENCHA-Durante%20S1-737373?style=for-the-badge)

> 🗺 **Você está aqui:** [Kit PT-BR](../README.md) → [Estágio 1](README.md) → **business-rules-catalog**

> **Para quem é isto?** Este é um **artefato preenchido pelo time** durante o Estágio 1 (Arqueologia).
>
> **O que você terá ao final do estágio:**
>
> 1. Este documento totalmente preenchido com os dados reais do legado SIFAP
> 2. Rastreabilidade para `01-arqueologia/legado-sifap/` (programas `.NSN` e DDMs)
> 3. Base de evidência usada nas EARS do Estágio 2 (`source_legacy:`)
>
> 📘 **Guia passo a passo:** [`GUIDE.md`](GUIDE.md).

> Registre aqui todas as regras de negócio extraídas do código Natural/Adabas.
> Cada regra precisa ter rastreabilidade até o código-fonte.
>
> **REGRA DURA:** linhas com `Programa Fonte` vazio são **inválidas** e não contam para o gate do Estágio 2. Use o formato `01-arqueologia/legado-sifap/natural-programs/ARQUIVO.NSN#L<inicio>-L<fim>` sempre que possível. Mínimo aceito: nome do arquivo .NSN.

## Como pensar em "regra de negócio"

O que conta:

- Um `IF` que decide algo no domínio (ex.: _"se a UF é do Nordeste e o programa é Seca, valor base × 1.2"_)
- Uma constante numérica sem explicação (ex.: `0.075` num cálculo de imposto)
- Uma transição de status com regra (ex.: _"só de A para S, nunca de I para A"_)
- Um tratamento especial para um caso (ex.: _"se o CPF começa com 999, é teste"_)

O que NÃO conta: paginação de relatório, formatação de saída, manipulação de cursor Adabas, abertura de arquivo. Ignore esses detalhes de implementação.

## Níveis de Risco

| Nível       | Descrição                                                     |
| ----------- | ------------------------------------------------------------- |
| **CRÍTICO** | Regra financeira ou de segurança — erro causa prejuízo direto |
| **ALTO**    | Regra de negócio central — afeta fluxo principal              |
| **MÉDIO**   | Regra de validação ou formatação — afeta qualidade dos dados  |
| **BAIXO**   | Regra de apresentação ou conveniência — impacto limitado      |

## Regras Encontradas

| ID     | Regra de Negócio | Programa Fonte | Campos DDM | Nível de Risco | Notas |
| ------ | ---------------- | -------------- | ---------- | -------------- | ----- |
| BR-001 |                  |                |            |                |       |
| BR-002 |                  |                |            |                |       |
| BR-003 |                  |                |            |                |       |
| BR-004 |                  |                |            |                |       |
| BR-005 |                  |                |            |                |       |
| BR-006 |                  |                |            |                |       |
| BR-007 |                  |                |            |                |       |
| BR-008 |                  |                |            |                |       |
| BR-009 |                  |                |            |                |       |
| BR-010 |                  |                |            |                |       |
| BR-011 |                  |                |            |                |       |
| BR-012 |                  |                |            |                |       |
| BR-013 |                  |                |            |                |       |
| BR-014 |                  |                |            |                |       |
| BR-015 |                  |                |            |                |       |

> Adicione mais linhas conforme necessário. Lembre-se: existem **10 regras escondidas** no código!

## Exemplo de linha bem preenchida

| ID     | Regra de Negócio                                                                        | Programa Fonte                                                        | Campos DDM                                                               | Nível de Risco | Notas                                      |
| ------ | --------------------------------------------------------------------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------ | -------------- | ------------------------------------------ |
| BR-013 | Desconto total não pode exceder 30% do valor bruto, exceto descontos judiciais (tipo J) | `01-arqueologia/legado-sifap/natural-programs/CALCDSCT.NSN#L142-L148` | `PAGAMENTO.VLR-BRUTO`, `PAGAMENTO.VLR-TOTAL-DSCT`, `PAGAMENTO.TIPO-DSCT` | CRÍTICO        | Regra financeira. Tipo 'J' = exceção legal |

## Regras por Categoria

### Cálculos Financeiros

<!-- Liste aqui as regras relacionadas a cálculos de valores, benefícios, etc. -->

### Validações de Status

<!-- Liste aqui as regras de transição de status (A, S, C, I, D) -->

### Regras de Autorização

<!-- Liste aqui as regras de quem pode fazer o quê -->

### Regras de Negócio Temporais

<!-- Liste aqui regras com prazos, datas-limite, períodos -->

## Resumo Estatístico

- Total de regras encontradas: **86** (extração bloco a bloco dos 15 programas `.NSN`)
- Regras críticas: **24** (cálculo financeiro, descontos, status de pagamento)
- Regras com duplicação: **8** (fator regional/familiar/renda/idade replicados em `CALCBENF` e `BATCHPGT`; validação CPF replicada em `CADBENEF`/`VALBENEF`/`VALDOCS`)
- Regras sem documentação (escondidas/mistério): **17** (marcadas com `<!-- mystery: ... -->` abaixo)

> Detalhamento bloco a bloco por programa nas seções `## Regras de <ARQUIVO>` a seguir. Classificação: **Confirmada** (corresponde a `REGRAS-NEGOCIO-2012.md`), **Inferida** (somente código) ou **Mistério** (lógica/origem pouco clara).

---

## Regras de BATCHPGT.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/BATCHPGT.NSN`. Cross-ref: `legacy-docs/REGRAS-NEGOCIO-2012.md` §5.

| #   | Declaração da Regra                                                                                                                   | Candidato EARS | Fonte                                                                 | Classificação | Notas                                                                                                                                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------- | -------------- | --------------------------------------------------------------------- | ------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | A competência de processamento é derivada da data do sistema (`#DT-HOJE`), no formato AAAAMM (ano×100 + mês).                         | Ubiquitous     | `01-arqueologia/legado-sifap/natural-programs/BATCHPGT.NSN#L108-L113` | Inferida      | Sem competência informada manualmente; sempre o mês corrente.                                                                                |
| 2   | Quando o CPF lido for igual ao CPF anterior, o sistema deve ignorar o registro (evitar duplicata consecutiva).                        | Unwanted       | `BATCHPGT.NSN#L197-L201`                                              | Inferida      | Dedup só funciona porque o READ é ordenado por CPF. Frágil.                                                                                  |
| 3   | Se o beneficiário não estiver com status `'A'` (ativo), o sistema deve ignorá-lo no processamento da folha.                           | Unwanted       | `BATCHPGT.NSN#L205-L208`                                              | Confirmada    | RN §5.1: "Todos os beneficiários ativos (BN-CD-SIT='A') são processados".                                                                    |
| 4   | Se já existir pagamento do beneficiário na competência corrente, o sistema deve ignorá-lo (idempotência).                             | Unwanted       | `BATCHPGT.NSN#L211-L221`                                              | Inferida      | Não documentado; protege contra reprocessamento duplo.                                                                                       |
| 5   | Se o programa social do beneficiário não for encontrado, o sistema deve registrar erro e não gerar pagamento.                         | Unwanted       | `BATCHPGT.NSN#L224-L240`                                              | Inferida      | RN §5.2 cita tratamento de erros, mas não este caso específico.                                                                              |
| 6   | Se o programa social não estiver com status `'A'`, o sistema deve ignorar o beneficiário.                                             | Unwanted       | `BATCHPGT.NSN#L241-L244`                                              | Inferida      | RN-003 exige vínculo a programa ativo (cadastro), mas não trata no batch.                                                                    |
| 7   | Quando a região estiver entre 1 e 25, aplica-se o fator regional da tabela; caso contrário, fator 1.0000.                             | Event-driven   | `BATCHPGT.NSN#L255-L259`                                              | Mistério      | <!-- mystery: 27 fatores regionais magic numbers (1.05 a 1.40), sem documentação de origem; tabela duplicada de CALCBENF -->                 |
| 8   | Quando há dependentes, o fator familiar cresce em faixas: ≤2 → 1.00+0.05/dep; 3-4 → 1.10+0.03/(dep-2); ≥5 → 1.16+0.02/(dep-4).        | Event-driven   | `BATCHPGT.NSN#L262-L275`                                              | Mistério      | <!-- mystery: código aceita >4 dependentes mas RN-004 (doc) limita a 3; conflito de regra e magic numbers sem fonte -->                      |
| 9   | A faixa de renda aplicável é a primeira cujo teto seja ≥ renda familiar (300/600/1000/1500/9999.99 → fator 1.00/0.85/0.70/0.55/0.40). | Event-driven   | `BATCHPGT.NSN#L153-L163` e `#L368-L376`                               | Confirmada    | RN-018: primeira faixa com limite superior ≥ renda declarada. Fatores não documentados.                                                      |
| 10  | O fator idade é 1.15 (≥65), 1.10 (≥60), 1.05 (<18) ou 1.00 (demais).                                                                  | Event-driven   | `BATCHPGT.NSN#L281-L293`                                              | Mistério      | <!-- mystery: magic numbers de fator etário sem documentação; benefício maior para <18 que para faixa 18-59 é contraintuitivo -->            |
| 11  | O valor bruto = base × fator_reg × fator_fam × fator_renda × fator_idade × (1 + fator_reajuste).                                      | Ubiquitous     | `BATCHPGT.NSN#L296-L305`                                              | Mistério      | <!-- mystery: fórmula MULTIPLICATIVA conflita com RN-013 (doc) que descreve fórmula ADITIVA: base + acrescimo*dependentes -->                |
| 12  | O valor é truncado para 2 casas decimais (não arredondado).                                                                           | Ubiquitous     | `BATCHPGT.NSN#L300-L304`                                              | Confirmada    | RN-014: truncamento, ex. 125,567 → 125,56.                                                                                                   |
| 13  | Em dezembro (mês 12), gera-se 13º (base×fator_reg×fator_idade) e, para programa tipo `'A'`, abono natalino de 15% do benefício.       | Event-driven   | `BATCHPGT.NSN#L312-L326`                                              | Confirmada    | RN §2.3/§6 citam 13º e abono natalino como pendentes de documentação; código confirma.                                                       |
| 14  | Se o valor bruto > 500,00, aplica-se desconto simplificado de 3% no batch.                                                            | Event-driven   | `BATCHPGT.NSN#L330-L335`                                              | Mistério      | <!-- mystery: BATCHPGT calcula desconto inline 3%, divergente do CALCDSCT (tabela progressiva + teto 30%); qual prevalece? -->               |
| 15  | Se o valor líquido resultar negativo, o sistema deve zerá-lo.                                                                         | Unwanted       | `BATCHPGT.NSN#L339-L342`                                              | Inferida      | Proteção contra líquido negativo; não documentado.                                                                                           |
| 16  | Todo pagamento gerado é gravado com status `'G'` (gerado).                                                                            | Event-driven   | `BATCHPGT.NSN#L344-L356`                                              | Mistério      | <!-- mystery: código grava status 'G', mas RN §5.1 (doc) afirma status 'P' (pendente). Divergência de máquina de estados -->                 |
| 17  | A leitura dos beneficiários ocorre em ordem ascendente de CPF, e sistemas downstream dependem dessa ordenação.                        | Ubiquitous     | `BATCHPGT.NSN#L182-L190`                                              | Mistério      | <!-- mystery: comentário diz "ordem por CPF / downstream depende"; RN §5.1 (doc) diz ordenação alfabética por NOME. Contradição de fonte --> |

## Regras de BATCHCON.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/BATCHCON.NSN`. Cross-ref: RN §6 (conciliação marcada como pendente).

| #   | Declaração da Regra                                                                                                                              | Candidato EARS | Fonte                    | Classificação             | Notas                                                                                                                                      |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------ | -------------- | ------------------------ | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| 1   | Apenas registros CNAB 240 do tipo `'3'` (detalhe) são processados; os demais são ignorados.                                                      | Unwanted       | `BATCHCON.NSN#L120-L126` | Inferida                  | Layout CNAB 240 BB; não documentado em §6 (pendente).                                                                                      |
| 2   | O valor de retorno bancário (em centavos) é convertido para reais dividindo por 100.                                                             | Ubiquitous     | `BATCHCON.NSN#L143-L147` | Inferida                  | Regra de formatação financeira.                                                                                                            |
| 3   | Se não houver pagamento correspondente (num-pagto + CPF + competência), o registro é marcado como "não encontrado".                              | Unwanted       | `BATCHCON.NSN#L150-L167` | Inferida                  | Conciliação por tripla chave.                                                                                                              |
| 4   | Se a diferença absoluta entre líquido SIFAP e valor do banco for > 0,01, registra-se divergência + auditoria.                                    | Unwanted       | `BATCHCON.NSN#L170-L185` | Inferida                  | Tolerância de 1 centavo (magic).                                                                                                           |
| 5   | Conciliado com código retorno `'00'` → status `'P'` (pago); `'01'` → `'D'` (devolvido); `'02'` → `'E'` (estornado); outros → log "desconhecido". | Event-driven   | `BATCHCON.NSN#L188-L214` | Mistério                  | <!-- mystery: mapeamento de códigos de retorno CNAB para status sem documentação; tolerância 0,01 e códigos 00/01/02 são magic numbers --> |
| 6   | Toda conciliação e toda divergência gera registro de auditoria (ações `'CO'` e `'DV'`).                                                          | Event-driven   | `BATCHCON.NSN#L208-L260` | Confirmada                | RN-010: auditoria automática (genérico); ações CO/DV confirmadas em RELAUDIT.                                                              |
| 7   | A integração com o Banco Real está desativada (banco adquirido pelo Santander em 2007).                                                          | Ubiquitous     | `BATCHCON.NSN#L216-L234` | Confirmada (código morto) | Bloco comentado mantido "para referência histórica"; não é regra ativa.                                                                    |

## Regras de BATCHREL.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/BATCHREL.NSN`.

| #   | Declaração da Regra                                                                                                      | Candidato EARS | Fonte                    | Classificação | Notas                                                                                                                                    |
| --- | ------------------------------------------------------------------------------------------------------------------------ | -------------- | ------------------------ | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | O índice de região é mapeado em 5 grupos: cod 1-5→Norte, 6-10→Nordeste, 11-15→Sudeste, 16-20→Sul, demais→Centro-Oeste.   | Event-driven   | `BATCHREL.NSN#L110-L128` | Inferida      | Faixas de cod-regiao→macrorregião; não documentado.                                                                                      |
| 2   | No relatório, o valor bruto é ARREDONDADO (soma 0,005 e trunca), diferente do truncamento do cálculo.                    | Ubiquitous     | `BATCHREL.NSN#L130-L140` | Mistério      | <!-- mystery: comentário admite "ARREDONDAMENTO DIFERE DO CALCBENF (ROUND VS TRUNCATE)"; gera divergência de totalizadores vs RN-014 --> |
| 3   | Pagamentos são sumarizados por status: `'G'`→1, `'P'`→2, `'C'`→3, `'D'`→4, `'E'`→5; status desconhecido cai em "gerado". | Event-driven   | `BATCHREL.NSN#L143-L160` | Inferida      | Máquina de status de pagamento (G/P/C/D/E).                                                                                              |

## Regras de CALCBENF.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/CALCBENF.NSN`. Cross-ref: RN §2.

| #   | Declaração da Regra                                                                                             | Candidato EARS | Fonte                    | Classificação        | Notas                                                                                                                                                                    |
| --- | --------------------------------------------------------------------------------------------------------------- | -------------- | ------------------------ | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1   | Se o mês da competência for < 1 ou > 12, a competência é inválida e o cálculo é abortado.                       | Unwanted       | `CALCBENF.NSN#L150-L155` | Inferida             | Validação de competência AAAAMM.                                                                                                                                         |
| 2   | Se o beneficiário não for encontrado, abortar o cálculo.                                                        | Unwanted       | `CALCBENF.NSN#L158-L168` | Inferida             | -                                                                                                                                                                        |
| 3   | Se o beneficiário não estiver com status `'A'`, abortar o cálculo.                                              | Unwanted       | `CALCBENF.NSN#L170-L173` | Confirmada           | Coerente com RN §5.1 (somente ativos recebem).                                                                                                                           |
| 4   | Se o programa não for encontrado, abortar o cálculo.                                                            | Unwanted       | `CALCBENF.NSN#L176-L185` | Inferida             | RN-003 (vínculo a programa).                                                                                                                                             |
| 5   | Fator regional: cod-regiao 1-25 usa tabela; senão 1.0000.                                                       | Event-driven   | `CALCBENF.NSN#L188-L192` | Mistério             | <!-- mystery: 27 fatores regionais magic numbers com comentários de UF; origem/critério não documentado -->                                                              |
| 6   | Fator familiar em faixas por nº de dependentes (0; ≤2; 3-4; ≥5).                                                | Event-driven   | `CALCBENF.NSN#L195-L210` | Mistério             | <!-- mystery: aceita >3 dependentes, conflitando com RN-004 (máx 3); incrementos 0.05/0.03/0.02 sem fonte -->                                                            |
| 7   | A faixa de renda determina o fator multiplicador (mesma tabela do BATCHPGT).                                    | Event-driven   | `CALCBENF.NSN#L130-L142` | Confirmada           | RN-017/RN-018.                                                                                                                                                           |
| 8   | Fator idade: 1.15 (≥65), 1.10 (≥60), 1.05 (<18), 1.00 (demais).                                                 | Event-driven   | `CALCBENF.NSN#L213-L227` | Mistério             | <!-- mystery: magic numbers de fator etário sem documentação -->                                                                                                         |
| 9   | Valor benefício = base × fator_reg × fator_fam × fator_renda × fator_idade, depois × (1 + fator_reajuste).      | Ubiquitous     | `CALCBENF.NSN#L230-L240` | Mistério             | <!-- mystery: fórmula multiplicativa conflita com RN-013 (aditiva); reajuste aplicado sobre o total conflita com RN-020 (sobre a base) -->                               |
| 10  | O valor é truncado para 2 casas decimais.                                                                       | Ubiquitous     | `CALCBENF.NSN#L243-L246` | Confirmada           | RN-014.                                                                                                                                                                  |
| 11  | Em dezembro: 13º = base × fator_reg × fator_idade; para programa tipo `'A'`, abono natalino = 15% do benefício. | Event-driven   | `CALCBENF.NSN#L252-L268` | Confirmada (parcial) | RN §6 lista 13º/abono como pendentes; código confirma fórmula. EARS: "Quando o mês for dezembro, o sistema deverá adicionar 13º e, para programas tipo A, abono de 15%." |

## Regras de CALCCORR.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/CALCCORR.NSN`. Cross-ref: RN-019.

| #   | Declaração da Regra                                                                           | Candidato EARS | Fonte                    | Classificação             | Notas                                                                                                                       |
| --- | --------------------------------------------------------------------------------------------- | -------------- | ------------------------ | ------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 1   | Se a competência inicial for maior que a final, o período é inválido e a correção é abortada. | Unwanted       | `CALCCORR.NSN#L120-L124` | Inferida                  | Validação de período.                                                                                                       |
| 2   | Pagamentos fora do período [comp-ini, comp-fim] são ignorados.                                | Unwanted       | `CALCCORR.NSN#L130-L142` | Inferida                  | -                                                                                                                           |
| 3   | Pagamentos já corrigidos (`IND-CORRIGIDO = 'S'`) são ignorados (não recorrige).               | Unwanted       | `CALCCORR.NSN#L145-L147` | Inferida                  | Idempotência da correção.                                                                                                   |
| 4   | O valor corrigido = valor original × índice IPCA acumulado do período, truncado a 2 casas.    | Ubiquitous     | `CALCCORR.NSN#L150-L160` | Confirmada                | RN-019: reajuste por índice; IPCA mensal tabelado.                                                                          |
| 5   | A correção só é gravada quando a diferença (corrigido − original) for positiva.               | Event-driven   | `CALCCORR.NSN#L156-L160` | Inferida                  | Não aplica correção negativa.                                                                                               |
| 6   | A tabela IPCA está carregada apenas para 2010-2012 (última carga 2014).                       | Ubiquitous     | `CALCCORR.NSN#L52-L102`  | Mistério                  | <!-- mystery: índices IPCA magic numbers; cobertura limitada de anos pode causar correção zero para períodos sem índice --> |
| 7   | (Código morto) Correção do Plano Verão 1989-1991 (fatores 2.7500 e 1.4289) está comentada.    | Ubiquitous     | `CALCCORR.NSN#L104-L118` | Confirmada (código morto) | Mantido como histórico; não ativo.                                                                                          |

## Regras de CALCDSCT.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/CALCDSCT.NSN`. Cross-ref: RN §3 (RN-021/022/023).

| #   | Declaração da Regra                                                                                                                    | Candidato EARS | Fonte                                | Classificação        | Notas                                                                                                                                                                                                            |
| --- | -------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------------------------------------ | -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Se o pagamento não for encontrado, abortar o cálculo de descontos.                                                                     | Unwanted       | `CALCDSCT.NSN#L72-L86`               | Inferida             | -                                                                                                                                                                                                                |
| 2   | Se o beneficiário não for encontrado, abortar.                                                                                         | Unwanted       | `CALCDSCT.NSN#L88-L96`               | Inferida             | -                                                                                                                                                                                                                |
| 3   | A contribuição social é calculada por faixa de valor bruto (500/1000/2000/9999.99 → 3%/5%/7%/9%) e é obrigatória.                      | Event-driven   | `CALCDSCT.NSN#L57-L66` e `#L98-L100` | Inferida             | Tabela progressiva de alíquota; não detalhada na doc.                                                                                                                                                            |
| 4   | O teto máximo de desconto é 30% do valor bruto.                                                                                        | State-driven   | `CALCDSCT.NSN#L102-L108`             | Confirmada           | RN-021: descontos não podem exceder 30% do bruto.                                                                                                                                                                |
| 5   | Descontos com vigência expirada (dt-fim < hoje) ou ainda não iniciados (dt-inicio > hoje) são ignorados.                               | Unwanted       | `CALCDSCT.NSN#L113-L120`             | Inferida             | Controle de vigência do PE group.                                                                                                                                                                                |
| 6   | Por tipo de desconto: J=judicial (fixo ou %), P=pensão (fixo ou %), I=imposto (%), S=sindical (1% fixo), A=administrativo (fixo ou %). | Event-driven   | `CALCDSCT.NSN#L124-L168`             | Confirmada (parcial) | RN-022 lista tipos por código numérico (01-05); aqui são letras C/I/J/S/P/A — divergência de codificação.                                                                                                        |
| 7   | Descontos do tipo `'J'` (judicial) NÃO respeitam o teto de 30%; todos os demais tipos são limitados ao teto.                           | Unwanted       | `CALCDSCT.NSN#L170-L176`             | Confirmada           | RN-021 nota: exceção para retenções judiciais (não confirmada na época; código confirma). EARS: "Se o desconto não for judicial e o total exceder 30% do bruto, então o sistema deverá limitar o total ao teto." |
| 8   | O total de descontos é truncado a 2 casas e grava `VLR-DESCONTO` no pagamento.                                                         | Ubiquitous     | `CALCDSCT.NSN#L178-L188`             | Confirmada           | RN-014 (truncamento).                                                                                                                                                                                            |

## Regras de CADBENEF.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/CADBENEF.NSN`. Cross-ref: RN §1.

| #   | Declaração da Regra                                                                      | Candidato EARS | Fonte                                   | Classificação        | Notas                                                                                                                                                                        |
| --- | ---------------------------------------------------------------------------------------- | -------------- | --------------------------------------- | -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | A operação deve ser `'I'` (inclusão) ou `'A'` (alteração); caso contrário, rejeita.      | Unwanted       | `CADBENEF.NSN#L100-L105`                | Inferida             | -                                                                                                                                                                            |
| 2   | CPF é obrigatório (≠ 0).                                                                 | Unwanted       | `CADBENEF.NSN#L107-L111`                | Confirmada           | RN-001 (CPF obrigatório/válido).                                                                                                                                             |
| 3   | O CPF deve ser válido pelo algoritmo Módulo 11 (2 dígitos verificadores); senão rejeita. | Unwanted       | `CADBENEF.NSN#L113-L119` e `#L221-L270` | Confirmada           | RN-001: validação por dígito verificador (subprograma VALCPF).                                                                                                               |
| 4   | Nome, data de nascimento e sexo (`'M'`/`'F'`) são obrigatórios.                          | Unwanted       | `CADBENEF.NSN#L121-L138`                | Confirmada (parcial) | RN-006 (data obrigatória); nome/sexo só no código.                                                                                                                           |
| 5   | Na inclusão, se o CPF já existir, rejeita ("já cadastrado").                             | Unwanted       | `CADBENEF.NSN#L148-L152`                | Confirmada           | RN-002: CPF único para beneficiário ativo.                                                                                                                                   |
| 6   | Na alteração, se o CPF não existir, rejeita.                                             | Unwanted       | `CADBENEF.NSN#L154-L158`                | Inferida             | -                                                                                                                                                                            |
| 7   | Na inclusão, o status inicial é `'A'` (ativo).                                           | Event-driven   | `CADBENEF.NSN#L167-L170`                | Inferida             | Estado inicial do cadastro.                                                                                                                                                  |
| 8   | Se a idade do beneficiário for > 75 anos, o status é definido como `'S'` (suspenso).     | Event-driven   | `CADBENEF.NSN#L172-L175`                | Mistério             | <!-- mystery: comentário "AJUSTE STATUS IDOSO" (2011); suspender automaticamente >75 anos não tem regra documentada e é contraintuitivo (idosos costumam ter prioridade) --> |

## Regras de CADDEPEND.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/CADDEPEND.NSN`. Cross-ref: RN-004.

| #   | Declaração da Regra                                                                                      | Candidato EARS | Fonte                    | Classificação | Notas                                                                                                                                                                   |
| --- | -------------------------------------------------------------------------------------------------------- | -------------- | ------------------------ | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Se o beneficiário titular não for encontrado, abortar.                                                   | Unwanted       | `CADDEPEND.NSN#L48-L54`  | Inferida      | -                                                                                                                                                                       |
| 2   | Se o titular estiver com status `'C'` (cancelado) ou `'D'` (desligado), não permite incluir dependentes. | Unwanted       | `CADDEPEND.NSN#L56-L59`  | Inferida      | Coerente com máquina de status.                                                                                                                                         |
| 3   | O número máximo de dependentes é 5 (limite atingido em > 5).                                             | State-driven   | `CADDEPEND.NSN#L62-L66`  | Mistério      | <!-- mystery: código impõe limite 5, mas RN-004 (doc) afirma máximo 3; a nota da doc suspeitava alteração para 5 não confirmada — AQUI ESTÁ A CONFIRMAÇÃO no código --> |
| 4   | Parentesco deve ser `'FI'`, `'CO'`, `'IR'` ou `'OU'`; senão rejeita.                                     | Unwanted       | `CADDEPEND.NSN#L82-L87`  | Inferida      | Domínio de parentesco.                                                                                                                                                  |
| 5   | Nome do dependente é obrigatório.                                                                        | Unwanted       | `CADDEPEND.NSN#L77-L80`  | Inferida      | -                                                                                                                                                                       |
| 6   | Dependente com CPF já cadastrado (e CPF ≠ 0) é rejeitado como duplicado.                                 | Unwanted       | `CADDEPEND.NSN#L95-L104` | Inferida      | -                                                                                                                                                                       |

## Regras de CADPROG.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/CADPROG.NSN`.

| #   | Declaração da Regra                                                                          | Candidato EARS | Fonte                  | Classificação | Notas                                                                                                                                                |
| --- | -------------------------------------------------------------------------------------------- | -------------- | ---------------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | A operação deve ser `'I'` (inclusão) ou `'C'` (consulta); senão rejeita.                     | Unwanted       | `CADPROG.NSN#L50-L53`  | Inferida      | -                                                                                                                                                    |
| 2   | Na inclusão, se o programa já existir, rejeita.                                              | Unwanted       | `CADPROG.NSN#L77-L84`  | Inferida      | -                                                                                                                                                    |
| 3   | O valor base é ajustado por um Fator K = 1.00 + (fator_reajuste × 0.347215) antes de gravar. | Ubiquitous     | `CADPROG.NSN#L86-L89`  | Mistério      | <!-- mystery: "FATOR-K" com constante 0.347215 — RN §6 e nota RN-013 confirmam que ninguém na equipe soube explicar a origem deste multiplicador --> |
| 4   | Programa incluído nasce com status `'A'` (ativo).                                            | Event-driven   | `CADPROG.NSN#L91-L104` | Inferida      | Estado inicial.                                                                                                                                      |
| 5   | O tipo de programa é `'A'` (assistencial), `'P'` (previdenciário) ou `'T'` (trabalho).       | Ubiquitous     | `CADPROG.NSN#L17`      | Confirmada    | Coerente com VALELEG (tipos A/P/T) e RN §4.                                                                                                          |

## Regras de VALBENEF.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/VALBENEF.NSN`. Cross-ref: RN-001/RN-005.

| #   | Declaração da Regra                                                                                                  | Candidato EARS | Fonte                                   | Classificação        | Notas                                                                                                                          |
| --- | -------------------------------------------------------------------------------------------------------------------- | -------------- | --------------------------------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| 1   | O CPF é validado por Módulo 11; CPF inválido gera erro.                                                              | Unwanted       | `VALBENEF.NSN#L116-L122` e `#L182-L245` | Confirmada           | RN-001.                                                                                                                        |
| 2   | CPF com todos os dígitos iguais é inválido, EXCETO quando inicia com `000` (considerado teste de governo, válido).   | Unwanted       | `VALBENEF.NSN#L194-L210`                | Mistério             | <!-- mystery: exceção "CPFs iniciados com 000 são válidos (teste governo)" — não documentado; risco de bypass de validação --> |
| 3   | A data de nascimento deve ter ano entre 1900 e o ano atual, mês 1-12 e dia válido para o mês (fevereiro fixo em 29). | Unwanted       | `VALBENEF.NSN#L124-L132` e `#L247-L268` | Inferida             | Fevereiro sempre 29 (não checa bissexto real).                                                                                 |
| 4   | O nome deve conter ao menos um espaço (nome + sobrenome); senão inválido.                                            | Unwanted       | `VALBENEF.NSN#L134-L142` e `#L270-L286` | Inferida             | RN não cobre; regra de qualidade.                                                                                              |
| 5   | A UF deve pertencer à tabela de 27 UFs válidas.                                                                      | Unwanted       | `VALBENEF.NSN#L144-L162`                | Confirmada (parcial) | RN-005 trata região (01-27) ↔ UF.                                                                                              |
| 6   | O status deve ser um de `'A'`,`'S'`,`'C'`,`'I'`,`'D'`; senão inválido.                                               | Unwanted       | `VALBENEF.NSN#L164-L172`                | Confirmada           | Define o domínio da máquina de status do beneficiário.                                                                         |

## Regras de VALDOCS.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/VALDOCS.NSN`.

| #   | Declaração da Regra                                                                                                                            | Candidato EARS | Fonte                                | Classificação | Notas                                                                                                                                                             |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------------------------------------ | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | CPF zero ou reprovado no Módulo 11 é inválido.                                                                                                 | Unwanted       | `VALDOCS.NSN#L70-L78` e `#L100-L150` | Confirmada    | RN-001.                                                                                                                                                           |
| 2   | O RG deve ter ao menos 5 caracteres; senão inválido.                                                                                           | Unwanted       | `VALDOCS.NSN#L80-L88` e `#L152-L170` | Inferida      | Regra de formato; não documentada.                                                                                                                                |
| 3   | Se o CPF iniciar com prefixo especial (`000`,`001`,`002`,`010`,`011`,`099`,`100`,`999`), o documento é marcado válido e zera erros anteriores. | Unwanted       | `VALDOCS.NSN#L50-L58` e `#L172-L185` | Mistério      | <!-- mystery: lista de 8 prefixos "governo/teste" que forçam CPF válido e limpam erros — bypass de validação documental sem regra escrita; risco de segurança --> |

## Regras de VALELEG.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/VALELEG.NSN`. Cross-ref: RN §4.

| #   | Declaração da Regra                                                                                                                            | Candidato EARS | Fonte                   | Classificação        | Notas                                                                                                                                                                                                 |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ----------------------- | -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Se o beneficiário ou o programa não for encontrado, abortar a validação.                                                                       | Unwanted       | `VALELEG.NSN#L70-L96`   | Inferida             | -                                                                                                                                                                                                     |
| 2   | Se o programa estiver inativo (status ≠ `'A'`), abortar (não elegível).                                                                        | Unwanted       | `VALELEG.NSN#L98-L102`  | Confirmada           | RN-003.                                                                                                                                                                                               |
| 3   | Se a região do beneficiário for 99, ele é considerado elegível e TODA a validação é ignorada.                                                  | Optional       | `VALELEG.NSN#L104-L110` | Mistério             | <!-- mystery: bypass total de elegibilidade quando regiao=99 ("internacional/diplomático"); RN-005 e nota da doc confirmam que a origem é desconhecida ("bypass do Roberto") — falha de segurança --> |
| 4   | Status `'S'` → suspenso, `'C'`/`'D'` → cancelado/desligado, `'I'` → inativo tornam o beneficiário não elegível.                                | Unwanted       | `VALELEG.NSN#L112-L132` | Confirmada           | RN §4.2: situação cadastral ativa exigida.                                                                                                                                                            |
| 5   | Idade fora de [IDADE-MIN, IDADE-MAX] do programa torna não elegível.                                                                           | Unwanted       | `VALELEG.NSN#L134-L152` | Confirmada (parcial) | RN-015 pendente; código define a regra.                                                                                                                                                               |
| 6   | Renda familiar acima de RENDA-MAX do programa torna não elegível.                                                                              | Unwanted       | `VALELEG.NSN#L154-L164` | Confirmada           | RN §4.2: renda dentro das faixas do programa.                                                                                                                                                         |
| 7   | Por tipo: A=assistencial (renda>600 sem dependentes → não elegível; exige docs `'S'`); P=previdenciário (idade ≥60); T=trabalho (idade 16-65). | Event-driven   | `VALELEG.NSN#L166-L200` | Inferida             | RN-015 pendente na doc; magic numbers 600/60/16/65.                                                                                                                                                   |
| 8   | Código de elegibilidade: 1º char `'R'` exige NIS cadastrado; 2º char `'D'` exige dependentes.                                                  | Optional       | `VALELEG.NSN#L202-L240` | Inferida             | Codificação posicional do COD-ELEGIBILIDADE; não documentada.                                                                                                                                         |

## Regras de CONSBENF.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/CONSBENF.NSN`.

| #   | Declaração da Regra                                                                                                   | Candidato EARS | Fonte                                   | Classificação        | Notas                                                                                                                               |
| --- | --------------------------------------------------------------------------------------------------------------------- | -------------- | --------------------------------------- | -------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| 1   | A busca pode ser por CPF (`'C'`) ou NIS (`'N'`); tipo em branco assume CPF; tipo inválido rejeita.                    | Event-driven   | `CONSBENF.NSN#L82-L104`                 | Inferida             | -                                                                                                                                   |
| 2   | O CPF exibido é mascarado para ocultar dados sensíveis (formato **_._**.XXX-XX).                                      | Ubiquitous     | `CONSBENF.NSN#L120-L124` e `#L176-L192` | Confirmada (conduta) | Boa prática de PII; alinha-se às instruções de mascarar CPF.                                                                        |
| 3   | A máscara de CPF é inconsistente: dependendo do tamanho armazenado, mostra os 3 primeiros dígitos em vez dos últimos. | Unwanted       | `CONSBENF.NSN#L168-L190`                | Mistério             | <!-- mystery: comentário admite inconsistência conhecida na máscara; "não corrigir sem aprovação da auditoria" — pode expor PII --> |
| 4   | O histórico exibe apenas os últimos 12 pagamentos do beneficiário.                                                    | Event-driven   | `CONSBENF.NSN#L154-L168`                | Inferida             | Limite de apresentação (magic 12).                                                                                                  |
| 5   | Descrição de status: A=Ativo, S=Suspenso, C=Cancelado, I=Inativo, D=Desligado.                                        | Ubiquitous     | `CONSBENF.NSN#L106-L120`                | Confirmada           | Domínio de status do beneficiário.                                                                                                  |

## Regras de RELPGT.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/RELPGT.NSN`.

| #   | Declaração da Regra                                                                                 | Candidato EARS | Fonte                  | Classificação        | Notas                                                                   |
| --- | --------------------------------------------------------------------------------------------------- | -------------- | ---------------------- | -------------------- | ----------------------------------------------------------------------- |
| 1   | Pagamentos fora do intervalo de competências [ini, fim] não entram no relatório.                    | Unwanted       | `RELPGT.NSN#L83-L88`   | Inferida             | -                                                                       |
| 2   | Se um código de programa filtro (≠ 0) for informado, apenas pagamentos desse programa são listados. | Optional       | `RELPGT.NSN#L90-L94`   | Inferida             | Filtro opcional.                                                        |
| 3   | O CPF é mascarado no relatório (\*\*\*.XXX.XXX-XX, com 3 primeiros ocultos).                        | Ubiquitous     | `RELPGT.NSN#L108-L116` | Confirmada (conduta) | PII; máscara diferente da do CONSBENF (inconsistência entre programas). |
| 4   | Tipo de pagamento: N=Normal, D=Décimo, T=Terceiro.                                                  | Ubiquitous     | `RELPGT.NSN#L118-L130` | Confirmada (parcial) | Domínio TIPO-PGTO; 'T' não aparece sendo gravado nos cálculos (só N/D). |
| 5   | Quebra/subtotal por código de programa ao mudar o programa.                                         | Event-driven   | `RELPGT.NSN#L96-L106`  | Inferida             | Controle de relatório.                                                  |

## Regras de RELAUDIT.NSN

> Fonte: `01-arqueologia/legado-sifap/natural-programs/RELAUDIT.NSN`. Cross-ref: RN-010.

| #   | Declaração da Regra                                                                                                  | Candidato EARS | Fonte                    | Classificação | Notas                                                                                                                                                             |
| --- | -------------------------------------------------------------------------------------------------------------------- | -------------- | ------------------------ | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Período padrão: se data inicial = 0, usa 1997-01-01; se data final = 0, usa hoje.                                    | Event-driven   | `RELAUDIT.NSN#L88-L96`   | Inferida      | Default de período.                                                                                                                                               |
| 2   | Eventos de auditoria com ação `'EX'` (exclusão) NUNCA são exibidos no relatório.                                     | Unwanted       | `RELAUDIT.NSN#L100-L110` | Mistério      | <!-- mystery: ação 'EX' é silenciosamente filtrada da trilha de auditoria ("LIMPEZA RELATORIO", 2014); ocultar exclusões da auditoria é risco de conformidade --> |
| 3   | Filtros opcionais por ação, usuário e tabela quando informados.                                                      | Optional       | `RELAUDIT.NSN#L112-L140` | Inferida      | -                                                                                                                                                                 |
| 4   | Códigos de ação de auditoria: IN=inclusão, AL=alteração, CO=conciliação, CN=consulta, DV=divergência, demais=outras. | Ubiquitous     | `RELAUDIT.NSN#L143-L168` | Confirmada    | RN-010 (auditoria automática); domínio de ações confirma BATCHCON (CO/DV).                                                                                        |

---

### Continuar a leitura

<table width="100%">
<tr>
<td width="50%" valign="top" align="left">
<sub><strong>← ANTERIOR</strong></sub><br/>
<a href="GUIDE.md"><strong>GUIDE do Estágio 1</strong></a><br/>
<sub>Passo a passo do estágio.</sub>
</td>
<td width="50%" valign="top" align="right">
<sub><strong>PRÓXIMO →</strong></sub><br/>
<a href="dependency-map.md"><strong>dependency-map.md</strong></a><br/>
<sub>Mapa de quem chama quem.</sub>
</td>
</tr>
</table>

<sub>↑ <a href="README.md">Voltar ao Kit PT-BR</a></sub>
