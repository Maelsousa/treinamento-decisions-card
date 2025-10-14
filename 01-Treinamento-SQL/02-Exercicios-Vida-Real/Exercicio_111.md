# Exercício 111: Ativação por Safra (1º, 2º, 3º Mês+)

## 📝 Pergunta

Analise a ativação de clientes por safra de cadastro. Para cada mês de cadastro dos últimos 6 meses, mostre:

- `mes_cadastro` (YYYY-MM)
- `total_cadastrados`
- `ativados_mes1` (fizeram primeira compra no 1º mês após cadastro)
- `ativados_mes2` (primeira compra no 2º mês)
- `ativados_mes3_plus` (primeira compra no 3º mês ou depois)
- `taxa_ativacao_mes1` (%)
- `taxa_ativacao_mes2` (%)
- `taxa_ativacao_mes3_plus` (%)
- `taxa_ativacao_total` (% do total que se ativou)

## 🎯 Objetivo

Demanda de CRM para entender o padrão de ativação dos clientes e otimizar campanhas de onboarding.

## 💡 Contexto de Negócio

Entender quando os clientes se ativam após o cadastro permite personalizar campanhas e identificar o momento ideal para ações de retenção.

---

## ✍️ Sua Resposta

```sql
WITH cliente_cadastro AS (
    SELECT
        id_cliente,
        dt_cadastro::DATE                                                                                              AS date_cadastro,
        TO_CHAR (dt_cadastro, 'YYYY-MM' )                                                                              AS mes_cadastro
    FROM decisionscard.t_cliente
    WHERE dt_cadastro >= (SELECT MAX(dt_cadastro) - INTERVAL '6 MONTH' FROM decisionscard.t_cliente tc)
),
primeira_compra AS (
    SELECT
        id_cliente,
        MIN(dt_venda)::DATE                                                                                         AS date_venda
    FROM decisionscard.t_venda
    GROUP BY id_cliente
),
dados_completos AS (
    SELECT
        cc.id_cliente,
        cc.date_cadastro,
        cc.mes_cadastro,
        pc.date_venda,
        CASE
            WHEN pc.date_venda IS NOT NULL
            THEN DATE_PART('year', AGE(pc.date_venda, cc.date_cadastro))*12 + DATE_PART('month', AGE(pc.date_venda, cc.date_cadastro))
            ELSE NULL
        END AS meses_para_ativacao
    FROM cliente_cadastro cc LEFT JOIN primeira_compra pc
    ON cc.id_cliente = pc.id_cliente
),
relatorios AS (
SELECT
    mes_cadastro,
    COUNT(id_cliente)                                                                                                  AS total_cadastrados,
    COUNT(*) FILTER (WHERE meses_para_ativacao = 0)                                                                    AS ativados_mes1,
    COUNT(*) FILTER (WHERE meses_para_ativacao = 1)                                                                    AS ativados_mes2,
    COUNT(*) FILTER (WHERE meses_para_ativacao >= 2)                                                                   AS ativados_mes3_plus,
    ROUND(
    (COUNT(*) FILTER (WHERE meses_para_ativacao = 0)::NUMERIC / COUNT(id_cliente)::NUMERIC *100)
    ,2)                                                                                                                AS taxa_ativacao_mes1,
    ROUND(
    (COUNT(*) FILTER (WHERE meses_para_ativacao = 1)::NUMERIC / COUNT(id_cliente)::NUMERIC *100)
    ,2)                                                                                                                AS taxa_ativacao_mes2,
    ROUND(
    (COUNT(*) FILTER (WHERE meses_para_ativacao = 2)::NUMERIC / COUNT(id_cliente)::NUMERIC *100)
    ,2)                                                                                                                AS taxa_ativacao_mes3_plus
FROM dados_completos
GROUP BY mes_cadastro
)
SELECT *
FROM relatorios
UNION ALL
SELECT
    '2023-06'                                                                                                          AS mes_cadastro,
    0                                                                                                                  AS total_cadastrados,
    0                                                                                                                  AS ativados_mes1,
    0                                                                                                                  AS ativados_mes2,
    0                                                                                                                  AS ativados_mes3_plus,
    0                                                                                                                  AS taxa_ativacao_mes1,
    0                                                                                                                  AS taxa_ativacao_mes2,
    0                                                                                                                  AS taxa_ativacao_mes3_plus
    ORDER BY mes_cadastro;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Identifica primeira compra por cliente
- [ ] Calcula diferença em meses corretamente
- [ ] Categoriza ativação por período
- [ ] Calcula todas as taxas solicitadas
