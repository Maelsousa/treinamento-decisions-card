# Exercício 100: Quantidade de Contas Cadastradas

## 📝 Pergunta

Calcule a quantidade total de contas (clientes) cadastradas na base de dados da DecisionsCard. Mostre apenas o número total.

## 🎯 Objetivo

Esta é uma demanda real da área de negócios para acompanhar o crescimento da base de clientes.

## 💡 Contexto de Negócio

A quantidade de contas cadastradas é um KPI fundamental para medir o crescimento da empresa e o sucesso das campanhas de aquisição de clientes.

---

## ✍️ Sua Resposta

```sql
SELECT  
    COUNT(id_cliente)
FROM decisionscard.t_cliente;
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Conta todos os registros de clientes
- [ ] Retorna apenas um número
- [ ] Usa a tabela correta (t_cliente)

