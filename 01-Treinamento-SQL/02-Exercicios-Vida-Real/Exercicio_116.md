# Exercício 116: Analítico das Contas sem Compras há Mais de 90 Dias

## 📝 Pergunta

Crie um relatório analítico (TABELÃO) das contas que não fazem compras há mais de 90 dias. Mostre:

- `id_cliente` (ID do cliente)
- `nm_cliente` (Nome do cliente)
- `nm_fantasia` (Origem comercial - nome da rede)
- `dt_ultima_compra` (Data da última compra)
- `vl_ultima_compra` (Valor da última compra)
- `dias_desde_ultima_compra` (Dias desde a última compra)

Use a maior data de venda da base como referência. Considere apenas clientes com contas ativas. Ordene por dias desde última compra (decrescente).

## 🎯 Objetivo

Demanda da área de retenção para criar campanhas direcionadas de reativação de clientes inativos.

## 💡 Contexto de Negócio

Este relatório permite ações de CRM personalizadas baseadas no perfil do cliente e tempo de inatividade.

---

## ✍️ Sua Resposta

```sql
WITH cliente AS ( 
SELECT
    tc.id_cliente,
    tc.nm_cliente,
    tr.nm_fantasia
FROM decisionscard.t_rede tr JOIN decisionscard.t_cliente tc
ON tc.cd_uf = tr.cd_uf JOIN decisionscard.t_venda tv 
ON tc.id_cliente = tv.id_cliente
WHERE fl_status_conta = 'A'
),
venda_valor AS(
SELECT DISTINCT ON (id_cliente)
    id_cliente,
    dt_venda::DATE                                                   AS dt_venda,
    vl_venda                                                         AS vl_ultima_compra
FROM decisionscard.t_venda
ORDER BY id_cliente, dt_venda DESC 
),
max_dt AS( 
SELECT 
    MAX(dt_venda)::DATE                                              AS dt_max 
FROM decisionscard.t_venda
),
ultima_venda as( 
SELECT 
    id_cliente,
   MAX(dt_venda)::DATE                                               AS dt_ultima_compra
FROM decisionscard.t_venda
GROUP BY id_cliente
),
tabelao AS (
SELECT
    c.id_cliente,
    c.nm_cliente,
    c.nm_fantasia,
    uv.dt_ultima_compra,
    vv.vl_ultima_compra,
    (m.dt_max - uv.dt_ultima_compra)                                 AS dias_desde_ultima_compra
FROM cliente c JOIN venda_valor vv 
ON c.id_cliente = vv.id_cliente 
JOIN ultima_venda uv
ON uv.id_cliente = c.id_cliente 
CROSS JOIN max_dt m 
)
SELECT 
    id_cliente,
    nm_cliente,
    nm_fantasia,
    dt_ultima_compra,
    vl_ultima_compra,
    dias_desde_ultima_compra
FROM tabelao
WHERE dias_desde_ultima_compra > 90
ORDER BY dias_desde_ultima_compra DESC;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Identifica última compra por cliente
- [ ] Calcula dias desde última compra
- [ ] JOINs com cliente e rede (origem)
- [ ] Filtra clientes inativos há 90+ dias

