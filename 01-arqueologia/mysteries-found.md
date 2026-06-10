<!-- markdownlint-disable MD013 MD025 MD026 MD028 MD029 MD034 MD040 MD051 MD060 -->

# Mistérios Encontrados — SIFAP Legado

![ESTÁGIO 01 Arqueologia](https://img.shields.io/badge/ESTÁGIO-01%20Arqueologia-F25022?style=for-the-badge) ![TIPO Worksheet](https://img.shields.io/badge/TIPO-Worksheet-1A1A1A?style=for-the-badge) ![PREENCHA Durante S1](https://img.shields.io/badge/PREENCHA-Durante%20S1-737373?style=for-the-badge)

> 🗺 **Você está aqui:** [Kit PT-BR](../README.md) → [Estágio 1](README.md) → **mysteries-found**

> **Para quem é isto?** Este é um **artefato preenchido pelo time** durante o Estágio 1 (Arqueologia).
>
> **O que você terá ao final do estágio:**
>
> 1. Este documento totalmente preenchido com os dados reais do legado SIFAP
> 2. Rastreabilidade para `01-arqueologia/legado-sifap/` (programas `.NSN` e DDMs)
> 3. Base de evidência usada nas EARS do Estágio 2 (`source_legacy:`)
>
> 📘 **Guia passo a passo:** [`GUIDE.md`](GUIDE.md).

> Registre aqui toda lógica, comportamento ou código que o time não conseguiu explicar.
> "Mistérios" são trechos de código sem documentação, com lógica não-óbvia ou que parecem workarounds.
>
> **Cota mínima para passar pelo portão do Estágio 2:** 5 mistérios documentados.

## O que conta como "mistério"?

- Código que faz algo inesperado sem comentário explicando por quê
- Valores hardcoded sem explicação (números mágicos)
- Lógica condicional que parece um workaround ou gambiarra
- Campos no DDM que não são usados por nenhum programa
- Programas que existem mas não são chamados por ninguém
- Comportamento diferente entre o que a documentação diz e o que o código faz
- Easter eggs deixados pelos desenvolvedores originais

## Níveis de Confiança

| Nível     | Significado                                         |
| --------- | --------------------------------------------------- |
| **ALTA**  | Temos certeza de que há algo estranho aqui          |
| **MÉDIA** | Parece suspeito, mas pode ter explicação            |
| **BAIXA** | Pode ser intencional, mas não conseguimos confirmar |

## Catálogo de Mistérios — Estágio 1

**Resumo:** Total de mistérios: **22** | Bloqueadores (Crítico): **7** | Investigação necessária (Alto): **3** | Facilitador (Médio): **8** | Estacionados (Baixo): **4**

> Fontes escaneadas: todos os artefatos sob `01-arqueologia/` — marcadores `<!-- mystery: ... -->` em [`business-rules-catalog.md`](business-rules-catalog.md) e itens não resolvidos (`[A COMPLETAR]`, `(?)`, "Não documentad\*") em [`legado-sifap/README.md`](legado-sifap/README.md), [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md) e [`legacy-docs/MANUAL-TECNICO-SIFAP-2008.md`](legado-sifap/legacy-docs/MANUAL-TECNICO-SIFAP-2008.md). Ordenado por severidade (Crítico primeiro).

Classificações: **blocks-stage-2** (resolver antes das EARS) · **needs-investigation** (resposta provável na codebase) · **needs-facilitator** (conhecimento de domínio, fora do código) · **parked** (fora de escopo do hackathon).

| ID      | Descrição                                                                                                                                                                                    | Fonte                                                                                 | Classificação       | Severidade | Ação sugerida                                                                                                                                                          |
| ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- | ------------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| MYS-001 | Fórmula do benefício é **multiplicativa** no código (base × fator_reg × fator_fam × fator_renda × fator_idade) mas a doc RN-013 descreve fórmula **aditiva** (base + acréscimo×dependentes). | `CALCBENF.NSN#L230-L240`, `BATCHPGT.NSN#L296-L305`, RN-013 (REGRAS-NEGOCIO-2012 §2.1) | blocks-stage-2      | Crítico    | Decisão de domínio com facilitador SENARC/CGPB: qual fórmula é a verdadeira? Sem isso, a EARS de cálculo é não-confiável.                                              |
| MYS-002 | Reajuste do programa é aplicado sobre o **valor total** no código, mas RN-020 diz que incide só sobre o **valor base**.                                                                      | `CALCBENF.NSN#L237-L240`, `BATCHPGT.NSN#L297`, RN-020                                 | blocks-stage-2      | Crítico    | Confirmar base de incidência do reajuste com facilitador antes de especificar.                                                                                         |
| MYS-003 | `BATCHPGT` aplica desconto **inline de 3%** (bruto > 500) enquanto `CALCDSCT` aplica tabela progressiva (3/5/7/9%) + teto 30%. Qual governa o pagamento real?                                | `BATCHPGT.NSN#L330-L335`, `CALCDSCT.NSN#L57-L176`                                     | blocks-stage-2      | Crítico    | Investigar a ordem de execução batch (BATCHPGT chama CALCDSCT?) e qual valor persiste em `PAGAMENTO.VLR-DESCONTO`.                                                     |
| MYS-004 | Pagamento é gravado com status **`'G'`** (gerado) no código, mas a doc §5.1 afirma status **`'P'`** (pendente). Máquina de estados divergente.                                               | `BATCHPGT.NSN#L344-L356`, REGRAS-NEGOCIO-2012 §5.1                                    | blocks-stage-2      | Crítico    | Mapear a máquina de estados completa (G→P→C/D/E) cruzando `BATCHCON` e `BATCHREL` antes da EARS.                                                                       |
| MYS-005 | Arredondamento **inconsistente**: o cálculo **trunca** (RN-014) mas o relatório `BATCHREL` **arredonda** (soma 0,005). O próprio código admite a divergência.                                | `BATCHREL.NSN#L130-L140`, RN-014                                                      | blocks-stage-2      | Crítico    | Definir política única de arredondamento; quantificar impacto financeiro acumulado (centavos × volume).                                                                |
| MYS-006 | Limite de dependentes é **5** no código (`CADDEPEND`) mas RN-004 e o Manual 2008 dizem **3**.                                                                                                | `CADDEPEND.NSN#L62-L66`, RN-004                                                       | blocks-stage-2      | Crítico    | Verificar o número de ocorrências do grupo PE `DEPENDENTES` em `adabas-ddms/BENEFICIARIO.ddm` e decidir o limite oficial.                                              |
| MYS-007 | Beneficiários da **região 99** pulam **toda** a validação de elegibilidade (`VALELEG`). Doc confirma origem desconhecida ("bypass do Roberto").                                              | `VALELEG.NSN#L104-L110`, REGRAS-NEGOCIO-2012 §4.2 (nota)                              | blocks-stage-2      | Crítico    | Falha de segurança: decidir com facilitador se o bypass é migrado, removido ou substituído por papel/autorização explícita.                                            |
| MYS-008 | Mapeamento de códigos de retorno CNAB (`00`→pago, `01`→devolvido, `02`→estornado) sem documentação.                                                                                          | `BATCHCON.NSN#L188-L214`                                                              | needs-investigation | Alto       | Procurar layout CNAB 240 em [`MANUAL-TECNICO-SIFAP-2008.md`](legado-sifap/legacy-docs/MANUAL-TECNICO-SIFAP-2008.md) e tabela de códigos de retorno do Banco do Brasil. |
| MYS-009 | Tabela de índices IPCA é **hardcoded** e cobre apenas **2010-2012** (última carga 2014); períodos fora disso podem corrigir por zero.                                                        | `CALCCORR.NSN#L52-L102`                                                               | needs-investigation | Alto       | Procurar o subprograma `CALCIDX` (citado na doc RN-019) e a origem externa dos índices IPCA.                                                                           |
| MYS-010 | Máscara de CPF **inconsistente** entre `CONSBENF` (depende do tamanho armazenado; às vezes mostra os 3 primeiros dígitos) e `RELPGT` (oculta os 3 primeiros). Risco de exposição de PII.     | `CONSBENF.NSN#L168-L190`, `RELPGT.NSN#L108-L116`                                      | needs-investigation | Alto       | Comparar as subrotinas `MASCARA-CPF` dos dois programas; padronizar mascaramento (ocultar sempre os 9 primeiros).                                                      |
| MYS-011 | `CADBENEF` muda silenciosamente o status para **`'S'`** (suspenso) quando idade > 75 (comentário "AJUSTE STATUS IDOSO").                                                                     | `CADBENEF.NSN#L172-L175`                                                              | needs-facilitator   | Médio      | Perguntar a facilitador/SENARC a intenção: regra real ou bug? Idosos costumam ter prioridade, não suspensão.                                                           |
| MYS-012 | Constante **`FATOR-K = 0.347215`** ajusta o valor base do programa antes de gravar; doc RN §6 confirma que ninguém soube explicar a origem.                                                  | `CADPROG.NSN#L86-L89`, REGRAS-NEGOCIO-2012 §6                                         | needs-facilitator   | Médio      | Levar a constante ao facilitador; impacto alto (altera base usada por CALCBENF), mas a origem não está no código.                                                      |
| MYS-013 | Tabela de **27 fatores regionais** hardcoded (1.05 a 1.40) replicada em `CALCBENF` e `BATCHPGT`, sem documentação de critério/origem.                                                        | `CALCBENF.NSN#L83-L119`, `BATCHPGT.NSN#L120-L150`                                     | needs-facilitator   | Médio      | Confirmar com domínio o critério dos fatores por UF/região e quem os mantém.                                                                                           |
| MYS-014 | Fatores etários (≥65→1.15; ≥60→1.10; <18→1.05; senão 1.00) sem origem documentada; benefício maior para <18 que para 18-59 é contraintuitivo.                                                | `CALCBENF.NSN#L213-L227`, `BATCHPGT.NSN#L281-L293`                                    | needs-facilitator   | Médio      | Validar tabela etária com gestor do programa.                                                                                                                          |
| MYS-015 | CPFs aceitos **sem validação real**: dígitos todos iguais iniciando em `000` (`VALBENEF`) e 8 prefixos especiais `000/001/002/010/011/099/100/999` que zeram erros (`VALDOCS`).              | `VALBENEF.NSN#L194-L210`, `VALDOCS.NSN#L50-L58`, `VALDOCS.NSN#L172-L185`              | needs-facilitator   | Médio      | Risco de segurança: confirmar se são CPFs de teste/governo legítimos ou backdoor a remover.                                                                            |
| MYS-016 | Eventos de auditoria com ação **`'EX'`** (exclusão) **nunca** aparecem nos relatórios (comentário "LIMPEZA RELATORIO", 2014).                                                                | `RELAUDIT.NSN#L100-L110`                                                              | needs-facilitator   | Médio      | Risco de conformidade: confirmar se ocultar exclusões da trilha é intencional ou bug; provável violação de auditoria.                                                  |
| MYS-017 | Ordem de processamento do batch é por **CPF** (código), mas a doc §5.1 diz alfabética por **nome**; comentário afirma que sistemas downstream dependem da ordem.                             | `BATCHPGT.NSN#L182-L190`, REGRAS-NEGOCIO-2012 §5.1 (nota)                             | needs-facilitator   | Médio      | Levantar com operação quais sistemas downstream dependem da ordenação e se totalizadores parciais quebram.                                                             |
| MYS-018 | Subprograma `LOGAUDIT` é chamado por quase todos os programas, mas seu comportamento varia por parâmetros não documentados; não está entre os 15 `.NSN` do inventário.                       | `legado-sifap/README.md` §9.4                                                         | needs-facilitator   | Médio      | Código-fonte do subprograma indisponível no repositório; obter com a equipe legada.                                                                                    |
| MYS-019 | Transações **SF10/SF11** marcadas `[A COMPLETAR]`/`(?)` no Manual Técnico 2008.                                                                                                              | `legacy-docs/MANUAL-TECNICO-SIFAP-2008.md` §mapa de transações                        | parked              | Baixo      | SF10 provavelmente = `RELAUDIT` (ver `legado-sifap/README.md` §4.5); baixo impacto na modernização.                                                                    |
| MYS-020 | Integração com **CadÚnico** (RN-016): programa não catalogado e código-fonte não localizado.                                                                                                 | REGRAS-NEGOCIO-2012 §4.1 (RN-016)                                                     | parked              | Baixo      | Fora do escopo (sem código no repositório); registrar como lacuna conhecida.                                                                                           |
| MYS-021 | Cálculo **pro rata** (benefício iniciado no meio do mês) mencionado por Marcos Antônio, nunca detalhado.                                                                                     | REGRAS-NEGOCIO-2012 §2.1 (nota)                                                       | parked              | Baixo      | Sem evidência no código fornecido; perguntar a facilitador se está no escopo.                                                                                          |
| MYS-022 | **GDAs compartilhadas** e ordem de execução dos jobs batch existem só no JCL de produção / memória operacional.                                                                              | `legado-sifap/README.md` §9.4                                                         | parked              | Baixo      | JCL não está no repositório; documentar como dependência operacional externa.                                                                                          |

## Detalhamento dos Mistérios Bloqueadores (Crítico)

### MYS-001: Fórmula multiplicativa vs aditiva

- **Arquivo**: `01-arqueologia/legado-sifap/natural-programs/CALCBENF.NSN#L230-L240`
- **O que esperávamos**: fórmula aditiva `VALOR = BASE + (ACRESCIMO × DEPENDENTES)` (RN-013 da doc 2012).
- **O que o código faz**: produto de cinco fatores `BASE × fator_reg × fator_fam × fator_renda × fator_idade`, depois `× (1 + fator_reajuste)`.
- **Hipótese do time**: a doc de 2012 é parcial/desatualizada; o código é a fonte da verdade — porém não confirmado.
- **Risco se ignorarmos**: erro sistemático no valor de todos os benefícios da modernização.

### MYS-003: Dois cálculos de desconto concorrentes

- **Arquivo**: `01-arqueologia/legado-sifap/natural-programs/BATCHPGT.NSN#L330-L335` e `CALCDSCT.NSN#L57-L176`
- **O que esperávamos**: um único motor de descontos.
- **O que o código faz**: `BATCHPGT` grava 3% inline; `CALCDSCT` recalcula com tabela progressiva + teto de 30% (exceção judicial).
- **Hipótese do time**: `CALCDSCT` (2015) sobrepõe o desconto do batch em etapa posterior — não verificado no fluxo.
- **Risco se ignorarmos**: descontos duplicados ou divergentes entre o que é gerado e o que é cobrado.

### MYS-004 / MYS-006 / MYS-007: Estado, capacidade e bypass

- **MYS-004** — `BATCHPGT.NSN#L344-L356`: status `'G'` no código vs `'P'` na doc → mapear máquina de estados antes da EARS.
- **MYS-006** — `CADDEPEND.NSN#L62-L66`: limite 5 no código vs 3 na doc → confirmar ocorrências PE em `BENEFICIARIO.ddm`.
- **MYS-007** — `VALELEG.NSN#L104-L110`: região 99 ignora toda elegibilidade → decisão de segurança com facilitador.

---

> Detalhes adicionais por regra estão em [`business-rules-catalog.md`](business-rules-catalog.md), nos marcadores `<!-- mystery: ... -->` de cada programa.

## Easter Eggs

> Dica: existem **3 easter eggs** escondidos no código legado. Registre aqui os que encontrar (candidatos, a confirmar com facilitador):

1. [x] Easter Egg 1: **Bypass administrativo da região 99** em `VALELEG.NSN#L104-L110` — desvia toda a validação; doc o atribui a "bypass do Roberto". (ver MYS-007)
2. [x] Easter Egg 2: **CPFs "de teste/governo" sempre aceitos** — dígitos iguais iniciando em `000` (`VALBENEF.NSN#L194-L210`) e prefixos especiais em `VALDOCS.NSN#L50-L58`. (ver MYS-015)
3. [ ] Easter Egg 3 (candidato): **`FATOR-K = 0.347215`** em `CADPROG.NSN#L86-L89` — constante "mágica" sem origem conhecida. (ver MYS-012)

## Resumo

- Total de mistérios encontrados: **22**
- Bloqueadores do Estágio 2 (Crítico): **7**
- Precisa de investigação (Alto): **3**
- Precisa de facilitador (Médio): **8**
- Estacionados (Baixo): **4**
- Easter eggs encontrados: **2** confirmados + **1** candidato / 3

---

### Continuar a leitura

<table width="100%">
<tr>
<td width="50%" valign="top" align="left">
<sub><strong>← ANTERIOR</strong></sub><br/>
<a href="mysteries-checklist.md"><strong>mysteries-checklist.md</strong></a><br/>
<sub>Lista do que procurar.</sub>
</td>
<td width="50%" valign="top" align="right">
<sub><strong>PRÓXIMO →</strong></sub><br/>
<a href="discovery-report.md"><strong>discovery-report.md</strong></a><br/>
<sub>Síntese final.</sub>
</td>
</tr>
</table>

<sub>↑ <a href="README.md">Voltar ao Kit PT-BR</a></sub>
