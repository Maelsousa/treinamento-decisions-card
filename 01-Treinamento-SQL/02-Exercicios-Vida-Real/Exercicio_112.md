# Exercício 112: Ranking dos Clientes por Quantidade/Valor de Compras

## 📝 Pergunta

Crie um ranking dos top 50 clientes por valor de compras. Mostre:

- `posicao` (ranking)
- `id_cliente`
- `nm_cliente`
- `qtd_compras` (número de vendas ativas)
- `valor_total` (soma das vendas)
- `ticket_medio` (valor médio por compra)
- `primeira_compra` (data da primeira compra)
- `ultima_compra` (data da última compra)
- `dias_cliente` (dias entre primeira e última compra)
- `frequencia_compra` (compras por mês em média)

Considere apenas vendas ativas e ordene por valor total decrescente.

## 🎯 Objetivo

Demanda comercial para identificar clientes VIP e personalizar estratégias de relacionamento.

## 💡 Contexto de Negócio

Conhecer os melhores clientes permite criar programas de fidelidade e ações comerciais direcionadas para maximizar o lifetime value.

---

## ✍️ Sua Resposta

```sql
WITH ranking AS( 
SELECT 
    tc.id_cliente,
    tc.nm_cliente,
    SUM(tv.vl_venda)                                                        AS valor_total,
    ROW_NUMBER() OVER(ORDER BY SUM(vl_venda) DESC)                          AS posicao,
    COUNT(tv.id_venda)                                                      AS qtd_compras,
    MIN(tv.dt_venda)::DATE                                                  AS primeira_compra,
    MAX(tv.dt_venda)::DATE                                                  AS ultima_compra,
    (MAX(tv.dt_venda)::DATE - MIN(tv.dt_venda)::DATE)                       AS dias_cliente,
       ROUND(
    (SUM(tv.vl_venda) / COUNT(tv.id_venda)) 
    ,2)                                                                     AS ticket_medio,
    DATE_PART('year', AGE(MAX(dt_venda)::DATE, MIN(dt_venda)::DATE))*12 + 
    	DATE_PART('MONTH', AGE(MAX(dt_venda)::DATE, MIN(dt_venda)::DATE))   AS mes_venda
FROM decisionscard.t_venda tv JOIN decisionscard.t_cliente tc 
ON tv.id_cliente = tc.id_cliente 
WHERE tc.fl_status_conta = 'A'
GROUP BY tc.id_cliente , tc.nm_cliente
LIMIT 50
)
SELECT
    posicao,
    id_cliente,
    nm_cliente,
    qtd_compras,
    valor_total,
    ticket_medio,
    dias_cliente,
    COALESCE ( 
    ROUND( 
        qtd_compras::NUMERIC / NULLIF(mes_venda, 0)::NUMERIC 
       ,2)
             )                                                              AS frequencia_compra
FROM ranking;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Ranking correto (ROW_NUMBER)
- [ ] Todas as métricas calculadas
- [ ] Cálculo de frequência de compra
- [ ] Limitação aos top 50

