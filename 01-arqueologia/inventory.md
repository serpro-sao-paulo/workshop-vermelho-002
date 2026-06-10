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

> **Próximos prompts:** para ler conteúdo de arquivos, use `/extract-business-rules` (regras de negócio) ou `/map-dependencies` (grafo CALLNAT). Esta orientação não abre arquivos.
