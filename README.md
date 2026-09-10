# Incretina · Academia Médica

Suíte educacional em português para médicos: GLP-1, GIP, agonismo duplo/triplo e terapias metabólicas. Aplicação estática sem dependências de frontend, com função opcional da OpenAI para Netlify.

## O que está implementado

- 18 aulas sequenciais com objetivos, síntese, referências, conclusão e notas.
- 20 leituras de estudos: PICO, resultados, gráficos quando pertinentes, limitações e aplicação. Leituras baseadas em bulas estão explicitamente identificadas.
- 9 fichas farmacológicas, 8 protocolos, 8 casos comentados, 30 questões e 29 cartões com revisão espaçada.
- Busca global, filtros, favoritos, notas e backup/importação de progresso local.
- Calculadoras de risco/NNT, IMC/variação ponderal, custo e visualização do esquema inicial de titulação.
- Tutor com recuperação de conteúdo local, Responses API, fontes, código de acesso e chave apenas no servidor.
- Layout responsivo, navegação por teclado, CSS de impressão e política de segurança de conteúdo no Netlify.

## Estado clínico e editorial

Consulta de fontes: 10/09/2026. Esta edição tem referências EMA/FDA e artigos fundamentais. Não é revisão sistemática, curso acreditado nem garantia de cobertura completa da literatura. Não confere título de especialista.

**Revisão médica independente e auditoria integral das bulas brasileiras ainda pendentes.** Alguns artigos só tiveram registro/resumo acessível; números foram complementados com fontes regulatórias e a limitação é indicada por ficha. Não apresentar a versão como protocolo assistencial validado. Não foram usados relatos jornalísticos para definir doses.

Antes de usar informações na assistência, verificar produto, país, apresentação, indicação, população e bula profissional vigente. Não converter cliques, mg ou “unidades” entre produtos. Não inserir prontuários ou dados identificáveis na plataforma.

## Rodar e verificar

Node.js 22 ou superior e Python 3 para servidor local simples.

```sh
npm run build
npm test
npm run dev
```

A publicação usa diretamente `dist/`. O build valida a integridade do conteúdo e a sintaxe, sem compilação. Não existem dependências npm para instalar.

Rotas usam fragmentos (`#aula/origens`), evitando regras de fallback de SPA e colisões com `/api/tutor`.

## GitHub

O projeto está pronto para um repositório dedicado, recomendado como privado durante a revisão clínica. O conector disponível nesta sessão permite trabalhar em repositórios existentes, mas não oferece criação de repositório. Após criar `efigie/incretina-academy` vazio e autorizar acesso, publicar esta árvore sem a pasta `.openai/` (metadados do preview Sites), mantendo os demais arquivos, inclusive `.github/workflows/validate.yml`.

Não copiar arquivos `.env`, tokens, pasta `.netlify`, anotações locais ou artefatos de QA para GitHub. A pasta `.openai` pertence à identidade do preview e não é necessária no Netlify.

## Netlify

1. Importar o repositório do GitHub como novo projeto.
2. O arquivo `netlify.toml` define build `npm run build`, publicação `dist` e funções `netlify/functions`.
3. Fazer o primeiro deploy sem IA: todo o conteúdo deve funcionar, e o tutor mostra que aguarda ativação.
4. Para IA, configurar nas variáveis do servidor, com escopo de Functions:
   - `OPENAI_API_KEY`: chave de um projeto OpenAI.
   - `OPENAI_MODEL`: identificador de modelo de texto disponível na conta, compatível com Responses API.
   - `TUTOR_ACCESS_CODE`: código longo e aleatório para limitar acesso ao tutor.
5. Redeploy após configurar. GET `/api/tutor` deve responder `ready:true`, sem mostrar segredos.
6. Testar uma pergunta, código errado, fonte devolvida e erro de limite. Configurar orçamento e monitoramento na conta OpenAI.

O acesso ao conteúdo é público se o projeto Netlify for público; o código protege **somente o tutor**, não o site inteiro. Autenticação individual de médicos não está implementada. Configure proteção de deploy/SSO do provedor se necessário.

A limitação de taxa por IP/domínio está declarada na função; verificar disponibilidade e aplicação efetiva no plano. Ela não é orçamento global nem defesa contra abuso distribuído. O código de acesso é comparado no servidor; não é armazenado no navegador nem enviado à OpenAI.

A assinatura do ChatGPT não fornece automaticamente crédito de API para este app. Sem os segredos, a plataforma não faz chamadas à OpenAI.

## IA: funcionamento e limites

O servidor seleciona até quatro itens por relevância lexical, inclui fontes em contexto e limita a resposta. Não realiza busca ao vivo, não lê PDFs no momento da pergunta e não é RAG semântico. Responde uma pergunta por requisição; o histórico visual não é enviado como memória de conversa. Use perguntas completas.

`store:false` é enviado à Responses API. Isso não significa promessa de zero retenção por todos os provedores; políticas contratuais e logs de infraestrutura continuam aplicáveis. A aplicação não registra perguntas no código da função. O histórico do chat vive só na sessão da página; notas e progresso ficam em localStorage.

Fonte oficial da implementação: https://developers.openai.com/api/docs/guides/text
Funções Netlify: https://docs.netlify.com/build/functions/api/

## Estrutura

- `dist/content.js`: corpus clínico e metadados de evidência.
- `dist/learning.js`: questões, casos e cartões.
- `dist/app.js`: interface e fluxos.
- `dist/core.js`: busca, cálculos e revisão.
- `netlify/functions/tutor.mjs`: integração protegida com OpenAI.
- `tests/core.test.mjs`: cálculos, validação e fronteiras do backend, com API simulada.
- `scripts/validate.mjs`: integridade de IDs, referências e sintaxe.
- `EDITORIAL.md`: atualização e revisão clínica.

## Validação realizada

Integridade do conteúdo, sintaxe e testes automatizados de cálculos/regras e API simulada. Não houve chamada paga real à OpenAI, deploy Netlify ou validação visual em navegador. A versão privada do Sites hospeda apenas arquivos estáticos e não executa a função Netlify.

## Edição 1.1

Os 20 estudos receberam perguntas próprias de discussão crítica com comentários reveláveis. Cada artigo mostra o nível de acesso às fontes e permite inserir um roteiro estruturado nas notas existentes, incluído no backup habitual. Corrigida validação do tutor para rejeitar JSON nulo ou não objeto com HTTP 400. IA segue sem teste de API real; revisão médica e auditoria brasileira permanecem pendentes.
