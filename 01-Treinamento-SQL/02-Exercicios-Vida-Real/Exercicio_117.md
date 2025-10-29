# Exercício 117: Analítico das Contas em Atraso

## 📝 Pergunta

Crie um relatório analítico das contas em atraso. Mostre:

- `id_cliente` (ID do cliente)
- `nm_cliente` (Nome do cliente)
- `nm_fantasia` (Origem comercial)
- `qtd_bloqueios_atraso` (Quantidade de bloqueios por atraso/cobrança)
- `dt_vencimento_fatura_atraso` (Data de vencimento da fatura em atraso)
- `vl_fatura_atraso` (Valor da fatura em atraso)
- `dt_ultimo_pagamento` (Data do último pagamento)
- `vl_ultimo_pagamento` (Valor do último pagamento)

Considere clientes que estão em cobrança ativa (`t_cobranca` com `fl_ativo = 'S'`) ou têm bloqueios ativos relacionados a atraso. Ordene por valor da fatura em atraso (decrescente).

## 🎯 Objetivo

Demanda da área de cobrança para priorizar ações de recuperação e negociação.

## 💡 Contexto de Negócio

Este relatório permite à equipe de cobrança focar nos casos mais críticos e personalizar abordagens de negociação.

---

## ✍️ Sua Resposta

```sql
WITH cliente AS ( 
SELECT
    tc.id_cliente,
    tc.nm_cliente,
    tr.nm_fantasia
FROM decisionscard.t_rede tr             JOIN decisionscard.t_cliente tc
ON tc.id_origem_comercial = tr.id_rede   JOIN decisionscard.t_bloqueio_cliente tbc 
ON tbc.id_cliente = tc.id_cliente        JOIN decisionscard.t_tipo_bloqueio_cliente ttbc 
ON ttbc.id_tipo_bloqueio_cliente = tbc.id_tipo_bloqueio_cliente
WHERE ttbc.id_tipo_bloqueio_cliente IN (25, 2, 9)
GROUP BY tc.id_cliente, tr.nm_fantasia
),
qnt_bloqueios AS ( 
SELECT 
    c.id_cliente,
    COUNT(id_bloqueio_cliente)                                             AS qtd_bloqueios_atraso 
FROM cliente c JOIN decisionscard.t_bloqueio_cliente tbc 
ON c.id_cliente = tbc.id_cliente
GROUP BY c.id_cliente
),
dt_ultimo_pgmt AS (
SELECT 
    c.id_cliente,
    MAX(tpf.dt_pagamento::date)                                                    AS dt_ultimo_pagamento,
    MAX(tpf.vl_pagamento)                                                          AS vl_ultimo_pagamento
FROM cliente c JOIN decisionscard.t_pagamento_fatura tpf
ON c.id_cliente = tpf.id_cliente
GROUP BY c.id_cliente
),
fatura AS ( 
SELECT 
    id_cliente,
    dt_vencimento::date                                                 AS dt_vencimento_fatura_atraso,
    vl_fatura                                                           AS vl_fatura_atraso
FROM decisionscard.t_fatura 
WHERE id_fatura NOT IN  (SELECT id_fatura FROM decisionscard.t_pagamento_fatura) AND vl_fatura != 0 AND fl_ativa = 'S'
)
SELECT 
    c.id_cliente,
    c.nm_cliente,
    c.nm_fantasia,
    qb.qtd_bloqueios_atraso,
    f.dt_vencimento_fatura_atraso,
    f.vl_fatura_atraso,
    dup.dt_ultimo_pagamento,
    dup.vl_ultimo_pagamento
FROM cliente c                   JOIN qnt_bloqueios qb
ON c.id_cliente = qb.id_cliente  JOIN dt_ultimo_pgmt dup
ON c.id_cliente = dup.id_cliente JOIN fatura f
ON c.id_cliente = f.id_cliente
ORDER BY vl_fatura_atraso DESC;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Identifica clientes em cobrança/atraso
- [ ] JOINs com múltiplas tabelas (cliente, rede, cobrança, fatura, pagamento)
- [ ] Conta bloqueios por atraso
- [ ] Identifica último pagamento por cliente

