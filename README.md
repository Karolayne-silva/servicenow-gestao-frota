# Sistema de Gestão de Frota 🚗📊

Este repositório contém uma aplicação customizada desenvolvida nativamente na plataforma **ServiceNow** (via *Application Scope*). O sistema gere o ciclo de vida de veículos corporativos e automatiza o processo de reservas, integrando validações no lado do cliente (Client-side), regras de negócio na base de dados (Server-side) e fluxos de aprovação automatizados.

🎥 **[Clique aqui para assistir ao Vídeo de Demonstração do Projeto a funcionar](COLOQUE_O_LINK_DO_SEU_VIDEO_AQUI)**

---

## 🏗️ Estrutura de Dados

O modelo de dados foi construído utilizando tabelas inter-relacionadas no ServiceNow:

*   **Veículo:** 
    *   **Campos:** Placa, Modelo, Ano, Estado (Disponível, Em Manutenção, Em Uso), Quilometragem Atual.
*   **Reserva:** 
    *   **Campos:** Solicitante (Referência `sys_user`), Veículo (Referência à tabela de Veículos), Data de Retirada, Data de Devolução, Estado da Reserva (Solicitada, Aprovada, Rejeitada, Concluída).

---

## ⚙️ Funcionalidades e Regras de Negócio Implementadas

### 1. Interface e Experiência do Utilizador (Client-side)
Foco em interatividade e prevenção de erros durante o preenchimento dos formulários.
*   **Validação de Campos (UI Policy):** A **Data de Devolução** nunca pode ser preenchida antes da **Data de Retirada**.
*   **Consulta Assíncrona (Client Script + GlideAjax):** Um script do tipo `onChange` monitoriza o campo "Veículo" no formulário de reserva. Ao selecionar um carro, uma chamada AJAX consulta a base de dados e exibe a quilometragem atual do veículo selecionado em tempo real através de um `g_form.addInfoMessage()`.

### 2. Validações de Servidor e Base de Dados (Server-side)
Garantia de integridade das regras de negócio através de *Business Rules*.
*   **Validação de Datas (Before Insert/Update):** Um script que verifica se a *Data de Devolução* é anterior à *Data de Retirada*. Caso seja, a transação é abortada através do método `setAbortAction(true)` e o usuário recebe um alerta de erro na tela, garantindo a integridade cronológica.
*   **Bloqueio de Veículos Indisponíveis (Before Insert):** Interceta tentativas de criação de reservas. Se o veículo referenciado possuir o estado "Em Manutenção", a transação é abortada e uma mensagem de erro é exibida.
*   **Atualização de Estado em Cascata (After Update):** Quando o estado de uma reserva é alterado para "Concluída", esta regra localiza o registo do veículo associado na base de dados e atualiza o seu estado automaticamente de volta para "Disponível".

### 3. Automação de Processos (Flow Designer)
Orquestração do ciclo de aprovações sem necessidade de scripts complexos.
*   **Fluxo de Aprovação de Reserva:** Gatilho ativado na criação de um novo registo de Reserva.
    1. O sistema altera o estado inicial para "Solicitada".
    2. Um pedido de aprovação (Approval Action) é encaminhado para o gestor direto do solicitante.
    3. Mediante aprovação, o estado da reserva evolui automaticamente para "Aprovada".

---

## 🛠️ Tecnologias e Recursos ServiceNow Utilizados

*   **ServiceNow Studio:** Gestão do Application Scope e integração via Source Control (GitHub).
*   **JavaScript (ES5):** Lógica customizada para manipulação de objetos da plataforma e consultas via Server API.
*   **GlideRecord & GlideAjax:** APIs nativas para operações de CRUD (Create, Read, Update, Delete) e chamadas assíncronas entre cliente e servidor.
*   **Flow Designer:** Automação de processos baseada em gatilhos e ações (low-code/no-code).
*   **System Definition:** Criação de tabelas e dicionários de dados.

---

## 🚀 Como testar este projeto (Para Desenvolvedores)

Se possui uma PDI (Personal Developer Instance) do ServiceNow, pode importar este código:
1. Faça o fork deste repositório.
2. Na sua instância ServiceNow, abra o **Studio**.
3. Selecione **Import From Source Control**.
4. Insira o URL deste repositório e as suas credenciais do GitHub.
5. A aplicação estará disponível no seu menu de navegação sob o escopo criado.
