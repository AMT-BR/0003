Descrição: Testando a Autenticação com API Gateway e Cognito (Teste de Integração)
O teste verifica se um usuário com credenciais válidas consegue fazer login, acessar a página inicial e realizar uma requisição à API Gateway.

Cenário: Um usuário acessa a página de login da aplicação Web Angular.
O usuário insere suas credenciais válidas (nome de usuário e senha) no formulário de login.
O usuário clica no botão "Entrar".
O sistema valida as credenciais do usuário com o Amazon AWS Cognito.
Se as credenciais forem válidas, o usuário é redirecionado para a página inicial da aplicação.
O teste verifica se o usuário está autenticado e se consegue realizar uma requisição à API Gateway.

Implementação do Teste: JavaScript

describe('Autenticação com Cognito', () => {
  it('deve permitir o login de um usuário válido e o acesso à API Gateway', () => {
    // Acessa a página de login
    cy.visit('/login');

    // Preenche o formulário de login com credenciais válidas
    cy.get('input[name="username"]').type('usuario@exemplo.com');
    cy.get('input[name="password"]').type('senha123');

    // Clica no botão "Entrar"
    cy.get('button[type="submit"]').click();

    // Verifica se o usuário foi redirecionado para a página inicial
    cy.url().should('include', '/home');

    // Obtém o token de acesso do armazenamento local
    cy.window().localStorage.getItem('cognito-access-token').then((accessToken) => {
      // Faz uma requisição à API Gateway utilizando o token de acesso
      cy.request({
        url: 'https://api.exemplo.com/protected-resource',
        method: 'GET',
        headers: {
          Authorization: `Bearer ${accessToken}`,
        },
      }).then((response) => {
        // Verifica se a requisição foi bem sucedida
        expect(response.status).to.equal(200);

        // Verifica se o corpo da resposta contém o conteúdo esperado
        expect(response.body).to.deep.equal({ message: 'Olá, mundo!' });
      });
    });
  });
});

Explicação do Teste:

Acessar a página de login:
cy.visit('/login') navega para a página de login da aplicação.

Preencher o formulário de login:
cy.get('input[name="username"]').type('usuario@exemplo.com') insere o nome de usuário no campo correspondente.
cy.get('input[name="password"]').type('senha123') insere a senha no campo correspondente.

Clicar no botão "Entrar":
cy.get('button[type="submit"]').click() simula o clique no botão "Entrar".

Verificar o redirecionamento para a página inicial:
cy.url().should('include', '/home') verifica se a URL atual da página contém "/home", indicando que o usuário foi redirecionado para a página inicial após o login bem-sucedido.

Obter o token de acesso do armazenamento local:
cy.window().localStorage.getItem('cognito-access-token') recupera o token de acesso do armazenamento local do navegador. O token de acesso é obtido após o login bem-sucedido e é utilizado para autorizar requisições à API Gateway.

Realizar uma requisição à API Gateway:
cy.request é utilizado para fazer uma requisição HTTP à API Gateway.
url: 'https://api.exemplo.com/protected-resource' define a URL do recurso protegido na API Gateway.
method: 'GET' define o método HTTP da requisição (GET neste caso).
headers: { Authorization:Bearer ${accessToken}} define o cabeçalho de autorização com o token de acesso obtido anteriormente.

Verificar o status da resposta:
expect(response.status).to.equal(200) verifica se o status da resposta HTTP é 200, indicando que a requisição foi bem sucedida.
