<properties
	pageTitle="Como documentar as fontes de dados | Microsoft Azure"
	description="Artigo de instruções que destaca como documentar ativos de dados no Catálogo de Dados do Azure."
	services="data-catalog"
	documentationCenter=""
	authors="spelluru"
	manager="NA"
	editor=""
	tags=""/>
<tags
	ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="04/07/2016"
	ms.author="spelluru"/>

# Fontes de dados de documentos

## Introdução

O**Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em fontes de dados da empresa. Em outras palavras, o **Catálogo de Dados do Azure** ajuda as pessoas a descobrir, *entender* e usar fontes de dados, ajudando as empresas a obter mais valor de seus dados existentes.

Quando uma fonte de dados é registrada no **Catálogo de Dados do Azure**, seus metadados são copiados e indexados pelo serviço, mas a história não para por aí. O **Catálogo de Dados do Azure** também permite que os usuários forneçam sua própria documentação completa que pode descrever o uso e cenários comuns para a fonte de dados.

Em [Como anotar fontes de dados](data-catalog-how-to-annotate.md), você aprenderá que especialistas que conhecem a fonte de dados podem fazer anotações nela com marcas e uma descrição. O portal do **Catálogo de Dados do Azure** inclui um editor de texto avançado para que os usuários possam documentar totalmente contêineres e ativos de dados. O editor inclui formatação de parágrafo, como cabeçalhos, formatação de texto, listas com marcadores, listas numeradas e tabelas.

Marcas e descrições são ótimas para anotações simples. No entanto, para ajudar os consumidores de dados a entender melhor o uso de uma fonte de dados e cenários de negócios para uma fonte de dados, um especialista pode fornecer documentação completa e detalhada. É fácil documentar uma fonte de dados. Basta selecionar um ativo de dados ou um contêiner e escolher **Documentação**.

![](media\data-catalog-documentation\data-catalog-documentation.png)

## Documentar ativos de dados

O benefício da documentação do **Catálogo de Dados do Azure** permite que você use seu Catálogo de Dados como um repositório de conteúdo para criar uma narrativa completa de seus ativos de dados. Os usuários podem explorar conteúdo detalhado que descreve contêineres e tabelas. Se já tem conteúdo em outro repositório de conteúdo, como o SharePoint ou um compartilhamento de arquivos, você pode adicionar ao ativo links de documentação para fazer referência a esse conteúdo existente. Isso torna os documentos existentes mais detectáveis.

> [AZURE.NOTE] A documentação não está incluída no índice de pesquisa.

![](media\data-catalog-documentation\data-catalog-documentation2.png)

O nível de documentação pode variar desde a descrição das características e do valor de um contêiner de ativos de dados até uma descrição detalhada do esquema de tabela em um contêiner. O nível da documentação fornecido deve ser orientado por suas necessidades comerciais. Porém, em geral, aqui estão alguns prós e contras para a documentação de ativos de dados:

-	Documentar apenas um contêiner: todo o conteúdo está em um local, mas talvez não tenha os detalhes necessários para que os usuários a tomem uma decisão embasada.
-	Documentar apenas as tabelas: o conteúdo é específico para o objeto, mas os usuários têm vários lugares para documentos.
-	Documentar tabelas e contêineres: essa é uma abordagem mais abrangente, mas pode exigir mais manutenção dos documentos.

## Resumo

A documentação de fontes de dados com o **Catálogo de Dados do Azure** pode criar uma narrativa sobre seus ativos de dados com todos os detalhes necessários. Usando links, você pode vincular ao conteúdo armazenado em um repositório de conteúdo existente, que reúne os documentos e os ativos de dados existentes. Depois que os usuários descobrirem ativos de dados apropriados, eles poderão ter um conjunto completo de documentação.

<!---HONumber=AcomDC_0511_2016-->