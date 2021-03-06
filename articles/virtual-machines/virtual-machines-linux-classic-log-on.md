<properties
	pageTitle="Logon em uma VM Linux no Azure | Microsoft Azure"
	description="Saiba como fazer logon em uma máquina virtual do Azure que executa Linux usando um cliente SSH (Secure Shell)."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="rasquill"/>


#Como fazer logon em uma máquina virtual que executa o Linux #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Saiba como [executar estas etapas usando o modelo de implantação do Resource Manager](virtual-machines-linux-quick-create-portal.md).

Instale um cliente SSH no computador que você deseja usar para fazer logon na máquina virtual. Há muitos programas de cliente SSH que você pode escolher. Estas são algumas opções:

- Para uma máquina virtual executando um sistema operacional Linux, você deve usar um cliente SSH (Secure Shell) para fazer logon; é difícil imaginar uma distribuição que ainda não isso tenha instalado por padrão. Consulte [Como usar SSH](virtual-machines-linux-ssh-from-linux.md) para obter mais informações sobre o Linux.
- Em um computador que executa um sistema operacional Windows, poderá usar um cliente SSH como PuTTY. Para obter mais informações, consulte a [Página de download do PuTTY (em inglês)](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).


>[AZURE.NOTE] Para mais dicas de requisitos e solução de problemas, confira [Conecte-se à uma máquina virtual Azure com RDP ou SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

Este procedimento mostra como usar o cliente SSH no OS X para acessar a máquina virtual.

1. Encontre o **nome do host** e **informações da porta** no [Portal de Gerenciamento](http://manage.windowsazure.com). Você pode encontrar as informações que você precisa no painel da máquina virtual. Clique no nome da máquina virtual e procure os **Detalhes de SSH** na seção **Visão Rápida** do painel de controle.

	![Obter detalhes de SSH](./media/virtual-machines-linux-classic-log-on/portalsshdetails.png)

2. Faça logon na máquina virtual usando a conta especificada durante sua criação, juntamente com o nome do host e a porta apropriados. Para obter mais detalhes sobre como criar uma máquina virtual com o nome de usuário e senha, confira [Criar uma máquina virtual que executa Linux](virtual-machines-linux-classic-createportal.md).

	![Faça logon na máquina virtual](./media/virtual-machines-linux-classic-log-on/sshport.png)

>[AZURE.NOTE] A extensão VMAccess pode ajudá-lo a redefinir a chave SSH ou a senha, caso a esqueça. Se você esquecer o nome de usuário, você pode usar a extensão para criar um novo com a autoridade sudo. Para instruções, confira [Como redefinir uma senha ou SSH para máquinas virtuais Linux].

Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

<!-- LINKS -->
[Como redefinir uma senha ou SSH para máquinas virtuais Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!---HONumber=AcomDC_0629_2016-->