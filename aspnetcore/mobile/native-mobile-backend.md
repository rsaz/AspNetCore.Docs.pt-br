---
title: "Criando serviços de back-end para aplicativos móveis nativos"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3b6a32f2-5af9-4ede-9b7f-17ab300526d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: mobile/native-mobile-backend
ms.openlocfilehash: 7b2b35b1519badbc8b8fbddab97a0c6336926185
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2017
---
# <a name="creating-backend-services-for-native-mobile-applications"></a><span data-ttu-id="a1ff9-103">Criando serviços de back-end para aplicativos móveis nativos</span><span class="sxs-lookup"><span data-stu-id="a1ff9-103">Creating Backend Services for Native Mobile Applications</span></span>

<span data-ttu-id="a1ff9-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a1ff9-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a1ff9-105">Aplicativos móveis facilmente podem se comunicar com serviços de back-end do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-105">Mobile apps can easily communicate with ASP.NET Core backend services.</span></span>

[<span data-ttu-id="a1ff9-106">Exibir ou baixar o exemplo de código de serviços de back-end</span><span class="sxs-lookup"><span data-stu-id="a1ff9-106">View or download sample backend services code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="a1ff9-107">O aplicativo móvel nativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="a1ff9-107">The Sample Native Mobile App</span></span>

<span data-ttu-id="a1ff9-108">Este tutorial demonstra como criar serviços de back-end usando o ASP.NET MVC de núcleo para dar suporte a aplicativos móveis nativo.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-108">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="a1ff9-109">Ele usa o [aplicativo Xamarin Forms ToDoRest](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/) como seu cliente nativo, que inclui clientes nativos separados para dispositivos Android, iOS, Universal do Windows e Windows Phone.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-109">It uses the [Xamarin Forms ToDoRest app](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="a1ff9-110">Você pode seguir o tutorial vinculado para criar o aplicativo nativo (e instalar as ferramentas Xamarin livres necessárias), bem como baixar a solução de exemplo Xamarin.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-110">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="a1ff9-111">O exemplo de Xamarin inclui um projeto de serviços ASP.NET Web API 2, que substitui o aplicativo do ASP.NET Core deste artigo (com nenhuma alteração exigida pelo cliente).</span><span class="sxs-lookup"><span data-stu-id="a1ff9-111">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Aplicativo Do Rest em execução em um smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="a1ff9-113">Recursos</span><span class="sxs-lookup"><span data-stu-id="a1ff9-113">Features</span></span>

<span data-ttu-id="a1ff9-114">O aplicativo ToDoRest dá suporte à lista, adicionar, excluir e atualizar itens de tarefas.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-114">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="a1ff9-115">Cada item possui uma ID, um nome, anotações e uma propriedade que indica se ele está sendo feito ainda.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-115">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="a1ff9-116">O modo de exibição principal dos itens, como mostrado acima, lista o nome de cada item e indica se isso é feito com uma marca de seleção.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-116">The main view of the items, as shown above, lists each item's name and indicates if it is done with a checkmark.</span></span>

<span data-ttu-id="a1ff9-117">Tocar o `+` ícone abre uma caixa de diálogo Adicionar item:</span><span class="sxs-lookup"><span data-stu-id="a1ff9-117">Tapping the `+` icon opens an add item dialog:</span></span>

![Item de caixa de diálogo Adicionar](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="a1ff9-119">Ao tocar em um item na tela principal de lista abre uma caixa de diálogo Editar onde o nome do item, observações e configurações concluídas podem ser modificadas, ou o item pode ser excluído:</span><span class="sxs-lookup"><span data-stu-id="a1ff9-119">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Editar caixa de diálogo de item](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="a1ff9-121">Este exemplo é configurado por padrão para usar serviços de back-end hospedados em developer.xamarin.com, que permitem operações somente leitura.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-121">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="a1ff9-122">Para testá-lo por conta própria em relação o aplicativo do ASP.NET Core criado na próxima seção em execução no seu computador, você precisará atualizar o aplicativo `RestUrl` constante.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="a1ff9-123">Navegue até o `ToDoREST` do projeto e abra o *Constants.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-123">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="a1ff9-124">Substitua o `RestUrl` com uma URL que inclui o IP do seu computador, endereço (não localhost ou 127.0.0.1, desde que esse endereço é usado do emulador de dispositivo, não em seu computador).</span><span class="sxs-lookup"><span data-stu-id="a1ff9-124">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="a1ff9-125">Inclua o número da porta (5000).</span><span class="sxs-lookup"><span data-stu-id="a1ff9-125">Include the port number as well (5000).</span></span> <span data-ttu-id="a1ff9-126">Para testar se os serviços de trabalhar com um dispositivo, verifique se que você não tiver um firewall ativas bloqueando o acesso a essa porta.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-126">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "csharp"} -->

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="a1ff9-127">Criando o projeto do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1ff9-127">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="a1ff9-128">Crie um novo aplicativo de Web do ASP.NET Core no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-128">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="a1ff9-129">Escolha o modelo de API da Web e sem autenticação.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-129">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="a1ff9-130">Nomeie o projeto *ToDoApi*.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-130">Name the project *ToDoApi*.</span></span>

![Caixa de diálogo nova aplicativo Web ASP.NET com modelo de projeto de API da Web selecionado](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="a1ff9-132">O aplicativo deve responder a todas as solicitações feitas para a porta 5000.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-132">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="a1ff9-133">Atualização *Program.cs* para incluir `.UseUrls("http://*:5000")` para fazer isso:</span><span class="sxs-lookup"><span data-stu-id="a1ff9-133">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

<span data-ttu-id="a1ff9-134">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-134">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]</span></span>

> [!NOTE]
> <span data-ttu-id="a1ff9-135">Certifique-se de que executar o aplicativo diretamente, em vez de por trás do IIS Express, que ignora solicitações não local por padrão.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="a1ff9-136">Executar `dotnet run` em um prompt de comando, ou escolha o perfil de nome do aplicativo no menu suspenso de destino de depuração na barra de ferramentas do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-136">Run `dotnet run` from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="a1ff9-137">Adicione uma classe de modelo para representar itens pendentes.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="a1ff9-138">Marca necessários campos usando a `[Required]` atributo:</span><span class="sxs-lookup"><span data-stu-id="a1ff9-138">Mark required fields using the `[Required]` attribute:</span></span>

<span data-ttu-id="a1ff9-139">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-139">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]</span></span>

<span data-ttu-id="a1ff9-140">Os métodos da API exigem alguma maneira de trabalhar com dados.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="a1ff9-141">Use a mesma `IToDoRepository` interface os usos de exemplo originais do Xamarin:</span><span class="sxs-lookup"><span data-stu-id="a1ff9-141">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

<span data-ttu-id="a1ff9-142">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-142">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]</span></span>

<span data-ttu-id="a1ff9-143">Para este exemplo, a implementação apenas usa um conjunto particular de itens:</span><span class="sxs-lookup"><span data-stu-id="a1ff9-143">For this sample, the implementation just uses a private collection of items:</span></span>

<span data-ttu-id="a1ff9-144">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-144">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]</span></span>

<span data-ttu-id="a1ff9-145">Configurar a implementação em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1ff9-145">Configure the implementation in *Startup.cs*:</span></span>

<span data-ttu-id="a1ff9-146">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-146">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]</span></span>

<span data-ttu-id="a1ff9-147">Neste ponto, você está pronto para criar o *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-147">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="a1ff9-148">Saiba mais sobre como criar web APIs em [criando sua primeira API da Web com ASP.NET MVC de núcleos e do Visual Studio](../tutorials/first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="a1ff9-148">Learn more about creating web APIs in [Building Your First Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="a1ff9-149">Criando o controlador</span><span class="sxs-lookup"><span data-stu-id="a1ff9-149">Creating the Controller</span></span>

<span data-ttu-id="a1ff9-150">Adicionar um novo controlador para o projeto, *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-150">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="a1ff9-151">Ele deve herdar de Microsoft.AspNetCore.Mvc.Controller.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-151">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="a1ff9-152">Adicionar um `Route` atributo para indicar que o controlador de manipular as solicitações feitas para caminhos que começam com `api/todoitems`.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-152">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="a1ff9-153">O `[controller]` token na rota é substituído pelo nome do controlador (omitindo o `Controller` sufixo) e é especialmente útil para rotas global.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-153">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="a1ff9-154">Saiba mais sobre [roteamento](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="a1ff9-154">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="a1ff9-155">O controlador requer um `IToDoRepository` a função; solicitar uma instância desse tipo por meio do construtor do controlador.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-155">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="a1ff9-156">Em tempo de execução, esta instância será fornecida com suporte do framework [injeção de dependência](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="a1ff9-156">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

<span data-ttu-id="a1ff9-157">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-157">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]</span></span>

<span data-ttu-id="a1ff9-158">Essa API dá suporte a quatro verbos HTTP diferentes para executar operações CRUD (criação, leitura, atualização, exclusão) na fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-158">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="a1ff9-159">A mais simples delas é a operação de leitura, que corresponde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-159">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="a1ff9-160">Lendo itens</span><span class="sxs-lookup"><span data-stu-id="a1ff9-160">Reading Items</span></span>

<span data-ttu-id="a1ff9-161">Solicitar uma lista de itens é feito com uma solicitação GET para a `List` método.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-161">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="a1ff9-162">O `[HttpGet]` atributo no `List` método indica que esta ação só deve tratar as solicitações GET.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-162">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="a1ff9-163">A rota para esta ação é a rota especificada no controlador.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-163">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="a1ff9-164">Você não precisa necessariamente usar o nome da ação como parte da rota.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-164">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="a1ff9-165">Você precisa garantir que cada ação tem uma rota exclusiva e não ambígua.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-165">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="a1ff9-166">Atributos de roteamento podem ser aplicados nos níveis de método para criar rotas específico e controlador.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-166">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

<span data-ttu-id="a1ff9-167">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-167">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]</span></span>

<span data-ttu-id="a1ff9-168">O `List` método retorna um código de resposta Okey 200 e todos os itens de tarefas, serializados como JSON.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-168">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="a1ff9-169">Você pode testar o novo método de API usando uma variedade de ferramentas, como [carteiro](https://www.getpostman.com/docs/), mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="a1ff9-169">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Console carteiro mostrando uma solicitação GET para todoitems e o corpo da resposta mostrando o JSON para três itens retornados](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="a1ff9-171">Criando itens</span><span class="sxs-lookup"><span data-stu-id="a1ff9-171">Creating Items</span></span>

<span data-ttu-id="a1ff9-172">Por convenção, criando novos itens de dados é mapeado para o verbo HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-172">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="a1ff9-173">O `Create` método tem um `[HttpPost]` atributo aplicado a ele e aceita um `ToDoItem` instância.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-173">The `Create` method has an `[HttpPost]` attribute applied to it, and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="a1ff9-174">Como o `item` argumento será passado no corpo da POSTAGEM, este parâmetro é decorado com o `[FromBody]` atributo.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-174">Since the `item` argument will be passed in the body of the POST, this parameter is decorated with the `[FromBody]` attribute.</span></span>

<span data-ttu-id="a1ff9-175">Dentro do método, o item é verificado para validade e existência anterior no repositório de dados e se nenhum problema ocorrer, ele é adicionado usando o repositório.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-175">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it is added using the repository.</span></span> <span data-ttu-id="a1ff9-176">Verificando `ModelState.IsValid` executa [validação do modelo](../mvc/models/validation.md)e deve ser feito em todos os métodos de API que aceita a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-176">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

<span data-ttu-id="a1ff9-177">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-177">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]</span></span>

<span data-ttu-id="a1ff9-178">O exemplo usa uma enumeração que contém códigos de erro que são passados para o cliente móvel:</span><span class="sxs-lookup"><span data-stu-id="a1ff9-178">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

<span data-ttu-id="a1ff9-179">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-179">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]</span></span>

<span data-ttu-id="a1ff9-180">Teste a adição de novos itens usando carteiro escolhendo o verbo POST fornecendo o novo objeto no formato JSON no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-180">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="a1ff9-181">Você também deve adicionar um cabeçalho de solicitação que especifica um `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-181">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Console carteiro mostrando um POST e resposta](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="a1ff9-183">O método retorna o item recém-criado na resposta.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-183">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="a1ff9-184">Atualizando itens</span><span class="sxs-lookup"><span data-stu-id="a1ff9-184">Updating Items</span></span>

<span data-ttu-id="a1ff9-185">Modificando registros é feito usando solicitações HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-185">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="a1ff9-186">Além desta mudança, o `Edit` método é quase idêntico ao `Create`.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-186">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="a1ff9-187">Observe que, se o registro não for encontrado, o `Edit` ação irá retornar um `NotFound` resposta (404).</span><span class="sxs-lookup"><span data-stu-id="a1ff9-187">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

<span data-ttu-id="a1ff9-188">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-188">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]</span></span>

<span data-ttu-id="a1ff9-189">Para testar com carteiro, altere o verbo para PUT.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-189">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="a1ff9-190">Especifique os dados do objeto atualizado no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-190">Specify the updated object data in the Body of the request.</span></span>

![Console carteiro mostrando um PUT e resposta](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="a1ff9-192">Este método retorna um `NoContent` resposta (204) quando obtiver êxito, para manter a consistência com a API já existente.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-192">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="a1ff9-193">A exclusão de itens</span><span class="sxs-lookup"><span data-stu-id="a1ff9-193">Deleting Items</span></span>

<span data-ttu-id="a1ff9-194">Excluindo registros é feito, fazendo solicitações de exclusão para o serviço e passar a ID do item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-194">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="a1ff9-195">Como com as atualizações, solicitações de itens que não existem receberão `NotFound` respostas.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-195">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="a1ff9-196">Caso contrário, receberá uma solicitação bem-sucedida um `NoContent` resposta (204).</span><span class="sxs-lookup"><span data-stu-id="a1ff9-196">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

<span data-ttu-id="a1ff9-197">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]</span><span class="sxs-lookup"><span data-stu-id="a1ff9-197">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]</span></span>

<span data-ttu-id="a1ff9-198">Observe que ao testar a funcionalidade de exclusão, nada é necessário no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-198">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Console carteiro mostrando uma exclusão e resposta](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="a1ff9-200">Convenções de API da Web comuns</span><span class="sxs-lookup"><span data-stu-id="a1ff9-200">Common Web API Conventions</span></span>

<span data-ttu-id="a1ff9-201">À medida que desenvolve os serviços de back-end para seu aplicativo, você desejará criar um conjunto de convenções ou políticas para a manipulação resolvem preocupações consistente.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-201">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="a1ff9-202">Por exemplo, no serviço mostrado acima, as solicitações de registros específicos que não foram encontrados recebidos um `NotFound` resposta, em vez de `BadRequest` resposta.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-202">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="a1ff9-203">Da mesma forma, os comandos feitos para este serviço passados em tipos de modelo associado sempre verificados `ModelState.IsValid` e retornado um `BadRequest` para tipos de modelo inválido.</span><span class="sxs-lookup"><span data-stu-id="a1ff9-203">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="a1ff9-204">Depois de identificar uma diretiva comum para suas APIs, você geralmente pode encapsulá-lo em uma [filtro](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="a1ff9-204">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="a1ff9-205">Saiba mais sobre [como encapsular políticas comuns da API em aplicativos ASP.NET MVC de núcleo](https://msdn.microsoft.com/magazine/mt767699.aspx).</span><span class="sxs-lookup"><span data-stu-id="a1ff9-205">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](https://msdn.microsoft.com/magazine/mt767699.aspx).</span></span>