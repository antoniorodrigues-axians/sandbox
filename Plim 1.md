# Arquitetura do Workflow de Aprovação

```mermaid
graph TD
    subgraph "Utilizadores"
        U1(👨‍💼 Requisitante)
        U2(👩‍⚖️ Aprovador)
    end

    subgraph "Aplicação Web"
        Browser[🌐 Navegador Web]
        Flask[🐍 Aplicação Flask <br> (Lógica de Negócio)]
    end

    subgraph "Base de Dados (SQL Server)"
        DB_Pedidos(📄 PedidosDeAlteracao)
        DB_Log(📜 LogTransacoes)
        DB_Principal(🗄️ Tabela Principal <br> Ex: Produtos)
    end

    %% Fluxo de Submissão
    U1 -- "1. Preenche formulário" --> Browser
    Browser -- "2. Envia dados (POST)" --> Flask
    Flask -- "3a. INSERT Pedido <br> (Estado='Pendente')" --> DB_Pedidos
    Flask -- "3b. INSERT Log <br> ('SUBMETEU PEDIDO')" --> DB_Log

    %% Fluxo de Aprovação
    U2 -- "4. Acede ao painel" --> Browser
    Browser -- "5. Pede lista de pendentes" --> Flask
    Flask -- "6. SELECT * FROM Pedidos <br> WHERE Estado='Pendente'" --> DB_Pedidos
    DB_Pedidos -- "Devolve lista" --> Flask
    Flask -- "Devolve página ao Aprovador" --> Browser
    Browser -- "7. Aprovador clica 'Aprovar'" --> Flask
    
    Flask -- "8a. UPDATE Pedido <br> (Estado='Aprovado')" --> DB_Pedidos
    Flask -- "8b. INSERT Log <br> ('APROVOU PEDIDO')" --> DB_Log
    Flask -- "8c. UPDATE Tabela Principal <br> (Aplica a alteração real)" --> DB_Principal
    Flask -- "8d. INSERT Log <br> ('EXECUTOU ALTERACAO')" --> DB_Log

    style U1 fill:#cce5ff,stroke:#333,stroke-width:2px
    style U2 fill:#d4edda,stroke:#333,stroke-width:2px
```
