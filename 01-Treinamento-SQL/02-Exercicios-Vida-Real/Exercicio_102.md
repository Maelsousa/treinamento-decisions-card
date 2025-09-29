# Exercício 102: Quantidade de Contas Ativas

## 📝 Pergunta

Calcule a quantidade de contas ativas na base. Para identificar o código correspondente a contas ativas, consulte a tabela `t_dominio` apropriada. Mostre apenas o número total.

## 🎯 Objetivo

Demanda da área comercial para monitorar quantos clientes estão aptos a realizar compras.

## 💡 Contexto de Negócio

Contas ativas representam o potencial de receita da empresa. É um indicador importante para projeções de vendas e planejamento comercial.

---

## ✍️ Sua Resposta

```sql
select

	COUNT(tc.fl_status_conta) as Contas_Ativas

from decisionscard.t_cliente tc 
where tc.fl_status_conta = 'A';

```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Filtra apenas contas ativas
- [ ] Retorna apenas um número
- [ ] Consulta a tabela t_dominio para identificar o código de status ativo
- [ ] Usa o campo fl_status_conta corretamente

