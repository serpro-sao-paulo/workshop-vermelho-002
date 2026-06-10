<!-- markdownlint-disable MD013 MD025 MD026 MD028 MD029 MD034 MD040 MD051 MD060 -->

# Inventário Legado — [Nome da Equipe]

> **Data:** 2026-06-10
> **Equipe:** _[preencher nome da equipe]_
> **Status:** Primeira passada (top-down, somente nomes e estrutura). A ser revisada conforme a equipe lê arquivos individuais.
> **Escopo:** Esta orientação opera apenas sobre estrutura de pastas e nomes de arquivos. **Nenhum arquivo de programa foi aberto/lido.**

---

## Estrutura de Pastas

Raiz analisada: `01-arqueologia/legado-sifap/`

```
legado-sifap/
├── README.md
├── COMO-LER-NATURAL.md            (referenciado pela navegação; confirmar)
├── natural-programs/
│   ├── README.md
│   ├── BATCHCON.NSN
│   ├── BATCHPGT.NSN
│   ├── BATCHREL.NSN
│   ├── CADBENEF.NSN
│   ├── CADDEPEND.NSN
│   ├── CADPROG.NSN
│   ├── CALCBENF.NSN
│   ├── CALCCORR.NSN
│   ├── CALCDSCT.NSN
│   ├── CONSBENF.NSN
│   ├── RELAUDIT.NSN
│   ├── RELPGT.NSN
│   ├── VALBENEF.NSN
│   ├── VALDOCS.NSN
│   └── VALELEG.NSN
├── adabas-ddms/
│   ├── README.md
│   ├── BENEFICIARIO.ddm
│   ├── PAGAMENTO.ddm
│   ├── PROGRAMA-SOCIAL.ddm
│   └── AUDITORIA.ddm
└── legacy-docs/
    ├── README.md
    ├── ARQUITETURA-ORIGINAL-1997.md
    ├── ARQUITETURA-ORIGINAL-1997.docx
    ├── MANUAL-TECNICO-SIFAP-2008.md
    ├── MANUAL-TECNICO-SIFAP-2008.docx
    ├── REGRAS-NEGOCIO-2012.md
    └── REGRAS-NEGOCIO-2012.docx
```

**Diretórios sob `legado-sifap/`:** 3 subdiretórios (`natural-programs/`, `adabas-ddms/`, `legacy-docs/`) + a própria raiz = **4 níveis de pasta no total**.

> Nota: a demo interativa fica em `01-arqueologia/demo/` (irmã da pasta de legado, fora de `legado-sifap/`) — não faz parte do código legado a ser arqueologado.

---

## Contagem de Arquivos por Tipo

| Extensão | Contagem | Finalidade provável |
| -------- | -------- | ------------------- |
| `.NSN`   | 15       | Programa-fonte Natural (código legado) |
| `.ddm`   | 4        | Data Definition Module (definição de estrutura Adabas) |
| `.md`    | 7        | Documentação Markdown (3 docs legados + 4 READMEs de navegação) |
| `.docx`  | 3        | Documentação original em Word (pares dos `.md` em `legacy-docs/`) |

> **Verificação sugerida (segunda pessoa da equipe):**
> `find 01-arqueologia/legado-sifap -type f | sed 's/.*\.//' | sort | uniq -c`
> Os tamanhos em bytes não foram medidos nesta passada; rode `ls -la` / `wc -l` para confirmar os "itens incomuns" abaixo.

---

## Padrões de Convenção de Nomes

Agrupamento por prefixo dos 15 `.NSN` (somente nomes, sem abrir arquivos):

| Prefixo | Contagem | Hipótese |
| ------- | -------- | -------- |
| `BATCH` | 3 | Programas batch (`BATCHCON`, `BATCHPGT`, `BATCHREL`). Convenção genérica para processos não-interativos/agendados. |
| `CAD`   | 3 | Programas de cadastro/CRUD (`CADBENEF`, `CADDEPEND`, `CADPROG`). "CAD" = cadastro. |
| `CALC`  | 3 | Programas de cálculo (`CALCBENF`, `CALCCORR`, `CALCDSCT`). "CALC" = cálculo. |
| `VAL`   | 3 | Programas de validação (`VALBENEF`, `VALDOCS`, `VALELEG`). "VAL" = validação. |
| `REL`   | 2 | Programas de relatório (`RELAUDIT`, `RELPGT`). "REL" = relatório. |
| `CONS`  | 1 | Consulta (`CONSBENF`). "CONS" = consulta — apenas 1 ocorrência. |

Sufixos/raízes de entidade que se repetem entre prefixos (sugerem relacionamento entre programas):

| Raiz | Aparece em | Hipótese |
| ---- | ---------- | -------- |
| `BEN(E)F` | `CADBENEF`, `CALCBENF`, `VALBENEF`, `CONSBENF` | Todos operam sobre a entidade **Beneficiário** (DDM `BENEFICIARIO.ddm`). |
| `PGT`/`PG` | `BATCHPGT`, `RELPGT` | Operam sobre **Pagamento** (DDM `PAGAMENTO.ddm`). |
| `PROG` | `CADPROG` | Opera sobre **Programa Social** (DDM `PROGRAMA-SOCIAL.ddm`). |
| `AUDIT` | `RELAUDIT` | Opera sobre **Auditoria** (DDM `AUDITORIA.ddm`). |

> Cada um dos 4 DDMs tem um programa de nome correspondente — bom indício de mapeamento entidade↔código a confirmar na leitura.

---

## Itens Incomuns (Top 3)

1. **`natural-programs/CADDEPEND.NSN`** — Inconsistência de comprimento de nome.
   - O que torna incomum: é o único `.NSN` com **9 caracteres** de nome-base (os demais têm 8: `CADBENEF`, `BATCHCON`...). Nomes de membros Natural no mainframe costumam ser limitados a 8 caracteres.
   - Ação sugerida: confirmar se o nome no mainframe é truncado e se há outro programa ligado a "dependentes" não catalogado.

2. **Divergência de nome `BENEF` vs `BENF`** — `CADBENEF.NSN`/`VALBENEF.NSN` (com `E`) contra `CALCBENF.NSN`/`CONSBENF.NSN` (sem `E`).
   - O que torna incomum: a mesma entidade (Beneficiário) é abreviada de duas formas no conjunto de arquivos. O `dependency-map.md` do kit ainda usa uma terceira grafia (`CADBENF`).
   - Ação sugerida: padronizar a grafia no glossário e verificar se os programas realmente apontam para a mesma entidade ao mapear CALLNAT.

3. **`adabas-ddms/PROGRAMA-SOCIAL.ddm`** — Único nome de DDM com hífen / mais longo.
   - O que torna incomum: os outros 3 DDMs são palavras únicas (`BENEFICIARIO`, `PAGAMENTO`, `AUDITORIA`); este é o único composto/hifenizado. O README do sistema indica que ele usa campos `MU` (multivalor) e `PE` (grupo periódico).
   - Ação sugerida: priorizar a leitura deste DDM no mapeamento → PostgreSQL, pois `MU`/`PE` não têm equivalente relacional direto e geram tabelas extras.

> Observação: tamanhos em bytes não foram medidos nesta passada. Ao rodar `ls -la`, reavalie "maior arquivo" — o README do sistema sugere que `CALCBENF` (~4.800 linhas) tende a ser o maior `.NSN`.

---

## Ordem de Leitura Proposta

> **Esta é uma hipótese baseada apenas em nomes e estrutura.** A ordem real mudará quando a equipe rastrear dependências (`CALLNAT`) e leitura/escrita de DDMs.

1. **DDMs primeiro (dados antes do código):** `BENEFICIARIO.ddm` → `PROGRAMA-SOCIAL.ddm` → `PAGAMENTO.ddm` → `AUDITORIA.ddm`. Entender as entidades antes de ler a lógica.
2. **Entry point batch (revela o fluxo de negócio):** `BATCHPGT.NSN` — o README do sistema o descreve como o processamento principal da folha; provável orquestrador que chama outros programas.
3. **Programas mais conectados (núcleo financeiro):** `CALCBENF.NSN`, depois `CALCCORR.NSN` e `CALCDSCT.NSN` — a raiz `BENF`/cálculo aparece referenciada por vários módulos e é o coração do sistema.
4. **Cadastros (entidades-subject das regras):** `CADBENEF.NSN`, `CADPROG.NSN`, `CADDEPEND.NSN`.
5. **Validações e consulta:** `VALBENEF.NSN`, `VALELEG.NSN`, `VALDOCS.NSN`, `CONSBENF.NSN`.
6. **Conciliação e relatórios (saídas):** `BATCHCON.NSN`, `BATCHREL.NSN`, `RELPGT.NSN`, `RELAUDIT.NSN`.

**Justificativa:** prioriza (a) DDMs para fixar o vocabulário de dados, (b) o entry point batch `BATCHPGT` por posição estrutural, e (c) os programas de cálculo por serem a raiz de entidade mais repetida e o núcleo financeiro a ser reproduzido no Estágio 3.

---

## Dicionário de Campos por DDM

> **Origem:** leitura direta dos 4 arquivos `.ddm` em `01-arqueologia/legado-sifap/adabas-ddms/`.
> Legenda: `DE` = descriptor (campo indexado para busca) · `MU` = multiple-value field · `PE` = periodic group · `Lv` = level Adabas.
> Formato `N x.y` = numérico com `y` casas decimais (mapear para `BigDecimal` com `scale` correspondente, **nunca** `double`).

### BENEFICIARIO — FNR 150

| Lv | Cod | Campo | Tipo | Tam | Flag |
|----|-----|-------|------|-----|------|
| 1 | AA | NUM-INSCRICAO | N | 11 | |
| 1 | AB | NUM-CPF | A | 11 | DE |
| 1 | AC | NOME-COMPLETO | A | 60 | |
| 1 | AD | NOME-MAE | A | 60 | |
| 1 | AE | NOME-PAI | A | 60 | |
| 1 | AF | DT-NASCIMENTO | N | 8 | |
| 1 | AG | SEXO | A | 1 | |
| 1 | AH | EST-CIVIL | A | 1 | |
| 1 | AI | RG-NUMERO | A | 15 | |
| 1 | AJ | RG-ORGAO | A | 10 | |
| 1 | AK | RG-UF | A | 2 | |
| 1 | AL | RG-DT-EXPEDICAO | N | 8 | |
| 1 | BA | GRP-ENDERECO | (grupo) | — | |
| 2 | BB | LOGRADOURO | A | 60 | |
| 2 | BC | NUMERO | A | 10 | |
| 2 | BD | COMPLEMENTO | A | 30 | |
| 2 | BE | BAIRRO | A | 40 | |
| 2 | BF | MUNICIPIO | A | 40 | |
| 2 | BG | UF | A | 2 | DE |
| 2 | BH | CEP | N | 8 | |
| 2 | BI | COD-IBGE | N | 7 | |
| 2 | BJ | COD-REGIAO | A | 2 | |
| 1 | CA | COD-PROGRAMA | A | 4 | (PE)* |
| 1 | CB | DT-CADASTRO | N | 8 | DE |
| 1 | CC | DT-INICIO-BENEF | N | 8 | |
| 1 | CD | DT-FIM-BENEF | N | 8 | |
| 1 | CE | SIT-BENEFICIARIO | A | 1 | |
| 1 | CF | MOT-SITUACAO | A | 3 | |
| 1 | CG | DT-ULT-SITUACAO | N | 8 | |
| 1 | CH | VLR-RENDA-FAMILIAR | N | 9.2 | |
| 1 | CI | QTD-MEMBROS-FAMILIA | N | 2 | |
| 1 | CJ | IND-RENDA-PERCAP | N | 7.2 | |
| 1 | **DA** | **GRP-DEPENDENTE** | **PE** | — | **PE (máx 10)** |
| 2 | DB | CPF-DEPENDENTE | A | 11 | |
| 2 | DC | NOME-DEPENDENTE | A | 60 | |
| 2 | DD | DT-NASC-DEPEND | N | 8 | |
| 2 | DE | PARENTESCO | A | 2 | |
| 2 | DF | SIT-DEPENDENTE | A | 1 | |
| 2 | DG | IND-DEFICIENCIA | A | 1 | |
| 1 | EA | TEL-FIXO | A | 14 | |
| 1 | EB | TEL-CELULAR | A | 15 | |
| 1 | EC | EMAIL | A | 80 | |
| 1 | FA | IND-BIOMETRIA | A | 1 | |
| 1 | FB | DT-COLETA-BIO | N | 8 | |
| 1 | FC | COD-POSTO-BIO | A | 6 | |
| 1 | FD | HASH-DIGITAL | A | 64 | |
| 1 | GA | DT-INCLUSAO | N | 8 | DE |
| 1 | GB | HR-INCLUSAO | N | 6 | |
| 1 | GC | USR-INCLUSAO | A | 8 | |
| 1 | GD | DT-ULT-ALTERACAO | N | 8 | |
| 1 | GE | HR-ULT-ALTERACAO | N | 6 | |
| 1 | GF | USR-ULT-ALTERACAO | A | 8 | |
| 1 | GG | NUM-VERSAO | N | 5 | |

> \* `CA COD-PROGRAMA` aparece anotado `(PE)` no remark, mas é declarado como campo simples `level 1` (sem flag `PE` nem `OCC`). Conflito de modelagem — ver Mistério no log de mistérios.

### PROGRAMA-SOCIAL — FNR 151

| Lv | Cod | Campo | Tipo | Tam | Flag |
|----|-----|-------|------|-----|------|
| 1 | AA | COD-PROGRAMA | A | 4 | DE |
| 1 | AB | NOME-PROGRAMA | A | 60 | |
| 1 | AC | SIGLA-PROGRAMA | A | 10 | |
| 1 | AD | TIPO-PROGRAMA | A | 1 | |
| 1 | AE | ORGAO-RESPONSAVEL | A | 10 | |
| 1 | AF | LEI-CRIACAO | A | 20 | |
| 1 | AG | DT-CRIACAO | N | 8 | |
| 1 | AH | DT-ENCERRAMENTO | N | 8 | |
| 1 | AI | SIT-PROGRAMA | A | 1 | |
| 1 | BA | VLR-BASE-INDIVIDUAL | N | 7.2 | |
| 1 | BB | VLR-BASE-FAMILIAR | N | 7.2 | |
| 1 | BC | VLR-TETO-BENEF | N | 9.2 | |
| 1 | BD | VLR-PISO-BENEF | N | 7.2 | |
| 1 | BE | PCT-REAJUSTE-ANUAL | N | 3.2 | |
| 1 | BF | DT-ULT-REAJUSTE | N | 8 | |
| 1 | BG | FATOR-K | N | 5.4 | ⚠ não documentado |
| 1 | CA | RENDA-MAX-PERCAP | N | 7.2 | |
| 1 | CB | IDADE-MIN | N | 3 | |
| 1 | CC | IDADE-MAX | N | 3 | |
| 1 | CD | IND-EXIGE-FILHOS | A | 1 | |
| 1 | CE | QTD-MIN-FILHOS | N | 2 | |
| 1 | CF | IND-EXIGE-ESCOLA | A | 1 | |
| 1 | CG | IND-EXIGE-VACINA | A | 1 | |
| 1 | CH | IND-EXIGE-PRENATAL | A | 1 | |
| 1 | CI | IND-EXIGE-BIOMETRIA | A | 1 | |
| 1 | **DA** | **GRP-FAIXA-CALCULO** | **PE** | — | **PE (máx 5)** |
| 2 | DB | RENDA-INICIO | N | 7.2 | |
| 2 | DC | RENDA-FIM | N | 7.2 | |
| 2 | DD | FATOR-MULTIPLICADOR | N | 3.4 | |
| 2 | DE | VLR-ADICIONAL | N | 7.2 | |
| 2 | DF | IND-ACUMULATIVO | A | 1 | |
| 1 | **EA** | **TIPO-DSCT-APLIC** | **MU A** | 3 | **MU (máx 8)** |
| 1 | **FA** | **GRP-PARAM-REGIONAL** | **PE** | — | **PE (máx 6)** |
| 2 | FB | COD-REGIAO | A | 2 | |
| 2 | FC | FATOR-REGIONAL | N | 3.4 | |
| 2 | FD | VLR-COMPLEMENTO-REG | N | 7.2 | |
| 2 | FE | IND-ATIVO-REGIAO | A | 1 | |
| 1 | GA | DT-INCLUSAO | N | 8 | |
| 1 | GB | USR-INCLUSAO | A | 8 | |
| 1 | GC | DT-ULT-ALTERACAO | N | 8 | |
| 1 | GD | USR-ULT-ALTERACAO | A | 8 | |

### PAGAMENTO — FNR 152

| Lv | Cod | Campo | Tipo | Tam | Flag |
|----|-----|-------|------|-----|------|
| 1 | AA | NUM-PAGAMENTO | N | 15 | DE |
| 1 | AB | NUM-CPF | A | 11 | DE |
| 1 | AC | NUM-INSCRICAO | N | 11 | |
| 1 | AD | COD-PROGRAMA | A | 4 | DE |
| 1 | AE | ANO-MES-REF | N | 6 | DE |
| 1 | AF | NUM-CICLO | N | 6 | |
| 1 | BA | VLR-BRUTO | N | 9.2 | |
| 1 | BB | VLR-LIQUIDO | N | 9.2 | |
| 1 | BC | VLR-DESCONTO-TOTAL | N | 7.2 | |
| 1 | **CA** | **GRP-DESCONTO** | **PE** | — | **PE (máx 8)** |
| 2 | CB | TIPO-DESCONTO | A | 3 | |
| 2 | CC | VLR-DESCONTO | N | 7.2 | |
| 2 | CD | PCT-DESCONTO | N | 3.2 | |
| 2 | CE | NUM-PROCESSO | A | 20 | |
| 2 | CF | DT-INICIO-DSCT | N | 8 | |
| 2 | CG | DT-FIM-DSCT | N | 8 | |
| 1 | DA | SIT-PAGAMENTO | A | 1 | |
| 1 | DB | DT-GERACAO | N | 8 | DE |
| 1 | DC | HR-GERACAO | N | 6 | |
| 1 | DD | DT-EMISSAO | N | 8 | |
| 1 | DE | DT-CONFIRMACAO | N | 8 | |
| 1 | DF | DT-CANCELAMENTO | N | 8 | |
| 1 | DG | MOT-CANCELAMENTO | A | 3 | |
| 1 | EA | COD-BANCO | A | 3 | |
| 1 | EB | COD-AGENCIA | A | 6 | |
| 1 | EC | NUM-CONTA | A | 13 | |
| 1 | ED | TIPO-CONTA | A | 1 | |
| 1 | EE | COD-OPERACAO | A | 3 | |
| 1 | FA | NUM-OB-SIAFI | A | 12 | |
| 1 | FB | NUM-NE-SIAFI | A | 12 | |
| 1 | FC | COD-UG-EMITENTE | A | 6 | |
| 1 | FD | COD-GESTAO | A | 5 | |
| 1 | FE | SIT-INTEG-SIAFI | A | 1 | |
| 1 | GA | DT-CONCILIACAO | N | 8 | |
| 1 | GB | SIT-CONCILIACAO | A | 1 | |
| 1 | GC | VLR-CONCILIADO | N | 9.2 | |
| 1 | GD | COD-RETORNO-BANCO | A | 2 | |
| 1 | GE | DES-RETORNO-BANCO | A | 40 | |
| 1 | HA | HASH-ARQ-REMESSA | A | 64 | |
| 1 | HB | HASH-ARQ-RETORNO | A | 64 | |
| 1 | IA | DT-INCLUSAO | N | 8 | |
| 1 | IB | HR-INCLUSAO | N | 6 | |
| 1 | IC | USR-INCLUSAO | A | 8 | |
| 1 | ID | DT-ULT-ALTERACAO | N | 8 | |
| 1 | IE | HR-ULT-ALTERACAO | N | 6 | |
| 1 | IF | USR-ULT-ALTERACAO | A | 8 | |

### AUDITORIA — FNR 153

| Lv | Cod | Campo | Tipo | Tam | Flag |
|----|-----|-------|------|-----|------|
| 1 | AA | NUM-AUDITORIA | N | 15 | DE |
| 1 | AB | DT-EVENTO | N | 8 | DE |
| 1 | AC | HR-EVENTO | N | 6 | |
| 1 | AD | TS-EVENTO | N | 14 | |
| 1 | BA | COD-ACAO | A | 2 | DE |
| 1 | BB | COD-MODULO | A | 8 | |
| 1 | BC | DES-ACAO | A | 80 | |
| 1 | CA | TIPO-ENTIDADE | A | 4 | |
| 1 | CB | ID-ENTIDADE | A | 15 | DE |
| 1 | CC | NUM-CPF-AFETADO | A | 11 | DE |
| 1 | DA | GRP-ANTES | (grupo) | — | |
| 2 | **DB** | **CAMPO-ALTERADO-ANT** | **MU A** | 30 | **MU (máx 20)** |
| 2 | **DC** | **VALOR-ANTERIOR** | **MU A** | 80 | **MU (máx 20)** |
| 1 | DD | GRP-DEPOIS | (grupo) | — | |
| 2 | **DE** | **CAMPO-ALTERADO-DEP** | **MU A** | 30 | **MU (máx 20)** |
| 2 | **DF** | **VALOR-POSTERIOR** | **MU A** | 80 | **MU (máx 20)** |
| 1 | EA | USR-EVENTO | A | 8 | DE |
| 1 | EB | NOME-USUARIO | A | 40 | |
| 1 | EC | COD-PERFIL | A | 3 | |
| 1 | ED | COD-LOTACAO | A | 10 | |
| 1 | EE | IP-ORIGEM | A | 15 | |
| 1 | EF | ID-SESSAO | A | 20 | |
| 1 | FA | NUM-CICLO-BATCH | N | 6 | |
| 1 | FB | NUM-SEQ-BATCH | N | 10 | |
| 1 | FC | NOM-JOB-BATCH | A | 16 | |
| 1 | FD | SIT-BATCH | A | 1 | |
| 1 | FE | DES-ERRO-BATCH | A | 120 | |
| 1 | GA | ID-CORRELACAO | A | 36 | |
| 1 | GB | NUM-SEQ-CORRELACAO | N | 3 | |

### Resumo de campos MU e PE

| DDM | PE (grupos periódicos) | MU (multivalor) |
|-----|------------------------|------------------|
| BENEFICIARIO | `DA GRP-DEPENDENTE` (máx 10) | — |
| PROGRAMA-SOCIAL | `DA GRP-FAIXA-CALCULO` (5), `FA GRP-PARAM-REGIONAL` (6) | `EA TIPO-DSCT-APLIC` A3 (8) |
| PAGAMENTO | `CA GRP-DESCONTO` (8) | — |
| AUDITORIA | — | `DB`, `DC`, `DE`, `DF` (todos A, máx 20) |

> **Notas de leitura:**
> - `GRP-ENDERECO`, `GRP-ANTES`, `GRP-DEPOIS` são **grupos simples** (level 1 com filhos level 2), **não** periódicos — não repetem.
> - <!-- MYSTERY: BENEFICIARIO.CA COD-PROGRAMA tem remark "(PE)" sem flag/ocorrência real. Conflito de modelagem a investigar com a equipe de domínio. -->
> - ⚠ `PROGRAMA-SOCIAL.BG FATOR-K` está explicitamente marcado como **não documentado** no próprio DDM — candidato a mistério para o log do Estágio 1.
> - Campos `MU`/`PE` não têm equivalente relacional direto: ao mapear para PostgreSQL geram tabelas filhas adicionais.

---

> **Próximos prompts:** para ler conteúdo de arquivos, use `/extract-business-rules` (regras de negócio) ou `/map-dependencies` (grafo CALLNAT). Esta orientação não abre arquivos.
