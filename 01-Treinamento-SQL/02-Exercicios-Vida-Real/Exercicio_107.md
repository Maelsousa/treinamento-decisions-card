# Exercício 107: Distribuição de Contas por Situação

## 📝 Pergunta

Crie um relatório mostrando a quantidade e percentual de contas por situação. Mostre:

- `situacao` (descrição da situação)
- `quantidade` (número de contas)
- `percentual` (% sobre o total)

Considere as situações baseadas no `fl_status_conta` e `fl_status_analise`:
- "Ativa": fl_status_conta = 'A'
- "Inativa": fl_status_conta = 'I' 
- "Pendente Análise": fl_status_analise = 'P'
- "Rejeitada": fl_status_analise = 'R'

Ordene por quantidade decrescente.

## 🎯 Objetivo

Demanda operacional para acompanhar a distribuição da carteira por status e identificar gargalos no processo.

## 💡 Contexto de Negócio

Este relatório ajuda a identificar problemas no funil de aprovação e monitorar a saúde operacional da carteira.

---

## ✍️ Sua Resposta

```sql
WITH situacao_conta AS (
		SELECT 
			CASE 
				WHEN tc.fl_status_conta  = 'A' THEN 'Ativa' 
				WHEN  tc.fl_status_conta  = 'I' THEN 'Inativa'
			END AS situacao
		FROM decisionscard.t_cliente tc
		UNION ALL
		SELECT 
			CASE 
				WHEN tc.fl_status_analise = 'P' THEN 'Pendente'
				WHEN tc.fl_status_analise = 'R' THEN 'Rejeitada'
			END AS situacao
		FROM decisionscard.t_cliente tc
),
status_fixos  AS (
	SELECT 'Ativa' AS status
	UNION ALL 
	SELECT 'Inativa' AS status
	UNION ALL 
	SELECT 'Pendente' AS status
	UNION ALL 
	SELECT 'Rejeitada' AS status
)
SELECT 
	situacao,
	quantidade,
	ROUND ((quantidade::NUMERIC / SUM(quantidade) OVER()) * 100, 2) AS percentual
FROM (SELECT 
		sf.status AS situacao,
		COALESCE(COUNT(sc.situacao), 0) AS quantidade
		FROM status_fixos sf LEFT JOIN situacao_conta sc ON sf.status = sc.situacao 
		GROUP BY sf.status)AS sub
ORDER BY quantidade DESC;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Categoriza situações corretamente
- [ ] Calcula quantidade por situação
- [ ] Calcula percentual sobre total
- [ ] Ordenação por quantidade decrescente

