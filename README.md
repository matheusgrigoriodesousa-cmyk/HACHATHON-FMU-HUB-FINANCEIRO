# 📱 MVP Hub Financeiro - Database

![SQL Server](https://img.shields.io/badge/Database-SQL_Server-CC2927?style=for-the-badge&logo=microsoft-sql-server&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=for-the-badge)

Sistema de banco de dados relacional desenvolvido para o MVP de um Hub Financeiro Móvel.

## 📋 Índice
- [Sobre o Projeto](#-sobre-o-projeto)
- [Modelo Entidade-Relacionamento (MER)](#-modelo-entidade-relacionamento-mer)
- [Diagrama Entidade-Relacionamento (DER)](#-diagrama-entidade-relacionamento-der)
- [Estrutura do Banco de Dados](#-estrutura-do-banco-de-dados)
- [Stored Procedures](#-stored-procedures)

---

## 📖 Sobre o Projeto
Este banco de dados foi modelado na **3ª Forma Normal (3FN)** para garantir integridade e performance. Ele gerencia:
* **Core Banking:** Usuários, múltiplas contas e autenticação.
* **Transações:** Entradas, saídas e categorização financeira.
* **Serviços:** PIX, pagamentos de boletos, recargas e cashback.
* **Produtos Financeiros:** Empréstimos e seguros.

---

## 🧠 Modelo Entidade-Relacionamento (MER)

O modelo conceitual define as regras de negócio e como as entidades interagem:

1.  **Usuário vs Contas:** Um **Usuário** pode possuir várias **Contas** (1:N), mas uma conta pertence a um único usuário.
2.  **Conta vs Transações:** Uma **Conta** realiza diversas **Transações** (1:N).
3.  **Transações vs Categorias:** Cada **Transação** pertence a uma **Categoria** específica (N:1).
4.  **Conta vs Serviços:** Uma **Conta** é a origem de múltiplas operações de **PIX**, **Pagamentos**, **Recargas** e recebimento de **Cashback** (1:N).
5.  **Usuário vs Produtos:**
    * Um **Usuário** pode contratar vários **Seguros** (1:N).
    * Um **Usuário** pode solicitar vários **Empréstimos** (1:N).

---
## 🗂 Estrutura do Banco de Dados
O banco MVP_HubFinanceiro conta com tabelas estruturadas com chaves estrangeiras (FK) e restrições de verificação (CHECK) para garantir a regra de negócio diretamente no banco.

---
## ⚙️ Stored Procedures (Automação)
O diferencial deste projeto é que o saldo não é manipulado manualmente. Utilizamos Procedures para garantir que toda operação financeira reflita imediatamente no saldo da conta.

---
## 🔄 Operações que atualizam saldo automaticamente
sp_registrar_transacao:

Se a categoria for 'entrada' ➝ Soma ao saldo.

Se a categoria for 'saida' ➝ Subtrai do saldo.

sp_registrar_pix:

Identifica se é 'envio' (subtrai) ou 'recebimento' (soma).

sp_registrar_pagamento:

Registra o boleto como 'pago' e desconta o valor.

sp_fazer_recarga:

Debita o valor da recarga da conta.

sp_adicionar_cashback:

Credita o valor do benefício na conta.

---
## 📋 Procedures de Leitura e Gestão
sp_criar_usuario / sp_listar_usuarios

sp_extrato_conta (Relatório completo com JOINs)

sp_solicitar_emprestimo

---
## 🛠 Como Executar o Projeto
Clone o repositório:

Bash

git clone [https://github.com/SEU-USUARIO/MVP_HubFinanceiro.git](https://github.com/SEU-USUARIO/MVP_HubFinanceiro.git)
Abra o SGBD: Utilize o SQL Server Management Studio (SSMS) ou Azure Data Studio.

Execute o Script: Abra o arquivo script_completo.sql e execute (tecla F5). O script irá:

Criar o banco de dados e as tabelas.

Inserir dados de teste (Seed Data).

Criar as Stored Procedures.

---
Teste uma operação:

---
## SQL

-- Exemplo: Fazer um PIX de R$ 50,00
EXEC sp_registrar_pix 1, 'ana@email.com', 'email', 'envio', 50.00;

-- Verifique o saldo atualizado
SELECT * FROM contas WHERE id_conta = 1;

---
## ✒️ Autor Matheus grigorio de sousa
Desenvolvido como parte do estudo de Arquitetura de Banco de Dados e SQL Server.
---

## 📊 Diagrama Entidade-Relacionamento (DER)

```mermaid
erDiagram
    USUARIOS ||--|{ CONTAS : possui
    USUARIOS ||--|{ SEGUROS : contrata
    USUARIOS ||--|{ EMPRESTIMOS : solicita
    
    CONTAS ||--|{ TRANSACOES : realiza
    CONTAS ||--|{ PIX : envia_recebe
    CONTAS ||--|{ PAGAMENTOS : efetua
    CONTAS ||--|{ RECARGAS : faz
    CONTAS ||--|{ CASHBACK : recebe
    
    CATEGORIAS ||--|{ TRANSACOES : classifica

    USUARIOS {
        int id_usuario PK
        string nome
        string email
        string senha_hash
        datetime data_criacao
        bit ativo
    }

    CONTAS {
        int id_conta PK
        int id_usuario FK
        decimal saldo
        string tipo_conta
    }

    TRANSACOES {
        int id_transacao PK
        int id_conta FK
        int id_categoria FK
        decimal valor
        string descricao
        datetime data_transacao
    }

    CATEGORIAS {
        int id_categoria PK
        string nome
        string tipo
    }

    PIX {
        int id_pix PK
        int id_conta FK
        string chave_destino
        string tipo_chave
        string tipo_operacao
        decimal valor
    }

    PAGAMENTOS {
        int id_pagamento PK
        int id_conta FK
        string codigo_barras
        decimal valor
        string status
    }





