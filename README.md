# TicketFlow

> 🚧 **Status: em construção.** Projeto de estudo e portfólio, desenvolvido de forma incremental. O domínio é fictício e não representa nenhuma empresa ou cliente real.

Aplicação fullstack para venda de ingressos de eventos, com controle de estoque, reserva temporária, pagamento via gateway externo e divisão automática do valor (split) entre produtor e plataforma.

## Problema de negócio

Produtoras de eventos vendem ingressos com estoque limitado, e dois clientes não podem comprar o último ingresso ao mesmo tempo. Quem inicia uma compra precisa ter o ingresso garantido por alguns minutos enquanto paga, e o ingresso volta a ficar disponível se o pagamento não acontecer. O pagamento depende de um gateway externo, que pode recusar a cobrança, demorar ou ficar indisponível. Depois que o pagamento é aprovado, o valor precisa ser repassado ao produtor, descontada a taxa da plataforma. Esse repasse também pode falhar e precisa ser registrado, rastreado e reprocessado sem cobrar nem repassar em duplicidade.

## Objetivo

Construir uma aplicação que:

- venda ingressos **sem overbooking**, mesmo com requisições simultâneas;
- mantenha **reservas temporárias** que expiram e devolvem o estoque;
- integre com um **gateway de pagamento externo**, tratando recusa, timeout e indisponibilidade;
- execute o **split** do pagamento em segundo plano, com retentativas e reprocessamento manual;
- seja **idempotente**, de modo que requisições repetidas não gerem cobranças ou repasses duplicados;
- mantenha o **histórico de status** de cada pedido para rastreabilidade;
- ofereça um **front-end** para o comprador reservar e pagar, com contagem regressiva da reserva.

## Stack prevista

| Área | Tecnologia |
|---|---|
| Linguagem | TypeScript (strict) sobre Node.js LTS |
| Back-end | NestJS |
| Front-end | React + TypeScript (Vite), React Router, React Hook Form |
| Banco de dados | PostgreSQL |
| ORM e migrations | Prisma |
| Agendamento | @nestjs/schedule |
| Integração HTTP | fetch nativo com timeout (AbortController) |
| Testes | Jest, Supertest, Testcontainers, nock |
| Documentação da API | OpenAPI (@nestjs/swagger) |
| Infraestrutura | Docker e Docker Compose |
| CI/CD | GitHub Actions |

O repositório também terá o **fakepay**, um gateway de pagamento simulado que reproduz cenários de sucesso, recusa, timeout, erro HTTP e resposta inválida.

## Estrutura

```
apps/
  api/       # NestJS — domain/ em TypeScript puro + módulos da API
  web/       # React + TypeScript + Vite
  fakepay/   # gateway de pagamento simulado
docs/        # diagrama ER, C4 e decisões de arquitetura
```

## Padrão de resposta da API

Todas as respostas seguem o mesmo envelope:

```json
{ "success": true, "code": 200, "message": "OK", "data": {}, "error": null }
```

## Roadmap

| Fase | Entrega | Release |
|---|---|---|
| **M1 — Domínio** | Regras de negócio em TypeScript puro: estoque, reserva com expiração, máquina de estados do pedido, cálculo de split e testes unitários | `v0.1.0` |
| **M2 — API + banco** | PostgreSQL com migrations, baixa atômica de estoque, endpoints REST de eventos, reservas e pedidos com erros padronizados | `v0.2.0` |
| **M3 — Front-end** | React com lista de eventos, reserva com contagem regressiva, checkout e status do pedido | `v0.3.0` |
| **M4 — Integração + assíncrono** | Pagamento via gateway externo, idempotência, split em segundo plano (outbox), reprocessamento e logs rastreáveis | `v0.4.0` |
| **M5 — Produção** | Autenticação e autorização, containerização, pipeline de CI e documentação completa | `v1.0.0` |

O andamento detalhado está nas [issues](../../issues) e nos [milestones](../../milestones) do repositório.

## Como contribuir

O projeto segue um fluxo simples, o mesmo para qualquer mudança:

1. **Issue primeiro:** toda mudança nasce de uma issue, vinculada a um milestone.
2. **Uma branch por issue**, no formato `tipo/numero-descricao` (ex.: `feat/5-lote-invariantes`).
3. **Commits** no padrão [Conventional Commits](https://www.conventionalcommits.org/pt-br/), em português, com o número da issue:

   | Tipo | Uso |
   |---|---|
   | `feat` | funcionalidade nova |
   | `fix` | correção de bug |
   | `test` | testes |
   | `refactor` | melhoria sem mudar comportamento |
   | `docs` | documentação |
   | `chore` | configuração e manutenção |

   Exemplo: `feat(api): adiciona baixa atômica de estoque (#12)`

4. **Pull Request** para a `main`, usando o template do repositório, com `Closes #N` na descrição para fechar a issue no merge.

> Este projeto teve uma primeira versão em Java/Spring Boot, mantida como histórico. Esta versão reescreve a solução em Node.js + TypeScript e adiciona o front-end em React.

## Licença

Projeto de estudo, sem fins comerciais.