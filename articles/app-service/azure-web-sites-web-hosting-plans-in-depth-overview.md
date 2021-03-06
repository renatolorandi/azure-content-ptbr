<properties 
	pageTitle="Visão geral detalhada de planos de serviço de aplicativo do Azure" 
	description="Saiba como os planos do Serviço de Aplicativo para o Serviço de Aplicativo do Azure funcionam e como eles beneficiam sua experiência de gerenciamento." 
	keywords="serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, custo de serviço de aplicativo"
	services="app-service" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2016" 
	ms.author="byvinyal"/>

#Visão geral detalhada de planos de serviço de aplicativo do Azure#

Um **plano do Serviço do Aplicativo** representa um conjunto de recursos e capacidades que pode ser compartilhado entre vários aplicativos em [serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714), inclusive aplicativos Web, aplicativos móveis, aplicativos lógicos ou aplicativos de API. Esses planos dão suporte a cinco tipos de preço (**Gratuito**, **Compartilhado**, **Básico**, **Standard** e **Premium**), em que cada tipo tem seus próprios recursos e capacidade. Aplicativos na mesma assinatura e localização geográfica podem compartilhar um plano. Todos os aplicativos que compartilham um plano podem aproveitar todos os recursos e recursos definidos pela camada do plano. Todos os aplicativos associados a determinado plano são executados nos recursos definidos pelo plano. Por exemplo, se seu plano for configurado para usar duas instâncias "pequenas" na camada de serviço padrão, todos os aplicativos associados a esse plano serão executados em ambas as instâncias e terão acesso à funcionalidade da camada de serviço padrão. Instâncias do plano nas quais aplicativos estão em execução são totalmente gerenciadas e altamente disponíveis.

Neste artigo, exploraremos as principais características, como camada e dimensionamento de um plano de Serviço de Aplicativo e como elas entram em jogo ao gerenciar seus aplicativos.

##Aplicativos e planos de Serviço de Aplicativo

Um aplicativo no Serviço de Aplicativo pode ser associado a apenas um plano de Serviço de Aplicativo de cada vez.

Os aplicativos e os planos estão contidos em um grupo de recursos. Um grupo de recursos funciona como o limite do ciclo de vida de todos os recursos contidos nele. Os grupos de recursos permitem que você gerencie todas as partes de um aplicativo, coletivamente.

A capacidade de ter vários planos de Serviço de Aplicativo em um único grupo de recursos permite que você aloque aplicativos diferentes para recursos físicos diferentes. Por exemplo, isso permite a separação de recursos entre ambientes de desenvolvimento, teste e produção. Um cenário para isso é quando talvez você queira alocar um plano com seu próprio conjunto dedicado de recursos para seus aplicativos de produção e um segundo plano para seus ambientes de desenvolvimento e teste. Dessa forma, os testes de carga em uma nova versão de seus aplicativos não competirão pelos mesmos recursos que seus aplicativos de produção, que estão atendendo a clientes reais.

Ter vários planos em um único grupo de recursos também o habilita a definir um aplicativo que abrange várias regiões geográficas. Por exemplo, um aplicativo altamente disponível executando em duas regiões incluirá dois planos, um para cada região e um aplicativo associado com cada plano. Nessa situação, todas as cópias do aplicativo serão associadas a um único grupo de recursos. Ter uma exibição única de um grupo de recursos com vários planos e vários aplicativos faz com que seja fácil de gerenciar, controlar e exibir a integridade do aplicativo.

## Criar um novo plano de Serviço de Aplicativo versus usar um plano existente

Ao criar um novo aplicativo, deve-se considerar a criação de um novo grupo de recursos quando o aplicativo que você está prestes a criar representa um projeto inteiramente novo. Nesse caso, criar um novo grupo de recursos, plano e aplicativo é a escolha certa.

Se o aplicativo que você está prestes a criar for um componente de um aplicativo maior, esse aplicativo Web deverá ser criado no grupo de recursos alocado para o aplicativo maior.

Independentemente do novo aplicativo ser um aplicativo totalmente novo ou parte de um maior, é possível utilizar um plano de Serviço de Aplicativo existente para hospedá-lo ou criar um novo. Essa é mais uma questão de capacidade e carga esperada. Se esse novo aplicativo fará uso intenso de recursos e terá fatores de dimensionamento diferentes dos outros aplicativos hospedados em um plano existente, recomenda-se isolá-lo em seu próprio plano.

Criar um novo plano permite alocar um novo conjunto de recursos para seu aplicativo Web e oferece maior controle sobre a alocação de recurso, já que cada plano é criado com seu próprio conjunto de instâncias.
 
Ter a capacidade de mover aplicativos entre planos também permite alterar a forma como os recursos são alocados pelo aplicativo maior.
 
Finalmente, se você desejar criar um novo aplicativo em uma região diferente, e essa região não tiver um plano existente, você terá que criar um novo plano na região para poder hospedar seu aplicativo nele.

## Criar um plano de Serviço de Aplicativo

É possível criar um **plano do Serviço de Aplicativo** vazio por meio da experiência de navegação do **plano do Serviço de Aplicativo** ou como parte da criação do aplicativo.

Para fazer isso no [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), clique em **NOVO**, selecione **Web + móvel** e depois selecione **Aplicativos Web**, **Aplicativos Móveis**, **Aplicativos Lógicos** ou **Aplicativos de API**. ![][createWebApp]

Você pode selecionar ou criar o plano de Serviço de Aplicativo para o novo aplicativo.
  
 ![][createASP]

Para criar um novo Plano do Serviço de Aplicativo, clique em **+ Criar Novo**, digite o nome do **plano do Serviço de Aplicativo** e selecione um **Local** apropriado. Clique em **Tipo de preço** e selecione um tipo de preço apropriado para o serviço. Selecione **Exibir tudo** para exibir mais opções de preço, como **Gratuito** e **Compartilhado**. Depois de selecionar o tipo de preço, clique no botão **Selecionar**.
 
## Mover um aplicativo para um plano de Serviço de Aplicativo diferente

Você pode mover um aplicativo para um plano de Serviço de Aplicativo diferente no [Portal do Azure](https://portal.azure.com). Os aplicativos podem ser movidos entre planos, desde que os planos estejam no mesmo grupo de recursos e na mesma região geográfica.

Para mover um aplicativo para outro plano, navegue até o aplicativo que deseja mover. No menu Configurações, procure **Alterar Plano do Serviço de Aplicativo**.
 
Isso abrirá o seletor do Plano do Serviço de Aplicativo. Nesse ponto, você poderá selecionar um plano existente ou criar um novo. Apenas planos válidos (no mesmo grupo de recursos e na mesma localização geográfica) são mostrados.

![][change]

Observe que cada plano tem sua própria camada de preços. Por exemplo, ao mover um site de uma camada **Gratuita** para uma camada **Standard**, seu aplicativo poderá aproveitar todos os recursos da camada **Standard**.

## Clonar um aplicativo para um plano diferente do Serviço de Aplicativo
Se desejar mover o aplicativo para uma região diferente, uma alternativa será a clonagem do aplicativo. O clone fará uma cópia de seu aplicativo em um plano ou ambiente novo ou existente do Serviço de Aplicativo em qualquer região.

 ![][appclone]
 
É possível encontrar a opção **Clonar Aplicativo** no menu **ferramentas**.

O clone tem algumas limitações; leia mais sobre isso [aqui](../app-service-web/app-service-web-app-cloning-portal.md)

## Dimensionar um plano de Serviço de Aplicativo

Há três maneiras de dimensionar um plano:

- Altere a **camada de preços** do plano. Por exemplo, um plano na camada **Básica** pode ser convertido para a camada **Padrão** ou **Premium**, e todos os aplicativos associados a esse plano poderão aproveitar os recursos oferecidos na nova camada de serviço.
- Altere o **tamanho da instância** do plano. Por exemplo, um plano na camada **Básica** usando instâncias **pequenas** pode ser alterado para usar instâncias **grandes**. Todos os aplicativos associados a esse plano poderão aproveitar a memória adicional e os recursos de CPU oferecidos pelo tamanho de instância maior.
- Altere a **contagem de instâncias** do plano. Por exemplo, um plano **Standard** escalado horizontalmente para 3 instâncias pode ser escalado para 10 instâncias, e um plano **Premium** pode ser escalado horizontalmente para 20 instâncias (sujeito a disponibilidade). Todos os aplicativos associados a esse plano poderão aproveitar a memória adicional e os recursos de CPU oferecidos pela contagem de instâncias maior.

É possível alterar o tipo de preço e o tamanho de instância clicando no item **Escalar Verticalmente** nas configurações do Aplicativo ou do Plano do Serviço de Aplicativo. As alterações serão aplicadas ao **Plano do Serviço de Aplicativo** e afetarão todos os Aplicativos hospedados por ele.
 
 ![][pricingtier]

##Resumo

Os planos de Serviço de Aplicativo representam um conjunto de recursos e capacidades que pode ser compartilhado entre seus aplicativos. Os planos de Serviço de Aplicativo oferecem a flexibilidade para alocar aplicativos específicos para determinado conjunto de recursos e otimizar ainda mais sua utilização de recursos do Azure. Dessa forma, se quiser economizar dinheiro em seu ambiente de teste, você poderá compartilhar um plano entre vários aplicativos. Você também pode maximizar a produtividade para seu ambiente de produção dimensionando-o em várias regiões e planos.

## O que mudou

* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
   
[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png

<!---HONumber=AcomDC_0518_2016-->