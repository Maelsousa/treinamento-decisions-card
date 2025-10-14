# Exercício 109: Evolução das Contas Cadastradas x Ativadas por Trimestre

## 📝 Pergunta

Analise a evolução trimestral das contas cadastradas versus ativadas. Para cada trimestre dos últimos 2 anos (baseado na maior data de cadastro), mostre:

- `ano` 
- `trimestre` (1, 2, 3, 4)
- `periodo` (ex: "2023-T1")
- `contas_cadastradas` (cadastradas no trimestre)
- `contas_ativadas` (que fizeram primeira compra no trimestre)
- `taxa_ativacao` (% ativadas sobre cadastradas)
- `crescimento_cadastro` (% crescimento vs trimestre anterior)

Ordene por ano e trimestre.

## 🎯 Objetivo

Demanda estratégica para acompanhar tendências de crescimento e efetividade das campanhas de ativação.

## 💡 Contexto de Negócio

A evolução trimestral permite identificar sazonalidades e avaliar o impacto de mudanças na estratégia comercial.

---

## ✍️ Sua Resposta

```sql
WITH Contas_Por_periodo AS
(
    SELECT 
        EXTRACT(YEAR FROM TC.dt_cadastro)                                                                              AS ano,
        EXTRACT(quarter FROM TC.dt_cadastro)                                                                           AS trimestre,
        TO_CHAR(TC.dt_cadastro, 'YYYY')||'-T'||EXTRACT(quarter FROM  TC.dt_cadastro)                                   AS periodo,
        COUNT(TC.id_cliente)                                                                                           AS contas_cadastradas,
        SUM(CASE
            WHEN TC.fl_status_conta = 'A' THEN 1 
            ELSE 0
        END )                                                                                                          AS contas_ativas
    FROM decisionscard.t_cliente TC
        WHERE TC.dt_cadastro >= 
        (
        SELECT MAX(dt_cadastro) - INTERVAL '2 YEAR' FROM decisionscard.t_cliente
        )
    GROUP BY 
        ano,
        trimestre,
        periodo
    ORDER BY
        ano, trimestre
)
SELECT
ano,
trimestre,
periodo,
contas_cadastradas,
contas_ativas,
ROUND
(
(COALESCE(contas_ativas, 0)::DECIMAL / NULLIF(contas_cadastradas, 0))* 100, 2 
)                                                                                                                      AS taxa_ativacao,
ROUND (
    COALESCE(
        NULLIF(contas_cadastradas, 0)::DECIMAL / NULLIF(LAG(contas_cadastradas)OVER(ORDER BY periodo), 0)*100
        , 0)
, 2)                                                                                                                   AS crescimento_cadastro
FROM Contas_Por_periodo;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Agrupamento por trimestre correto
- [ ] Identifica primeira compra por cliente
- [ ] Calcula taxa de ativação
- [ ] Calcula crescimento vs período anterior

