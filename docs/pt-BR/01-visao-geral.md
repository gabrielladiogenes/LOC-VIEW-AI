# LOC VIEW AI
## 01 — Visão Geral do Projeto

**Projeto:** LOC VIEW AI  
**Versão:** v5.9.3  
**Categoria:** Geolocalização, Validação de Dados e Inteligência Geográfica  
**Tecnologias:** Python · Gradio · OpenStreetMap Nominatim · API REST  
**Ambiente de desenvolvimento:** Google Colab  
**Repositório:** [LOC-VIEW-AI](https://github.com/gabrielladiogenes/LOC-VIEW-AI)

---

### 1. Resumo Executivo

**Encontrar uma localização geográfica não significa, necessariamente, verificar um endereço físico.**

Os sistemas de geocodificação transformam endereços textuais em coordenadas geográficas, permitindo que aplicações conectem informações de localização compreensíveis por pessoas a mapas digitais.

Entretanto, os resultados de pesquisas geográficas podem apresentar informações incompletas, ambíguas ou inconsistentes. Uma coordenada retornada por um serviço de geolocalização nem sempre confirma que um imóvel específico foi identificado corretamente.

O LOC VIEW AI foi desenvolvido para investigar esse desafio por meio de uma aplicação funcional, acessível pelo navegador e direcionada à interpretação de endereços brasileiros.

A aplicação combina interpretação de endereços, recuperação de dados geográficos, normalização textual, validação estruturada e visualização condicional de localizações para distinguir entre um endereço identificado e uma localização que não pôde ser suficientemente confirmada.

Seu princípio central é **priorizar a consistência geográfica em vez de apresentar uma precisão que os dados disponíveis não sustentam**.

Em vez de considerar qualquer resultado geográfico plausível como um endereço verificado, o sistema avalia os atributos da localização antes de apresentar coordenadas e links externos para mapas.

### 2. Definição do Problema

Os dados de endereçamento brasileiros apresentam diversos desafios para a interpretação automatizada.

Nomes de ruas podem conter números, abreviações ou expressões numéricas escritas por extenso. Números de imóveis podem apresentar complementos alfabéticos. Códigos de Endereçamento Postal (CEP) podem ser informados separadamente ou incorporados ao próprio endereço.

Além disso, serviços externos de geocodificação podem retornar registros que representam ruas, bairros ou elementos geográficos próximos, em vez do imóvel exato solicitado.

Essas condições introduzem o risco de apresentar um resultado geográfico aparentemente plausível, mas incorreto.

Diante desse cenário, o projeto procura responder à seguinte questão:

**Como uma aplicação de geolocalização leve e acessível pode identificar endereços brasileiros e, ao mesmo tempo, comunicar incertezas quando os dados geográficos são incompletos ou inconsistentes?**

### 3. Objetivo do Produto

O objetivo principal do LOC VIEW AI é oferecer uma interface simples para inserir um endereço brasileiro e recuperar informações geográficas quando os dados disponíveis satisfazem regras de validação previamente definidas.

A aplicação foi desenvolvida para:

- Interpretar endereços brasileiros estruturados e semiestruturados.
- Normalizar determinadas variações de nomes de ruas e expressões numéricas.
- Validar o número do imóvel solicitado, o município, o estado e as informações de CEP disponíveis.
- Recuperar possíveis correspondências geográficas por meio da API Nominatim, do OpenStreetMap.
- Exibir coordenadas somente quando um registro correspondente ao endereço exato satisfaz as regras de validação da aplicação.
- Diferenciar um endereço identificado de um resultado em que apenas a rua foi localizada, mas o número do imóvel permanece sem confirmação.
- Disponibilizar links para o Google Maps e para imagens eventualmente disponíveis no Street View.
- Apresentar mensagens compreensíveis quando não for possível confirmar um endereço.

### 4. Visão Geral da Solução

O LOC VIEW AI implementa um fluxo de validação geográfica baseado em regras determinísticas.

O usuário informa um endereço e, opcionalmente, um CEP. A aplicação processa os dados fornecidos, extrai os componentes relevantes do endereço e constrói consultas de pesquisa geográfica.

Os registros candidatos retornados pelo Nominatim são avaliados em relação às informações solicitadas.

Quando um registro satisfaz as verificações exigidas, a aplicação apresenta suas coordenadas e links de navegação.

Quando apenas a rua pode ser identificada, a interface informa que o número do imóvel permanece sem confirmação, em vez de apresentar o resultado como uma correspondência exata.

Caso os dados disponíveis não sustentem a localização solicitada, a aplicação retorna uma mensagem explicativa.

### 5. Escopo do Projeto

A versão atual concentra-se em endereços brasileiros e utiliza dados geográficos disponíveis publicamente.

O sistema inclui uma interface responsiva desenvolvida com Gradio, lógica de processamento em Python, consultas externas de geocodificação, regras de validação e recursos de navegação por mapas.

A aplicação não incorpora, atualmente, um modelo treinado de aprendizado de máquina ou um sistema de inteligência artificial generativa. Sua interpretação e validação de endereços são determinísticas, apoiadas por dados geográficos externos.

Da mesma forma, a disponibilização de um link para o Street View não garante a existência de imagens correspondentes ao imóvel exato solicitado.

Essas delimitações são intencionais e fazem parte da documentação técnica do projeto.

### 6. Resumo da Validação

A versão final, v5.9.3, foi avaliada por meio de dez cenários funcionais selecionados.

Os testes relatados abrangeram endereços completos, números de imóveis com letras, CEPs válidos e inválidos, conflitos entre códigos postais, nomes de ruas que contêm números, ausência de numeração, localizações não confirmadas e combinações incorretas de cidade e estado.

**Resultado informado: 10 dos 10 cenários funcionais selecionados foram aprovados.**

Esse resultado demonstra o comportamento satisfatório da aplicação nos casos avaliados, mas não representa uma garantia de precisão universal dos endereços ou de cobertura geográfica completa.

### 7. Relevância do Projeto

O LOC VIEW AI demonstra como princípios de qualidade de dados podem ser incorporados a uma aplicação voltada ao usuário final.

Sua contribuição não se limita à recuperação de coordenadas geográficas, mas envolve a implementação de regras explícitas para interpretar, validar e apresentar informações de localização.

O projeto evidencia experiência prática com integração de APIs, validação de entradas, tratamento de exceções, desenvolvimento de interfaces, testes funcionais, aprimoramento iterativo e comunicação de incertezas no comportamento de um produto digital.

As próximas seções documentam a arquitetura técnica, o histórico de desenvolvimento, as decisões de engenharia, a abordagem de validação, as limitações e as oportunidades de evolução da aplicação.
