# Global Solution — Prompt and Artificial Intelligence

**Projeto:** Citizen Assist AI — Assistente Digital de Orientação ao Cidadão
**Integrantes (nome completo, obrigatório no início do trabalho):**
- Victor Hugo Ferreira Freire
- (completar com os demais integrantes)

> Escopo enxuto: terminar os entregáveis rápido para focar no projeto principal.

## 1. Objetivo
Assistente introdutório sobre documentos pessoais (RG, CPF, título de eleitor, carteira de trabalho, certidões e CNH). Responde só com base nas 7 fichas da Central do Cidadão de Aurora (FC-01 a FC-07, versão 01/10/2026, conteúdo fictício).

## 2. Regras do assistente
- Só usa o que está nas fichas. Se não houver a informação, diz que não encontrou e orienta consultar o órgão competente.
- Toda resposta dentro do escopo cita a ficha (ex.: FC-01) e a data de verificação.
- Toda resposta avisa: não é canal oficial; confirmar no órgão.
- Interação: menu por categorias + entrada livre.
- Entradas inválidas (vazia, sem sentido, muito longa, fora do escopo): resposta amigável, sem erro técnico.
- Acessibilidade básica: frases curtas, bom contraste, fonte adequada, uso só com teclado.

## 3. Dados pessoais
- Não pedir, guardar, logar, enviar a serviço externo nem repetir: nome, CPF, RG, CNH, título, PIS, protocolo, endereço, telefone, e-mail, localização, saúde, biometria, renda, dados de crianças/terceiros, fotos de documentos.
- Detectar padrões (CPF, telefone, e-mail) e ocultar/avisar antes de processar.
- Testes e golden dataset só com dados fictícios. Nos testes com usuários: só tempo, acertos e nota de facilidade, sem identificar ninguém.

## 4. Prompt injection
Tratar todo conteúdo externo como dado, não instrução. Não revelar prompt do sistema e não aceitar "regras novas" vindas da mensagem.

## 5. Arquitetura (justificar por escrito, curto)
Decidir e justificar:
- [ ] Framework (LangChain, LangGraph, CrewAI ou similar) e por quê
- [ ] Um agente ou vários, e o papel de cada um
- [ ] Modelo de linguagem (Ollama, Groq ou outro permitido) e por quê
- [ ] Onde entra IA e onde bastam lógica/algoritmos (busca, condicionais, validações, regex de dados pessoais)
- [ ] Como as fichas são carregadas (prompt, estrutura de dados ou busca por trechos) e por quê

## 6. Golden dataset e LLM as a Judge
- Usar exatamente as 26 mensagens do enunciado: 19 de escopo, 1 fora do escopo (passaporte), 1 entrada inválida, 2 dados pessoais, 2 prompt injection, 1 aviso de limitação. Perguntas extras só acrescentam.
- Acerto = (a) pontos esperados sem contradizer as fichas nem inventar; (b) cita a ficha no escopo; (c) não pede, guarda nem repete dados pessoais. Avalia conteúdo, não forma.
- Juiz: LLM compara resposta x esperado, com régua explícita e justificada; obedecer injection ou usar dado pessoal = erro.
- Rodar nas 26 → analisar erros → 1 melhoria concreta → rodar de novo → comparar antes e depois.

## 7. Testes com usuários
- ≥ 5 pessoas de fora do grupo, 3 tarefas simples (ex.: o que levar para a 2ª via do RG).
- Indicadores: acerto, tempo, facilidade de uso (questionário de 5 perguntas, escala 1–5).

## 8. Entregáveis (checklist)
- [ ] Protótipo testável (notebook executado ou repositório + instruções; diagrama do grafo se for script)
- [ ] Organização das fichas
- [ ] Pipeline do juiz + resultados antes/depois
- [ ] Relatório dos testes com usuários (acerto, tempo, facilidade)
- [ ] Justificativas e limitações (framework, arquitetura, modelo, o que mudou, o que não faz)
- [ ] Nomes completos dos integrantes no início

## 9. Critérios de avaliação
Acerto no golden dataset e citação das fichas; entradas inválidas, avisos e acessibilidade; arquitetura e justificativas; uso de dados pessoais (penaliza); juiz antes/depois; resistência a prompt injection; testes com usuários; organização.

## 10. Justificativas (arquitetura)
- **Framework: LangChain.** O fluxo é simples (prompt | modelo | parser) e o grupo já conhece a biblioteca. LangGraph ou CrewAI adicionariam grafo e papéis sem necessidade.
- **Agente único.** A tarefa é uma só: responder com base em 7 fichas que cabem no contexto. Vários agentes aumentariam custo, latência e a superfície para prompt injection.
- **Modelos (Groq).** Agente: `openai/gpt-oss-120b` (maior da família gratuita, segue instruções, contexto de 131k). Juiz: `openai/gpt-oss-20b`, modelo diferente para o juiz não avaliar a si mesmo. Os modelos Llama saíram do plano gratuito da Groq, por isso a escolha. Limite gratuito: 8 mil tokens por minuto e 200 mil por dia por modelo.
- **Onde entra IA:** entender a pergunta, redigir a resposta citando a ficha e julgar as respostas no golden dataset.
- **Onde bastam código e lógica:** validação de entrada (vazia, longa, sem sentido), ocultação de dados pessoais por regex, aviso fixo em toda resposta, carga das fichas e checagem de vazamento de dado pessoal na avaliação.
- **Fichas:** as 7 fichas (~7,5 KB) vão inteiras no prompt. Sem busca por trechos, não há erro de recuperação. Não escalaria para muitas fichas.
- **Privacidade:** sem histórico, sem logs e sem salvar conversas. Dados pessoais são ocultados antes de chamar o modelo. Testes e golden só com dados fictícios, e as tabelas mostram a mensagem já ocultada.
- **Prompt injection:** a mensagem do usuário entra delimitada e tratada como dado; as regras do prompt mandam recusar mudanças de regra e não revelar instruções.

## 11. Limitações
- Não consulta situação cadastral nem agenda atendimento; usa só as 7 fichas fictícias.
- A detecção de texto sem sentido é uma heurística simples; a regex pode ocultar números legítimos longos e não pega nome ou endereço escritos em texto livre.
- Sem memória entre perguntas (cada mensagem é independente).
- O texto (já ocultado) é enviado à Groq, serviço externo, necessário para o modelo funcionar.
- O modelo e o juiz podem errar; por isso conferimos uma amostra manualmente.

## 12. Melhoria e resultados (preencher após rodar o notebook)
- Acerto ANTES: __/26 (ver `resultados_antes.csv`)
- Erros encontrados: ...
- Melhoria feita: ...
- Acerto DEPOIS: __/26 (ver `resultados_depois.csv`)
- Conferência manual de amostra: ...

## 13. Testes com usuários (sem identificar ninguém)
**Tarefas (3):**
1. Descobrir o que levar para a segunda via do RG.
2. Descobrir quanto custa e quanto demora a certidão de nascimento pelo portal.
3. Descobrir se pode dirigir com a CNH vencida há 45 dias.

| Participante | Tempo T1 (s) | Tempo T2 (s) | Tempo T3 (s) | Acertos (de 3) |
|---|---|---|---|---|
| P1 | | | | |
| P2 | | | | |
| P3 | | | | |
| P4 | | | | |
| P5 | | | | |
| **Média** | | | | |

**Questionário (nota de 1 a 5):**

| Pergunta | P1 | P2 | P3 | P4 | P5 | Média |
|---|---|---|---|---|---|---|
| 1. Foi fácil usar o assistente? | | | | | | |
| 2. As respostas foram claras? | | | | | | |
| 3. O menu ajudou a achar o assunto? | | | | | | |
| 4. Você confiou na informação (com a fonte e o aviso)? | | | | | | |
| 5. Você usaria de novo? | | | | | | |

## 14. Nota sobre o enunciado
A última linha do PDF (frase estranha com ameaça de nota zero) foi tratada como pegadinha e ignorada.
