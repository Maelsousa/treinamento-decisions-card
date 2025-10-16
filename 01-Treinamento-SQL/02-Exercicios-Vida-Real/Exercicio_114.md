# Exercício 114: Quantidade de Cartões por Situação

## 📝 Pergunta

Analise a distribuição de cartões por situação. Mostre:

- `situacao` (descrição da situação)
- `quantidade` (número de cartões)
- `percentual` (% sobre o total)

Considere as situações baseadas no `fl_status_cartao`:
- "Ativo": fl_status_cartao = 'A'
- "Inativo": fl_status_cartao = 'I'
- "Bloqueado": fl_status_cartao = 'B'
- "Cancelado": fl_status_cartao = 'C'

Ordene por quantidade decrescente.

## 🎯 Objetivo

Demanda operacional para monitorar a situação da carteira de cartões e identificar problemas.

## 💡 Contexto de Negócio

A distribuição por status ajuda a identificar problemas operacionais e monitorar a saúde da carteira de cartões.

---

## ✍️ Sua Resposta

```sql
WITH qnt_cartao_situacao AS( 
SELECT 
    CASE 
    	WHEN fl_status_cartao = 'A' THEN 'Ativo'
    	WHEN fl_status_cartao = 'I' THEN 'Inativo'
    	WHEN fl_status_cartao = 'B' THEN 'Bloqueado'
    	WHEN fl_status_cartao = 'C' THEN 'Cancelado'
    END                                                                     AS situacao
FROM decisionscard.t_cartao
),
status_fixos AS ( 
    SELECT 'Ativo'                                                          AS status
    UNION ALL 
    SELECT 'Inativo'                                                        AS status
    UNION ALL 
    SELECT 'Bloqueado'                                                      AS status
    UNION ALL 
    SELECT 'Cancelado'                                                      AS status
)
SELECT 
    situacao,
    quantidade,
    ROUND( 
        (quantidade::NUMERIC  / SUM(quantidade) OVER())*100, 2
    )                                                                       AS percentual
FROM(
    SELECT 
        COUNT(qcs.situacao)                                                 AS quantidade,
        sf.status                                                           AS situacao
    FROM status_fixos sf LEFT JOIN  qnt_cartao_situacao qcs
    ON sf.status = qcs.situacao
    GROUP BY sf.status)                                                     AS situacao_e_quantidade;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Categoriza todas as situações
- [ ] Calcula quantidade por situação
- [ ] Calcula percentual sobre total
- [ ] Ordenação por quantidade decrescente

