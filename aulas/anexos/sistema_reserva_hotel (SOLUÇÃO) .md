## SOLUÇÃO
Vamos modelar um [**Sistema Básico de Gerenciamento de Reservas de Hotel**](https://github.com/rns-yoda/analise-sistemas/blob/main/aulas/anexos/sistema_reserva_hotel.md) com quatro entidades principais. Essa modelagem abrange as seguintes entidades:

1. **Hospede**: Representa o cliente que realiza a reserva.
2. **Quarto**: Representa os quartos disponíveis para reserva.
3. **Reserva**: Representa o processo de reserva de um quarto por um hóspede.
4. **Pagamento**: Representa os detalhes do pagamento de uma reserva.

---

### **1. Diagrama de Classes**
Aqui está a estrutura básica das entidades e seus relacionamentos:

```mermaid
classDiagram
    
    Hospede "1" --> "0..*" Reserva : realiza
    Reserva "1" --> "1" Quarto : refere-se
    Reserva "1" --> "1" Pagamento : processa

    class Hospede {
        - id: int
        - nome: String
        - telefone: String
        + cadastrar(): void
        + atualizar(): void
        + buscarReservas(): List<Reserva>
    }

    class Quarto {
        - id: int
        - numero: String
        - tipo: String
        - status: String
        + alterarStatus(novoStatus: String): void
        + verificarDisponibilidade(): boolean
    }

    class Reserva {
        - id: int
        - dataInicio: Date
        - dataFim: Date
        - status: String
        + realizarReserva(hospede: Hospede, quarto: Quarto): void
        + cancelarReserva(): void
        + verificarDisponibilidade(): boolean
    }

    class Pagamento {
        - id: int
        - valor: double
        - metodo: String
        + processarPagamento(): void
        + gerarRecibo(): String
    }

```


---

### **Explicação**
1. **Hóspede**  
   - Atributos:
     - `id`: Identificação única do hóspede.
     - `nome`: Nome do hóspede.
     - `telefone`: Contato do hóspede.  
   - Métodos:
     - `cadastrar()`: Registra um novo hóspede.
     - `atualizar()`: Atualiza informações do hóspede.
     - `buscarReservas()`: Lista as reservas feitas pelo hóspede.

2. **Quarto**  
   - Atributos:
     - `id`: Identificação única do quarto.
     - `numero`: Número do quarto.
     - `tipo`: Tipo do quarto (ex.: Standard, Luxo).
     - `status`: Status atual do quarto (ex.: Livre, Reservado).  
   - Métodos:
     - `alterarStatus()`: Modifica o status do quarto.
     - `verificarDisponibilidade()`: Retorna se o quarto está disponível para reserva.

3. **Reserva**  
   - Atributos:
     - `id`: Identificação única da reserva.
     - `dataInicio` e `dataFim`: Período da reserva.
     - `status`: Status da reserva (ex.: Ativa, Cancelada).  
   - Métodos:
     - `realizarReserva()`: Realiza uma nova reserva associada a um hóspede e a um quarto.
     - `cancelarReserva()`: Cancela a reserva.
     - `verificarDisponibilidade()`: Checa se o quarto está disponível no período solicitado.

4. **Pagamento**  
   - Atributos:
     - `id`: Identificação única do pagamento.
     - `valor`: Valor do pagamento.
     - `metodo`: Método de pagamento (ex.: Cartão, Dinheiro).  
   - Métodos:
     - `processarPagamento()`: Processa o pagamento da reserva.
     - `gerarRecibo()`: Gera um recibo de pagamento.

---

Vamos detalhar os diagramas para o fluxo de **Realização de Reserva** com os seguintes passos:  

1. O hóspede inicia o processo de reserva.
2. O sistema verifica a disponibilidade do quarto.
3. Caso disponível, a reserva é criada e o status do quarto é alterado para "Reservado".
4. O pagamento é processado.

---

### **1. Diagrama de Sequência**

```mermaid
sequenceDiagram
    participant Hospede
    participant Sistema
    participant Reserva
    participant Quarto
    participant Pagamento

    Hospede->>Sistema: Solicitar reserva
    Sistema->>Reserva: Verificar disponibilidade
    Reserva->>Quarto: Consultar disponibilidade
    Quarto-->>Reserva: Disponível
    Reserva->>Sistema: Confirmação
    Sistema->>Reserva: Criar reserva
    Sistema->>Quarto: Atualizar status para "Reservado"
    Sistema->>Pagamento: Processar pagamento
    Pagamento-->>Sistema: Pagamento confirmado
    Sistema-->>Hospede: Reserva confirmada

```

---

### **2. Diagrama de Estado**

Representa as mudanças de estado do **Quarto** e da **Reserva** durante o processo:

#### **Estados do Quarto**
- **Livre** → Estado inicial quando está disponível.
- **Reservado** → Após a reserva ser confirmada.
- **Manutenção** → Caso precise de reparos ou esteja indisponível.

```mermaid
stateDiagram
    [*] --> Livre
    Livre --> Reservado : Reserva confirmada
    Reservado --> Livre : Reserva cancelada ou finalizada
    Livre --> Manutenção : Reparo necessário
    Manutenção --> Livre : Reparo concluído
```

#### **Estados da Reserva**
- **Criada** → Reserva foi registrada, mas não confirmada.
- **Confirmada** → Reserva paga e válida.
- **Cancelada** → Reserva foi cancelada.

```mermaid
stateDiagram
    [*] --> Criada
    Criada --> Confirmada : Pagamento processado
    Criada --> Cancelada : Cancelamento solicitado
    Confirmada --> Finalizada : Período da reserva concluído
    Confirmada --> Cancelada : Cancelamento solicitado

```

---

### **3. Diagrama de Atividades**

```mermaid
flowchart TD
    A[Início] --> B[Hospede solicita reserva]
    B --> C[Verificar disponibilidade do quarto]
    C -->|Disponível| D[Criar reserva]
    D --> E[Atualizar status do quarto]
    E --> F[Processar pagamento]
    F --> G[Confirmar reserva]
    G --> H[Fim]
    C -->|Não disponível| I[Informar hospede]
    I --> H

```

---

Nota: Esses diagramas cobrem as etapas principais do processo de realização de uma reserva.
