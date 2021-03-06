<properties
   pageTitle="Continuidade dos negócios em nuvem - recuperação de banco de dados - Banco de Dados SQL | Microsoft Azure"
   description="Saiba como o Banco de Dados SQL do Azure dá suporte para a continuidade dos negócios em nuvem e para a recuperação de banco de dados, além de ajudar a manter os aplicativos em nuvem críticos em execução."
   keywords="continuidade dos negócios, continuidade dos negócios em nuvem, recuperação de desastre do banco de dados, recuperação de banco de dados"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="carlrab"/>

# Continuidade dos negócios com o Banco de Dados SQL do Azure

O Banco de dados SQL do Azure fornece um número de soluções de continuidade de negócios. A continuidade dos negócios é sobre como criar, implantar e executar aplicativos de forma que eles sejam resistentes a eventos com interrupção planejados ou não, que resultem em perda permanente ou temporária da capacidade dos aplicativos de realizar sua função de negócios. O intervalo de eventos não planejados varia de erros humanos a interrupções permanentes ou temporárias até desastres regionais, que podem causar perdas na instalação em larga escala em uma região do Azure específica. Os eventos planejados incluem a reimplantação de aplicativos em uma região diferente e atualizações de aplicativos. O objetivo de continuidade de negócios é que seu aplicativo continue a funcionar durante esses eventos com um impacto mínimo sobre a função de negócios.

Para discutir as soluções de continuidade nos negócios em nuvem do Banco de Dados SQL, existem vários conceitos com os quais você precisa estar familiarizado. Estes são:

* **Recuperação de desastres (DR):** um processo de restauração a função normal de negócios do aplicativo

* **Tempo de recuperação estimado (ERT):** a duração estimada para que o banco de dados esteja totalmente disponível depois de uma solicitação de restauração ou failover.

* **Objetivo de tempo de recuperação (RTO)**: tempo máximo aceitável antes que o aplicativo se recupere totalmente após um evento de interrupção. RTO mede a perda máxima de disponibilidade durante as falhas.

* **Objetivo de ponto de recuperação (RPO)**: a quantidade máxima de últimas atualizações (intervalo de tempo) que o aplicativo pode perder no momento em que ele se recupera totalmente após o evento de interrupção. RPO mede a perda máxima de dados durante as falhas.


## Cenários de continuidade dos negócios em nuvem do Banco de Dados SQL

Os principais cenários a serem considerados ao planejar a continuidade dos negócios e a recuperação do banco de dados são os seguintes:

### Aplicativos de design para continuidade dos negócios

O aplicativo que estou criando é essencial para a minha empresa. Quero criá-lo e configurá-lo para ser capaz de sobreviver a um desastre regional de uma falha catastrófica do serviço. Conheço os requisitos de RPO e RTO para meu aplicativo e escolherei a configuração que atenda a esses requisitos.

### Recuperação de erro humano

Eu tenho direitos administrativos para acessar a versão de produção do aplicativo. Como parte do processo de manutenção regular eu cometi um erro e exclui alguns dados essenciais em produção. Desejo restaurar rapidamente os dados para minimizar o impacto do erro.

### Recuperação de uma interrupção

Eu estou executando o meu aplicativo em produção e recebo um alerta sugerindo que há uma grande interrupção na região em que está implantado. Eu quero iniciar o processo de recuperação para colocá-lo em uma região diferente para minimizar o impacto nos negócios.

### Executar análise de recuperação de desastres

Como a recuperação de uma interrupção realocará a camada de dados para uma região diferente, eu quero testar periodicamente o processo de recuperação e avaliar seu impacto sobre o aplicativo para estar preparado.

### Atualização de aplicativo sem tempo de inatividade

Eu estou liberando uma atualização importante do meu aplicativo. Ela envolve as alterações de esquema do banco de dados, implantação de procedimentos armazenados adicionais etc. Este processo implicará impedir que o usuário acesse o banco de dados Ao mesmo tempo, quero me certificar que a atualização não cause uma interrupção significativa das operações de negócios.

## Recursos de continuidade dos negócios do Banco de Dados SQL

A tabela abaixo lista os recursos de continuidade de negócios do Banco de Dados SQL e mostra as diferenças entre as [camadas de serviço](sql-database-service-tiers.md):

| Recurso | Camada básica | Camada padrão |Camada premium
| --- |--- | --- | ---
| Ponto de restauração pontual | Qualquer ponto de restauração dentro de 7 dias | Qualquer ponto de restauração dentro de 35 dias | Qualquer ponto de restauração dentro de 35 dias
| Restauração geográfica | ERT < 12h, RPO < 1h | ERT < 12h, RPO < 1h | ERT < 12h, RPO < 1h
| Replicação geográfica ativa | ERT < 30s, RPO < 5s | ERT < 30s, RPO < 5s | ERT < 30s, RPO < 5s

Esses recursos são fornecidos para tratar dos cenários listados anteriormente.

> [AZURE.NOTE] Os valores ERT e RPO são metas de engenharia e fornecem apenas diretrizes. Eles não são parte do [SLA para o Banco de Dados SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/)


###Restauração pontual

A [Restauração Pontual](sql-database-recovery-using-backups.md#point-in-time-restore) foi projetada para retornar seu banco de dados para um ponto anterior no tempo. Ele usa os backups de banco de dados, backups incrementais e backups de log de transações que o serviço mantém automaticamente para cada banco de dados do usuário. Esse recurso está disponível para todas as camadas de serviço. Você pode voltar sete dias com Basic, 35 dias com Standard e 35 dias com a Premium.

### Restauração geográfica

A [Restauração Geográfica](sql-database-recovery-using-backups.md#geo-restore) também está disponível para bancos de dados Basic, Standard e Premium. Ele fornece a opção de recuperação padrão quando o banco de dados também estiver indisponível devido a um incidente na região onde seu banco de dados está hospedado. Semelhante ao ponto de restauração pontual, a restauração geográfica depende de backups de banco de dados no armazenamento do Azure com redundância geográfica. Ele restaura a partir da cópia de backup replicado geograficamente e, portanto, é resistente a falhas de armazenamento na região primária.

### Replicação geográfica ativa

A [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md) está disponível para todas as camadas de banco de dados. Ela foi criada para aplicativos que têm requisitos de recuperação mais agressivos do que a Restauração Geográfica pode oferecer. Com a replicação geográfica ativa, você pode criar até quatro secundários legíveis nos servidores em regiões diferentes. Você pode iniciar o failover para qualquer um dos secundários. Além disso, a Replicação Geográfica Ativa pode ser usada para permitir a [cenários de atualização ou realocação de aplicativo](sql-database-manage-application-rolling-upgrade.md), bem como o balanceamento de carga para cargas de trabalho somente leitura.

## Escolhendo entre os recursos de continuidade dos negócios

Para criar seu aplicativo para continuidade dos negócios, é necessário que você responda às seguintes perguntas:

1. Qual recurso de continuidade dos negócios é adequado para proteger meu aplicativo contra interrupções?
2. Qual o nível de redundância e a topologia de replicação eu devo usar?

### Quando usar a restauração geográfica

A [Restauração Geográfica](sql-database-recovery-using-backups.md#geo-restore) fornece a opção de recuperação padrão quando um banco de dados não estiver disponível devido a um incidente na região em que está hospedado. O Banco de Dados SQL fornece uma proteção básica integrada para cada banco de dados por padrão. Isso é feito ao fazer e armazenar os [backups do banco de dados](sql-database-automated-backups.md) no GRS (Armazenamento do Azure com Redundância Geográfica). Se você escolher esse método, nenhuma configuração especial ou alocação adicional de recursos é necessária. Você pode recuperar o banco de dados para qualquer região fazendo a restauração para um novo banco de dados usando esses backups com redundância geográfica automatizados.

Você deve usar a proteção interna caso seu aplicativo atenda aos seguintes critérios:

1. Ele não é considerado crítico. Ele não tem um SLA vinculado, portanto, o tempo de inatividade de 24 horas ou mais não resultará em responsabilidade financeira.
2. A taxa de alteração de dados é baixa (por exemplo, transações por hora). O RPO de 1 hora não resultará em uma grande perda de dados.
3. O aplicativo é de baixo custo e não é possível justificar o custo adicional de replicação geográfica

> [AZURE.NOTE] A restauração geográfica não aloca previamente a capacidade de computação em nenhuma região específica para restaurar bancos de dados ativos do backup durante a interrupção. O serviço irá gerenciar a carga de trabalho associada às solicitações de restauração geográfica de maneira a minimizar o impacto sobre os bancos de dados existentes nessa região e suas demandas de capacidade terão prioridade. Portanto, o tempo de recuperação do banco de dados dependerá de quantos outros bancos de dados serão recuperados na mesma região ao mesmo tempo, bem como do tamanho do banco de dados, do número de log de transações, da largura de banda da rede, etc.

### Quando usar a replicação geográfica ativa

A [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md) permite a criação e a manutenção de bancos de dados (secundários) legíveis em uma região diferente do principal, mantendo-os atualizados usando um mecanismo de replicação assíncrona. Ele garante que seu banco de dados terá os dados e recursos de computação necessários para oferecer suporte às cargas de trabalho do aplicativo após a recuperação.

Você deve usar a Replicação Geográfica Ativa caso seu aplicativo atenda aos seguintes critérios:

1. Ele é de crítico. A perda de dados e a disponibilidade resultarão em responsabilidade financeira.
2. A taxa de alteração de dados é alta (por exemplo, transações por minuto ou segundos). O RPO de 1 h associado à proteção padrão provavelmente resultará em perda de dados inaceitável.
3. O custo associado ao uso da replicação geográfica é significativamente menor do que a responsabilidade financeira potencial e as perdas associadas do negócio.

## Projete soluções de nuvem para recuperação de desastres. 

Ao projetar seu aplicativo para continuidade dos negócios, você deve considerar várias opções de configuração. A escolha dependerá da topologia de implantação do aplicativo e quais partes de seus aplicativos são mais vulneráveis a uma interrupção. Para diretrizes, consulte [Criando soluções de nuvem para recuperação de desastres usando replicação geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

Para estratégias de recuperação detalhadas ao usar um pool elástico, confira [Estratégias de recuperação de desastre para aplicativos que usam o Pool Elástico do Banco de Dados SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## Próximas etapas

- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre cenários de design e recuperação de continuidade dos negócios, veja [Cenários de continuidade](sql-database-business-continuity-scenarios.md)
- Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre opções de recuperação mais rápidas, veja [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- Para saber mais sobre como usar backups automatizados de arquivamento, veja [Cópia de banco de dados](sql-database-copy.md)

<!---HONumber=AcomDC_0720_2016-->