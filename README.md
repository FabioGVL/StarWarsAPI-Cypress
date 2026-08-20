[![Cypress Tests](https://github.com/FabioGVL/StarWarsAPI/actions/workflows/cypress.yml/badge.svg)](https://github.com/FabioGVL/StarWarsAPI/actions/workflows/cypress.yml)

# Automação de Testes de API - Star Wars API (SWAPI)

## Escopo do Produto

A **SWAPI (Star Wars API)** é uma enciclopédia web pública e gratuita que centraliza dados estruturados sobre o universo de Star Wars. O projeto de automação visa garantir a integridade dos dados, a conformidade de contratos JSON e a estabilidade dos endpoints de consulta que fornecem informações detalhadas sobre personagens, planetas, naves, veículos, espécies e filmes da franquia.

## Escopo do Teste

A estratégia foca em validar a precisão dos dados retornados, o comportamento de paginação e a robustez do sistema diante de parâmetros válidos e limites de entrada inválidos.

* **Mapeamento de Features:** Enciclopédia SWAPI — Consulta de dados sobre personagens, planetas, naves, espécies, filmes e paginação de registros.
* **Features Testadas:** Busca de entidades por ID específico, consistência de resposta de atributos, validação de contrato JSON e tratamento de erros para rotas ou IDs inexistentes.
* **Massa de Dados:** Utilização de identificadores (IDs) específicos e conhecidos de personagens, planetas e recursos para verificar a precisão e fidelidade dos dados retornados.
* **Tipos de Testes:**
  * **Testes de Funcionalidade:** Garantir que os endpoints da API estão operando e retornando os dados corretos conforme o esperado.
  * **Testes de Integração:** Garantir que a comunicação entre o script de teste do Cypress e a SWAPI ocorra sem falhas de protocolo ou conexão.
  * **Testes de Contrato:** Verificar se a estrutura do JSON recebido (tipos de dados e presença de propriedades obrigatórias) está em total conformidade com o padrão da API.

## Arquitetura e Estrutura

O projeto foi organizado para garantir a separação entre a lógica de teste e a configuração das requisições, utilizando o ecossistema do Cypress:

* **Padrão de Projeto:** Organização baseada nas convenções do Cypress, priorizando testes de API via `cy.request()` para eliminar dependência de interface gráfica e acelerar a execução.
* **Tecnologias e Ambiente:** `Cypress v10.11.0` | `Node.js v20.15.0` | `Google Chrome` | `Windows 11` | `Postman` | `Git`

---

## Testes e Validações

A suíte de testes foi desenvolvida para validar a recuperação de informações da SWAPI, verificando a integridade dos dados retornados, o comportamento dos endpoints e os limites de entrada da API.

<details>
<summary><b>Suíte de Testes</b></summary>

### 1. Recuperação e validação de informações de personagens

* **Dado** que acesso o endpoint `/people/{id}/`
* **Quando** inserir ao final do endpoint um ID do número 1 ao 83 e realizar a busca
* **Então** a API deverá retornar os dados unitários corretos do personagem de ID buscado

### 2. Recuperação e validação de espécies

* **Dado** que acesso o endpoint `/species/{id}/`
* **Quando** inserir ao final do endpoint o ID da espécie associada a um personagem específico e realizar a busca
* **Então** a API deverá retornar os dados unitários corretos da espécie buscada

### 3. Recuperação e validação de veículos

* **Dado** que acesso o endpoint `/vehicles/{id}/`
* **Quando** inserir ao final do endpoint o ID do veículo associado a um personagem específico e realizar a busca
* **Então** a API deverá retornar os dados unitários corretos do veículo buscado

### 4. Recuperação e validação de espaçonaves

* **Dado** que acesso o endpoint `/starships/{id}/`
* **Quando** inserir ao final do endpoint o ID da espaçonave associado a um personagem específico e realizar a busca
* **Então** a API deverá retornar os dados unitários corretos da espaçonave buscada

### 5. Validação do comportamento da API com ID inexistente - Limite de entrada

* **Dado** que acesso o endpoint `/people/{id}/`
* **Quando** inserir um ID inexistente e realizar a busca
* **Então** a API deverá retornar a mensagem de erro `"Not found"`

### 6. Validação do comportamento da API com página inexistente - Limite de entrada

* **Dado** que acesso o endpoint `/people/?page={id}`
* **Quando** inserir um ID inexistente e realizar a busca
* **Então** a API deverá retornar a mensagem de erro `"Not found"`

### 7. Validação da paginação e listagem de personagens por página

* **Dado** que acesso o endpoint `/people/?page={id}`
* **Quando** efetuar a busca e a API retornar as informações da página
* **Então** as informações de paginação e personagens retornadas deverão estar de acordo com o resultado esperado

</details>

---

## Relatório de testes

Durante a execução da suíte, foram identificadas inconsistências entre os dados retornados pela API e as informações apresentadas na documentação. As divergências foram mapeadas nos testes de validação para facilitar sua análise e reprodução.

| Endpoint / Módulo | Suíte de Teste | Resultado esperado / Obtido |
| :--- | :--- | :--- |
| **`/people/{id}`** | `testeStarWarsApi.cy.js` | O personagem de ID `17` não existe, apesar de a API apresentar personagens mapeados do ID `1` ao `83` e informar um total de `82` personagens. |
| **`/people/{id}`** | `testeStarWarsApi.cy.js` | Dados do personagem R2-D2 divergem da documentação no campo `films`. |
| **`/people/{id}`** | `testeStarWarsApi.cy.js` | Dados do personagem Leia Organa divergem da documentação no campo `created`. |
| **`/people/{id}`** | `testeStarWarsApi.cy.js` | Dados do personagem Beru Whitesun lars divergem da documentação no campo `films`. |
| **`/people/{id}`** | `testeStarWarsApi.cy.js` | Dados do personagem R5-D4 divergem da documentação no campo `films`. |

<details>
<summary><b>Detalhes: Personagem inexistente</b></summary>

- **Endpoint:** `/people/83`
- **Divergência:** A API retorna o personagem existente `Tion Medon`, enquanto a contagem geral informa `82` personagens.
- **Validação:** Os personagens estão mapeados do ID `1` ao `83`, porém o personagem de ID `17` não existe.

### Passos para reproduzir o erro

1. Acesse o site `https://swapi.dev`.
2. Insira o endpoint `/people/?page=2`.
3. Realize a busca.
4. Aguarde a API retornar as informações.
5. Gire o scroll do mouse para baixo.
6. Verifique que os IDs passam do `16` para o `18`.

</details>

<details>
<summary><b>Detalhes: Dados inconsistentes - Personagem R2-D2</b></summary>

- **Dado** que insiro os dados do personagem R2-D2 presentes na documentação para execução do teste de validação das informações dos personagens.
- **Quando** executo o teste, o mesmo falha devido à inconsistência de dados entre a API e a documentação.
- **Então** verifico que as informações contidas no campo `FILMS` estão divergentes.

### Passos para reproduzir o erro

1. No GitHub, clique em **Code**.
2. Clique em **Download ZIP** para fazer o download do projeto.
3. Descompacte o arquivo.
4. Abra a pasta do projeto no VS Code.
5. Navegue até `Cypress > E2E`.
6. No terminal do Cypress, execute `npx cypress open`.
7. Selecione **E2E Testing**.
8. Selecione o navegador desejado.
9. Selecione o teste `testeStarWarsApi.cy.js`.
10. Aguarde a conclusão do teste.
11. Acesse `https://swapi.dev/api/people/3/` através do navegador ou utilize o Postman.
12. Compare as informações apresentadas no resultado do teste com os dados retornados pela API.

</details>

<details>
<summary><b>Detalhes: Dados inconsistentes - Personagem Leia Organa</b></summary>

- **Dado** que insiro os dados do personagem Leia Organa presentes na documentação para execução do teste de validação das informações dos personagens.
- **Quando** executo o teste, o mesmo falha devido à inconsistência de dados entre a API e a documentação.
- **Então** verifico que as informações contidas no campo `CREATED` estão divergentes.

### Passos para reproduzir o erro

1. No GitHub, clique em **Code**.
2. Clique em **Download ZIP** para fazer o download do projeto.
3. Descompacte o arquivo.
4. Abra a pasta do projeto no VS Code.
5. Navegue até `Cypress > E2E`.
6. No terminal do Cypress, execute `npx cypress open`.
7. Selecione **E2E Testing**.
8. Selecione o navegador desejado.
9. Selecione o teste `testeStarWarsApi.cy.js`.
10. Aguarde a conclusão do teste.
11. Acesse `https://swapi.dev/api/people/3/` através do navegador ou utilize o Postman.
12. Compare as informações apresentadas no resultado do teste com os dados retornados pela API.

</details>

<details>
<summary><b>Detalhes: Dados inconsistentes - Personagem Beru Whitesun lars</b></summary>

- **Dado** que insiro os dados do personagem Beru Whitesun lars presentes na documentação para execução do teste de validação das informações dos personagens.
- **Quando** executo o teste, o mesmo falha devido à inconsistência de dados entre a API e a documentação.
- **Então** verifico que as informações contidas no campo `FILMS` estão divergentes.

### Passos para reproduzir o erro

1. No GitHub, clique em **Code**.
2. Clique em **Download ZIP** para fazer o download do projeto.
3. Descompacte o arquivo.
4. Abra a pasta do projeto no VS Code.
5. Navegue até `Cypress > E2E`.
6. No terminal do Cypress, execute `npx cypress open`.
7. Selecione **E2E Testing**.
8. Selecione o navegador desejado.
9. Selecione o teste `testeStarWarsApi.cy.js`.
10. Aguarde a conclusão do teste.
11. Acesse `https://swapi.dev/api/people/3/` através do navegador ou utilize o Postman.
12. Compare as informações apresentadas no resultado do teste com os dados retornados pela API.

</details>

<details>
<summary><b>Detalhes: Dados inconsistentes - Personagem R5-D4</b></summary>

- **Dado** que insiro os dados do personagem R5-D4 presentes na documentação para execução do teste de validação das informações dos personagens.
- **Quando** executo o teste, o mesmo falha devido à inconsistência de dados entre a API e a documentação.
- **Então** verifico que as informações contidas no campo `FILMS` estão divergentes.

### Passos para reproduzir o erro

1. No GitHub, clique em **Code**.
2. Clique em **Download ZIP** para fazer o download do projeto.
3. Descompacte o arquivo.
4. Abra a pasta do projeto no VS Code.
5. Navegue até `Cypress > E2E`.
6. No terminal do Cypress, execute `npx cypress open`.
7. Selecione **E2E Testing**.
8. Selecione o navegador desejado.
9. Selecione o teste `testeStarWarsApi.cy.js`.
10. Aguarde a conclusão do teste.
11. Acesse `https://swapi.dev/api/people/3/` através do navegador ou utilize o Postman.
12. Compare as informações apresentadas no resultado do teste com os dados retornados pela API.

</details>

## Pré-requisitos

Certifique-se de possuir as seguintes ferramentas instaladas em seu ambiente:

- [Node.js](https://nodejs.org/) — versão 20.15.0 ou superior recomendada
- [Git](https://git-scm.com/)
- Editor de código de sua preferência, como o [VS Code](https://code.visualstudio.com/)

---

# Passos para Configurar e Reproduzir o Projeto

Siga o guia abaixo para clonar, configurar o ambiente e executar a suíte de testes automatizados em sua máquina local.

---

## Obtendo o Código do Projeto

Você pode obter os arquivos do projeto de duas formas.

### Opção A: Clonando via Git (Recomendado)

Abra o terminal e execute o comando abaixo para clonar o repositório:

```bash
git clone https://github.com/FabioGVL/StarWarsAPI.git
```

Em seguida, navegue para dentro da pasta do projeto:

```bash
cd StarWarsAPI
```

### Opção B: Baixando via ZIP

1. Acesse a página do repositório no GitHub.
2. Clique no botão verde **Code**.
3. Selecione **Download ZIP**.
4. Extraia o conteúdo do arquivo compactado em uma pasta no seu computador.
5. Abra o VS Code, vá em **Arquivo > Abrir Pasta** e selecione a pasta descompactada.

---

## Instalando as Dependências

Com o terminal aberto na raiz do projeto, execute o comando abaixo para instalar as dependências do projeto:

```bash
npm install
```

---

## Executando os Testes

O projeto suporta diferentes modos de execução do Cypress.

### Modo Interativo (Cypress App)

Abre a interface gráfica do Cypress para acompanhar a execução visualmente:

```bash
npx cypress open
```

Na interface, selecione **E2E Testing**, escolha o navegador desejado e clique no arquivo de teste correspondente para iniciar.

### Modo Headless (Linha de Comando)

Executa os testes diretamente pelo terminal de forma rápida:

```bash
npx cypress run
```

---

## Resumo dos Comandos

| **Objetivo** | **Comando** |
| -------------------------------------- | ------------------ |
| **Instalar dependências / Cypress** | `npm install` |
| **Abrir interface gráfica do Cypress** | `npx cypress open` |
| **Executar testes em modo Headless** | `npx cypress run` |
