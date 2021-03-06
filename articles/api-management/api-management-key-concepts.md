<properties 
	pageTitle="Principais conceitos de Gerenciamento de API" 
	description="Saiba mais sobre APIs, produtos, funções, grupos e outros conceitos principais do Gerenciamento de API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.date="05/24/2016" 
	ms.author="sdanie"/>

#O que é Gerenciamento de API?

O Gerenciamento de API ajuda as organizações a publicar APIs para parceiros externos e desenvolvedores internos a fim de desbloquear o potencial de seus dados e serviços. Empresas em todos os lugares estão procurando estender suas operações para uma plataforma digital, criando novos canais, encontrando novos clientes e estimulando uma interação mais profunda com os clientes já existentes. O Gerenciamento de API fornece as competências essenciais para garantir um programa de API de sucesso através do envolvimento do desenvolvedor, ideias de negócios, análises, segurança e proteção.

Assista ao vídeo a seguir para obter uma visão geral do Gerenciamento de API do Azure e aprenda a usar o Gerenciamento de API para adicionar muitos recursos para sua API, incluindo o controle de acesso, a limitação de taxa, o monitoramento, o log de eventos e o cache de resposta, com um mínimo de trabalho de sua parte.

> [AZURE.VIDEO azure-api-management-overview]

Para usar o Gerenciamento de API, os administradores criaram as APIs. Cada API consiste em uma ou mais operações e cada uma pode ser adicionada a um ou mais produtos. Para usar uma API, os desenvolvedores assinam um produto que contém essa API e, em seguida, eles podem chamar a operação da API, estando sujeitos a quaisquer políticas que possam estar em vigor.

Esse tópico fornece uma visão geral dos principais conceitos de Gerenciamento de API.

>[AZURE.NOTE] Para obter mais informações, consulte o white-paper em PDF [Gerenciamento de API baseado em nuvem: utilizando a energia das APIs](http://j.mp/ms-apim-whitepaper). Este white paper introdutório sobre o Gerenciamento de API, feito pelo CITO Research, abrange:
>
> - Desafios e requisitos comuns de API
>     - Dissociação de APIs e apresentação de fachadas
>     - Como ajudar os desenvolvedores a trabalhar de modo rápido e fácil
>     - Proteção de acesso
>     - Métricas e análise
> - Obtenção de controle e ideias com uma plataforma de Gerenciamento de API
> - Usando as soluções de nuvem vs local
> - Gerenciamento de API do Azure

## <a name="apis"> </a>APIs e operações

As APIs são a fundação de uma instância de serviço de Gerenciamento de API. Cada API representa um conjunto de operações disponíveis para desenvolvedores. Cada API contém uma referência para serviço back-end que implementa a API, e suas operações são mapeadas para as operações implementadas pelo serviço de back-end. As operações no Gerenciamento de API são altamente configuráveis, com controle sobre o mapeamento de URL, parâmetros de consulta e caminho, conteúdo de solicitação e resposta e caching de resposta de operação. As políticas de limite de taxa, cotas e restrições de IP podem também ser implementadas no nível de operação individual ou da API.

Para obter mais informações, consulte [Como criar APIs][] e [Como adicionar operações a uma API][].


## <a name="products"> </a> Produtos

Os produtos são como as APIs são exibidas para os desenvolvedores. Os produtos no Gerenciamento de API têm uma ou mais APIs e são configurados com título, descrição e termos de uso. Produtos podem ser **Abertos** ou **Protegidos**. Produtos protegidos devem ser assinados antes que possam ser usados, enquanto produtos abertos podem ser usados sem uma assinatura. Quando um produto fica pronto para uso pelo desenvolvedor ele pode ser publicado. Após ele ser publicado, pode ser visualizado (e em caso de produtos protegidos, assinado) pelos desenvolvedores. A aprovação de assinatura é configurada no nível do produto e pode requere a aprovação do administrador ou ser aprovada automaticamente.

Os grupos são usados para gerenciar a visibilidade dos produtos para os desenvolvedores. Os produtos dão visibilidade aos grupos e os desenvolvedores podem exibir e assinar os produtos visíveis para os grupos aos quais pertencem.

Para obter mais informações, consulte [Como criar e publicar um produto][] e o vídeo a seguir.

> [AZURE.VIDEO using-products]

## <a name="groups"> </a> Grupos

Os grupos são usados para gerenciar a visibilidade dos produtos para os desenvolvedores. O Gerenciamento de API tem os grupos de sistema imutáveis a seguir.

-	**Administradores** - os administradores de assinatura do Azure são membros desse grupo. Os administradores gerenciam instâncias de serviço de Gerenciamento de API, criando as APIs, operações e produtos que são usados pelos desenvolvedores.
-	**Desenvolvedores** - usuários autenticados do portal do desenvolvedor se enquadram nesse grupo. Os desenvolvedores são clientes que compilam aplicativos usando suas APIs. Os desenvolvedores têm acesso ao portal do desenvolvedor e criam aplicativos que chamam as operações de uma API.
-	**Convidados** - os usuários não autenticados no portal do desenvolvedor, tais como potenciais clientes visitando o portal do desenvolvedor de uma instância de Gerenciamento de API, pertencem a esse grupo. Eles podem receber certos acessos somente leitura, como a capacidade de exibir APIs, mas não de chamá-las.

Além desses grupos de sistema, os administradores podem criar grupos personalizados ou [aproveitar grupos externos em locatários do Active Directory do Azure](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Grupos personalizados e externos podem ser usados juntamente com grupos de sistema oferecendo visibilidade aos desenvolvedores e acesso a produtos de API. Por exemplo, você poderia criar um grupo personalizado para os desenvolvedores associados a uma organização parceira específica e conceder acesso às APIs de um produto que contém apenas as APIs relevantes. Um usuário pode ser um membro de mais de um grupo.

Para obter mais informações, consulte [Como criar e utilizar grupos][].

## <a name="developers"> </a> Desenvolvedores

Os desenvolvedores representam as contas de usuários em uma instância de serviço de Gerenciamento de API. Os desenvolvedores podem ser criados ou convidados para se juntar aos administradores ou podem fazer a inscrição no [Portal do desenvolvedor][]. Cada desenvolvedor é um membro de um ou mais grupos e pode assinar os produtos que tornam visíveis esses grupos.

Quando os desenvolvedores assinam um produto, recebem as chaves principal e secundária para esse produto. Essa chave é usada quando eles fazem chamadas às APIs dos produtos.

Para obter mais informações, consulte [Como criar ou convidar desenvolvedores][] e [Como associar grupos aos desenvolvedores][].

## <a name="policies"> </a> Políticas

As políticas são um poderoso recurso de Gerenciamento de API que permite ao editor alterar o comportamento da API através da configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. Instruções populares incluem a conversão do formato de XML para JSON e limite de taxa de chamada para restringir a quantidade de chamadas recebidas de um desenvolvedor, além de várias outras políticas disponíveis.

Expressões de política podem ser usadas como valores de atributo ou texto em qualquer uma das políticas de Gerenciamento de API, a menos que a política especifique o contrário. Algumas políticas, como [Controlar fluxo](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) e [Definir variável](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) se baseiam em expressões de políticas. Para obter mais informações, consulte [Políticas avançadas](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), [Expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx) e assista ao vídeo a seguir.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Para obter uma lista completa de políticas de Gerenciamento de API, consulte [Referência de política][]. Para obter mais informações sobre como usar e configurar políticas, consulte [Políticas de Gerenciamento de API][]. Para obter um tutorial sobre como criar um produto com políticas de cota e limite de taxa, consulte [Como criar e definir configurações avançadas de produto][]. Para ver uma demonstração, consulte o vídeo a seguir.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"> </a> Portal do desenvolvedor

O portal do desenvolvedor é onde os desenvolvedores podem aprender sobre suas operações de APIs, visualização e atendimento e assinar produtos. Clientes potenciais podem visitar o portal do desenvolvedor, exibir APIs e operações e fazer inscrição. A URL para o seu portal do desenvolvedor está localizada no painel no Portal Clássico do Azure da sua instância de serviço de Gerenciamento de API.

Você pode personalizar a aparência do portal do desenvolvedor adicionando conteúdo personalizado, personalização de estilo e adicionando sua marca.

## Gerenciamento de API e a economia de API

Para saber mais sobre o Gerenciamento de API, assista à apresentação a seguir da conferência Microsoft Ignite 2015.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Portal do desenvolvedor]: #developer-portal

[Como criar APIs]: api-management-howto-create-apis.md
[Como adicionar operações a uma API]: api-management-howto-add-operations.md
[Como criar e publicar um produto]: api-management-howto-add-products.md
[Como criar e utilizar grupos]: api-management-howto-create-groups.md
[Como associar grupos aos desenvolvedores]: api-management-howto-create-groups.md#associate-group-developer
[Como criar e definir configurações avançadas de produto]: api-management-howto-product-with-rules.md
[Como criar ou convidar desenvolvedores]: api-management-howto-create-or-invite-developers.md
[Referência de política]: api-management-policy-reference.md
[Políticas de Gerenciamento de API]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 

<!---HONumber=AcomDC_0525_2016-->