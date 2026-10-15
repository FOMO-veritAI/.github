# FOMO + VeritAI

**Grupo 5 · Challenge 1 (Fake News / Desinformação) · Residência em IA, Instituto Eldorado e UnB Gama, 2026**

> Como a inteligência artificial pode ajudar as pessoas a avaliar a confiabilidade de informações **sem substituir o pensamento crítico**?

A nossa resposta é verificar na origem. O **FOMO** (*Fear of Missing Objectivity*) é uma plataforma de notícias em que nenhuma matéria chega ao leitor sem verificação. A **VeritAI** é o serviço de IA que verifica cada afirmação antes da publicação, mostra as evidências e declara a incerteza. Quem decide é sempre uma pessoa.

## O produto

1. **O veículo escreve a notícia** e indica as afirmações factuais. A VeritAI pode sugerir quais frases verificar; o publisher escolhe e edita cada uma.
2. **A VeritAI analisa cada afirmação** antes da publicação e devolve um relatório por afirmação, com:
   - o resultado;
   - as evidências, com fonte, data, trecho e a relação de cada trecho com a afirmação;
   - as fontes independentes;
   - a justificativa e as limitações da análise.
3. **Um revisor humano decide:** aprovar, devolver ao veículo ou pedir comprovação. Só a aprovação publica.
4. **O leitor vê o resultado de cada afirmação** com as fontes à vista. Não há selo de "verdadeiro" ou "falso" para a notícia inteira.

Os resultados possíveis para uma afirmação são quatro: *as evidências apoiam*, *as evidências contradizem*, *evidências insuficientes* e *conclusões diferentes*. A falta de evidência nunca vira "falso". Enquanto o modelo não for calibrado com dados rotulados, não há porcentagem de confiança, e a tela mostra "Não avaliável".

| Princípio | Como aparece no produto |
|---|---|
| Autonomia do leitor | Resultado por afirmação, sempre junto das evidências |
| Incerteza honesta | "Insuficiente" não é "falso"; sem calibração, não há porcentagem |
| Evidência rastreável | Cada relatório guarda fonte, data, trecho, versões dos modelos e da base |
| Nenhum LLM decide | A decisão sai de NLI e regras explícitas, auditáveis e reprodutíveis |
| Humano nas decisões de dano | Só o revisor publica; afirmação contradita vai em destaque para revisão, sem bloqueio automático |

### Como a VeritAI decide

1. **Busca de evidências:**
   - numa base própria com 590 documentos públicos (IBGE, INEP, Agência Gov e Agência Brasil);
   - nos documentos anexados pelo veículo;
   - na web, de forma opcional.

   A busca é híbrida: palavras-chave (BM25) e similaridade semântica, com fusão RRF. Documentos publicados depois da data da notícia ficam de fora.
2. **Janelas de 1 a 2 frases** dos documentos encontrados, escolhidas por similaridade com a afirmação.
3. **NLI multilíngue:** diz se cada janela confirma, contradiz ou é neutra em relação à afirmação.
4. **Verificador por regras:** confere se a entidade, o período e o número da afirmação estão de fato na janela. Ele só rebaixa relações para neutro, nunca cria um resultado, e cada intervenção aparece nas limitações do relatório.
5. **Agregação** por fontes independentes. Cópias e páginas do mesmo domínio contam como uma fonte só.

Há dois perfis de modelos pré-treinados:
- **V0:** MiniLM;
- **V1:** e5-base com mDeBERTa-v3.

Não houve treino: não existe conjunto em português com afirmação, evidência e quatro classes sem ressalvas graves. O relatório técnico explica essa escolha e o critério para um modelo treinado substituir o atual.

### Resultados principais (perfil V1)

- **Estatísticas oficiais brasileiras, com busca na base:** nenhuma afirmação falsa aprovada. O verificador levou o macro-F1 de 0,264 para 0,617.
- **Validação dos releases:** macro-F1 de 0,589, com 4,8% de falsas aprovações.
- **Notícias reais** (71 afirmações, referência provisória: conferência por IA): nenhuma falsa aprovação, mas 22,7% das afirmações que a conferência considerou apoiadas saíram como contraditas. É por isso que o revisor humano existe.

Os rótulos dos conjuntos próprios foram escritos por IA e conferidos por um anotador. Falta um segundo anotador. Os números completos, com intervalos de confiança, estão no relatório técnico.

## Entregáveis

| Entregável | Onde está |
|---|---|
| Datasets | [`veritai-dados`](https://github.com/FOMO-veritAI/veritai-dados#conjuntos-de-dados) (conjuntos próprios, base de evidências e checagens) · [levantamento de 18 conjuntos públicos](https://github.com/FOMO-veritAI/fomo-docs/tree/main/entregas/semana-2-datasets) |
| Requisitos funcionais e não funcionais | [`fomo-requisitos/docs`](https://github.com/FOMO-veritAI/fomo-requisitos/tree/main/docs) (132 requisitos atômicos e matriz de rastreabilidade) · [relatório da semana 3](https://github.com/FOMO-veritAI/fomo-docs/tree/main/entregas/semana-3-eda-e-requisitos) |
| Análise exploratória (EDA) | [`veritai-dados/eda`](https://github.com/FOMO-veritAI/veritai-dados/tree/main/eda) (notebooks e relatório) |
| Guiding Questions | [`fomo-docs/entregas/semana-1-engage`](https://github.com/FOMO-veritAI/fomo-docs/tree/main/entregas/semana-1-engage) |
| Organização, Scrum e papéis | [relatório Scrum](https://github.com/FOMO-veritAI/fomo-docs/tree/main/entregas/semana-1-engage) · [equipe, abaixo](#equipe) |
| Modelo de IA e relatório técnico | [`veritai`](https://github.com/FOMO-veritAI/veritai) (serviço e configuração dos modelos) · [notebook de avaliação](https://github.com/FOMO-veritAI/veritai-dados/tree/main/avaliacao) · [relatórios, notebook e modelo da semana 5](https://github.com/FOMO-veritAI/fomo-docs/tree/main/entregas/semana-5-modelo) |
| Descrição final do produto | esta página ([O produto](#o-produto)) · [mapa navegável da arquitetura](https://fomo-veritai.github.io/fomo/arquitetura.html) · [apresentação final](https://github.com/FOMO-veritAI/fomo-docs/tree/main/entregas/semana-6-apresentacao) |
| Código e README | [`fomo`](https://github.com/FOMO-veritAI/fomo) e [`veritai`](https://github.com/FOMO-veritAI/veritai), cada um com o seu README |

Todas as entregas semanais, com o que cada uma traz, estão em [`fomo-docs/entregas`](https://github.com/FOMO-veritAI/fomo-docs/tree/main/entregas).

## Repositórios

| Repositório | Conteúdo |
|---|---|
| [fomo](https://github.com/FOMO-veritAI/fomo) | Plataforma: frontend Angular e API FastAPI de publicação, revisão e leitura; deploy com Docker |
| [veritai](https://github.com/FOMO-veritAI/veritai) | Serviço de IA: `POST /extrair` e `POST /analisar`, base própria de evidências, verificador, avaliação, container |
| [veritai-dados](https://github.com/FOMO-veritAI/veritai-dados) | Dados: fontes, coleta, conjuntos rotulados, revisão, avaliação e EDA |
| [fomo-requisitos](https://github.com/FOMO-veritAI/fomo-requisitos) | Requisitos: especificação, plano, constituição do projeto, fluxo de IA, matriz de rastreabilidade |
| [fomo-docs](https://github.com/FOMO-veritAI/fomo-docs) | Documentação, entregas semanais, capturas de tela e painel "VeritAI por dentro" |

Para rodar localmente, siga o README do [`veritai`](https://github.com/FOMO-veritAI/veritai) (serviço na porta 8100) e depois o do [`fomo`](https://github.com/FOMO-veritAI/fomo) (API e frontend). O frontend também está publicado em <https://fomo-veritai.github.io/fomo/>. Sozinho, sem a API, ele avisa que a API está desligada.

O [mapa navegável da arquitetura](https://fomo-veritai.github.io/fomo/arquitetura.html) mostra cada parte do sistema, o que passa entre elas, cinco fluxos guiados e as decisões de arquitetura.

## Equipe

| Integrante | Papel no Scrum | Frentes de trabalho |
|---|---|---|
| Mickeias Charles | Scrum Master | arquitetura, plataforma FOMO (frontend e backend), serviço VeritAI e integração, infraestrutura |
| Cauan Soares | Desenvolvedor | requisitos e regras de negócio; base de evidências, verificador, extração de afirmações e avaliação da VeritAI |
| Isabel Azar | Desenvolvedora | análise e ciência de dados; EDA dos conjuntos públicos |
| Mariah Gladys | Desenvolvedora | pesquisa, documentação, design de interface e identidade visual |

**Product Owners:** a mentoria. **Mentores:** Guilherme Daniel, Alexandre Santos, Jefferson Viana, Renan Evangelista e Isaque Alves.

Projeto acadêmico, sem fins comerciais. Os conjuntos de terceiros (AVeriTeC, AVERITEC-BR, LIAR-BR) não são redistribuídos; as licenças e as fontes dos textos citados estão no [`veritai-dados`](https://github.com/FOMO-veritAI/veritai-dados).
