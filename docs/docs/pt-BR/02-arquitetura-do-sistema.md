# LOC VIEW AI
## 02 — Arquitetura do Sistema

**Projeto:** LOC VIEW AI  
**Versão analisada:** v5.9.3  
**Arquitetura:** Aplicação Python com interface web e integração a serviço externo de geocodificação  
**Tecnologias principais:** Python · Gradio · Requests · OpenStreetMap Nominatim  
**Ambiente de desenvolvimento e execução:** Google Colab

---

### 1. Visão Geral da Arquitetura

O LOC VIEW AI foi desenvolvido como uma aplicação de geolocalização baseada em Python, com interface web construída em Gradio e integração à API Nominatim, do OpenStreetMap.

A arquitetura utiliza uma estrutura centralizada, na qual a mesma aplicação é responsável por receber os dados do usuário, interpretar o endereço, executar consultas geográficas, validar os resultados e construir a resposta apresentada na interface.

A solução foi organizada em componentes funcionais que possuem responsabilidades distintas, embora estejam implementados no mesmo arquivo Python.

Essa abordagem permitiu desenvolver e testar o protótipo em um ambiente de execução simplificado, sem exigir a implementação de um servidor de aplicação separado ou de um banco de dados próprio.

O sistema pode ser compreendido por meio de seis componentes:

1. **Camada de apresentação:** interface desenvolvida com Gradio, HTML e CSS.
2. **Camada de interpretação:** extração e normalização dos componentes do endereço.
3. **Camada de integração:** comunicação HTTP com a API Nominatim.
4. **Camada de validação:** comparação entre os dados solicitados e os registros geográficos retornados.
5. **Camada de resposta:** classificação dos resultados e geração de mensagens, coordenadas e links.
6. **Camada de verificação:** testes locais de regressão para determinadas regras de interpretação e validação.

A separação dessas responsabilidades em funções facilita a identificação de problemas e a evolução individual de partes do sistema.

### 2. Fluxo de Processamento

O fluxo principal é iniciado quando o usuário informa um endereço e aciona a pesquisa.

A função `search_location_ui()` coordena as etapas de processamento.

**Etapa 1 — Recebimento da entrada**

A interface recebe um endereço textual e, opcionalmente, um CEP informado em um campo separado.

**Etapa 2 — Interpretação do endereço**

A função `parse_address()` identifica os componentes disponíveis, incluindo logradouro, número, município, estado, bairro e CEP, quando presentes.

**Etapa 3 — Validação preliminar**

A função `postal_input()` verifica o formato do CEP opcional. O sistema também identifica conflitos entre o CEP incorporado ao endereço e o CEP informado separadamente.

Entradas incompletas ou inconsistentes podem ser rejeitadas antes da consulta externa.

**Etapa 4 — Construção das consultas**

O sistema gera diferentes representações do endereço para ampliar as possibilidades de encontrar registros relevantes no Nominatim.

**Etapa 5 — Consulta geográfica**

A função `search_api()` envia as requisições HTTP e recebe os registros candidatos.

**Etapa 6 — Verificação das correspondências**

A função `check()` compara os dados retornados com os componentes solicitados, aplicando critérios específicos para correspondências exatas ou em nível de rua.

**Etapa 7 — Apresentação da resposta**

A aplicação apresenta um dos estados previstos: localização identificada, rua localizada sem confirmação do número, localização não encontrada, erro de validação ou indisponibilidade temporária da pesquisa.

Somente o fluxo de correspondência exata produz o painel de sucesso com coordenadas e links externos.

### 3. Interpretação e Normalização de Endereços

A interpretação de endereços brasileiros representa uma das principais responsabilidades da aplicação.

O processamento é realizado por funções específicas, sem utilização de um modelo de linguagem ou serviço externo de interpretação textual.

#### 3.1 Normalização textual

A função `norm()` padroniza valores textuais por meio da conversão para letras minúsculas, remoção de acentos, substituição de caracteres especiais e organização dos espaços.

Essa transformação reduz diferenças superficiais entre os dados fornecidos pelo usuário e os registros retornados pela API.

#### 3.2 Normalização de logradouros

A função `normalize_street()` amplia determinadas abreviações e converte expressões numéricas reconhecidas para representações equivalentes.

Por exemplo, o sistema foi desenvolvido para comparar:

- `Rua 25 de Março` e `Rua Vinte e Cinco de Março`.
- `Avenida 9 de Julho` e `Avenida Nove de Julho`.

A função `street_equal()` realiza a comparação após a normalização.

Além da igualdade textual, existe uma verificação de similaridade com `SequenceMatcher`, aplicada a determinados nomes com mais de dez caracteres e limiar mínimo de 0,94.

Esse mecanismo oferece tolerância limitada a diferenças textuais, mas não constitui um modelo de correspondência semântica ou uma garantia de equivalência entre quaisquer nomes de ruas.

#### 3.3 Extração do número do imóvel

A função `parse_address()` utiliza expressões regulares para identificar o número do imóvel e separá-lo do nome do logradouro.

O processamento contempla números com sufixos alfabéticos, como `1000A`.

Uma preocupação específica da implementação é evitar que números pertencentes ao nome de uma rua sejam interpretados incorretamente como números de imóveis.

Quando o número do imóvel não é identificado, a aplicação retorna uma mensagem solicitando essa informação.

#### 3.4 Identificação de município e estado

O código contém uma tabela de unidades federativas brasileiras e suas abreviações.

A função `canonical_state()` permite comparar nomes completos e siglas, como `CE` e `Ceará`.

A interpretação considera a organização textual do endereço e identifica os componentes de localização conforme os padrões previstos pelo parser.

Essa implementação é direcionada a formatos brasileiros selecionados, não a todas as possíveis formas de escrita de endereços.

### 4. Integração com a API Nominatim

A aplicação utiliza o serviço de pesquisa geográfica Nominatim, associado aos dados do OpenStreetMap.

A integração é realizada por meio da biblioteca Python `requests`.

A função `search_api()` envia consultas ao endpoint:

`https://nominatim.openstreetmap.org/search`

As requisições utilizam parâmetros como:

- `format=jsonv2`, para receber dados estruturados em JSON.
- `addressdetails=1`, para solicitar os componentes do endereço.
- `limit=20`, para limitar a quantidade de registros retornados por consulta.
- `countrycodes=br`, para restringir a pesquisa ao Brasil.

A implementação também define um identificador `User-Agent`, um tempo limite de 25 segundos por requisição e um intervalo mínimo de aproximadamente 1,15 segundo entre chamadas controladas pela aplicação.

Um bloqueio de execução (`threading.Lock`) coordena esse intervalo entre as requisições realizadas pelo processo.

Essas medidas contribuem para um comportamento mais controlado durante a utilização do serviço externo.

A disponibilidade e a qualidade dos resultados dependem, entretanto, da infraestrutura e da cobertura dos dados do Nominatim/OpenStreetMap.

### 5. Mecanismo de Validação Geográfica

A validação é implementada principalmente pela função `check()`.

O sistema distingue dois níveis de correspondência.

#### 5.1 Correspondência exata

Para aceitar um registro como correspondência exata, a aplicação verifica:

- País identificado como Brasil.
- Município compatível com o solicitado.
- Estado compatível com o solicitado.
- Logradouro compatível após normalização.
- Número do imóvel correspondente.

O CEP também é comparado quando existe um CEP solicitado e o registro retornado contém essa informação.

Portanto, **a validação de CEP não representa uma consulta independente à base oficial dos Correios**. Trata-se de uma verificação de consistência entre informações disponíveis.

Da mesma forma, uma correspondência aprovada indica que o registro retornado satisfez os critérios da aplicação, não uma certificação independente da existência física do imóvel.

#### 5.2 Correspondência em nível de rua

Se nenhuma consulta produzir uma correspondência exata, a aplicação realiza pesquisas adicionais sem exigir o número do imóvel na consulta.

Os resultados são avaliados para verificar se país, município, estado e logradouro correspondem ao endereço solicitado.

Quando existe uma correspondência nesse nível, o sistema informa que a rua foi localizada, mas o número do imóvel não pôde ser confirmado.

Nesse estado, a aplicação não apresenta o painel de sucesso com coordenadas e links externos.

Essa diferenciação reduz o risco de transmitir ao usuário uma precisão maior do que aquela sustentada pelos registros disponíveis.

### 6. Interface, Respostas e Tratamento de Erros

A interface é construída com `gr.Blocks` e componentes de entrada, botão e exibição HTML.

O código utiliza CSS personalizado para definir identidade visual, organização dos campos, estilos de resposta e adaptação a telas menores.

A pesquisa pode ser iniciada pelo botão principal ou pelo envio do campo de endereço.

A aplicação constrói respostas visuais específicas conforme o resultado do processamento.

Em uma correspondência exata, a função `success_panel()` apresenta os dados da localização e disponibiliza dois links externos:

**Google Maps:** construído a partir do endereço interpretado pela aplicação.

**Google Street View:** construído a partir das coordenadas retornadas pelo Nominatim, solicitando uma visualização panorâmica quando disponível.

O sistema não incorpora fotografias do Street View diretamente à interface nem confirma automaticamente que uma imagem representa a fachada exata do imóvel.

Para respostas não confirmadas, a função `panel()` apresenta mensagens explicativas.

Erros de entrada são tratados como exceções de validação. Falhas de requisição HTTP são capturadas e comunicadas ao usuário como indisponibilidade temporária da pesquisa.

A implementação utiliza `html.escape()` para escapar determinados conteúdos textuais apresentados no HTML gerado, além de codificar os parâmetros dos links externos.

### 7. Testes e Verificação Interna

O código contém uma função denominada `run_offline_tests()`, executada antes da inicialização da interface.

Ela utiliza verificações por meio de `assert` e testes de exceções para avaliar comportamentos específicos, incluindo:

- Rejeição de endereços sem número do imóvel.
- Extração de componentes de um endereço brasileiro.
- Equivalência entre nomes de ruas com números e números escritos por extenso.
- Rejeição de nomes de ruas diferentes.
- Normalização de CEP.
- Rejeição de CEP com formato inválido.

Esses testes não dependem de chamadas à API Nominatim.

Além deles, o desenvolvimento contou com dez cenários funcionais manuais relatados durante a validação da versão v5.9.3.

Os testes locais e os cenários manuais possuem finalidades complementares e serão documentados separadamente no capítulo dedicado à validação.

### 8. Decisões Arquiteturais e Limitações

A arquitetura da versão v5.9.3 reflete decisões compatíveis com um protótipo funcional.

A utilização de Python e Gradio permitiu reunir lógica de processamento e interface em um ambiente acessível.

O uso do Nominatim evitou a necessidade de construir uma base geográfica própria, mas introduziu dependência de dados e serviços externos.

A validação determinística tornou os critérios de correspondência explícitos e verificáveis, embora limite a interpretação de formatos não previstos pelas regras.

A apresentação condicional dos resultados foi adotada para diferenciar correspondências exatas de localizações parcialmente identificadas.

Entre as limitações arquiteturais da versão analisada estão a ausência de armazenamento persistente próprio, a dependência de conectividade e serviços externos, a inexistência de um serviço independente de verificação cadastral de imóveis e a execução por meio de uma sessão Gradio, sem infraestrutura de produção dedicada documentada.

A arquitetura atual constitui uma base para futuras melhorias de modularização, cobertura de testes, observabilidade, gerenciamento de dependências e implantação.

### 9. Considerações Finais

O LOC VIEW AI v5.9.3 demonstra a integração entre processamento textual, consumo de APIs, validação estruturada e apresentação de informações geográficas em uma aplicação funcional.

Sua principal característica arquitetural é a separação lógica entre **encontrar um registro geográfico e aceitar esse registro como correspondente ao endereço solicitado**.

Essa distinção orienta o processamento, as verificações e a comunicação dos resultados.

O próximo capítulo apresenta a evolução do desenvolvimento, as alterações realizadas entre versões e os problemas técnicos que motivaram os aprimoramentos do produto.
