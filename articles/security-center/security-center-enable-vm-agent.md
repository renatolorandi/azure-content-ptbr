<properties
   pageTitle="Habilitar o Agente de VM na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação da Central de Segurança do Azure para **Habilitar o Agente de VM**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="terrylan"/>

# Habilitar o Agente de VM na Central de Segurança do Azure

O Agente de VM deve ser instalado em máquinas virtuais (VMs) para provisionar verificação de linha de base, verificação de patch e programas antimalware. A Central de Segurança do Azure permite que você veja quais VMs exigem o Agente de VM e recomenda que você habilite o Agente de VM nessas VMs.

O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. O artigo [Agente de VM e extensões – parte 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM.


> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## Implementar a recomendação

1. Na folha **Recomendações**, selecione **Agente de VM**. ![Habilitar o Agente de VM][1]

2. Isso abrirá a folha **O Agente de VM está Ausente ou Não Está Respondendo**. Essa folha lista as VMs que exigem o Agente de VM. Siga as instruções na folha para instalar o Agente de VM. ![O Agente de VM está ausente][2]

## Consulte também

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
- [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) -- saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png

<!---HONumber=AcomDC_0720_2016-->