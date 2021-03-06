<properties
	pageTitle="O que fazer caso ocorra uma interrupção de Armazenamento do Azure | Microsoft Azure"
	description="O que fazer caso ocorra uma interrupção de Armazenamento do Azure"
	services="storage"
	documentationCenter=".net"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="robinsh"/>


# O que fazer se uma interrupção no Armazenamento do Azure ocorrer

Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis. Às vezes, forças além do nosso controle nos afetam de formas que causam interrupções de serviço não planejadas em uma ou mais regiões. Para ajudá-lo a lidar com essas ocorrências raras, fornecemos as seguintes diretrizes de alto nível para serviços de Armazenamento do Azure.

## Como se preparar 

É essencial para todos os clientes preparar seu próprio plano de recuperação de desastre. O esforço para se recuperar de uma interrupção de armazenamento geralmente envolve a equipe de operações e procedimentos automatizados para reativar os aplicativos em um estado funcional. Consulte a documentação do Azure abaixo para compilar seu próprio plano de recuperação de desastre:

-   [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

-   [Orientações técnicas de resiliência do Azure](../resiliency/resiliency-technical-guidance.md)

-   [Serviço do Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)

-   [Replicação de Armazenamento do Azure](storage-redundancy.md)

-   [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/)

## Como detectar 

A maneira recomendada de determinar o status de serviço do Azure é assinar o [Painel de Integridade do Serviço do Azure](https://azure.microsoft.com/status/).

## O que fazer se uma interrupção no Armazenamento ocorrer

Se um ou mais serviços de Armazenamento estão indisponíveis no momento em uma ou mais regiões, há duas opções a serem consideradas. Se você quiser acesso imediato a seus dados, considere a Opção 2.

### Opção 1: aguardar a recuperação

Nesse caso, nenhuma ação sua é necessária. Estamos trabalhando cuidadosamente para restaurar a disponibilidade do serviço do Azure. Você pode ver o status atual do serviço no nosso [Painel de Integridade do Serviço do Azure](https://azure.microsoft.com/status/).

### Opção 2: copiar os dados do secundário

Se você escolheu [RA-GRS (armazenamento com redundância geográfica de acesso de leitura)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado) para suas contas de armazenamento, você terá acesso de leitura aos dados da região secundária. Você pode usar ferramentas como [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), e [biblioteca de Movimentação de Dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar dados da região secundária para outra conta de armazenamento em uma região não afetada e, em seguida, apontar os aplicativos para essa conta de armazenamento para disponibilidade de leitura e gravação.

## O que esperar se ocorrer um failover de Armazenamento

Se você escolheu [GRS (armazenamento com redundância geográfica)](storage-redundancy.md#geo-redundant-storage) ou [RA-GRS (armazenamento com redundância geográfica de acesso de leitura)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado), o Armazenamento do Azure mantém seus dados duráveis em duas regiões (primária e secundária). Em ambas as regiões, o Armazenamento do Azure mantém constantemente várias réplicas de seus dados.

Quando um desastre regional afeta sua região primária, primeiro tentaremos restaurar o serviço nessa região. Dependendo da natureza do desastre e seus impactos, em algumas ocasiões raras não será possível restaurar a região primária. Nesse ponto, executaremos um failover geográfico. A replicação de dados entre regiões é um processo assíncrono que pode envolver um atraso, portanto, é possível que as alterações que ainda não foram replicadas para a região secundária sejam perdidas. Você pode consultar o ["Horário da última sincronização" da sua conta de armazenamento](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) para obter detalhes sobre o status da replicação.

Alguns pontos sobre a experiência de failover geográfico de armazenamento:

-   O failover geográfico de armazenamento somente será disparado pela equipe do Armazenamento do Azure – não é necessária nenhuma ação do cliente.

-   Os pontos de extremidade de serviço armazenamento existente para blobs, tabelas, filas e arquivos permanecerão os mesmos após o failover; a entrada DNS precisa ser atualizada para alternar da região primária para a região secundária.

-   Antes e durante o failover geográfico, você não terá acesso de gravação à conta de armazenamento devido ao impacto do desastre, mas você poderá ler por meio do secundário se sua conta de armazenamento tiver sido configurada como RA-GRS.

-   Quando o failover geográfico for concluído e as alterações DNS propagadas, o acesso de leitura e gravação à conta de armazenamento será retomado. Você pode consultar ["Horário do Último Failover Geográfico" da sua conta de armazenamento](https://msdn.microsoft.com/library/azure/ee460802.aspx) para obter mais detalhes.

-   Após o failover, sua conta de armazenamento estará em funcionamento total, mas em um status "degradado", pois costuma ficar hospedada em uma região autônoma sem replicação geográfica possível. Para reduzir esse risco, podemos restaurar a região primária original e, em seguida, fazer um failback geográfico para restaurar o estado original. Se a região primária original for irrecuperável, alocaremos outra região secundária. Para obter mais detalhes sobre a infraestrutura de replicação geográfica de Armazenamento do Azure, consulte o artigo no blog da equipe de Armazenamento sobre [Opções de Redundância e RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

##Melhores Práticas para proteger seus dados

Há algumas abordagens recomendadas para fazer backup de seus dados de armazenamento regularmente.

-   Discos VM – Use o [serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) para fazer backup dos discos VM usados pelas máquinas virtuais do Azure.

-   Blobs de bloco – Crie um [instantâneo](https://msdn.microsoft.com/library/azure/hh488361.aspx) de cada blob de blocos ou copie os blobs para outra conta de armazenamento em outra região usando [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) ou [biblioteca de Movimentação de Dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).

-   Tabelas – Use [AzCopy](storage-use-azcopy.md) para exportar os dados da tabela para outra conta de armazenamento em outra região.

-   Arquivos – Use [AzCopy](storage-use-azcopy.md) ou [Azure PowerShell](storage-powershell-guide-full.md) para copiar os arquivos para outra conta de armazenamento em outra região.

<!---HONumber=AcomDC_0629_2016-->