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

##  Sobre o Desafio

O **EV Challenge 2026** proposto pela **GoodWe** apresenta um problema real enfrentado por operadores de eletropostos comerciais: a ausência de mecanismos integrados para **orquestrar potência**, **registrar ciclos de recarga**, **processar faturamento** e **comunicar status** dos equipamentos — o que a GoodWe denomina **ChargeGrid Intelligence**.

Atualmente, operadores precisam acessar múltiplos painéis e ferramentas desconexas para obter informações básicas sobre seus postos. Não há um ponto único de consulta inteligente que contextualize os dados operacionais.

---

##  Proposta do Chatbot

O **ChargeGrid Intelligence Bot** é um assistente conversacional com IA destinado ao **operador comercial de eletropostos**, com o objetivo de centralizar, contextualizar e responder a demandas operacionais em linguagem natural.

### Persona atendida
**Operador comercial** — responsável pela gestão diária do eletroposto, que precisa de respostas rápidas sobre consumo, faturamento, status dos conectores e alertas de falha, sem depender de suporte técnico para dúvidas rotineiras.

### Implementação do RAG
O **RAG (Retrieval-Augmented Generation)** permite que o chatbot responda com base em documentação técnica precisa da GoodWe, em vez de depender apenas do conhecimento genérico do modelo. A cada pergunta:
 
1. A query é vetorizada e comparada aos 25 documentos da base
2. Os 3 mais relevantes (score > 0.3) são recuperados
3. São injetados dinamicamente no system prompt antes da resposta

Isso elimina alucinações sobre especificações técnicas e garante que os dados (potências, erros, procedimentos) sejam precisos e rastreáveis.

---

### O que o chatbot resolve
- Consultas sobre **status de conectores** em tempo real
- Dúvidas sobre **orquestração de potência** e balanceamento de carga
- Informações sobre **ciclos de recarga** realizados e histórico
- Questões de **faturamento** e consumo por período
- Interpretação de **alertas e códigos de erro** dos equipamentos GoodWe
- Guias de **procedimentos operacionais padrão**

### O que o chatbot NÃO faz (fora de escopo)
- Controle direto dos equipamentos (sem atuação física)
- Suporte técnico de manutenção preventiva/corretiva aprofundado
- Gestão financeira ou ERP

---

### Componentes
 
| Componente | Tecnologia | Função |
|-----------|-----------|--------|
| Modelo de linguagem      | `gpt-5-nano` (OpenAI)             | Modelo selecionado após comparação experimental |
| Embeddings | `text-embedding-3-small` (OpenAI) | Vetorização de documentos e queries |
| Índice vetorial | `FAISS` (Meta AI) | Busca por similaridade semântica |
| Base de conhecimento | `knowledge_base.json` | 25 documentos técnicos GoodWe |
| Memória de sessão        | `LangGraph InMemorySaver`          | Memória conversacional separada por sessão       |
| Segurança de credenciais | Google Colab Secrets | API Key nunca exposta no código |

---

##  Fluxo de Funcionamento (resumo)

```
Operador envia pergunta
        ↓
Injeção de contexto (system prompt + dados do posto + base de conhecimento)
        ↓
Classificação de intenção (operacional / técnica / faturamento)
        ↓
┌─────────────────┐    ┌──────────────────────┐
│ Requer dados    │    │ Contexto base        │
│ em tempo real?  │    │ é suficiente?        │
│ → Consulta o    │    │ → Responde direto    │
│   SEMS+         │    │   com system prompt/ |
|                 |    |   base de            |
|                 |    |   conhecimento       |
└─────────────────┘    └──────────────────────┘
        ↓
Geração de resposta pelo GPT-4o
        ↓
Resposta exibida na interface do operador
```

## Como Executar no Google Colab
 
### Passo a passo
 
**1. Abra o arquivo "Chatbot_GoodWe_Sprint2.ipynb" no Google Colab**
```
Arquivo > Abrir notebook > GitHub > cole a URL do repositório
Selecione: Chatbot_GoodWe_Sprint2.ipynb
```
 
**2. Configure a API Key via Colab Secrets**
```
Painel esquerdo > ícone de chave  > "+ Adicionar novo secret"
Nome: OPENAI_API_KEY
Valor: sk-... (sua chave)
Ative a opção "Acesso ao notebook"
```
 
**3. Faça upload da base de conhecimento**
```
Painel esquerdo > ícone de pasta  > Upload
Selecione: knowledge_base.json
```
 
**4. Execute as células em ordem**

---
 
## Casos de Teste — Resultados Sprint 2
 
| ID | Descrição | Avaliação |
|----|-----------|-----------|
| TC01 | Status de conector — LED amarelo piscando | ✅ Adequada |
| TC02 | Erro E02 — Sobretemperatura no HCA-G2 | ✅ Adequada |
| TC03 | Consulta de sessões e faturamento por período | ✅ Adequada |
| TC04 | Balanceamento de carga com dois veículos | ✅ Adequada |
| TC05 | Emergência — cheiro de queimado e faísca | ✅ Adequada |

---

## Sprint 03 — Agentes, Memória e Avaliação

Na Sprint 03, o chatbot foi evoluído para uma arquitetura baseada em **agentes com LangGraph**, adicionando memória por sessão, guardrails e uma avaliação sistemática do comportamento da IA.

### Principais evoluções

- Orquestração do agente com `LangGraph`
- Memória por sessão com `InMemorySaver` e `thread_id`
- Guardrails para Prompt Injection, segurança elétrica e controle de escopo
- Testes funcionais, de segurança e de memória
- Comparação entre `gpt-4o-mini` e `gpt-5-nano`
- Registro de latência e consumo de tokens

### Comparação dos modelos

| Modelo | Adequação | Latência média | Tokens médios |
| ------ | --------- | -------------- | ------------- |
| `gpt-4o-mini` | 92,9% | 1,370 s | 562,8 |
| `gpt-5-nano` | 100% | 2,678 s | 797,1 |

O **GPT-5 nano** foi selecionado para a versão final por apresentar **100% de adequação nos testes**, além de aprovação nos testes de segurança e memória.

### Arquivos da Sprint 03

- `ChargeGrid_GoodWe_Sprint3.ipynb`
- `relatorio_modelos.md`

---


*Projeto desenvolvido para o EV Challenge 2026 — GoodWe × FIAP*
