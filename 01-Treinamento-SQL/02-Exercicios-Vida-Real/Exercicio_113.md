# Exercício 113: Quantidade Total de Cartões

## 📝 Pergunta

Calcule a quantidade total de cartões emitidos na base de dados. Mostre apenas o número total.

## 🎯 Objetivo

Demanda operacional para controle do estoque de cartões e acompanhamento da produção.

## 💡 Contexto de Negócio

O total de cartões emitidos é um indicador operacional importante para logística e controle de estoque de cartões físicos.

---

## ✍️ Sua Resposta

```sql
SELECT
    COUNT(id_cartao)
FROM decisionscard.t_cartao
```

---

## 📋 Critérios de Avaliação

- [ ] Query executa sem erros
- [ ] Conta todos os cartões da tabela
- [ ] Retorna apenas um número
- [ ] Usa a tabela t_cartao corretamente
