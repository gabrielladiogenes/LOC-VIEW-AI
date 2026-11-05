# LOC VIEW AI
## 05 — Testes e Validação

**Projeto:** LOC VIEW AI  
**Versão avaliada:** v5.9.3  
**Categoria:** Geolocalização, Validação de Dados e Inteligência Geográfica  
**Tecnologias:** Python · Gradio · OpenStreetMap Nominatim · API REST  
**Ambiente de desenvolvimento:** Google Colab

---

### 1. Introdução

A validação funcional do LOC VIEW AI foi orientada por uma questão central: determinar se a aplicação consegue interpretar endereços brasileiros e distinguir correspondências geográficas confirmadas de resultados incompletos ou inconsistentes.

Diferentemente de uma aplicação que apenas apresenta coordenadas retornadas por um serviço externo, o LOC VIEW AI incorpora regras de interpretação e verificação antes de classificar uma localização como correspondência exata.

Essa característica torna necessário avaliar não apenas a capacidade de recuperar resultados, mas também a maneira como o sistema responde a entradas inválidas, informações conflitantes e situações em que os dados disponíveis são insuficientes.

Este capítulo apresenta a estratégia de testes, os cenários funcionais avaliados, os resultados relatados e as limitações das evidências disponíveis para a versão v5.9.3.

### 2. Objetivos da Validação

A estratégia de validação foi organizada em torno de cinco objetivos.

#### 2.1 Verificar a interpretação de endereços

Avaliar se a aplicação consegue processar diferentes representações de endereços brasileiros, identificando componentes como logradouro, número, município, estado e CEP.

#### 2.2 Verificar a consistência das entradas

Avaliar se informações inválidas ou contraditórias são identificadas antes de produzir resultados potencialmente incorretos.

#### 2.3 Verificar os critérios de correspondência

Observar se o sistema diferencia resultados compatíveis com o endereço completo de correspondências limitadas ao nível do logradouro.

#### 2.4 Verificar o tratamento da incerteza

Avaliar se a aplicação evita apresentar um imóvel como localizado com precisão quando o número solicitado não pode ser confirmado.

#### 2.5 Verificar a comunicação dos resultados

Observar se a interface apresenta respostas e opções de navegação coerentes com o estado da validação.

### 3. Estratégia de Testes

O processo de verificação combina duas abordagens complementares: testes locais de regras determinísticas e cenários funcionais executados por meio da aplicação.

#### 3.1 Testes locais

A versão v5.9.3 contém a função `run_offline_tests()`, destinada a verificar comportamentos específicos da lógica de processamento.

Essa abordagem permite avaliar determinadas regras sem depender de uma consulta em tempo real à API Nominatim.

Os testes locais são relevantes para funções de normalização, interpretação e comparação, nas quais é possível estabelecer resultados esperados a partir de entradas controladas.

A presença dessa função no código não deve ser confundida com evidência de cobertura integral ou de execução automatizada contínua.

#### 3.2 Testes funcionais manuais

A segunda abordagem envolve a execução de cenários representativos por meio do fluxo funcional da aplicação.

Esses cenários contemplam entradas válidas, inválidas, incompletas e potencialmente ambíguas.

Durante o desenvolvimento, foram relatados dez cenários funcionais aprovados na versão v5.9.3.

Os resultados apresentados neste documento correspondem a esse registro de avaliação manual.

#### 3.3 Escopo da avaliação

A validação documentada concentra-se no comportamento funcional do protótipo.

Não foram estabelecidas, nesta documentação, métricas formais de cobertura de código, testes de carga, testes de segurança, avaliações estatísticas de precisão geográfica ou testes sistemáticos de acessibilidade.

Essas verificações são consideradas possibilidades de evolução futura.

### 4. Cenários Funcionais Avaliados

A matriz abaixo reúne os dez cenários funcionais relatados durante a consolidação da versão v5.9.3.

| ID | Cenário | Objetivo principal | Resultado relatado |
|---|---|---|---|
| T01 | Endereço completo em Fortaleza | Avaliar uma consulta com componentes completos | Aprovado |
| T02 | Endereço completo em São Paulo | Avaliar o processamento em outro contexto geográfico | Aprovado |
| T03 | Número de imóvel com sufixo alfabético | Avaliar a interpretação de números com letras | Aprovado |
| T04 | Endereço com CEP correto | Avaliar a utilização de informação postal consistente | Aprovado |
| T05 | CEP inválido | Avaliar a rejeição de informação postal malformada | Aprovado |
| T06 | CEPs conflitantes | Avaliar a identificação de informações postais contraditórias | Aprovado |
| T07 | Rua sem número do imóvel | Avaliar o tratamento de um endereço incompleto | Aprovado |
| T08 | Avenida com número no nome | Avaliar a distinção entre nome do logradouro e número do imóvel | Aprovado |
| T09 | Número do imóvel não confirmado | Avaliar o tratamento de correspondências insuficientes | Aprovado |
| T10 | Município e estado incorretos | Avaliar a identificação de inconsistências geográficas | Aprovado |

**Resultado consolidado informado: 10 de 10 cenários selecionados aprovados.**

Esse resultado descreve a aprovação dos casos selecionados, não uma taxa estatística de acerto para todos os endereços brasileiros.

### 5. Análise dos Cenários

#### 5.1 T01 e T02 — Endereços completos em diferentes cidades

Os cenários T01 e T02 avaliaram consultas com endereços completos em Fortaleza e São Paulo.

O objetivo foi observar o comportamento da aplicação diante de entradas contendo informações geográficas suficientes para buscar uma correspondência específica.

A utilização de cidades diferentes contribuiu para verificar o fluxo em mais de um contexto geográfico.

**Resultado relatado:** ambos os cenários foram aprovados.

A aprovação não permite concluir que todos os endereços dessas cidades possuem cobertura adequada na base geográfica utilizada.

#### 5.2 T03 — Número de imóvel com sufixo alfabético

Alguns endereços apresentam números acompanhados de letras, como identificadores do tipo `120A`.

Esse cenário foi selecionado para avaliar se o processamento preserva e interpreta adequadamente essa característica.

O objetivo é evitar que a estrutura do número seja modificada de maneira incompatível com o endereço solicitado.

**Resultado relatado:** cenário aprovado.

#### 5.3 T04 — CEP correto

O cenário T04 avaliou o comportamento diante de um endereço acompanhado de informação postal consistente.

A verificação foi direcionada à utilização do CEP como componente complementar da interpretação e validação.

**Resultado relatado:** cenário aprovado.

Esse resultado não representa validação independente na base oficial dos Correios.

#### 5.4 T05 — CEP inválido

O cenário T05 avaliou uma entrada contendo informação postal inválida.

O comportamento esperado é que a aplicação identifique a inconsistência e evite prosseguir com uma consulta baseada em dados malformados.

**Resultado relatado:** cenário aprovado.

Esse tipo de verificação contribui para reduzir consultas desnecessárias e respostas potencialmente inadequadas.

#### 5.5 T06 — CEPs conflitantes

O cenário T06 avaliou uma situação em que o endereço contém um CEP diferente daquele informado no campo específico.

O objetivo foi verificar se a aplicação identifica a contradição entre as informações fornecidas.

**Resultado relatado:** cenário aprovado.

A identificação antecipada de conflitos é relevante porque informações individualmente válidas podem ser incompatíveis quando utilizadas em conjunto.

#### 5.6 T07 — Rua sem número do imóvel

O cenário T07 avaliou uma entrada que identifica o logradouro, mas não apresenta um número de imóvel.

Nesse contexto, o sistema pode possuir informações suficientes para reconhecer uma rua, mas não para confirmar uma propriedade específica.

A avaliação foi direcionada ao tratamento dessa diferença.

**Resultado relatado:** cenário aprovado.

#### 5.7 T08 — Avenida com número no nome

O cenário T08 avaliou um endereço cujo nome do logradouro contém uma expressão numérica.

Esse caso é relevante porque números podem representar partes do nome oficial da rua ou avenida, em vez de identificar um imóvel.

O objetivo foi verificar a interpretação desses componentes sem confundir suas funções.

**Resultado relatado:** cenário aprovado.

#### 5.8 T09 — Número do imóvel não confirmado

O cenário T09 avaliou uma situação em que os dados geográficos disponíveis não permitem confirmar o número solicitado.

Esse é um caso especialmente relevante para o princípio de confiabilidade adotado pelo projeto.

O comportamento esperado é não apresentar uma correspondência incompleta como localização exata do imóvel.

**Resultado relatado:** cenário aprovado.

#### 5.9 T10 — Município e estado incorretos

O cenário T10 avaliou uma entrada contendo informações geográficas inconsistentes.

O objetivo foi observar se a aplicação consegue identificar incompatibilidades entre os componentes solicitados e os resultados geográficos obtidos.

**Resultado relatado:** cenário aprovado.

### 6. Interpretação dos Resultados

A aprovação dos dez cenários selecionados indica que, nas condições observadas durante a avaliação manual, a aplicação apresentou o comportamento esperado para os casos testados.

Os cenários contemplam diferentes categorias de entrada:

- **Entradas completas:** endereços com componentes geográficos suficientes para busca e validação.
- **Variações de formato:** números de imóveis com sufixos e logradouros com expressões numéricas.
- **Inconsistências postais:** CEP inválido ou conflitante.
- **Informações incompletas:** ausência de número ou impossibilidade de confirmação do imóvel.
- **Inconsistências geográficas:** município ou estado incompatível.

Essa diversidade é relevante porque permite observar tanto situações de sucesso quanto situações em que a aplicação deve rejeitar ou limitar uma confirmação.

Entretanto, os resultados não demonstram a proporção de endereços reais que seriam corretamente identificados em uma população ampla.

Para estabelecer essa medida, seria necessário utilizar um conjunto de dados de referência, critérios de amostragem e métricas quantitativas apropriadas.

### 7. Evidências e Rastreabilidade

A documentação técnica deve distinguir três tipos de evidência.

#### 7.1 Evidência de implementação

O código-fonte da versão v5.9.3 contém as funções e regras responsáveis pelo processamento dos endereços e pela avaliação das correspondências.

Esse material permite inspecionar a implementação e identificar os critérios programados.

#### 7.2 Evidência de testes locais

A função `run_offline_tests()` documenta a existência de verificações locais incorporadas ao projeto.

A avaliação detalhada de seus resultados depende da execução do código e do registro das saídas produzidas.

#### 7.3 Evidência de testes funcionais

Os dez cenários apresentados correspondem aos resultados relatados durante o desenvolvimento.

Não foram incorporados a este capítulo registros individuais completos de entrada, resposta da API, saída da aplicação e horário de execução para cada cenário.

Por isso, a matriz de testes deve ser interpretada como um registro resumido de avaliação funcional, e não como um relatório de auditoria independente.

### 8. Limitações da Validação

A estratégia documentada apresenta limitações importantes.

#### 8.1 Amostra reduzida

Dez cenários permitem observar comportamentos específicos, mas não representam a diversidade completa dos endereços brasileiros.

#### 8.2 Dependência de dados externos

Os resultados de geocodificação dependem da cobertura e da atualização dos dados do OpenStreetMap.

Alterações futuras nessa base podem modificar as respostas obtidas para uma mesma consulta.

#### 8.3 Ausência de métricas estatísticas

Não foram calculadas métricas formais de precisão, revocação, taxa de falsos positivos ou falsos negativos.

#### 8.4 Cobertura automatizada não quantificada

A existência de testes locais não permite afirmar que todos os caminhos de execução ou condições de erro foram avaliados.

#### 8.5 Ausência de testes de desempenho

Não foram documentadas medições sistemáticas de latência, volume máximo de consultas simultâneas ou comportamento sob carga.

#### 8.6 Ausência de auditoria independente

Os resultados funcionais apresentados são provenientes do processo de desenvolvimento, sem uma avaliação externa formal documentada.

Essas limitações não invalidam os resultados observados, mas delimitam as conclusões que podem ser extraídas deles.

### 9. Recomendações para Evolução dos Testes

Uma etapa futura de validação poderia incluir as seguintes melhorias.

#### 9.1 Construção de uma base de referência

Organizar um conjunto de endereços com resultados esperados previamente verificados, contemplando diferentes cidades, estados e tipos de logradouro.

#### 9.2 Ampliação dos testes automatizados

Utilizar uma estrutura de testes Python para verificar regras de interpretação, normalização, validação e tratamento de exceções.

#### 9.3 Simulação de respostas da API

Criar testes com respostas controladas para reproduzir situações como registros incompletos, dados conflitantes e falhas de comunicação.

#### 9.4 Registro reproduzível dos cenários

Documentar, para cada teste, a entrada utilizada, o resultado esperado, a resposta observada e a versão executada.

#### 9.5 Avaliação quantitativa de correspondências

Medir o desempenho da validação em um conjunto de referência, distinguindo correspondências corretas, rejeições adequadas e erros de classificação.

#### 9.6 Testes de desempenho e disponibilidade

Avaliar tempos de resposta, falhas de integração e comportamento em condições de uso mais exigentes, respeitando as políticas do serviço de geocodificação.

Essas recomendações representam propostas de evolução, não verificações já realizadas.

### 10. Considerações Finais

A validação do LOC VIEW AI v5.9.3 foi orientada pela necessidade de distinguir resultados geográficos recuperados de correspondências efetivamente confirmadas.

Os dez cenários funcionais selecionados foram relatados como aprovados e contemplaram entradas completas, variações de formato, inconsistências postais, endereços incompletos e divergências geográficas.

A implementação também contém verificações locais destinadas a avaliar comportamentos determinísticos.

Em conjunto, esses elementos fornecem evidências iniciais do funcionamento do protótipo dentro dos casos documentados.

Entretanto, a ausência de uma base de referência abrangente, métricas quantitativas e registros reproduzíveis completos impede generalizações sobre a precisão geográfica do sistema.

A continuidade do projeto poderá ampliar a cobertura dos testes e fortalecer a rastreabilidade das evidências.

O próximo capítulo apresenta as limitações atuais do LOC VIEW AI e um roteiro de possíveis melhorias técnicas e funcionais.
