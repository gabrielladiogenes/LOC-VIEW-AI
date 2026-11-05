# LOC VIEW AI
## 04 — Decisões de Engenharia

**Projeto:** LOC VIEW AI  
**Versão documentada:** v5.9.3  
**Categoria:** Geolocalização, Validação de Dados e Inteligência Geográfica  
**Tecnologias:** Python · Gradio · OpenStreetMap Nominatim · API REST  
**Ambiente de desenvolvimento:** Google Colab

---

### 1. Introdução

O desenvolvimento do LOC VIEW AI envolveu decisões técnicas relacionadas à interpretação de endereços, integração com serviços geográficos, validação de resultados e apresentação de informações ao usuário.

O principal desafio de engenharia não consistia apenas em transformar um endereço em coordenadas geográficas, mas em determinar quando essas coordenadas poderiam ser apresentadas como uma correspondência confiável.

Essa distinção influenciou a arquitetura da aplicação, os critérios de validação e o tratamento de situações incertas.

Este capítulo examina as principais decisões presentes na versão v5.9.3, suas justificativas, alternativas tecnológicas e consequências para o funcionamento do produto.

As alternativas discutidas representam possibilidades de engenharia, não necessariamente implementações experimentadas durante o desenvolvimento.

### 2. Decisão 01 — Utilizar processamento determinístico em vez de IA generativa

#### 2.1 Contexto

O LOC VIEW AI recebe informações textuais que podem apresentar diferentes formatos, abreviações, números e componentes geográficos.

Uma possibilidade seria utilizar um modelo de linguagem para interpretar os endereços e decidir se os resultados encontrados correspondem à solicitação.

Entretanto, essa abordagem introduziria uma camada probabilística em uma tarefa que exige critérios verificáveis de correspondência.

#### 2.2 Solução adotada

A aplicação utiliza funções Python e regras explícitas para interpretar, normalizar e comparar os componentes dos endereços.

Entre as funções identificadas na implementação estão:

- `parse_address()` — interpretação dos componentes da entrada.
- `norm()` — normalização textual.
- `normalize_street()` — padronização de nomes de logradouros.
- `street_equal()` — comparação de nomes de ruas.
- `canonical_state()` — padronização de referências estaduais.
- `check()` — avaliação de correspondências geográficas.

A integração com a API Nominatim fornece candidatos geográficos, que são posteriormente avaliados pelas regras da aplicação.

#### 2.3 Justificativa técnica

A abordagem determinística foi adequada ao escopo do protótipo por permitir:

- Regras de validação explícitas e inspecionáveis.
- Comportamento mais previsível diante de entradas equivalentes.
- Testes locais sem necessidade de consultar modelos externos.
- Menor dependência de serviços adicionais.
- Investigação mais direta de falhas de interpretação.

#### 2.4 Alternativas e limitações

Modelos de linguagem poderiam ampliar a flexibilidade na interpretação de entradas informais, mas também exigiriam mecanismos adicionais para controlar respostas inconsistentes e validar informações geradas.

A solução adotada possui limitações: regras explícitas não conseguem antecipar todas as variações linguísticas e estruturais dos endereços brasileiros.

**Decisão consolidada:** priorizar validação determinística e verificável, sem depender de inteligência artificial generativa.

### 3. Decisão 02 — Utilizar OpenStreetMap Nominatim como serviço de geocodificação

#### 3.1 Contexto

A aplicação precisava converter endereços brasileiros em registros geográficos contendo coordenadas e atributos estruturados.

Para isso, seria necessário integrar um serviço de geocodificação.

#### 3.2 Solução adotada

O LOC VIEW AI utiliza a API Nominatim, baseada nos dados do OpenStreetMap.

A função `search_api()` participa da comunicação com o serviço, enquanto outras funções processam e verificam os resultados obtidos.

#### 3.3 Justificativa técnica

A escolha permite construir um protótipo utilizando dados geográficos abertos e uma API que disponibiliza informações úteis para validação, como município, estado, logradouro e, quando presentes, número do imóvel e CEP.

A estrutura dos resultados possibilita avaliar atributos individualmente, em vez de depender apenas de um endereço textual formatado.

#### 3.4 Alternativas consideradas na análise

Outros serviços de geocodificação poderiam ser utilizados, incluindo soluções comerciais.

Essas alternativas podem apresentar diferenças em cobertura, qualidade dos dados, condições de uso, disponibilidade e custos.

Não foi realizada uma comparação quantitativa documentada entre fornecedores.

#### 3.5 Consequências e limitações

A qualidade das correspondências depende da cobertura e da atualização dos dados disponíveis no OpenStreetMap.

A ausência de um número de imóvel no registro retornado não significa necessariamente que o imóvel não exista.

Além disso, o serviço público do Nominatim possui políticas de utilização e limites operacionais que precisam ser respeitados. Um eventual produto com maior volume de consultas exigiria uma avaliação específica de infraestrutura e capacidade.

**Decisão consolidada:** utilizar o Nominatim como fonte de candidatos geográficos, sem tratá-lo como autoridade absoluta para confirmar qualquer endereço.

### 4. Decisão 03 — Separar geocodificação de validação

#### 4.1 Contexto

Uma API de geocodificação pode retornar um resultado aproximado mesmo quando não encontra o endereço completo solicitado.

Por exemplo, uma consulta contendo rua e número pode retornar apenas o centro geométrico de um logradouro.

Se a aplicação apresentasse esse resultado como localização exata, poderia induzir o usuário a uma interpretação incorreta.

#### 4.2 Solução adotada

A arquitetura diferencia duas operações:

**Geocodificação:** obtenção de candidatos geográficos e coordenadas.

**Validação:** avaliação da compatibilidade entre os atributos retornados e o endereço solicitado.

A função `check()` integra essa etapa de avaliação.

O sistema considera diferentes componentes geográficos, incluindo país, município, estado, rua, número do imóvel e informações postais quando disponíveis.

#### 4.3 Justificativa técnica

A separação reduz a dependência da classificação de relevância produzida pelo serviço externo.

Um resultado pode ser útil para identificar uma rua sem possuir evidências suficientes para confirmar um imóvel específico.

Essa distinção permite que a aplicação comunique resultados com diferentes níveis de certeza.

#### 4.4 Consequências

A validação mais rigorosa pode rejeitar resultados que, visualmente, pareçam próximos do endereço desejado.

Esse comportamento é uma consequência deliberada da prioridade atribuída à confiabilidade da informação apresentada.

**Decisão consolidada:** um resultado geográfico retornado pela API não deve ser automaticamente considerado uma correspondência exata.

### 5. Decisão 04 — Normalizar dados antes da comparação

#### 5.1 Contexto

Endereços podem conter diferenças de acentuação, capitalização, abreviações e representação numérica.

Comparações textuais diretas podem produzir resultados inadequados quando duas entradas equivalentes possuem formatos diferentes.

#### 5.2 Solução adotada

O LOC VIEW AI incorpora funções de normalização e comparação textual.

A implementação inclui tratamento específico para determinadas variações numéricas presentes em nomes de logradouros.

Exemplo conceitual:

`Rua Vinte e Cinco de Março`

e

`Rua 25 de Março`

podem exigir tratamento adicional para que a comparação considere suas representações equivalentes.

#### 5.3 Justificativa técnica

A normalização reduz diferenças superficiais de representação e contribui para tornar a validação mais consistente.

Também permite concentrar regras de comparação em funções específicas, facilitando sua manutenção e verificação.

#### 5.4 Limitações

A normalização não equivale à compreensão completa da linguagem natural.

Nomes oficiais incomuns, abreviações não previstas e ambiguidades regionais ainda podem produzir resultados não confirmados.

**Decisão consolidada:** padronizar os dados antes de compará-los, preservando critérios explícitos de correspondência.

### 6. Decisão 05 — Validar o CEP como informação complementar

#### 6.1 Contexto

O CEP pode ajudar a restringir a interpretação geográfica, mas também pode apresentar inconsistências.

Um usuário pode informar um CEP dentro do endereço e outro em um campo separado.

Além disso, um registro geográfico pode não possuir informação postal disponível.

#### 6.2 Solução adotada

A aplicação oferece um campo opcional de CEP e incorpora verificações de formato e consistência.

A função `postal_input()` participa do processamento da informação postal.

Quando o CEP aparece em mais de uma parte da entrada, a aplicação pode identificar conflitos antes da consulta.

Na avaliação dos resultados geográficos, o CEP retornado pelo Nominatim é considerado quando disponível.

#### 6.3 Justificativa técnica

O tratamento permite utilizar o CEP como evidência adicional sem torná-lo obrigatório para todas as consultas.

Também reduz a possibilidade de executar pesquisas baseadas em informações postais contraditórias.

#### 6.4 Limitações

A implementação não constitui uma validação independente na base oficial dos Correios.

A ausência de CEP nos dados do Nominatim limita as verificações possíveis.

**Decisão consolidada:** tratar o CEP como componente de consistência e apoio à validação, respeitando a disponibilidade dos dados.

### 7. Decisão 06 — Não apresentar correspondências incompletas como endereços exatos

#### 7.1 Contexto

Um dos riscos do produto é transmitir uma precisão geográfica maior do que aquela sustentada pelo resultado obtido.

Esse risco é particularmente relevante quando o sistema encontra a rua solicitada, mas não consegue confirmar o número do imóvel.

#### 7.2 Solução adotada

A aplicação diferencia situações confirmadas de situações em que a correspondência é insuficiente.

Quando não há confirmação adequada do número, o resultado não é apresentado como localização exata do imóvel.

A interface utiliza painéis e mensagens para comunicar o estado da verificação.

#### 7.3 Justificativa técnica

Essa decisão reduz o risco de o usuário interpretar uma coordenada aproximada como identificação precisa de uma fachada.

Ela também preserva a distinção entre dados recuperados e conclusões efetivamente sustentadas pela validação.

#### 7.4 Consequências

Alguns endereços reais podem permanecer sem confirmação por insuficiência dos dados geográficos disponíveis.

Essa limitação é preferível, dentro do escopo adotado, à apresentação de uma confirmação sem evidência suficiente.

**Decisão consolidada:** priorizar a comunicação explícita da incerteza em vez de presumir precisão.

### 8. Decisão 07 — Utilizar Google Maps e Street View como recursos externos

#### 8.1 Contexto

O objetivo do produto inclui facilitar a visualização geográfica do endereço pesquisado.

Uma alternativa seria incorporar diretamente mapas e imagens panorâmicas à interface.

Essa solução, porém, poderia exigir integrações adicionais, gestão de credenciais e avaliação das condições de utilização dos serviços.

#### 8.2 Solução adotada

O LOC VIEW AI gera links externos para Google Maps e Google Street View.

A função `links()` é responsável pela construção dessas URLs.

A visualização ocorre no serviço externo, e não por meio de imagens capturadas ou armazenadas pela aplicação.

#### 8.3 Justificativa técnica

A solução mantém o protótipo mais simples e separa a responsabilidade pela validação geográfica da responsabilidade pela apresentação de mapas e imagens panorâmicas.

Também evita a necessidade de desenvolver um visualizador geográfico próprio.

#### 8.4 Limitações

A existência de um link do Street View não garante cobertura panorâmica para o ponto solicitado.

A imagem disponibilizada pelo Google pode representar uma posição próxima, não necessariamente a fachada exata do imóvel.

A aplicação também depende da disponibilidade e do comportamento dos serviços externos.

**Decisão consolidada:** fornecer acesso à visualização geográfica por links externos, sem afirmar que a imagem exibida corresponde necessariamente ao imóvel validado.

### 9. Decisão 08 — Utilizar Gradio para a interface do protótipo

#### 9.1 Contexto

O projeto precisava disponibilizar um fluxo interativo para entrada de endereços e apresentação de resultados.

Era desejável que a interface pudesse ser executada no ambiente Python utilizado durante o desenvolvimento.

#### 9.2 Solução adotada

A aplicação utiliza `gr.Blocks` para estruturar a interface.

A implementação inclui componentes de entrada, elementos HTML, estilos personalizados e funções responsáveis pela apresentação das respostas.

Entre as funções identificadas estão:

- `search_location_ui()`
- `panel()`
- `success_panel()`

#### 9.3 Justificativa técnica

O Gradio permitiu integrar a interface diretamente à lógica Python, reduzindo a necessidade de desenvolver um frontend separado.

Essa característica foi adequada ao objetivo de construir e testar um protótipo funcional.

#### 9.4 Desafio de implementação

Durante o refinamento visual, determinados contêineres padrão do Gradio permaneceram visíveis apesar de alterações no CSS.

A versão v5.9.3 incorporou o uso de `container=False` em componentes relevantes e rótulos HTML separados para aprimorar a apresentação.

#### 9.5 Limitações

Embora adequado para prototipagem, o Gradio não elimina a necessidade de avaliar infraestrutura, acessibilidade, segurança e manutenção caso a aplicação seja disponibilizada como serviço de produção.

**Decisão consolidada:** utilizar Gradio para acelerar a construção da interface, com personalizações compatíveis com o escopo do protótipo.

### 10. Decisão 09 — Separar testes locais de consultas externas

#### 10.1 Contexto

Nem todas as regras de interpretação e validação precisam de uma consulta real à API para serem verificadas.

Depender exclusivamente de testes conectados ao serviço externo poderia dificultar a identificação da origem de determinadas falhas.

#### 10.2 Solução adotada

A versão final contém a função `run_offline_tests()`, destinada a verificar comportamentos locais específicos.

O desenvolvimento também incluiu cenários funcionais manuais para observar o comportamento integrado da aplicação.

#### 10.3 Justificativa técnica

A distinção entre testes locais e testes de integração permite avaliar separadamente determinadas regras de processamento e o comportamento completo da aplicação.

Os testes locais podem contribuir para identificar regressões em regras determinísticas sem depender de respostas externas.

#### 10.4 Limitações

A presença de testes locais não comprova cobertura completa do código.

Da mesma forma, a aprovação dos dez cenários funcionais relatados não garante funcionamento correto para todos os endereços brasileiros.

**Decisão consolidada:** combinar verificações locais com cenários funcionais representativos, reconhecendo os limites de cada abordagem.

### 11. Síntese das Decisões e Compromissos Técnicos

| Decisão | Benefício principal | Compromisso ou limitação |
|---|---|---|
| Processamento determinístico | Regras verificáveis | Menor flexibilidade diante de entradas não previstas |
| Nominatim / OpenStreetMap | Acesso a dados geográficos abertos | Dependência da cobertura e das políticas do serviço |
| Validação separada da geocodificação | Maior rigor na confirmação | Possibilidade de rejeitar resultados aproximados úteis |
| Normalização textual | Comparações mais consistentes | Cobertura limitada das variações linguísticas |
| CEP complementar | Detecção de inconsistências | Dependência dos dados postais disponíveis |
| Tratamento explícito da incerteza | Comunicação mais confiável | Alguns imóveis permanecem sem confirmação |
| Links externos de navegação | Menor complexidade de integração | Dependência de serviços e cobertura externos |
| Interface com Gradio | Prototipagem integrada ao Python | Limitações de personalização e implantação |
| Testes locais e funcionais | Verificação de comportamentos específicos | Cobertura não exaustiva |

### 12. Considerações sobre Evolução Arquitetural

As decisões descritas foram adequadas à construção de um protótipo funcional, mas não representam necessariamente a arquitetura ideal para uma operação em larga escala.

Uma evolução futura poderia incluir:

- Separação mais explícita entre interface, lógica de negócio e integração com serviços externos.
- Testes automatizados mais abrangentes, incluindo respostas simuladas da API.
- Monitoramento de falhas, latência e disponibilidade.
- Avaliação de alternativas de geocodificação e qualidade de cobertura.
- Estratégias de cache e controle de volume de consultas, respeitando as condições de uso dos serviços.
- Implantação em ambiente próprio, com configuração e observabilidade adequadas.
- Avaliações sistemáticas de acessibilidade e usabilidade.

Essas possibilidades constituem direções de evolução e não funcionalidades já implementadas.

### 13. Considerações Finais

As decisões de engenharia do LOC VIEW AI foram orientadas por um princípio central: **a recuperação de um resultado geográfico não deve ser confundida com a confirmação de um endereço exato**.

A arquitetura da versão v5.9.3 procura aplicar esse princípio por meio de interpretação estruturada, normalização, integração com dados geográficos, validação de correspondências e comunicação explícita da incerteza.

A escolha por regras determinísticas, ferramentas acessíveis e serviços externos permitiu consolidar um protótipo funcional sem introduzir complexidade incompatível com seu escopo.

Ao mesmo tempo, o projeto evidencia que a confiabilidade de uma aplicação depende tanto das informações disponíveis quanto das decisões adotadas para interpretá-las e apresentá-las.

O próximo capítulo apresenta a estratégia de testes e validação, os cenários avaliados, os resultados relatados e as limitações das evidências disponíveis.
