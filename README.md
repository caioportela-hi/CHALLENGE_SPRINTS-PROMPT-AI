# ChargeGrid Intelligence Bot
### Chatbot operacional para gerenciamento de eletropostos GoodWe — EV Challenge 2026 | FIAP

---

## Integrantes do Projeto

| Nome | RM |
|------|-----|
| *Caio César Portela França* | *573127* |
| *Davi Teodoro Novais* | *571022* |
| *Gustavo Curis de Francisco* | *569704* |
| *Lourenco Borges da Silva* | *569515* |
| *Tiago Pimentel Muniz* | *574148* |

---

## Sobre o Desafio

O **EV Challenge 2026**, proposto pela **GoodWe**, apresenta um problema real relacionado à operação comercial de eletropostos: a necessidade de organizar informações sobre recargas, consumo, faturamento, disponibilidade e comportamento dos equipamentos em uma experiência mais centralizada e acessível.

A partir desse desafio, o grupo desenvolveu a solução **ChargeGrid Intelligence**, voltada ao suporte operacional de eletropostos por meio de um assistente conversacional.

---

## Proposta do Chatbot

O **ChargeGrid Intelligence Bot** é um assistente conversacional com IA destinado ao **operador comercial de eletropostos**, com o objetivo de centralizar, contextualizar e responder a demandas operacionais em linguagem natural.

### Persona atendida

**Operador comercial** — responsável pela gestão diária do eletroposto, que precisa de respostas rápidas sobre consumo, faturamento, status dos conectores e alertas operacionais, sem depender de suporte técnico para dúvidas rotineiras.

---

## Implementação do RAG — Sprint 2

Na Sprint 2, foi implementada uma camada de **RAG (Retrieval-Augmented Generation)** para permitir que o chatbot utilizasse uma base de conhecimento técnica, em vez de depender exclusivamente do conhecimento genérico do modelo.

A cada pergunta:

1. A query era vetorizada e comparada aos documentos da base
2. Os documentos mais relevantes eram recuperados
3. O contexto recuperado era inserido no prompt antes da geração da resposta

Essa abordagem reduzia o risco de alucinações e permitia fundamentar respostas com informações recuperadas da base de conhecimento.

Na Sprint 3, o foco da evolução passou a ser o **núcleo conversacional**, com uso de agentes, memória por sessão, guardrails e avaliação sistemática entre diferentes modelos de linguagem.

---

## O que o chatbot resolve

- Consultas e orientações sobre **status de conectores**
- Dúvidas sobre **orquestração de potência** e balanceamento de carga
- Informações sobre **ciclos de recarga** e histórico
- Questões de **faturamento** e consumo por período
- Interpretação de **alertas e códigos de erro**
- Orientações operacionais relacionadas ao contexto GoodWe

### O que o chatbot NÃO faz

- Controle direto ou atuação física nos equipamentos
- Manutenção técnica aprofundada
- Aconselhamento jurídico profissional
- Aconselhamento financeiro profissional
- Orientações elétricas potencialmente perigosas
- Gestão financeira ou ERP

---

## Componentes do Projeto

| Componente | Tecnologia | Função | Sprint |
|-----------|------------|--------|--------|
| Modelo de linguagem | `gpt-5-nano` (OpenAI) | Modelo final selecionado experimentalmente | Sprint 3 |
| Framework de agentes | `LangGraph` | Orquestração do fluxo conversacional | Sprint 3 |
| Memória de sessão | `InMemorySaver` + `thread_id` | Memória conversacional separada por sessão | Sprint 3 |
| Guardrails | Regras + nós do LangGraph | Segurança, controle de escopo e bloqueio de situações críticas | Sprint 3 |
| Embeddings | `text-embedding-3-small` (OpenAI) | Vetorização de documentos e queries | Sprint 2 |
| Índice vetorial | `FAISS` | Busca por similaridade semântica | Sprint 2 |
| Base de conhecimento | `knowledge_base.json` | Documentação utilizada pelo RAG | Sprint 2 |
| Segurança de credenciais | Google Colab Secrets | API Key mantida fora do código | Sprints 2 e 3 |

---

## Fluxo de Funcionamento — Sprint 3

```text
Operador envia pergunta
        ↓
Recuperação do histórico da sessão
        ↓
Validação por guardrails
        ↓
┌──────────────────────────────┐
│ Mensagem segura e no escopo? │
└──────────────────────────────┘
        ↓                ↓
       Sim              Não
        ↓                ↓
Geração pela LLM   Resposta segura/
selecionada        bloqueio controlado
        ↓
Atualização da memória da sessão
        ↓
Resposta exibida ao operador
```

---

## Resultados da Sprint 03

| Modelo | Adequação | Latência média | Tokens médios |
| ------ | --------- | -------------- | ------------- |
| `gpt-4o-mini` | 92,9% | 1,370 s | 562,8 |
| `gpt-5-nano` | 100% | 2,678 s | 797,1 |

O **GPT-5 nano** foi selecionado para a versão final por apresentar 100% de adequação nos testes, além de aprovação nos testes de segurança e memória.

---

## Arquivos da Sprint 03

- `ChargeGrid_GoodWe_Sprint3.ipynb`
- `relatorio_modelos.md`

---

*Projeto desenvolvido para o EV Challenge 2026 — GoodWe × FIAP*
