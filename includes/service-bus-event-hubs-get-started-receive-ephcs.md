## Receber mensagens com EventProcessorHost

[EventProcessorHost][] é uma classe .NET que simplifica o recebimento de eventos de Hubs de Eventos ao gerenciar pontos de verificação persistentes e recebimentos paralelos desses Hubs de Eventos. Usando o [EventProcessorHost][], você pode dividir eventos entre vários destinatários, mesmo quando hospedados em nós diferentes. Este exemplo mostra como usar o [EventProcessorHost][] para um único destinatário. O exemplo de [processamento de eventos de escala horizontal][] mostra como usar [EventProcessorHost][] com vários destinatários.

Para usar o [EventProcessorHost][], você deve ter uma [Conta de armazenamento do Azure][]\:

1. Faça logon no [portal clássico do Azure][] e clique em **NOVO** na parte inferior da tela.

2. Clique em **Serviços de Dados**, em **Armazenamento**, em **Criação Rápida** e, em seguida, digite um nome para sua conta de armazenamento. Selecione a região desejada e, em seguida, clique em **Criar Conta de Armazenamento**.

    ![][11]

3. Clique na conta de armazenamento criada recentemente e, em seguida, clique em **Gerenciar Chaves de Acesso**:

    ![][12]

    Copie a chave de acesso primária para usar posteriormente neste tutorial.

4. No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console**. Nomeie o projeto como **Destinatário**.

    ![][14]

5. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e clique em **Gerenciar Pacotes NuGet para Solução**.

6. Clique na guia **Procurar** e pesquise `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Verifique se o nome do projeto (**Receptor**) está especificado na caixa **Versão(ões)**. Clique em **Instalar** e aceite os termos de uso.

    ![][13]

	Isso baixa, instala e adiciona uma referência ao [Hub de Eventos do Barramento de Serviço do Azure - pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.

7. Clique com o botão direito do mouse no projeto do **Receptor**, clique em **Adicionar** e, em seguida, clique em **Classe**. Nomeie a nova classe **SimpleEventProcessor** e clique em **Adicionar** para criar a classe.

	![][15]

8. Adicione as seguintes instruções na parte superior do arquivo SimpleEventProcessor.cs file:

	```
	using Microsoft.ServiceBus.Messaging;
	using System.Diagnostics;
	```

	Em seguida, substitua o seguinte código no corpo da classe:

	```
    class SimpleEventProcessor : IEventProcessor
	{
	    Stopwatch checkpointStopWatch;

	    async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	    {
	        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	        if (reason == CloseReason.Shutdown)
	        {
	            await context.CheckpointAsync();
	        }
	    }

	    Task IEventProcessor.OpenAsync(PartitionContext context)
	    {
	        Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	        this.checkpointStopWatch = new Stopwatch();
	        this.checkpointStopWatch.Start();
	        return Task.FromResult<object>(null);
	    }

	    async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	    {
	        foreach (EventData eventData in messages)
	        {
	            string data = Encoding.UTF8.GetString(eventData.GetBytes());

	            Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                context.Lease.PartitionId, data));
	        }

	        //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	        if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
	    }
	}
    ```

	Essa classe será chamada pelo **EventProcessorHost** para processar eventos recebidos do Hub de Eventos. Observe que a classe `SimpleEventProcessor` usa um cronômetro para chamar o método de ponto de verificação periodicamente no contexto do **EventProcessorHost**. Isso garante que, se o destinatário for reiniciado, ele perderá, no máximo, cinco minutos de trabalho de processamento.

9. Na classe **Program**, adicione a seguinte instrução `using` na parte superior do arquivo:

	```
	using Microsoft.ServiceBus.Messaging;
	```

	Em seguida, substitua o método `Main` na classe `Program` pelo código a seguir, substituindo o nome do Hub de Eventos e a cadeia de conexão no nível de namespace que você salvou anteriormente, e a conta de armazenamento e a chave que você copiou nas seções anteriores.

    ```
	static void Main(string[] args)
    {
      string eventHubConnectionString = "{Event Hub connection string}";
      string eventHubName = "{Event Hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      var options = new EventProcessorOptions();
      options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
	```

> [AZURE.NOTE] Este tutorial usa uma única instância do [EventProcessorHost][]. Para aumentar a taxa de transferência, é recomendável que você execute várias instâncias do [EventProcessorHost][], conforme mostrado no exemplo de [Processamento de eventos em escala][]. Nesses casos, as diversas instâncias são coordenadas automaticamente umas com as outras para balancear a carga de eventos recebidos. Se você quiser que vários destinatários processem, cada um, *todos* os eventos, você deve usar o conceito **ConsumerGroup**. Ao receber eventos de máquinas diferentes, pode ser útil especificar nomes para instâncias de [EventProcessorHost][] com base em máquinas (ou funções) nas quais eles foram implantados. Para saber mais sobre esses tópicos, confira a [Visão geral de Hubs de Eventos][] e o [Guia de Programação de Hubs de Eventos][].

<!-- Links -->
[Visão geral de Hubs de Eventos]: event-hubs-overview.md
[Guia de Programação de Hubs de Eventos]: event-hubs-programming-guide.md
[Processamento de eventos em escala]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[processamento de eventos de escala horizontal]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Conta de armazenamento do Azure]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[portal clássico do Azure]: http://manage.windowsazure.com

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-receiver-csharp1.png
[15]: ./media/service-bus-event-hubs-getstarted/create-receiver-csharp2.png

