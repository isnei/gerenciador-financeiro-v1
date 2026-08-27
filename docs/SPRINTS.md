# SPRINTS.md — Plano Mestre do Projeto

> **DOCUMENTO DE PLANEJAMENTO**
>
> Este arquivo define o plano original das Sprints.
>
> **Não atualizar este arquivo ao concluir uma Sprint.**
>
> O resultado real da execução deve ser registrado exclusivamente no `PROGRESSO.md`.

---

# Sprints da V1

## Sprint 0 — Fundação

### Objetivo
Criar a base técnica do projeto.

### Escopo
- Laravel 13;
- PHP 8.3;
- MySQL;
- Git/GitHub;
- branches `main` e `develop`;
- Laravel Breeze;
- autenticação;
- usuários `usuario/admin`;
- status `ativo/bloqueado`;
- Tailwind;
- Livewire;
- Alpine;
- Vite;
- Lucide Icons;
- layout responsivo;
- estrutura inicial de documentação.

---

## Sprint 1 — Usuários e isolamento

### Objetivo
Garantir autenticação, autorização e isolamento entre usuários.

### Escopo
- Policies;
- isolamento por `user_id`;
- middleware admin;
- área administrativa básica;
- bloqueio de usuários;
- segurança.

---

## Sprint 2 — Bancos e contas

### Objetivo
Criar a estrutura de bancos e contas financeiras.

### Escopo
- cadastro de bancos;
- imagem/logo do banco;
- cadastro de contas;
- saldo inicial;
- data do saldo inicial;
- `considerar_no_saldo`;
- `considerar_no_patrimonio`;
- criação automática da Carteira durante o cadastro do usuário;
- idempotência das contas padrão.

### Regra
A Carteira não será criada por Seeder.

---

## Sprint 3 — Categorias

### Objetivo
Criar categorias e subcategorias independentes para receitas e despesas.

### Escopo
- categorias de receita;
- categorias de despesa;
- subcategorias;
- ícones Lucide;
- Seed das categorias padrão;
- ativação/desativação;
- validação de tipo.

---

## Sprint 4 — Movimentações e transferências

### Objetivo
Criar o núcleo de receitas, despesas, transferências e saldo.

### Escopo
- receitas;
- despesas;
- transferências;
- conta origem;
- conta destino;
- valor;
- data da movimentação;
- histórico;
- cálculo de saldo;
- validações;
- atomicidade;
- transferência sem impacto indevido no resultado;
- aporte em investimento como transferência.

### Regra de transferência
Transferência é uma única operação lógica:

```text
origem - valor
destino + valor
```

Não é receita e não é despesa.

### Validações
- origem e destino devem pertencer ao usuário;
- valor deve ser maior que zero;
- origem e destino não podem ser iguais;
- período fechado bloqueia transferência.

---

## Sprint 5 — Parcelamentos

### Objetivo
Criar operações parceladas.

### Escopo
- valor total ou valor da parcela;
- parcela inicial;
- quantidade;
- vencimento;
- status pendente/paga;
- alteração individual;
- alteração desta + próximas pendentes.

---

## Sprint 6 — Cartões e faturas

### Objetivo
Criar cartões de crédito e controle de faturas.

### Escopo
- cartões;
- limite;
- fechamento;
- vencimento;
- faturas;
- compras;
- compras parceladas;
- pagamento da fatura.

---

## Sprint 7 — Recorrências

### Objetivo
Criar receitas e despesas recorrentes.

### Escopo
- despesas recorrentes;
- receitas recorrentes;
- periodicidade;
- vencimento;
- data de movimentação quando realizada;
- alteração;
- encerramento.

---

## Sprint 8 — Investimentos

### Objetivo
Criar o módulo de investimentos.

### Escopo
- investimentos;
- consórcios;
- posição inicial;
- parcelas já pagas;
- aportes;
- aplicações;
- retiradas;
- rendimentos;
- patrimônio.

---

## Sprint 9 — Fechamento financeiro

### Objetivo
Impedir alterações em períodos financeiros já fechados.

### Escopo
- períodos mensais;
- aberto/fechado;
- fechamento;
- reabertura;
- bloqueio de criação;
- bloqueio de edição;
- bloqueio de exclusão.

---

## Sprint 10 — Dashboard

### Objetivo
Criar visão consolidada das finanças.

### Escopo
- saldo disponível;
- receitas;
- despesas;
- resultado;
- patrimônio;
- investimentos;
- a pagar;
- a receber;
- previsto x realizado;
- filtros;
- histórico.

---

## Sprint 11 — Auditoria e refinamento

### Objetivo
Aumentar segurança, rastreabilidade e qualidade.

### Escopo
- auditoria;
- histórico;
- segurança;
- conciliação preparada;
- UX;
- responsividade;
- validações;
- limpeza técnica.

---

## Sprint 12 — Homologação V1

### Objetivo
Validar e preparar a primeira versão estável.

### Escopo
- testes finais;
- correções;
- limpeza;
- documentação;
- produção;
- estabilidade;
- promoção de `develop` para `main`.

---

# Regra do documento

Este arquivo representa o **planejamento original**.

Durante a execução:

```text
SPRINTS.md
    ↓
define o que será feito

código
    ↓
implementa

PROGRESSO.md
    ↓
registra o que realmente foi feito
```

Se houver necessidade real de alteração no planejamento:
1. não apagar o planejamento original;
2. registrar a necessidade no `PROGRESSO.md`;
3. avaliar se a mudança é arquitetural;
4. somente alterar este documento se for indispensável;
5. registrar claramente a alteração e o motivo.

Mudanças normais de implementação não devem modificar `SPRINTS.md`.
