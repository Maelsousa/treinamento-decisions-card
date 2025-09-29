# Exercício 104: Contas sem Compras há Mais de 90 Dias

## 📝 Pergunta

Identifique quantas contas não fizeram compras há mais de 90 dias. Considere a maior data de venda existente na base como referência.

## 🎯 Objetivo

Demanda da área de retenção para identificar clientes em risco de churn e criar campanhas de reativação.

## 💡 Contexto de Negócio

Clientes inativos por muito tempo têm alta probabilidade de cancelamento. Identificá-los permite ações proativas de retenção.

## 💡 Dica Importante

Como a base não é atualizada há muito tempo, use `(SELECT MAX(dt_venda) FROM decisionscard.t_venda)` como data de referência ao invés de CURRENT_DATE.

---

## ✍️ Sua Resposta

```sql
SELECT COUNT(DISTINCT tc.id_cliente)
FROM decisionscard.t_cliente tc 
JOIN decisionscard.t_venda tv
ON tc.id_cliente = tv.id_cliente 
WHERE 
tc.fl_status_conta = 'A'  AND 
tv.fl_status_venda = 'A' AND 
tc.id_cliente
NOT IN (SELECT DISTINCT id_cliente FROM decisionscard.t_venda WHERE dt_venda BETWEEN 
(SELECT MAX(dt_venda) - INTERVAL '90 days' FROM decisionscard.t_venda) AND (SELECT MAX(dt_venda) FROM decisionscard.t_venda) )
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Usa maior data da base como referência
- [ ] Calcula diferença de 90 dias corretamente
- [ ] Identifica última compra por cliente

