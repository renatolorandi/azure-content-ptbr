<properties
	pageTitle="Tutorial: Integração do Azure Active Directory com o Ultipro | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Ultipro."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Azure Active Directory com o Ultipro

O objetivo deste tutorial é mostrar como integrar o Ultipro ao Azure AD (Azure Active Directory). A integração do Ultipro ao Azure AD oferece os seguintes benefícios:

- No AD do Azure, é possível controlar quem tem acesso ao Ultipro
- Você pode permitir que seus usuários façam logon automaticamente no Ultipro (Logon Único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure


Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do AD do Azure ao Ultipro, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Ultipro


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Como adicionar o Ultipro da galeria
2. Configurar e testar o logon único do AD do Azure


## Como adicionar o Ultipro da galeria
Para configurar a integração do Ultipro ao AD do Azure, você precisará adicionar o Ultipro da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Ultipro da galeria, execute as etapas a seguir:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Ultipro**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_01.png)

7. No painel de resultados, selecione **Ultipro** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_02.png)

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o Ultipro, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do Ultipro é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no Ultipro. Essa relação de vinculação é estabelecida com a atribuição do valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Ultipro.

Para configurar e testar o logon único do AD do Azure com o Ultipro, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criando um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Ultipro](#creating-a-ultipro-test-user)** - para ter um equivalente de Brenda Fernandes no Ultipro que esteja vinculado à representação dela no Azure AD.
5. **[Atribuindo o usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal clássico do Azure e configurar o logon único em seu aplicativo Ultipro.



**Para configurar o logon único do AD do Azure com Ultipro, execute as etapas a seguir:**

1. No portal clássico do Azure, na página de integração de aplicativo do **Ultipro**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Ultipro**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
 
	![Configurar o logon único](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_03.png)

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_04.png)


    a. Na caixa de texto URL de Logon, digite a URL usada pelos usuários para entrar no aplicativo Ultipro usando o seguinte padrão: **"https://<nome da empresa>.ultipro.com/"**.

    b. Clique em **Próximo**.

4. Na página **Configurar logon único no Ultipro**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_05.png)

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


5. Para que o SSO seja configurado para seu aplicativo, entre em contato com o Gerente de Conta do UltiPro e forneça o seguinte:

    - O arquivo de certificado baixado

    - A **URL do Emissor**

    - A **URL de SSO do SAML**

    - A **URL do Serviço de Logoff Único**


6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.

	![Logon único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.
  
	![Logon único do AD do Azure][11]



### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ultipro-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ultipro-tutorial/create_aaduser_03.png)

4. Para abrir o diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ultipro-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ultipro-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página do diálogo **Perfil do Usuário**, realize as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ultipro-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ultipro-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ultipro-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criação de um usuário de teste do Ultipro

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Ultipro. Trabalhe com a equipe de suporte do Ultipro para adicionar os usuários à conta do Ultipro.


> [AZURE.NOTE] Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Ultipro.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure concedendo a ela acesso ao Ultipro.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Ultipro, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Ultipro**.

	![Configurar o logon único](./media/active-directory-saas-ultipro-tutorial/tutorial_ultipro_50.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Quando você clicar no bloco Ultipro no Painel de Acesso, deverá ser conectado automaticamente ao seu aplicativo Ultipro.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ultipro-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0622_2016-->