<!-- markdownlint-disable MD013 MD025 MD026 MD028 MD029 MD034 MD040 MD051 MD060 -->

# Glossário do SIFAP Legado

![ESTÁGIO 01 Arqueologia](https://img.shields.io/badge/ESTÁGIO-01%20Arqueologia-F25022?style=for-the-badge) ![TIPO Worksheet](https://img.shields.io/badge/TIPO-Worksheet-1A1A1A?style=for-the-badge) ![PREENCHA Durante S1](https://img.shields.io/badge/PREENCHA-Durante%20S1-737373?style=for-the-badge)

> 🗺 **Você está aqui:** [Kit PT-BR](../README.md) → [Estágio 1](README.md) → **glossary**

> **Para quem é isto?** Este é um **artefato preenchido pelo time** durante o Estágio 1 (Arqueologia).
>
> **O que você terá ao final do estágio:**
>
> 1. Este documento totalmente preenchido com os dados reais do legado SIFAP
> 2. Rastreabilidade para `01-arqueologia/legado-sifap/` (programas `.NSN` e DDMs)
> 3. Base de evidência usada nas EARS do Estágio 2 (`source_legacy:`)
>
> 📘 **Guia passo a passo:** [`GUIDE.md`](GUIDE.md).


> Preencha esta tabela com todos os termos, abreviações e siglas encontrados no código Natural/Adabas.
> **Meta: no mínimo 30 termos.**

## Por que isso importa

Sistemas legados têm vocabulário próprio que ninguém documenta em lugar nenhum — só está no nome das variáveis. Se o time do Estágio 2 não souber o que `DSCT`, `BENF`, `PE` ou `CTC` significam, vai escrever uma spec sobre o que ele _acha_ que isso significa. Glossário é o que evita esse desencontro.

## Como preencher

- **Termo**: a abreviação ou sigla exatamente como aparece no código
- **Expansão**: o significado completo do termo
- **Programa**: em qual arquivo `.NSN` ou `.ddm` o termo foi encontrado
- **Contexto**: breve explicação de como/onde o termo é usado

## Dica de extração

Prompt útil no Copilot Chat (cole o conteúdo de 2–3 arquivos `.NSN` no chat antes):

> _"Liste todas as abreviações e siglas usadas neste código Natural. Para cada uma, sugira a expansão e marque com 'CONFIRMADO' ou 'HIPÓTESE'."_

## Termos encontrados

| #  | Termo            | Expansão                                   | Programa / DDM            | Contexto | Origem |
| -- | ---------------- | ------------------------------------------ | ------------------------- | -------- | ------ |
| 1  | SIFAP            | Sistema de Fiscalização e Administração de Pagamentos | (sistema)        | Nome do sistema legado | DOC |
| 2  | DDM              | Data Definition Module                     | adabas-ddms/*.ddm         | Definição de estrutura de arquivo Adabas | DOC |
| 3  | FDT              | Field Definition Table                     | adabas-ddms/              | Tabela de definição de campos do Adabas | DOC |
| 4  | FNR              | File Number                                | adabas-ddms/              | Número do arquivo Adabas (ex.: 150–153) | DOC |
| 5  | MU               | Multiple Value (campo multivalor)          | PROGRAMA-SOCIAL.ddm       | Campo Adabas com múltiplos valores; vira tabela à parte no PostgreSQL | DOC |
| 6  | PE               | Periodic Group (grupo periódico)           | PROGRAMA-SOCIAL.ddm       | Grupo repetido por exercício; vira tabela à parte | DOC |
| 7  | BN-              | Prefixo de campo: Beneficiário             | BENEFICIARIO.ddm          | Prefixo de nomes de campo do cadastro de beneficiário | DOC |
| 8  | PS-              | Prefixo de campo: Programa Social          | PROGRAMA-SOCIAL.ddm       | Prefixo de nomes de campo de programa social | DOC |
| 9  | PG-              | Prefixo de campo: Pagamento                | PAGAMENTO.ddm             | Prefixo de nomes de campo de pagamento | DOC |
| 10 | AU-              | Prefixo de campo: Auditoria                | AUDITORIA.ddm             | Prefixo de nomes de campo de auditoria | DOC |
| 11 | NM               | Nome                                       | DDMs                      | Abreviação de campo (ex.: BN-NM-BENEF) | DOC |
| 12 | NR               | Número                                      | DDMs                      | Abreviação de campo (ex.: BN-NR-CPF) | DOC |
| 13 | CD               | Código                                     | DDMs                      | Abreviação de campo (ex.: BN-CD-SIT) | DOC |
| 14 | DT               | Data                                       | DDMs                      | Abreviação de campo (ex.: PG-DT-CRED) | DOC |
| 15 | VL               | Valor                                      | DDMs                      | Abreviação de campo (ex.: PG-VL-BRUTO) | DOC |
| 16 | QT               | Quantidade                                 | DDMs                      | Abreviação de campo | DOC |
| 17 | SG               | Sigla                                      | DDMs                      | Abreviação de campo | DOC |
| 18 | IN               | Indicador                                  | DDMs                      | Abreviação de campo | DOC |
| 19 | BENF / BENEF     | Beneficiário                               | CADBENEF, CALCBENF, VALBENEF, CONSBENF | Raiz de entidade (duas grafias no conjunto) | NOME |
| 20 | CAD              | Cadastro                                   | CADBENEF, CADDEPEND, CADPROG | Prefixo de programas de cadastro/CRUD | NOME |
| 21 | CALC             | Cálculo                                    | CALCBENF, CALCCORR, CALCDSCT | Prefixo de programas de cálculo | NOME |
| 22 | VAL              | Validação                                  | VALBENEF, VALDOCS, VALELEG | Prefixo de programas de validação | NOME |
| 23 | REL              | Relatório                                  | RELPGT, RELAUDIT          | Prefixo de programas de relatório | NOME |
| 24 | CONS             | Consulta                                   | CONSBENF                  | Prefixo de programa de consulta | NOME |
| 25 | BATCH            | Processamento batch (não interativo)       | BATCHPGT, BATCHCON, BATCHREL | Prefixo de programas agendados | NOME |
| 26 | PGT / PG         | Pagamento                                  | BATCHPGT, RELPGT          | Raiz de entidade pagamento | NOME |
| 27 | PROG             | Programa (social)                          | CADPROG                   | Raiz de entidade programa social | NOME |
| 28 | DEPEND           | Dependente                                 | CADDEPEND                 | Dependente vinculado ao beneficiário titular | NOME |
| 29 | CORR             | Correção / reajuste                        | CALCCORR                  | Cálculo de correção por índices anuais | NOME |
| 30 | DSCT             | Desconto                                   | CALCDSCT                  | Cálculo de descontos e deduções legais | NOME |
| 31 | ELEG             | Elegibilidade                              | VALELEG                   | Validação de elegibilidade | NOME |
| 32 | DOCS             | Documentos                                 | VALDOCS                   | Validação de documentação comprobatória | NOME |
| 33 | AUDIT            | Auditoria                                  | RELAUDIT, AUDITORIA.ddm   | Trilha/relatório de auditoria | NOME |
| 34 | CPF              | Cadastro de Pessoa Física                  | VALBENEF (validação)      | Documento validado na inclusão cadastral | DOC |
| 35 | NIS / NIT        | Número de Inscrição Social / Trabalhador   | VALBENEF                  | Identificador social validado | DOC |
| 36 | CNAB             | Centro Nacional de Automação Bancária (240)| BATCHPGT/integração BB    | Layout de arquivo de remessa bancária | DOC |
| 37 | SIAFI            | Sistema Integrado de Adm. Financeira       | BATCHCON                  | Sistema externo de conciliação financeira | DOC |
| 38 | GDA              | Global Data Area                           | (vários programas)        | Área de dados global compartilhada (Natural) | DOC |
| 39 | JES2             | Job Entry Subsystem 2                       | (scheduler batch)         | Agendador de jobs do mainframe | DOC |
| 40 | 3270             | Terminal IBM 3270 (24x80)                  | programas online          | Padrão de tela dos maps Natural | DOC |

> Adicione mais linhas conforme necessário. Não se limite a 30!

## Exemplo de linha bem preenchida

| #   | Termo  | Expansão | Programa                        | Contexto                                                                                                         |
| --- | ------ | -------- | ------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| 1   | `DSCT` | Desconto | `CALCDSCT.NSN`, `PAGAMENTO.ddm` | Tipo de dedução aplicada sobre valor bruto do pagamento. Tipos: 'J' (judicial), 'I' (imposto), 'T' (trabalhista) |

## Observações

- Anote aqui qualquer padrão de nomenclatura que o time identificou:
- Convenções de prefixo/sufixo encontradas:
- Termos ambíguos que precisam de validação com especialista:

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
<a href="business-rules-catalog.md"><strong>business-rules-catalog.md</strong></a><br/>
<sub>Catálogo de regras.</sub>
</td>
</tr>
</table>

<sub>↑ <a href="README.md">Voltar ao Kit PT-BR</a></sub>

