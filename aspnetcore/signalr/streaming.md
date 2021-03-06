---
title: Usar o streaming em SignalR do ASP.NET Core
author: bradygaster
description: Saiba como transmitir dados entre o cliente e o servidor.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/05/2019
uid: signalr/streaming
ms.openlocfilehash: a75156f398e113393ddb891d16eec3f09de80c09
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750193"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>Usar o streaming em SignalR do ASP.NET Core

Por [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

SignalR do ASP.NET Core dá suporte a streaming do cliente ao servidor e do servidor ao cliente. Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo. Quando o streaming, cada fragmento é enviado ao cliente ou servidor assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

SignalR do ASP.NET Core dá suporte a streaming valores de retorno dos métodos de servidor. Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo. Quando um valor de retorno é transmitido ao cliente, cada fragmento é enviado ao cliente assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.

::: moniker-end

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Configurar um hub para streaming

::: moniker range=">= aspnetcore-3.0"

Um método de hub automaticamente se torna um método de hub streaming quando ele retorna <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, ou `Task<ChannelReader<T>>`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Um método de hub automaticamente se torna um método de hub streaming quando ele retorna um <xref:System.Threading.Channels.ChannelReader%601> ou um `Task<ChannelReader<T>>`.

::: moniker-end

### <a name="server-to-client-streaming"></a>Streaming Server-para-cliente

::: moniker range=">= aspnetcore-3.0"

Métodos de hub de streaming podem retornar `IAsyncEnumerable<T>` além `ChannelReader<T>`. A maneira mais simples de retornar `IAsyncEnumerable<T>` é, tornando o método de hub em um método de iterador assíncrono como o exemplo a seguir demonstra. Métodos de iterador assíncrono hub podem aceitar um `CancellationToken` parâmetro que é disparado quando o cliente cancela a assinatura do fluxo. Métodos de iterador assíncrono evitar problemas comuns com canais, como não retornar os `ChannelReader` cedo suficiente ou saindo do método sem concluir o <xref:System.Threading.Channels.ChannelWriter`1>.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

O exemplo a seguir mostra os conceitos básicos do fluxo de dados para o cliente usando canais. Sempre que um objeto é gravado para o <xref:System.Threading.Channels.ChannelWriter%601>, o objeto imediatamente é enviado ao cliente. No final, o `ChannelWriter` estiver concluído para dizer ao cliente o fluxo está fechado.

> [!NOTE]
> Gravar o `ChannelWriter<T>` em um thread em segundo plano e retorne o `ChannelReader` assim que possível. Outras chamadas de hub são bloqueadas até que um `ChannelReader` é retornado.
>
> Encapsular a lógica em um `try ... catch`. Conclua o `Channel` no `catch` quanto fora o `catch` para garantir que o hub de invocação de método é concluída corretamente.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Métodos de hub do streaming Server para o cliente podem aceitar um `CancellationToken` parâmetro que é disparado quando o cliente cancela a assinatura do fluxo. Use esse token para interromper a operação do servidor e liberar quaisquer recursos se o cliente se desconecta antes do final do fluxo.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Cliente-servidor de streaming

Um método de hub automaticamente se torna um método de hub de streaming de cliente-servidor quando ele aceita um ou mais objetos do tipo <xref:System.Threading.Channels.ChannelReader%601> ou <xref:System.Collections.Generic.IAsyncEnumerable%601>. O exemplo a seguir mostra as Noções básicas de leitura de dados de streaming enviados do cliente. Sempre que o cliente grava os <xref:System.Threading.Channels.ChannelWriter%601>, os dados são gravados para o `ChannelReader` no servidor do qual o método de hub está lendo.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

Um <xref:System.Collections.Generic.IAsyncEnumerable%601> segue a versão do método.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<Stream> stream) 
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a>Cliente .NET

### <a name="server-to-client-streaming"></a>Streaming Server-para-cliente


::: moniker range=">= aspnetcore-3.0"

O `StreamAsync` e `StreamAsChannelAsync` métodos em `HubConnection` são usados para invocar métodos de streaming do servidor para cliente. Passe o nome do método de hub e argumentos definidos no método de hub para `StreamAsync` ou `StreamAsChannelAsync`. O parâmetro genérico na `StreamAsync<T>` e `StreamAsChannelAsync<T>` Especifica o tipo de objetos retornados pelo método de transmissão. Um objeto do tipo `IAsyncEnumerable<T>` ou `ChannelReader<T>` é retornada da invocação de fluxo e representa o fluxo no cliente.

Um `StreamAsync` exemplo que retorna `IAsyncEnumerable<int>`:

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

Um correspondente `StreamAsChannelAsync` exemplo que retorna `ChannelReader<int>`:

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

O `StreamAsChannelAsync` método no `HubConnection` é usado para invocar um método de transmissão de servidor para cliente. Passe o nome do método de hub e argumentos definidos no método de hub para `StreamAsChannelAsync`. O parâmetro genérico em `StreamAsChannelAsync<T>` Especifica o tipo de objetos retornados pelo método de transmissão. Um `ChannelReader<T>` é retornada da invocação de fluxo e representa o fluxo no cliente.

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

O `StreamAsChannelAsync` método no `HubConnection` é usado para invocar um método de transmissão de servidor para cliente. Passe o nome do método de hub e argumentos definidos no método de hub para `StreamAsChannelAsync`. O parâmetro genérico em `StreamAsChannelAsync<T>` Especifica o tipo de objetos retornados pelo método de transmissão. Um `ChannelReader<T>` é retornada da invocação de fluxo e representa o fluxo no cliente.

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Cliente-servidor de streaming

Há duas maneiras para invocar um método de hub de streaming de cliente-servidor do cliente .NET. Você pode passe uma `IAsyncEnumerable<T>` ou um `ChannelReader` como um argumento para `SendAsync`, `InvokeAsync`, ou `StreamAsChannelAsync`, dependendo do método de hub invocado.

Sempre que os dados são gravados para o `IAsyncEnumerable` ou `ChannelWriter` do objeto, o método de hub no servidor recebe um novo item com os dados do cliente.

Se usando um `IAsyncEnumerable` do objeto, o fluxo termina após o método retornar saídas de itens de fluxo.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Ou, se você estiver usando um `ChannelWriter`, você conclui o canal com `channel.Writer.Complete()`:

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>Cliente JavaScript

### <a name="server-to-client-streaming"></a>Streaming Server-para-cliente

Os clientes JavaScript chamem métodos de streaming do servidor-para-cliente em hubs com `connection.stream`. O `stream` método aceita dois argumentos:

* O nome do método de hub. No exemplo a seguir, o nome do método de hub é `Counter`.
* Argumentos definidos no método de hub. No exemplo a seguir, os argumentos são uma contagem do número de itens de fluxo para receber e o atraso entre itens de fluxo.

`connection.stream` Retorna um `IStreamResult`, que contém um `subscribe` método. Passar uma `IStreamSubscriber` para `subscribe` e defina as `next`, `error`, e `complete` retornos de chamada para receber notificações do `stream` invocação.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Para terminar o fluxo do cliente, chame o `dispose` método em de `ISubscription` que é retornado do `subscribe` método. Chamar esse método faz com que o cancelamento do `CancellationToken` parâmetro do método de Hub, se você tiver fornecido um.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Para terminar o fluxo do cliente, chame o `dispose` método em de `ISubscription` que é retornado do `subscribe` método.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Cliente-servidor de streaming

Os clientes JavaScript chamar métodos de streaming de cliente-servidor em hubs, passando um `Subject` como um argumento para `send`, `invoke`, ou `stream`, dependendo do método de hub invocado. O `Subject` é uma classe que se parece com um `Subject`. Por exemplo no RxJS, você pode usar o [assunto](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) classe na biblioteca.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

Chamar `subject.next(item)` com um item grava o item para o fluxo e o método de hub recebe o item no servidor.

Para terminar o fluxo, chame `subject.complete()`.

## <a name="java-client"></a>Cliente Java

### <a name="server-to-client-streaming"></a>Streaming Server-para-cliente

O cliente SignalR Java usa o `stream` método para invocar métodos de streaming. `stream` aceita três ou mais argumentos:

* O tipo esperado de itens de fluxo.
* O nome do método de hub.
* Argumentos definidos no método de hub.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

O `stream` método no `HubConnection` retorna um observável do tipo de item de fluxo. O tipo observável `subscribe` método é onde `onNext`, `onError` e `onCompleted` manipuladores são definidos.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Hubs](xref:signalr/hubs)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
