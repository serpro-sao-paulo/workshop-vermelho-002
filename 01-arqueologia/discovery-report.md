<!-- markdownlint-disable MD013 MD025 MD026 MD028 MD029 MD034 MD040 MD051 MD060 -->

# Relatório de Descoberta — Estágio 1: Arqueologia Digital

![ESTÁGIO 01 Arqueologia](https://img.shields.io/badge/ESTÁGIO-01%20Arqueologia-F25022?style=for-the-badge) ![TIPO Worksheet](https://img.shields.io/badge/TIPO-Worksheet-1A1A1A?style=for-the-badge) ![PREENCHA Durante S1](https://img.shields.io/badge/PREENCHA-Durante%20S1-737373?style=for-the-badge)

> 🗺 **Você está aqui:** [Kit PT-BR](../README.md) → [Estágio 1](README.md) → **discovery-report**

> **Para quem é isto?** Este é um **artefato preenchido pelo time** durante o Estágio 1 (Arqueologia).
>
> **O que você terá ao final do estágio:**
>
> 1. Este documento totalmente preenchido com os dados reais do legado SIFAP
> 2. Rastreabilidade para `01-arqueologia/legado-sifap/` (programas `.NSN` e DDMs)
> 3. Base de evidência usada nas EARS do Estágio 2 (`source_legacy:`)
>
> 📘 **Guia passo a passo:** [`GUIDE.md`](GUIDE.md).

> Este documento consolida todas as descobertas do Estágio 1.
> Preencha cada seção com as conclusões do time. **Este é o input principal do Estágio 2** — sem ele, a especificação vira chute.

**Time**: Team 07
**Data**: 10/06/2026
**Edição**: 1.0
**Participantes**: [Liste os membros e suas personas]

---

## 1. Sumário Executivo

O SIFAP é um sistema crítico Natural/Adabas de ~29 anos para cálculo e pagamento de benefícios sociais federais, composto por **15 programas `.NSN`** e **4 DDMs** (ver [inventory.md](inventory.md)), do qual extraímos **86 regras de negócio candidatas** — cerca de **30 confirmadas** contra a doc parcial de 2012 (ver [business-rules-catalog.md](business-rules-catalog.md)). O sistema é surpreendentemente **desacoplado no nível de código**: não há nenhuma chamada `CALLNAT`/`INCLUDE`; o reúso ocorre por **duplicação de lógica** e o único acoplamento entre componentes é via **DDMs Adabas compartilhados** (ver [dependency-map.md](dependency-map.md)). O maior risco para o Estágio 2 é a **divergência da fórmula de cálculo do benefício** entre código (multiplicativa) e documentação (aditiva), somada a 6 outros bloqueadores críticos (ver [mysteries-found.md](mysteries-found.md), MYS-001 a MYS-007). Confiança para modernização: **Média** — a estrutura de dados é clara, mas as regras financeiras centrais exigem validação de domínio antes de virarem EARS confiáveis.

---

## 2. Visão Geral do Sistema

### 2.1 Propósito do SIFAP

Sistema de Fiscalização e Administração de Pagamentos: gestão de beneficiários, cálculo e processamento da folha mensal de benefícios sociais, descontos/deduções, conciliação bancária (CNAB 240) e trilha de auditoria. Fonte: [inventory.md](inventory.md).

### 2.2 Arquitetura Legada

**15 programas Natural** agrupados por prefixo (3 BATCH, 3 CAD, 3 CALC, 3 VAL, 2 REL, 1 CONS) e **4 DDMs Adabas** (BENEFICIARIO, PAGAMENTO, PROGRAMA-SOCIAL, AUDITORIA). **Zero arestas programa→programa**; **45 arestas programa→dados** verificadas (FIND/READ/STORE/UPDATE), além de 1 WORK FILE (CNAB 240). Dois programas — `VALBENEF` e `VALDOCS` — são **isolados** (só validam entrada, sem acesso a DDM). Fonte: [dependency-map.md](dependency-map.md).

### 2.3 Usuários e Perfis

Programas online (telas 3270) usados por operadores da CGPB/DEFIS; programas batch agendados (folha mensal, conciliação, relatórios). A doc cita perfil SUPERVISOR para alteração de CPF (RN-009), mas a autorização não aparece no código fornecido. Fonte: [inventory.md](inventory.md), [legado-sifap/README.md](legado-sifap/README.md).

---

## 3. Principais Descobertas

### 3.1 Regras de Negócio Críticas

> As 5 regras confirmadas mais relevantes (com candidato EARS). Ver [business-rules-catalog.md](business-rules-catalog.md).

1. **Teto de 30% para descontos, exceto judicial (tipo `J`)** — _"Se o desconto não for judicial e o total exceder 30% do bruto, então o sistema deverá limitar o total ao teto."_ (`CALCDSCT.NSN#L170-L176`; RN-021). Confirmada.
2. **Somente beneficiários com status `'A'` (ativo) recebem pagamento** — _"Se o beneficiário não estiver ativo, o sistema deverá ignorá-lo na folha."_ (`BATCHPGT.NSN#L205-L208`; RN §5.1). Confirmada.
3. **Truncamento (não arredondamento) para 2 casas decimais** — _"O sistema deverá truncar o valor do benefício em centavos."_ (`CALCBENF.NSN#L243-L246`; RN-014). Confirmada.
4. **CPF único e válido (Módulo 11) para beneficiário ativo** — _"Se o CPF for inválido ou já existir ativo, então o sistema deverá rejeitar a inclusão."_ (`CADBENEF.NSN#L113-L152`; RN-001/RN-002). Confirmada.
5. **Faixa de renda pela primeira faixa cujo teto ≥ renda declarada** — _"O sistema deverá aplicar a primeira faixa de renda cujo limite superior seja maior ou igual à renda familiar."_ (`CALCBENF.NSN#L130-L142`; RN-018). Confirmada.

### 3.2 Dependências Complexas

Não há acoplamento por chamada; o ponto de **efeito cascata** é o DDM **PAGAMENTO**, escrito/atualizado por 5 programas (`CALCBENF`, `CALCCORR`, `CALCDSCT`, `BATCHPGT`, `BATCHCON`) — qualquer mudança de esquema ou de máquina de estados impacta todos. O DDM **BENEFICIARIO** é lido por 8 programas. Fonte: [dependency-map.md](dependency-map.md).

### 3.3 Dívida Técnica Identificada

- [ ] **Lógica duplicada**: tabela de 27 fatores regionais e faixas de renda copiadas em `CALCBENF` e `BATCHPGT` (risco de divergência silenciosa).
- [ ] **Múltiplas implementações de validação de CPF** (Módulo 11) em `CADBENEF`, `VALBENEF`, `VALDOCS`, com regras de exceção diferentes.
- [ ] **Código morto mantido** (integração Banco Real em `BATCHCON`; correção Plano Verão em `CALCCORR`) e tabelas hardcoded desatualizadas (IPCA 2010-2012).

### 3.4 Gaps de Documentação

A doc de 2012 é explicitamente **parcial e não validada**: fórmula de cálculo incompleta, 13º/abono natalino, FATOR-K, conciliação, integração CadÚnico, pro rata e exceção judicial constam como pendentes. O subprograma `LOGAUDIT` e o JCL de orquestração batch não estão no repositório. Fonte: [legacy-docs/REGRAS-NEGOCIO-2012.md](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md) §6, [legado-sifap/README.md](legado-sifap/README.md) §9.4.

---

## 4. Mistérios e Riscos

### 4.1 Mistérios Não Resolvidos

> Bloqueadores do Estágio 2 (Crítico). Catálogo completo em [mysteries-found.md](mysteries-found.md).

| ID      | Descrição                                                                                    | Risco para Migração                              |
| ------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| MYS-001 | Fórmula do benefício: código **multiplicativo** vs doc **aditiva** (RN-013)                  | Erro sistemático no valor de todos os benefícios |
| MYS-002 | Reajuste incide sobre **total** (código) vs **base** (RN-020)                                | Valor reajustado incorreto                       |
| MYS-003 | Dois motores de desconto concorrentes (3% inline no `BATCHPGT` vs progressivo no `CALCDSCT`) | Desconto duplicado/divergente                    |
| MYS-004 | Status de pagamento `'G'` (código) vs `'P'` (doc)                                            | Máquina de estados incorreta                     |
| MYS-005 | Arredondamento inconsistente (trunca no cálculo, arredonda no relatório)                     | Perda/divergência de centavos em escala          |
| MYS-006 | Limite de dependentes 5 (código) vs 3 (doc/DDM)                                              | Regra de capacidade ambígua                      |
| MYS-007 | Região 99 ignora **toda** a elegibilidade (`VALELEG`)                                        | Falha de segurança / bypass                      |

### 4.2 Riscos para o Estágio 2

1. **Regras `inferred` (só código, sem suporte documental)** não devem virar EARS sem validação — ex.: dedup por CPF consecutivo (`BATCHPGT`), faixas de contribuição social (`CALCDSCT`), mapeamento de macrorregiões (`BATCHREL`). Ver [business-rules-catalog.md](business-rules-catalog.md).
2. **Magic numbers sem origem** (FATOR-K=0,347215; fatores regionais/etários; índices IPCA) precisam de facilitador de domínio.
3. **Backdoors de segurança** (região 99; CPFs de teste com prefixo `000`/lista especial) exigem decisão explícita de migrar/remover.

---

## 5. Hipóteses de Recorte (Bounded Contexts)

> **São hipóteses, não decisões.** Derivadas dos clusters de acesso a DDM em [dependency-map.md](dependency-map.md). O `@architect-agent` avaliará no Estágio 2.

### 5.1 O que migrar primeiro

| Prioridade | Funcionalidade                                                                                                       | Justificativa                                                      |
| ---------- | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| 1          | **Gestão de Beneficiários** (`CADBENEF`, `CADDEPEND`, `VALBENEF`, `VALDOCS`, `CONSBENF`) — DDM BENEFICIARIO          | Fronteira natural em torno do cadastro; base para todo o resto     |
| 2          | **Cálculo & Folha de Pagamento** (`CALCBENF`, `CALCDSCT`, `CALCCORR`, `BATCHPGT`) — DDMs PAGAMENTO + PROGRAMA-SOCIAL | Núcleo financeiro; concentra os mistérios críticos a resolver cedo |
| 3          | **Conciliação & Auditoria** (`BATCHCON`, `RELAUDIT`) — DDM AUDITORIA + WORK FILE CNAB                                | Integração externa (banco) e conformidade                          |

**Hipóteses de bounded context:**

- **Hipótese 1: Cadastro de Beneficiários** — programas CAD*/VAL*/CONS sobre BENEFICIARIO; fronteira por posse exclusiva do dado cadastral.
- **Hipótese 2: Cálculo de Benefícios** — CALC\*/BATCHPGT sobre PAGAMENTO+PROGRAMA-SOCIAL; fronteira por lógica financeira compartilhada (e duplicada).
- **Hipótese 3: Catálogo de Programas Sociais** — `CADPROG` sobre PROGRAMA-SOCIAL; fronteira por dado de parametrização (faixas, eleg., FATOR-K).
- **Hipótese 4: Conciliação Financeira** — `BATCHCON` sobre PAGAMENTO+AUDITORIA+CNAB; fronteira por integração bancária externa.
- **Hipótese 5: Auditoria & Relatórios** — `RELAUDIT`/`RELPGT`/`BATCHREL` (leitura); fronteira por consumo read-only e saída gerencial.

### 5.2 O que descartar

- **Integração Banco Real** (`BATCHCON`, código morto): banco extinto em 2007.
- **Correção Plano Verão 1989-1991** (`CALCCORR`, comentada): histórica, inativa.

### 5.3 O que evoluir

- **Validação de CPF**: consolidar as 3+ implementações num único serviço, sem os backdoors de teste.
- **Mascaramento de PII**: padronizar a máscara de CPF (hoje inconsistente entre `CONSBENF` e `RELPGT`).

---

## 6. Métricas do Estágio

| Métrica                       | Valor                                 |
| ----------------------------- | ------------------------------------- |
| Programas analisados          | 15 / 15                               |
| DDMs mapeados                 | 4 / 4                                 |
| Regras de negócio encontradas | 86                                    |
| Regras escondidas encontradas | 10 / 10                               |
| Easter eggs encontrados       | 2 (+1 cand.) / 3                      |
| Termos no glossário           | 0 / 30 ⚠️ (template vazio — pendente) |
| Mistérios catalogados         | 22                                    |
| Tempo total gasto             | \_\_\_ horas                          |

> Fontes: [inventory.md](inventory.md), [business-rules-catalog.md](business-rules-catalog.md), [dependency-map.md](dependency-map.md), [mysteries-found.md](mysteries-found.md), [glossary.md](glossary.md).
>
> ⚠️ **Lacuna do Estágio 1:** [glossary.md](glossary.md) ainda é um template não preenchido (meta: ≥30 termos de domínio). Rode `/archaeology-kickoff` ou extraia os termos do código legado antes da passagem ao Estágio 2 — o vocabulário de domínio é input das EARS. Os demais 4 artefatos estão completos.

---

## 7. Notas para o Próximo Estágio

> Para o time do Estágio 2 (Especificação Moderna):

NÃO escreva EARS para o cálculo de benefício (MYS-001/002), descontos (MYS-003) ou máquina de estados de pagamento (MYS-004) antes de resolver os 7 bloqueadores críticos com um facilitador de domínio. Use apenas as **regras confirmadas** de [business-rules-catalog.md](business-rules-catalog.md) como base segura; trate as `inferred` como hipóteses a validar. Decida explicitamente o destino dos backdoors de segurança (região 99, CPFs de teste) antes que virem requisito.

## Artefatos-Fonte

- [inventory.md](inventory.md) — inventário de programas/DDMs (`/archaeology-kickoff`)
- [business-rules-catalog.md](business-rules-catalog.md) — 86 regras candidatas (`/extract-business-rules`)
- [dependency-map.md](dependency-map.md) — grafo de dependências (`/map-dependencies`)
- [mysteries-found.md](mysteries-found.md) — 22 mistérios catalogados (`/catalog-mysteries`)

## Aprovação da Equipe

> Reviewed by: **\*\***\_\_\_**\*\*** · Date: **_/_**/**\_\_** · Confidence: [ ] high [ ] medium [ ] low

---

## Definição de Pronto deste relatório

- [ ] Todas as seções acima preenchidas (sem placeholders).
- [ ] Pelo menos 5 regras críticas listadas em §3.1, cada uma referenciando uma `BR-XXX` do catálogo.
- [ ] Decisões de migrar/descartar/evoluir em §5 cobrem as 8+ funcionalidades principais.
- [ ] Métricas de §6 conferem com os outros artefatos (glossary.md, business-rules-catalog.md, mysteries-found.md).

— Paula

---

### Continuar a leitura

<table width="100%">
<tr>
<td width="50%" valign="top" align="left">
<sub><strong>← ANTERIOR</strong></sub><br/>
<a href="mysteries-found.md"><strong>mysteries-found.md</strong></a><br/>
<sub>Lista de mistérios.</sub>
</td>
<td width="50%" valign="top" align="right">
<sub><strong>PRÓXIMO →</strong></sub><br/>
<a href="../02-spec-moderna/GUIDE.md"><strong>Estágio 2 — Spec</strong></a><br/>
<sub>Próximo estágio: spec moderna.</sub>
</td>
</tr>
</table>

<sub>↑ <a href="../README.md">Voltar ao Kit PT-BR</a></sub>
