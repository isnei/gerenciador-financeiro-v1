# ARQUITETURA.md — Controle Financeiro Pessoal V1

> **FONTE DE VERDADE DO PROJETO**
>
> Este documento define como o sistema deve funcionar. A arquitetura está congelada e alterações estruturais somente devem ocorrer em último caso.

## 1. Objetivo

Aplicação web de controle financeiro pessoal, multiusuário e responsiva.

Na V1:
- cada usuário possui seu próprio ambiente financeiro;
- nenhum usuário acessa dados financeiros de outro usuário;
- existem usuários `usuario` e `admin`;
- usuários podem estar `ativo` ou `bloqueado`;
- não haverá família/compartilhamento;
- haverá preparação para futura gestão de planos/mensalidades, sem cobrança na V1.

## 2. Regra fundamental de desenvolvimento

O projeto deve ser desenvolvido rapidamente, por Sprints, evitando mudanças de escopo.

### Alterar a arquitetura somente quando houver:
- erro grave;
- impossibilidade técnica;
- requisito essencial incompatível com a arquitetura;
- decisão de negócio indispensável ainda não definida.

Antes de qualquer alteração arquitetural:
1. identificar o problema;
2. avaliar a consequência;
3. propor a solução;
4. registrar a decisão;
5. atualizar `ARQUITETURA.md`;
6. registrar a decisão no `PROGRESSO.md`;
7. somente então alterar o código.

Novas ideias não entram automaticamente na V1. Devem ser classificadas como melhoria futura.

## 3. Documentação oficial

```text
docs/
├── ARQUITETURA.md
├── SPRINTS.md
└── PROGRESSO.md
```

### ARQUITETURA.md
Responde:
> Como o sistema deve funcionar?

Contém regras estruturais e de negócio congeladas.

### SPRINTS.md
Responde:
> O que foi planejado fazer?

É o plano mestre das Sprints. Não deve ser alterado durante a execução normal do projeto.

### PROGRESSO.md
Responde:
> O que já foi realizado e onde estamos?

É o histórico real da execução. Deve ser atualizado ao final de cada Sprint e sempre que houver uma informação relevante sobre o estado do projeto.

## 4. Regra de continuidade entre conversas

Ao iniciar qualquer nova conversa sobre este projeto, consultar os três arquivos:

```text
docs/ARQUITETURA.md
docs/SPRINTS.md
docs/PROGRESSO.md
```

Interpretar assim:

```text
ARQUITETURA.md → regras do sistema
SPRINTS.md     → plano original
PROGRESSO.md   → estado real atual
```

Nunca presumir que uma Sprint foi concluída apenas porque está planejada em `SPRINTS.md`.

A Sprint atual e o próximo passo devem ser determinados pelo `PROGRESSO.md`.

## 5. Stack

- Laravel 13
- PHP 8.3
- MySQL
- Blade
- Tailwind CSS
- Alpine.js
- Livewire 4
- Laravel Breeze
- Vite
- Lucide Icons
- Git/GitHub

**Não usar Bootstrap.**

## 6. Responsividade

Todas as telas devem ser responsivas desde o início:
- celular;
- tablet;
- notebook;
- desktop.

## 7. Git

Branches:
```text
main
develop
```

Desenvolvimento em `develop`.
`main` representa versão estável.

Padrão de commits:
```text
feat:
fix:
refactor:
docs:
test:
```

### Fechamento de Sprint

A ordem obrigatória é:

```text
Implementar
↓
Testar
↓
Corrigir
↓
Homologar
↓
Atualizar PROGRESSO.md
↓
Revisar documentação
↓
Commit
↓
Tag
↓
Push
```

O `PROGRESSO.md` deve estar completo e revisado **antes da criação da tag da Sprint**.

`SPRINTS.md` não é alterado ao concluir a Sprint.

## 8. Usuários

`users`:
```text
id
name
email
email_verified_at
password
tipo
status
created_at
updated_at
```

Tipos:
```text
usuario
admin
```

Status:
```text
ativo
bloqueado
```

Usuário bloqueado não pode acessar o sistema.

Admin possui área administrativa, mas não acessa automaticamente o financeiro dos usuários.

## 9. Isolamento

Toda entidade financeira deve possuir `user_id` quando aplicável.

Toda consulta deve respeitar o usuário autenticado.

Usar Policies/autorizações para impedir acesso por ID a registros de outro usuário.

## 10. Bancos

Campos previstos:
```text
id
user_id
nome
codigo
imagem
ativo
created_at
updated_at
```

O usuário pode cadastrar banco e anexar imagem/logo.

Um banco pode possuir várias contas e cartões do mesmo usuário.

**Banco é obrigatório para toda conta.**

Na criação do usuário, além das contas padrão, podem ser criados bancos padrão necessários para essas contas.

## 11. Contas

Campos previstos:
```text
id
user_id
banco_id
nome
tipo
saldo_inicial
data_saldo_inicial
considerar_no_saldo
considerar_no_patrimonio
possui_limite
limite
icone
ativa
created_at
updated_at
deleted_at
```

`banco_id` é obrigatório.

A conta pode possuir limite/cheque especial:

```text
possui_limite = true/false
limite = valor
```

Saldo negativo é permitido quando o usuário registrar uma conta com limite/cheque especial. O sistema não deve bloquear automaticamente uma movimentação apenas porque ela produzirá saldo negativo.

Contas utilizam **Soft Delete**.

## 12. Contas padrão e Carteira

**Contas padrão NÃO serão criadas por Seeder.**

Durante a criação de cada novo usuário, o sistema deve criar automaticamente suas contas e bancos padrão.

Na V1, a conta padrão obrigatória é:

```text
Banco: Carteira
Conta: Carteira
```

Características:
- banco `Carteira` criado para o usuário;
- conta `Carteira` vinculada ao banco `Carteira`;
- saldo inicial `0`;
- ícone padrão;
- `considerar_no_saldo = true`;
- `considerar_no_patrimonio = true`;
- `possui_limite = false`;
- não pode ser duplicada se o fluxo de criação for repetido.

Também poderão ser definidos outros bancos/contas padrão na criação do usuário, conforme o conjunto padrão do sistema.

Fluxo:

```text
Novo usuário
↓
Usuário criado
↓
Criar bancos padrão
↓
Criar contas padrão
↓
Criar Carteira vinculada ao banco Carteira
```

A criação deve ser idempotente e protegida por transação quando aplicável.

Cartão de crédito não é criado automaticamente.

## 13. Saldo disponível

Não usar `saldo_atual` como fonte principal de verdade.

Regra:

```text
saldo inicial
+ entradas
- saídas
+ transferências recebidas
- transferências enviadas
= saldo
```

Cada conta possui:

```text
considerar_no_saldo = true/false
```

Uma conta pode existir normalmente e não participar do saldo disponível.

## 14. Patrimônio

Separar:
- saldo disponível;
- resultado do período;
- patrimônio;
- investimentos.

Cada conta possui:

```text
considerar_no_patrimonio
```

## 15. Categorias

Tipos:

```text
receita
despesa
```

Receitas e despesas não compartilham categorias.

Campos:

```text
id
user_id
nome
tipo
categoria_pai_id
icone
sistema
ativa
created_at
updated_at
```

Subcategoria é uma categoria com `categoria_pai_id`.

Subcategorias são opcionais.

## 16. Categorias padrão

### Despesas

```text
Moradia — house
Alimentação — utensils
Transporte — car
Saúde — heart-pulse
Educação — graduation-cap
Lazer — gamepad
Compras — shopping-cart
Contas e Serviços — lightbulb
Vestuário — shirt
Animais — paw-print
Viagens — plane
Impostos e Taxas — landmark
Outros — package
```

### Receitas

```text
Salário — briefcase
Rendimentos — banknote
Aluguéis — house
Investimentos — circle-dollar-sign
Recebimentos — hand-coins
Outros — package
```

Subcategorias padrão não são obrigatórias.

Categorias utilizadas não devem ser apagadas fisicamente; devem ser desativadas.

## 17. Ícones

Usar Lucide Icons.

Armazenar o identificador do ícone no banco.

Usuário pode escolher o ícone ao criar/editar categoria ou subcategoria.

## 18. Movimentações

Tipos:

```text
receita
despesa
transferencia
```

Investimento não é despesa.

Aporte em investimento é transferência/aporte.

Movimentações devem utilizar **Soft Delete**.

## 19. Data da movimentação

A `data_movimentacao` representa a **data efetiva em que a movimentação financeira aconteceu**.

Para lançamentos normais, não parcelados e que não sejam compras de cartão, a data da movimentação é obrigatória.

Exemplo:

```text
Despesa normal
Data da movimentação: 15/08/2026
```

A data da movimentação não deve ser confundida com a data de criação/registro do lançamento no sistema.

## 20. Vencimento

`data_vencimento` representa quando uma obrigação/recebimento deve ser liquidado.

Não é obrigatória em todo lançamento.

É usada principalmente em:
- parcelas;
- recorrências;
- contas a pagar;
- contas a receber;
- faturas.

Não confundir:

```text
data_movimentacao = quando aconteceu
data_vencimento = quando deveria ser pago/recebido
data_fechamento_fatura = quando a fatura fecha
data_pagamento = quando foi efetivamente pago
```

## 21. Previsto x realizado

O sistema deve distinguir previsto de realizado, principalmente para:
- recorrências;
- parcelas;
- contas a pagar;
- contas a receber.

Quando uma obrigação prevista for efetivamente paga, registrar `data_pagamento`.

A data da movimentação continua representando quando o fato financeiro ocorreu.

## 22. Parcelamentos

Permitir:
- valor total;
- valor da parcela;
- quantidade total;
- parcela inicial;
- data da movimentação;
- vencimento.

Perguntar:

```text
O valor informado é:
( ) Valor total
( ) Valor da parcela
```

A soma das parcelas deve ser exatamente igual ao valor total, inclusive centavos.

## 23. Parcela inicial

Permitir cadastrar uma operação já existente.

Exemplo:

```text
10 parcelas
parcela atual = 5
```

Criar/controlar:

```text
5/10
6/10
7/10
8/10
9/10
10/10
```

As parcelas anteriores à parcela inicial são **ignoradas**.

Não criar parcelas anteriores.
Não registrar movimentações anteriores.
Não registrar histórico financeiro artificial para parcelas que aconteceram antes da entrada do usuário no sistema.

## 24. Status de parcela

Somente:

```text
pendente
paga
```

Não usar `cancelada` como status de parcela.

## 25. Alteração de parcelas

Ao editar uma parcela, perguntar:

```text
Somente esta parcela
```

ou

```text
Esta parcela e as próximas parcelas pendentes
```

Parcelas pagas nunca são alteradas automaticamente por alterações futuras.

## 26. Recorrências

Recorrência é diferente de parcelamento.

Exemplos:
- celular;
- internet;
- streaming;
- salário;
- aluguel recebido.

Possui:

```text
periodicidade
valor
data_inicio
dia_vencimento
data_encerramento
ativa
```

Não criar infinitas ocorrências futuras.

A recorrência possui vencimento previsto. Quando efetivamente paga/recebida, a ocorrência recebe `data_movimentacao`.

Exemplo:

```text
Vencimento: 10/09
Pago em:    08/09
```

## 27. Cartões

Campos previstos:

```text
id
user_id
banco_id
nome
limite
dia_fechamento
dia_vencimento
icone
ativo
created_at
updated_at
```

Cartões são cadastrados pelo usuário.

## 28. Fechamento x vencimento do cartão

São independentes.

Exemplo:

```text
fechamento = dia 10
vencimento = dia 17
```

Compra no dia 09 entra na fatura que fecha dia 10.

Compra no dia 11 entra na próxima fatura.

## 29. Faturas

Campos previstos:

```text
id
cartao_id
mes_referencia
data_fechamento
data_vencimento
data_pagamento
status
valor_total
created_at
updated_at
```

Status:

```text
aberta
fechada
paga
atrasada
```

## 30. Compras no cartão

Compra:
- possui data da compra/movimentação;
- pertence a uma fatura;
- pode ser parcelada;
- parcelas podem pertencer a faturas diferentes.

Pagamento da fatura não gera uma segunda despesa.

## 31. Transferências

Transferência é uma operação lógica entre duas contas/estruturas pertencentes ao mesmo usuário.

**Não é receita e não é despesa.**

Estrutura lógica mínima:

```text
user_id
conta_origem_id
conta_destino_id
valor
data_movimentacao
descricao
```

Exemplo:

```text
Conta corrente - R$ 1.000
Poupança       + R$ 1.000
```

Saldo total não muda.

Nunca registrar como:

```text
Receita + R$ 1.000
Despesa - R$ 1.000
```

### Transferência para investimento

```text
Conta corrente
↓
Investimento
```

é aporte/transferência, não despesa.

### Atomicidade

Origem e destino devem ser processados dentro de uma transação do banco de dados.

Se qualquer parte falhar, nenhuma das duas pontas deve ser efetivada.

### Validações

Não permitir:
- origem inexistente;
- destino inexistente;
- origem/destino de outro usuário;
- valor menor ou igual a zero;
- origem igual ao destino;
- transferência em período fechado.

Transferência possui `data_movimentacao` e não exige `data_vencimento`.

No histórico deve aparecer como operação única:

```text
Origem → Destino
Valor
Data
Usuário
```

## 32. Investimentos

Tipos iniciais:
- consórcio;
- aplicação financeira;
- outros.

Aporte:

```text
conta origem → investimento
```

é transferência.

## 33. Consórcios

Permitir posição inicial.

Exemplo:

```text
Carta: R$ 300.000
Parcelas totais: 120
Parcelas já pagas: 20
Valor já pago: R$ 24.000
Parcela atual: 21
Valor da parcela: R$ 1.200
```

Começar com a posição existente e somar aportes futuros.

Mostrar:
- valor da carta;
- total investido;
- percentual;
- parcelas pagas;
- parcelas restantes.

Valor da carta não é saldo disponível.

## 34. Aplicações financeiras

Permitir:
- posição inicial;
- aportes;
- retiradas;
- rendimentos.

Aportes são transferências.

Rendimentos são receitas de investimento.

## 35. Fechamento financeiro

Período mensal por usuário.

Status:

```text
aberto
fechado
```

Período fechado bloqueia:
- criação;
- edição;
- exclusão.

Pode ser reaberto mediante confirmação.

Registrar:

```text
fechado_em
fechado_por
```

## 36. Exclusão e Soft Delete

Entidades que representem histórico financeiro ou estruturas utilizadas por movimentações devem utilizar **Soft Delete** quando houver exclusão lógica.

A exclusão lógica:
- não remove fisicamente o registro do banco;
- remove o registro das consultas normais;
- preserva o histórico;
- permite recuperação administrativa quando aplicável.

Movimentações devem obrigatoriamente utilizar Soft Delete.

## 37. Auditoria

Preparar estrutura para:
- usuário;
- ação;
- entidade;
- registro;
- data/hora;
- valor anterior;
- valor novo.

A exclusão lógica não elimina a necessidade de auditoria.

Movimentações não devem ser apagadas fisicamente.

## 38. Conciliação

Arquitetura preparada para:
- saldo do sistema;
- saldo do banco;
- conciliado;
- não conciliado.

Não é prioridade inicial.

## 39. Dashboard

Separar:
- saldo disponível;
- resultado do período;
- patrimônio;
- investimentos;
- a pagar;
- a receber;
- previsto x realizado.

## 40. Segurança

Obrigatório:
- autenticação;
- recuperação de senha;
- Policies;
- isolamento por usuário;
- middleware admin;
- bloqueio de usuário;
- auditoria futura.

## 41. Área administrativa

```text
/admin
```

Usuário:

```text
/dashboard
```

Admin poderá futuramente administrar:
- usuários;
- status;
- planos;
- assinaturas;
- pagamentos;
- configurações.

Admin não acessa automaticamente o financeiro dos usuários.

## 42. Fora da V1

- família/compartilhamento;
- planos;
- mensalidades;
- assinaturas;
- cobrança;
- gateway;
- PIX automático;
- boleto;
- inadimplência automática;
- OFX;
- integração bancária;
- aplicativo mobile;
- multiempresa;
- notificações avançadas.

## 43. Padrão de entrega

Como o usuário é iniciante em programação:
- fornecer caminho completo do arquivo;
- fornecer arquivo completo, não apenas trecho;
- fornecer comandos completos;
- fornecer migrations completas;
- fornecer Models completos;
- fornecer Controllers/Livewire completos;
- fornecer Views completas;
- fornecer rotas completas;
- fornecer seeders completos quando aplicáveis;
- fornecer testes quando aplicáveis;
- indicar exatamente onde executar cada comando.

Evitar explicações longas. Priorizar código e passos objetivos.

Nunca solicitar arquivos desnecessariamente quando a documentação ou o código já disponível for suficiente.
