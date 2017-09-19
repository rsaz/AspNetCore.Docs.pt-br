---
title: "Partes do aplicativo no núcleo do ASP.NET"
author: ardalis
description: "Saiba como usar partes do aplicativo, que são abstrations sobre os recursos de um aplicativo, para configurar seu aplicativo para descobrir ou evitar o carregamento de recursos de um assembly."
keywords: ASP.NET Core, parte do aplicativo, parte do aplicativo
ms.author: riande
manager: wpickett
ms.date: 01/04/2017
ms.topic: article
ms.assetid: b355a48e-a15c-4d58-b69c-899963613a98
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 77d3a58d58493bf1b0b760ab9037d2778ba23441
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2017
---
# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="694a9-104">Partes do aplicativo no núcleo do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="694a9-104">Application Parts in ASP.NET Core</span></span>

[<span data-ttu-id="694a9-105">Exibir ou baixar o código de exemplo</span><span class="sxs-lookup"><span data-stu-id="694a9-105">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample)

<span data-ttu-id="694a9-106">Um *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo, da qual MVC recursos, como controladores, componentes do modo de exibição, ou podem ser descobertos auxiliares de marcação.</span><span class="sxs-lookup"><span data-stu-id="694a9-106">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="694a9-107">Um exemplo de uma parte do aplicativo é um AssemblyPart, que encapsula uma referência de assembly e expõe tipos e referências de compilação.</span><span class="sxs-lookup"><span data-stu-id="694a9-107">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="694a9-108">*Recurso provedores* funcionam com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET MVC de núcleo.</span><span class="sxs-lookup"><span data-stu-id="694a9-108">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="694a9-109">É o caso de uso principal para partes do aplicativo permitir que você configure seu aplicativo para descobrir (ou evitar o carregamento) recursos MVC de um assembly.</span><span class="sxs-lookup"><span data-stu-id="694a9-109">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="694a9-110">Apresentando as partes do aplicativo</span><span class="sxs-lookup"><span data-stu-id="694a9-110">Introducing Application Parts</span></span>

<span data-ttu-id="694a9-111">Aplicativos MVC carregar seus recursos de [partes do aplicativo](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span><span class="sxs-lookup"><span data-stu-id="694a9-111">MVC apps load their features from [application parts](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="694a9-112">Em particular, o [AssemblyPart](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) classe representa uma parte do aplicativo que é apoiada por um assembly.</span><span class="sxs-lookup"><span data-stu-id="694a9-112">In particular, the [AssemblyPart](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) class represents an application part that is backed by an assembly.</span></span> <span data-ttu-id="694a9-113">Você pode usar essas classes para descobrir e carregar recursos MVC, como controladores, componentes do modo de exibição, auxiliares de marcação e fontes de compilação do razor.</span><span class="sxs-lookup"><span data-stu-id="694a9-113">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="694a9-114">O [ApplicationPartManager](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) é responsável por controlar as partes do aplicativo e os provedores de recursos disponíveis para o aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="694a9-114">The [ApplicationPartManager](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="694a9-115">Você pode interagir com o `ApplicationPartManager` em `Startup` quando você configura o MVC:</span><span class="sxs-lookup"><span data-stu-id="694a9-115">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => p.ApplicationParts.Add(part));
```

<span data-ttu-id="694a9-116">Por padrão o MVC pesquisar a árvore de dependência e localizar controladores (mesmo em outros assemblies).</span><span class="sxs-lookup"><span data-stu-id="694a9-116">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="694a9-117">Para carregar um assembly arbitrário (por exemplo, a partir de um plug-in que não é referenciado em tempo de compilação), você pode usar uma parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="694a9-117">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="694a9-118">Você pode usar as partes do aplicativo para *evitar* procurando controladores em um determinado assembly ou local.</span><span class="sxs-lookup"><span data-stu-id="694a9-118">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="694a9-119">Você pode controlar quais partes (ou assemblies) estão disponíveis para o aplicativo modificando o `ApplicationParts` coleção do `ApplicationPartManager`.</span><span class="sxs-lookup"><span data-stu-id="694a9-119">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="694a9-120">A ordem das entradas na `ApplicationParts` coleção não é importante.</span><span class="sxs-lookup"><span data-stu-id="694a9-120">The order of the entries in the `ApplicationParts` collection is not important.</span></span> <span data-ttu-id="694a9-121">É importante configurar totalmente o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner.</span><span class="sxs-lookup"><span data-stu-id="694a9-121">It is important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="694a9-122">Por exemplo, você deve configurar totalmente o `ApplicationPartManager` antes de chamar `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="694a9-122">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="694a9-123">Falha ao fazer isso, significa que os controladores em partes do aplicativo adicionado depois que a chamada de método não será afetada (não irá obter registrados como serviços) que pode resultar em bevavior incorreta do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="694a9-123">Failing to do so, will mean that controllers in application parts added after that method call will not be affected (will not get registered as services) which might result in incorrect bevavior of your application.</span></span>

<span data-ttu-id="694a9-124">Se você tiver um assembly que contém os controladores que você não deseja usar, removê-lo do `ApplicationPartManager`:</span><span class="sxs-lookup"><span data-stu-id="694a9-124">If you have an assembly that contains controllers you do not want to be used, remove it from the `ApplicationPartManager`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p =>
    {
        var dependentLibrary = p.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           p.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

<span data-ttu-id="694a9-125">Além de assembly do projeto e seus assemblies dependentes, o `ApplicationPartManager` incluirá partes para `Microsoft.AspNetCore.Mvc.TagHelpers` e `Microsoft.AspNetCore.Mvc.Razor` por padrão.</span><span class="sxs-lookup"><span data-stu-id="694a9-125">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="694a9-126">Provedores de recurso do aplicativo</span><span class="sxs-lookup"><span data-stu-id="694a9-126">Application Feature Providers</span></span>

<span data-ttu-id="694a9-127">Provedores de recurso de aplicativo examinar partes do aplicativo e fornece recursos para as partes.</span><span class="sxs-lookup"><span data-stu-id="694a9-127">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="694a9-128">Há provedores de recurso interno para os seguintes recursos MVC:</span><span class="sxs-lookup"><span data-stu-id="694a9-128">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="694a9-129">Controladores</span><span class="sxs-lookup"><span data-stu-id="694a9-129">Controllers</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="694a9-130">Referência de metadados</span><span class="sxs-lookup"><span data-stu-id="694a9-130">Metadata Reference</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="694a9-131">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="694a9-131">Tag Helpers</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="694a9-132">Componentes do modo de exibição</span><span class="sxs-lookup"><span data-stu-id="694a9-132">View Components</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="694a9-133">Recurso provedores herdam `IApplicationFeatureProvider<T>`, onde `T` é o tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="694a9-133">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="694a9-134">Você pode implementar seu próprio recurso provedores para qualquer um dos tipos de recurso do MVC listados acima.</span><span class="sxs-lookup"><span data-stu-id="694a9-134">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="694a9-135">A ordem dos provedores de recurso no `ApplicationPartManager.FeatureProviders` coleção pode ser importante, pois provedores posteriores possam reagir às ações tomadas pelo provedores anteriores.</span><span class="sxs-lookup"><span data-stu-id="694a9-135">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="694a9-136">Exemplo: Recurso do controlador genérico</span><span class="sxs-lookup"><span data-stu-id="694a9-136">Sample: Generic controller feature</span></span>

<span data-ttu-id="694a9-137">Por padrão, o ASP.NET Core MVC ignora controladores genéricos (por exemplo, `SomeController<T>`).</span><span class="sxs-lookup"><span data-stu-id="694a9-137">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="694a9-138">Este exemplo usa um provedor de recursos do controlador que é executado depois que o provedor padrão e adiciona instâncias de controlador genérico para uma lista especificada de tipos (definido em `EntityTypes.Types`):</span><span class="sxs-lookup"><span data-stu-id="694a9-138">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

<span data-ttu-id="694a9-139">Os tipos de entidade:</span><span class="sxs-lookup"><span data-stu-id="694a9-139">The entity types:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

<span data-ttu-id="694a9-140">O provedor de recursos é adicionado no `Startup`:</span><span class="sxs-lookup"><span data-stu-id="694a9-140">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p => 
        p.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="694a9-141">Por padrão, os nomes de controlador genérico usados para roteamento seriam do formulário *GenericController'1 [Widget]* em vez de *Widget*.</span><span class="sxs-lookup"><span data-stu-id="694a9-141">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="694a9-142">O seguinte atributo é usado para modificar o nome que corresponde ao tipo genérico usado pelo controlador:</span><span class="sxs-lookup"><span data-stu-id="694a9-142">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="694a9-143">O `GenericController` classe:</span><span class="sxs-lookup"><span data-stu-id="694a9-143">The `GenericController` class:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

<span data-ttu-id="694a9-144">O resultado, quando uma rota correspondente é solicitada:</span><span class="sxs-lookup"><span data-stu-id="694a9-144">The result, when a matching route is requested:</span></span>

![Exemplo de saída de amostra de aplicativo lê, 'Hello de um controlador de Sproket genérico'.](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="694a9-146">Exemplo: Recursos disponíveis de exibição</span><span class="sxs-lookup"><span data-stu-id="694a9-146">Sample: Display available features</span></span>

<span data-ttu-id="694a9-147">Você pode iterar por meio dos recursos preenchidos disponíveis para seu aplicativo solicitando uma `ApplicationPartManager` por meio de [injeção de dependência](../../fundamentals/dependency-injection.md) e usá-lo para preencher as instâncias dos recursos apropriados:</span><span class="sxs-lookup"><span data-stu-id="694a9-147">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="694a9-148">Exemplo de saída:</span><span class="sxs-lookup"><span data-stu-id="694a9-148">Example output:</span></span>

![Exemplo de saída de amostra de aplicativo](app-parts/_static/available-features.png)