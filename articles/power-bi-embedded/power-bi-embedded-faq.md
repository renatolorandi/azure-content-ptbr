<properties
   pageTitle="Perguntas frequentes"
   description="Perguntas Frequentes sobre o Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="owend"/>

# Perguntas Frequentes sobre o Power BI Embedded

## Qual é nosso anúncio mais recente sobre o Power BI Embedded?

O Serviço Microsoft Power BI Embedded agora está disponível com o SLA de GA padrão. Para saber mais sobre esta versão, confira [Novidades](power-bi-embedded-whats-new.md).

## O que é o Microsoft Power BI Embedded?

O Microsoft Power BI Embedded agora está disponível de forma geral. O Power BI Embedded é um serviço do Azure que permite que os desenvolvedores de aplicativos insiram visualizações e relatórios impressionantes e totalmente interativos em aplicativos voltados para o cliente, sem o tempo nem as despesas de compilar seus próprios controles desde o início. Agora temos o Power BI Embedded disponíveis com o SLA em nove data centers em todo o mundo. Também aprimoramos funcionalidades no serviço, como suporte para segurança de dados por meio da RLS (segurança em nível de linha) no Power BI Embedded para filtragem avançada. Também temos simplificado e atualizado o modelo de preços do Power BI Embedded. Para saber mais, confira [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/).

## Quem gostaria de usar o Microsoft Power BI Embedded e por que?

O Microsoft Power BI Embedded é para desenvolvedores de aplicativos que desejam oferecer experiências de visualização de dados impressionantes e interativas para os seus usuários em qualquer um dos seus dispositivos, sem necessidade de compilá-las por conta própria. Com o Power BI Embedded, os desenvolvedores podem fornecer exibições sempre atualizadas com a Consulta Direta. Os desenvolvedores podem, também programaticamente, implantar e gerenciar o Power BI automatizado com as APIs do ARM do Azure e APIs do Power BI. Assim como acontece com todas as coisas do Power BI, o serviço inserido automaticamente é dimensionado para atender às necessidades e ao uso do seu aplicativo. O serviço do Power BI Embedded apresenta um modelo de preços Pré-pago baseado em consumo.

## Qual a relação entre o Power BI Embedded e o serviço do Power BI?

O Power BI Embedded e o serviço do Power BI são ofertas separadas. O Power BI Embedded conta com um modelo de cobrança baseado em consumo, é implantado por meio do portal do Azure e foi projetado para habilitar os ISVs a inserir visualizações de dados em aplicativos para que sejam usadas por seus respectivos clientes. O serviço do Power BI é cobrado e implantado por meio do portal do O365 e é uma oferta autônoma de BI para uso geral, destinada principalmente ao uso corporativo interno. Para saber mais sobre o serviço Power BI, confira [www.powerbi.com](https://powerbi.microsoft.com).

## Como o Power BI Embedded aprimora meu aplicativo?

Os aplicativos são significativamente mais poderosos quando você pode aproveitar visualizações de dados incríveis e interativas para informar decisões do usuário diretamente em seu aplicativo. O Power BI Embedded permite que você aprimore seu aplicativo com visualizações de dados interativas, avançadas e sempre atualizadas para que você possa aumentar a utilidade, a satisfação do usuário e a fidelidade do seu aplicativo, além de entregar análise contextual com facilidade em qualquer dispositivo.

## Existem regras ou restrições sobre como é possível usar o Power BI Embedded no meu aplicativo?

O Power BI Embedded destina-se a seus aplicativos fornecidos para uso por terceiros. Se você desejar usar o serviço Power BI Embedded para criar um aplicativo de negócios interno, cada um dos seus usuários internos precisará de uma USL do Power BI Pro e o consumo do serviço Power BI Embedded será cobrado da sua organização, além de taxas da USL do Power Pro BI. Para evitar a cobrança das taxas da USL do Power BI Pro e custos de consumo do Power BI Embedded para aplicativos internos, o serviço do Power BI oferece seu próprio conteúdo, inserindo funcionalidades fora do Power BI Embedded sem nenhum custo adicional para os titulares da USL do Power BI (dev.powerbi.com).

## É possível usar o Power BI Embedded para criar aplicativos internos?

Não, o Power BI Embedded é destinado apenas para uso por usuários externos e não pode ser usado em aplicativos de negócios internos. Para inserir conteúdo do Power BI para uso em aplicativos de negócios internos, você deve usar o serviço do Power BI e todos os usuários consumindo esse conteúdo devem ter licença de assinatura de usuário do Power BI Gratuito ou do Power BI Pro. Mais informações sobre como integrar aplicativos internos ao serviço do Power BI estão disponíveis em [https://dev.powerbi.com](https://dev.powerbi.com).

## Este serviço está disponível globalmente?

O serviço Power BI Embedded agora está disponível na maioria dos data centers. Você sempre pode verificar a disponibilidade mais recente [aqui](https://azure.microsoft.com/status/).

## Qual é o SLA disponível para o serviço?

Power BI Embedded com SLA padrão do Azure. Confira [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) para saber mais.

## Como esse serviço é cobrado?

Confira [Preços do Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760527) para obter informações sobre preços.

## O que é uma renderização e como ela é cobrada?

>[AZURE.NOTE] O desconto no preço por renderização foi oferecido durante a visualização do Power BI Embedded e, devido a comentários do usuário, será interrompido em favor do preço por sessão. A mudança do preço por renderização para o preço por sessão entrará em vigor em 1º de setembro de 2016.

Uma renderização consiste em um elemento visual exibido para um usuário final que resulta em uma consulta para o serviço. Por exemplo, se um usuário exibisse um relatório contendo quatro visuais, teríamos quatro renderizações como resultado. Se o usuário atualizasse o relatório e mais consultas fossem enviadas ao serviço, teríamos mais quatro renderizações. O proprietário do serviço terá controle sobre a extensão pela qual os usuários finais podem conduzir novas consultas que resultam em renderizações pagas para limitar a exposição do custo e minimizar os custos em cenários de dados estáticos.

As renderizações são cobradas a cada 1.000 unidades. Para quantidades de renderizações inferiores a 1.000, a cobrança ocorre proporcionalmente. Os clientes recebem 1.000 renderizações gratuitas por mês. Os clientes que compram por contratos de Licenciamento por Volume devem consultar seu vendedor ou parceiro da Microsoft para obter informações sobre preços.

## O que é uma sessão de relatório e como ela é cobrada?

Uma sessão é um conjunto de interações entre um usuário final e um relatório do Power BI Embedded. Cada vez que um relatório do Power BI Embedded é exibido a um usuário, uma sessão é iniciada e será cobrada uma sessão do titular da assinatura. As sessões são cobradas a uma taxa fixa, independentemente da quantidade de elementos visuais em um relatório ou da frequência com que o conteúdo do relatório é atualizado. Uma sessão termina quando o usuário fecha o relatório ou quando a sessão expira após uma hora.

## Você oferece ferramentas ou diretrizes para ajudar a estimar quantas renderizações/sessões devem ser esperadas? Como saberei quantas renderizações foram concluídas?

O Portal do Azure fornecerá detalhes de cobrança sobre quantas sessões de relatório/renderizações foram executadas para a sua assinatura.

## Eu preciso de uma assinatura do Power BI para desenvolver aplicativos com o Power BI Embedded? Como começar?

Como desenvolvedor de aplicativos, você não precisa ter uma assinatura do Power BI para compilar relatórios e visualizações que deseja usar em seu aplicativo. Você precisará de uma assinatura do Microsoft Azure e do aplicativo Power BI Desktop gratuito.

Consulte nossa documentação do serviço para obter detalhes sobre como usar o serviço do Power BI Embedded.

## Tenho uma assinatura do Azure. Posso usar o Power BI Embedded usando minha assinatura existente?

Sim. Você pode usar sua assinatura do Azure existente para provisionar e usar o serviço do Microsoft Power BI Embedded.

## O usuário final do meu aplicativo precisa de uma licença do Power BI?

Não. Os usuários finais do aplicativo não precisarão comprar uma assinatura do Power BI separada para acessar as visualizações de dados no aplicativo. No modelo do Power BI Embedded, você, como provedor do aplicativo, será cobrado pelo serviço por meio do medidor de consumo do Azure. Consulte a [página Preços e licenciamento](http://go.microsoft.com/fwlink/?LinkId=760527).

## Como a autenticação do usuário funciona com o Power BI Embedded?

O serviço Power BI Embedded usa Tokens de Aplicativo para autenticação e autorização, em vez de usar autenticação explícita do usuário final. No modelo de Token de Aplicativo, seu aplicativo gerencia a autenticação e autorização para os usuários finais. Em seguida, quando necessário, seu aplicativo cria

e envia os Tokens de Aplicativo, que ordenam ao nosso serviço a renderização do relatório solicitado. Esse design não requer que o aplicativo use o Azure AD para autorização e autenticação de usuário, embora você possa fazer isso. Você pode aprender mais sobre Tokens de Aplicativo [aqui](power-bi-embedded-app-token-flow.md). Também introduzimos o recurso RLS (segurança em nível de linha) no Power BI Embedded para cenários de filtragem de segurança avançada.

## Para quais fontes de dados há suporte com o Power BI Embedded?

Daremos suporte ao acesso a fontes de dados em nuvem que usem credenciais básicas por meio de Consulta Direta. Isso significa que fontes como BD SQL do Azure e DW do Azure SQL têm suporte no momento. Adicionaremos suporte para outras fontes de dados e tipos de acesso nos próximos meses. Anunciaremos novas fontes de dados com suporte no fórum do desenvolvedor do Power BI em [http://dev.powerbi.com](https://dev.powerbi.com/).

## Como funciona o modelo de locação para o Power BI Embedded?

No modelo do Power BI Embedded, não há nenhum requisito explícito para ter seus clientes em locatários do Azure AD. Você pode optar por exigir ou não o Azure AD dos seus clientes. Como resultado, a arquitetura do seu aplicativo e a infraestrutura serão os fatores determinantes para o modelo de locação exigido para o Power BI Embedded.

Os desenvolvedores/funcionários trabalhando para compilar seu aplicativo precisarão ter uma conta de usuário do AAD quando tiverem que gerenciar sua assinatura do Azure e Coleções de Espaço de Trabalho por meio do Portal do Azure. APIs programáticas que permitem aos desenvolvedores importar relatórios, modificar cadeias de conexão e fazer com que as URLs de inserção aproveitem Tokens de Aplicativo para autenticação em seu lugar, de modo que, como resultado, não exigem o AAD. Detalhes sobre como usar nossas APIs e o Portal do Azure podem ser encontrados na [página de documentação do Power BI Embedded no Azure.com](https://azure.microsoft.com/documentation/services/power-bi-embedded/).

## Onde posso saber mais?

Você pode visitar a [página de documentação do Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760526). Você pode manter-se atualizado sobre esse serviço visitando o [blog do desenvolvedor do Power BI](http://blogs.msdn.com/powerbidev) ou visitando o centro de desenvolvedores do Power BI em dev.powerbi.com. Você também pode fazer perguntas no [Stackoverflow](http://stackoverflow.com/questions/tagged/powerbi).

## Como começar?

Você pode começar gratuitamente agora! Se você tiver uma assinatura do Azure, você poderá então provisionar o Power BI Embedded diretamente do portal do Azure. Você também pode criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/). Depois de provisionar o serviço Power BI Embedded, você poderá usar com facilidade APIs REST do Power BI diretamente ou usar o SDK de desenvolvedor disponível no [GitHub](http://go.microsoft.com/fwlink/?LinkID=746472). São fornecidas amostras de como utilizar o SDK de desenvolvedor.

## Consulte também

- [O que é o Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Introdução ao Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0713_2016-->