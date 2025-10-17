# Exercício 115: Quantidade de Cartões por Tipo de Bloqueio

## 📝 Pergunta

Analise os cartões bloqueados por tipo de bloqueio. Mostre:

- `ds_tipo_bloqueio_cartao` (descrição do tipo de bloqueio)
- `quantidade_cartoes` (número de cartões com este tipo de bloqueio)
- `percentual_total_bloqueios` (% sobre total de bloqueios)

Considere apenas bloqueios ativos (`fl_ativo = 'S'`) e faça JOIN com a tabela de tipos de bloqueio para obter a descrição. Ordene por quantidade decrescente.

## 🎯 Objetivo

Demanda de risco para entender os principais motivos de bloqueio de cartões e otimizar políticas.

## 💡 Contexto de Negócio

Identificar os tipos de bloqueio mais frequentes ajuda a melhorar processos e reduzir bloqueios desnecessários.

---

## ✍️ Sua Resposta

```sql
SELECT 
     ds_tipo_bloqueio_cartao,
     COUNT(tbc.id_bloqueio_cartao)                                                                                AS quantidade_cartoes,
     ROUND(
     (COUNT(tbc.id_tipo_bloqueio_cartao)::NUMERIC / SUM(COUNT(tbc.id_tipo_bloqueio_cartao)) OVER())*100 , 2
     )                                                                                                            AS percentual_total_bloqueios
FROM decisionscard.t_tipo_bloqueio_cartao ttbc JOIN decisionscard.t_bloqueio_cartao tbc 
ON ttbc.id_tipo_bloqueio_cartao = tbc.id_tipo_bloqueio_cartao 
WHERE fl_liberado = 'N'
GROUP BY ds_tipo_bloqueio_cartao
ORDER BY COUNT(tbc.id_bloqueio_cartao) DESC;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] JOIN com tabela de tipos de bloqueio
- [ ] Filtra apenas bloqueios ativos
- [ ] Calcula quantidade por tipo
- [ ] Calcula percentual sobre total de bloqueios

