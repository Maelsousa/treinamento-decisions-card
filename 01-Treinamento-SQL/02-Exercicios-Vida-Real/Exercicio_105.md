# Exercício 105: Percentual de Contas Bloqueadas

## 📝 Pergunta

Calcule o percentual de contas bloqueadas em relação ao total de contas cadastradas. Considere como bloqueadas as contas que possuem pelo menos um registro ativo na tabela `t_bloqueio_cliente`. Consulte a tabela `t_dominio` para identificar o código correspondente a bloqueios não liberados.

Mostre o resultado como: `total_contas`, `contas_bloqueadas`, `percentual_bloqueadas`.

## 🎯 Objetivo

Demanda da área de risco para monitorar a saúde da carteira de clientes e identificar tendências de bloqueios.

## 💡 Contexto de Negócio

Alto percentual de bloqueios pode indicar problemas na política de crédito ou deterioração da qualidade da carteira.

---

## ✍️ Sua Resposta

```sql
SELECT
	(SELECT COUNT(DISTINCT tc.id_cliente)  FROM decisionscard.t_cliente tc) AS total_contas, -- total de clientes
	(SELECT COUNT(DISTINCT tbc.id_bloqueio_cliente) FROM decisionscard.t_bloqueio_cliente tbc WHERE tbc.fl_liberado = 'N') AS contas_bloqueadas, -- total de contas bloqueadas ainda ativas
	ROUND( -- calcula a porcentagem de contas bloqueadas referente ao total de clientes
		((SELECT count(DISTINCT tbc.id_bloqueio_cliente)::FLOAT FROM decisionscard.t_bloqueio_cliente tbc WHERE tbc.fl_liberado = 'N') 
		/ NULLIF((SELECT count(DISTINCT tc.id_cliente) FROM decisionscard.t_cliente tc), 0) * 100)::NUMERIC, 2) AS porcentual_bloqueadas
FROM decisionscard.t_cliente tc
LIMIT 1;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Conta total de contas corretamente
- [ ] Consulta a tabela t_dominio para identificar bloqueios não liberados
- [ ] Identifica contas com bloqueio ativo
- [ ] Calcula percentual corretamente
- [ ] Apresenta os três valores solicitados

