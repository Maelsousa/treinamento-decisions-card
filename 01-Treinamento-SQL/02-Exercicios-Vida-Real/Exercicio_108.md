# Exercício 108: Distribuição de Contas por Origem Comercial

## 📝 Pergunta

Analise a distribuição de contas por origem comercial. Mostre:

- `nm_fantasia` (nome da rede/origem)
- `quantidade_contas` (número de contas originadas)
- `percentual_total` (% sobre total de contas)
- `contas_ativas` (quantas estão ativas)
- `taxa_ativacao` (% de ativas sobre total da origem)

Considere apenas origens que geraram pelo menos 10 contas. Ordene por quantidade decrescente.

## 🎯 Objetivo

Demanda comercial para avaliar a performance dos canais de aquisição e otimizar investimentos em parcerias.

## 💡 Contexto de Negócio

Identificar quais origens comerciais geram mais clientes e qual a qualidade desses clientes (taxa de ativação) é crucial para estratégia comercial.

---

## ✍️ Sua Resposta

```sql
SELECT
        TR.nm_fantasia,
        COUNT(DISTINCT TC.id_cliente) AS quantidade_contas,
        COUNT(DISTINCT CASE WHEN TC.fl_status_conta = 'A' THEN TC.id_cliente END) AS contas_ativas
    FROM decisionscard.t_rede TR INNER JOIN decisionscard.t_cliente TC 
    ON TR.id_rede = TC.id_origem_comercial 
    GROUP BY TR.nm_fantasia
    HAVING COUNT(DISTINCT TC.id_cliente) >= 10
)
SELECT 
    C.nm_fantasia,
    C.quantidade_contas,
    ROUND 
    (
    COALESCE(C.quantidade_contas, 0S)::DECIMAL / SUM(NULLIF(C.quantidade_contas, 0))OVER()*100, 2
    ) AS percentual_total,
    C.contas_ativas,
    ROUND 
    (
        COALESCE(C.contas_ativas, 0)::DECIMAL / NULLIF(C.quantidade_contas, 0) * 100, 2
    ) AS taxa_ativacao
FROM CONTAS C
ORDER BY quantidade_contas DESC;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] JOIN entre cliente e rede (origem)
- [ ] Calcula métricas por origem
- [ ] Filtra origens com 10+ contas
- [ ] Calcula taxa de ativação corretamente

