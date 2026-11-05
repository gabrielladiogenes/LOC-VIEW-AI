# LOC VIEW AI
## 03 — Histórico de Desenvolvimento

**Projeto:** LOC VIEW AI  
**Versão final documentada:** v5.9.3  
**Categoria:** Geolocalização, Validação de Dados e Inteligência Geográfica  
**Tecnologias:** Python · Gradio · OpenStreetMap Nominatim · API REST  
**Ambiente de desenvolvimento:** Google Colab

---

### 1. Introdução

O desenvolvimento do LOC VIEW AI foi conduzido por um processo iterativo de implementação, avaliação funcional e refinamento.

O projeto partiu de um objetivo relativamente simples: permitir que uma pessoa informasse um endereço brasileiro e obtivesse informações geográficas úteis para localizar o imóvel solicitado.

Entretanto, o desenvolvimento evidenciou uma questão técnica relevante: a recuperação de coordenadas por um serviço de geocodificação não significa, necessariamente, que o endereço solicitado foi identificado com precisão.

Essa constatação orientou a evolução do produto, especialmente no tratamento de nomes de ruas, números de imóveis, códigos postais e correspondências geográficas incompletas.

Ao longo das versões documentadas, o foco passou da recuperação de resultados geográficos para a construção de um fluxo de validação mais criterioso, acompanhado de uma interface capaz de comunicar diferentes níveis de confirmação.

O resultado desse processo foi a versão v5.9.3, consolidada após sucessivos ajustes funcionais e visuais.

### 2. Estratégia de Desenvolvimento

O LOC VIEW AI foi desenvolvido em Python, utilizando o Google Colab como ambiente principal de execução e o Gradio para disponibilizar uma interface acessível pelo navegador.

A evolução do projeto ocorreu por meio de ciclos de trabalho com quatro atividades principais:

1. **Identificação de um problema:** reconhecimento de uma inconsistência funcional, dificuldade de interpretação ou problema de interface.
2. **Implementação de uma alteração:** atualização das funções de processamento, regras de validação ou componentes visuais.
3. **Verificação do comportamento:** execução de cenários representativos para observar o resultado da alteração.
4. **Refinamento:** novos ajustes quando o comportamento obtido ainda não correspondia ao esperado.

Essa abordagem permitiu incorporar melhorias progressivamente, sem depender da implementação de toda a solução em uma única etapa.

O histórico apresentado neste capítulo reúne as alterações registradas durante o desenvolvimento e as características identificáveis na versão final do código-fonte.

Não representa um registro exaustivo de todos os commits, alterações intermediárias ou experimentos realizados.

### 3. Evolução Funcional — Série v5.7

A série v5.7 concentrou esforços no aprimoramento da interpretação de endereços e na definição de critérios mais rigorosos para aceitar resultados geográficos.

#### 3.1 Problema: nomes de ruas contendo números

Endereços brasileiros frequentemente apresentam números como parte do nome oficial do logradouro.

Exemplos incluem:

- Rua 25 de Março.
- Rua Vinte e Cinco de Março.
- Avenida 9 de Julho.
- Avenida Nove de Julho.

Essas representações podem se referir ao mesmo logradouro, mas possuem diferenças textuais que dificultam comparações diretas.

Além disso, um sistema de interpretação de endereços precisa distinguir os números que fazem parte do nome da rua daqueles que identificam imóveis.

#### 3.2 Solução implementada

O desenvolvimento incorporou regras de normalização textual e conversão de determinadas expressões numéricas escritas por extenso.

A versão final contém as funções `norm()`, `normalize_street()` e `street_equal()`, responsáveis por padronizar e comparar nomes de logradouros.

A interpretação do endereço também utiliza regras para separar o número do imóvel dos demais componentes.

Esses mecanismos foram desenvolvidos para reduzir diferenças de representação sem aceitar automaticamente qualquer resultado semelhante como uma correspondência válida.

#### 3.3 Evolução dos critérios de validação

Outra preocupação dessa etapa foi impedir que resultados geográficos aproximados fossem apresentados como confirmações exatas.

A validação passou a considerar atributos estruturados, incluindo país, município, estado, logradouro e número do imóvel.

O CEP também passou a integrar as verificações de consistência quando disponível.

A implementação final diferencia correspondências exatas de resultados em nível de rua, preservando essa distinção na apresentação ao usuário.

**Resultado da etapa:** evolução da interpretação textual e estabelecimento de critérios explícitos para correspondências geográficas.

### 4. Evolução Funcional — Série v5.8

A série v5.8 ampliou o tratamento de códigos postais e aprimorou a apresentação dos recursos de navegação.

#### 4.1 Problema: informações postais inconsistentes

Um endereço pode conter um CEP incorporado ao texto, enquanto o usuário informa outro CEP em um campo separado.

Essas informações podem ser válidas individualmente, mas incompatíveis entre si.

Também podem ocorrer entradas com quantidade incorreta de dígitos ou caracteres inesperados.

Sem uma validação preliminar, essas inconsistências poderiam produzir consultas geográficas inadequadas.

#### 4.2 Solução: CEP opcional e verificação de conflitos

O desenvolvimento incorporou um campo opcional para CEP, acompanhado de regras específicas de interpretação e validação.

A função `postal_input()` verifica o formato do código postal informado separadamente.

O processamento também considera o CEP eventualmente extraído do endereço completo.

Quando as informações postais fornecidas entram em conflito, a aplicação pode rejeitar a solicitação antes de executar a pesquisa geográfica.

Na versão final, a comparação com o CEP retornado pelo Nominatim ocorre quando essa informação está presente no registro geográfico.

Essa validação representa uma verificação de consistência, não uma consulta independente à base oficial dos Correios.

#### 4.3 Evolução dos recursos de navegação

Outra melhoria registrada foi a separação dos links externos para Google Maps e Google Street View.

Esses recursos possuem finalidades distintas:

**Google Maps:** permite pesquisar e visualizar a localização utilizando o endereço interpretado.

**Google Street View:** solicita uma visualização panorâmica com base nas coordenadas retornadas pelo serviço de geocodificação.

A implementação final utiliza a função `links()` para construir essas URLs.

A disponibilização do link do Street View não garante a existência de imagens da fachada exata do imóvel.

#### 4.4 Comunicação com o usuário

A evolução também incluiu ajustes na linguagem da interface, com o objetivo de apresentar resultados e mensagens de maneira mais compreensível.

As explicações técnicas detalhadas foram concentradas na documentação, enquanto a interface passou a priorizar orientações relacionadas à tarefa do usuário.

**Resultado da etapa:** melhoria da consistência das entradas e maior clareza na apresentação das opções de navegação.

### 5. Evolução da Interface — Série v5.9

A série v5.9 concentrou-se na apresentação visual, na organização dos componentes e na experiência de utilização.

#### 5.1 Primeira implementação visual

A versão inicial da série v5.9 apresentou uma interface com identidade visual mais definida.

Durante a revisão, foram identificados elementos que não correspondiam à apresentação desejada para o produto.

Entre eles estavam uma identificação visual de demonstração ou portfólio e um contêiner externo acinzentado ao redor dos campos.

Esses elementos foram considerados desnecessários para a experiência pretendida.

#### 5.2 Refinamentos nas versões v5.9.1 e v5.9.2

As versões intermediárias envolveram ajustes de CSS para aprimorar a aparência dos componentes.

Entretanto, as alterações visuais não eliminaram completamente o contêiner acinzentado.

Esse comportamento demonstrou que o problema não dependia apenas das propriedades CSS aplicadas aos campos.

A estrutura gerada pelos próprios componentes do Gradio também precisava ser considerada.

#### 5.3 Solução consolidada na v5.9.3

A versão v5.9.3 incorporou a utilização de `container=False` em componentes relevantes do Gradio, permitindo maior controle sobre a apresentação dos campos.

A interface também passou a utilizar rótulos HTML separados e estilos personalizados para produzir uma composição visual mais consistente.

A versão final apresenta:

- Campos com aparência clara e organização visual simplificada.
- Identidade visual própria do LOC VIEW AI.
- Rótulos e mensagens organizados de acordo com a tarefa.
- Remoção da identificação visual de demonstração.
- Adaptações de layout para diferentes tamanhos de tela.
- Painéis de resposta distintos para resultados confirmados e não confirmados.

**Resultado da etapa:** consolidação de uma interface visualmente mais consistente, alinhada ao funcionamento do produto.

### 6. Consolidação da Versão v5.9.3

A versão v5.9.3 reuniu os principais aprimoramentos desenvolvidos nas etapas anteriores.

Seu funcionamento integra cinco capacidades centrais.

#### 6.1 Interpretação de endereços brasileiros

O sistema processa endereços estruturados e semiestruturados, identifica componentes relevantes e aplica normalizações específicas.

#### 6.2 Integração com dados geográficos

As consultas são realizadas por meio da API Nominatim, utilizando informações do OpenStreetMap.

A aplicação avalia os registros retornados em vez de assumir que o primeiro resultado é necessariamente correto.

#### 6.3 Validação de correspondências

A implementação compara atributos geográficos e diferencia resultados compatíveis com o endereço completo daqueles que permitem identificar apenas o logradouro.

#### 6.4 Tratamento de situações incertas

Quando o número do imóvel não pode ser confirmado, a aplicação evita apresentar a localização como uma correspondência exata.

Erros de entrada e falhas de comunicação com a API também possuem mensagens específicas.

#### 6.5 Apresentação e navegação

A interface apresenta informações de localização e disponibiliza links externos quando os critérios de correspondência exata são satisfeitos.

Essas capacidades constituem o escopo funcional documentado da versão final.

### 7. Validação Funcional da Versão Final

Após os refinamentos, a versão v5.9.3 foi submetida a dez cenários funcionais manuais relatados durante o desenvolvimento.

Os cenários selecionados foram:

| ID | Cenário avaliado | Resultado informado |
|---|---|---|
| T01 | Endereço completo em Fortaleza | Aprovado |
| T02 | Endereço completo em São Paulo | Aprovado |
| T03 | Número de imóvel com sufixo alfabético | Aprovado |
| T04 | Endereço com CEP correto | Aprovado |
| T05 | CEP inválido | Aprovado |
| T06 | CEPs conflitantes | Aprovado |
| T07 | Rua sem número do imóvel | Aprovado |
| T08 | Avenida com número no nome | Aprovado |
| T09 | Número do imóvel não confirmado | Aprovado |
| T10 | Município e estado incorretos | Aprovado |

**Resultado relatado: 10 de 10 cenários funcionais selecionados aprovados.**

Esses resultados correspondem à avaliação manual dos cenários indicados e não devem ser interpretados como garantia de precisão geográfica universal.

A versão final também contém testes locais automatizados, implementados na função `run_offline_tests()`, voltados a comportamentos específicos de interpretação e validação.

A metodologia, os resultados e as limitações da verificação serão detalhados no Capítulo 05 — Testes e Validação.

### 8. Principais Aprendizados do Processo

A evolução do LOC VIEW AI evidenciou diferentes aspectos do desenvolvimento de aplicações orientadas por dados.

**A qualidade de um resultado depende de sua validação.**

Uma resposta fornecida por uma API pode ser tecnicamente válida, mas insuficiente para confirmar a informação solicitada pelo usuário.

**A interpretação de dados exige atenção ao contexto.**

Números presentes em endereços podem representar elementos diferentes, como parte do nome de uma rua, número de imóvel ou código postal.

**A interface influencia a interpretação dos resultados.**

A apresentação de coordenadas ou links sem uma distinção adequada entre níveis de confirmação pode transmitir uma precisão que os dados não sustentam.

**O refinamento visual também envolve decisões técnicas.**

O problema identificado com os contêineres do Gradio demonstrou a importância de compreender o comportamento dos componentes utilizados, além de modificar propriedades CSS.

**Testes representativos orientam melhorias.**

A utilização de cenários com entradas válidas, inválidas e ambíguas contribuiu para avaliar diferentes comportamentos da aplicação.

### 9. Limites do Histórico Documentado

Este capítulo descreve a evolução conhecida do projeto e as funcionalidades observáveis na versão v5.9.3.

As alterações atribuídas às séries v5.7, v5.8 e v5.9 foram organizadas a partir dos registros do processo de desenvolvimento.

O código-fonte final permite confirmar a presença de determinadas funções e comportamentos, mas não comprova isoladamente em qual versão cada alteração foi introduzida.

Por esse motivo, o histórico não apresenta datas de lançamento, métricas de desempenho anteriores ou comparações quantitativas entre versões sem evidências correspondentes.

Essa delimitação preserva a rastreabilidade e a confiabilidade da documentação técnica.

### 10. Considerações Finais

O desenvolvimento do LOC VIEW AI demonstra um processo de evolução orientado pela identificação de problemas, implementação de soluções e avaliação de resultados.

Entre as versões documentadas, o projeto avançou em três dimensões principais:

- **Confiabilidade funcional:** interpretação e validação mais criteriosas dos endereços.
- **Consistência dos dados:** tratamento de informações postais e correspondências incompletas.
- **Experiência do usuário:** organização da interface e comunicação mais clara dos resultados.

A versão v5.9.3 representa a consolidação dessas melhorias em um protótipo funcional.

O próximo capítulo examina as principais decisões de engenharia que sustentam a solução, incluindo suas justificativas, alternativas e consequências técnicas.
