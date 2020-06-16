---
title: DisposeAsync メソッドの実装
description: ''
ms.date: 06/02/2020
ms.technology: dotnet-standard
dev_langs:
- csharp
helpviewer_keywords:
- DisposeAsync method
- garbage collection, DisposeAsync method
ms.openlocfilehash: c4f541d5a4f5b5fd31b344a299789d86cd78424c
ms.sourcegitcommit: 5280b2aef60a1ed99002dba44e4b9e7f6c830604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84311000"
---
# <a name="implement-a-disposeasync-method"></a><span data-ttu-id="2b0df-102">DisposeAsync メソッドの実装</span><span class="sxs-lookup"><span data-stu-id="2b0df-102">Implement a DisposeAsync method</span></span>

<span data-ttu-id="2b0df-103"><xref:System.IAsyncDisposable?displayProperty=nameWithType> インターフェイスが、C# 8.0 の一部として導入されました。</span><span class="sxs-lookup"><span data-stu-id="2b0df-103">The <xref:System.IAsyncDisposable?displayProperty=nameWithType> interface was introduced as part of C# 8.0.</span></span> <span data-ttu-id="2b0df-104"><xref:System.IAsyncDisposable.DisposeAsync?displayProperty=nameWithType> メソッドは、[Dispose メソッドの実装](implementing-dispose.md)と同様、リソースのクリーンアップを実行する必要がある場合に実装します。</span><span class="sxs-lookup"><span data-stu-id="2b0df-104">You implement the <xref:System.IAsyncDisposable.DisposeAsync?displayProperty=nameWithType> method when you need to perform resource cleanup, just as you would when [implementing a Dispose method](implementing-dispose.md).</span></span> <span data-ttu-id="2b0df-105">両者の重要な違いの 1 つは、この実装により、非同期のクリーンアップ操作が可能になることです。</span><span class="sxs-lookup"><span data-stu-id="2b0df-105">One of the key differences however, is that this implementation allows for asynchronous cleanup operations.</span></span> <span data-ttu-id="2b0df-106"><xref:System.IAsyncDisposable.DisposeAsync> は、非同期の破棄操作を表す <xref:System.Threading.Tasks.ValueTask> を返します。</span><span class="sxs-lookup"><span data-stu-id="2b0df-106">The <xref:System.IAsyncDisposable.DisposeAsync> returns a <xref:System.Threading.Tasks.ValueTask> that represents the asynchronous dispose operation.</span></span>

<span data-ttu-id="2b0df-107">通常は、<xref:System.IAsyncDisposable> インターフェイスを実装するときに、クラスは <xref:System.IDisposable> インターフェイスも実装します。</span><span class="sxs-lookup"><span data-stu-id="2b0df-107">It is typical that when implementing the <xref:System.IAsyncDisposable> interface, classes will also implement the <xref:System.IDisposable> interface.</span></span> <span data-ttu-id="2b0df-108">推奨される <xref:System.IAsyncDisposable> インターフェイスの実装パターンは、同期の破棄か非同期の破棄のために準備をすることです。</span><span class="sxs-lookup"><span data-stu-id="2b0df-108">A good implementation pattern of the <xref:System.IAsyncDisposable> interface is to be prepared for either synchronous, or asynchronous dispose.</span></span> <span data-ttu-id="2b0df-109">破棄パターンの実装に関するガイダンスのすべての説明は、非同期の実装に適用されます。</span><span class="sxs-lookup"><span data-stu-id="2b0df-109">All of the guidance for implementing the dispose pattern applies to the asynchronous implementation.</span></span> <span data-ttu-id="2b0df-110">この記事では、読者が [Dispose メソッドの実装](implementing-dispose.md)方法について既に理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="2b0df-110">This article assumes that you're already familiar with how to [implement a Dispose method](implementing-dispose.md).</span></span>

## <a name="disposeasync-and-disposeasynccore"></a><span data-ttu-id="2b0df-111">DisposeAsync() および DisposeAsyncCore()</span><span class="sxs-lookup"><span data-stu-id="2b0df-111">DisposeAsync() and DisposeAsyncCore()</span></span>

<span data-ttu-id="2b0df-112"><xref:System.IAsyncDisposable> インターフェイスは、パラメーターなしの単一のメソッド <xref:System.IAsyncDisposable.DisposeAsync> を宣言します。</span><span class="sxs-lookup"><span data-stu-id="2b0df-112">The <xref:System.IAsyncDisposable> interface declares a single parameterless method, <xref:System.IAsyncDisposable.DisposeAsync>.</span></span> <span data-ttu-id="2b0df-113">すべての非シールド クラスには、追加の `DisposeAsyncCore()` メソッドが必要です。このメソッドも <xref:System.Threading.Tasks.ValueTask> を返します。</span><span class="sxs-lookup"><span data-stu-id="2b0df-113">Any non-sealed class should have an additional `DisposeAsyncCore()` method that also returns a <xref:System.Threading.Tasks.ValueTask>.</span></span>

- <span data-ttu-id="2b0df-114">パラメーターを持たない `public` <xref:System.IAsyncDisposable.DisposeAsync?displayProperty=nameWithType> の実装。</span><span class="sxs-lookup"><span data-stu-id="2b0df-114">A `public` <xref:System.IAsyncDisposable.DisposeAsync?displayProperty=nameWithType> implementation that has no parameters.</span></span>
- <span data-ttu-id="2b0df-115">次のシグネチャを持つ `protected virtual ValueTask DisposeAsyncCore()` メソッド。</span><span class="sxs-lookup"><span data-stu-id="2b0df-115">A `protected virtual ValueTask DisposeAsyncCore()` method whose signature is:</span></span>

```csharp
protected virtual ValueTask DisposeAsyncCore()
{
}
```

<span data-ttu-id="2b0df-116">`DisposeAsyncCore()` メソッドは `virtual` であるので、派生クラスはオーバーライドで追加のクリーンアップを定義できます。</span><span class="sxs-lookup"><span data-stu-id="2b0df-116">The `DisposeAsyncCore()` method is `virtual` so that derived classes can define additional cleanup in their overrides.</span></span>

### <a name="the-disposeasync-method"></a><span data-ttu-id="2b0df-117">DisposeAsync() メソッド</span><span class="sxs-lookup"><span data-stu-id="2b0df-117">The DisposeAsync() method</span></span>

<span data-ttu-id="2b0df-118">`public` のパラメーターなしの `DisposeAsync()` メソッドは、`await using` ステートメントで暗黙的に呼び出されます。その目的は、アンマネージド リソースを解放し、通常のクリーンアップを実行し、ファイナライザーが存在する場合、それを実行する必要がないことを示すことです。</span><span class="sxs-lookup"><span data-stu-id="2b0df-118">The `public` parameterless `DisposeAsync()` method is called implicitly in an `await using` statement, and its purpose is to free unmanaged resources, perform general cleanup, and to indicate that the finalizer, if one is present, needn't run.</span></span> <span data-ttu-id="2b0df-119">管理されたオブジェクトに関連付けられているメモリを解放するのは、常に[ガベージ コレクター](index.md)のドメインです。</span><span class="sxs-lookup"><span data-stu-id="2b0df-119">Freeing the memory associated with a managed object is always the domain of the [garbage collector](index.md).</span></span> <span data-ttu-id="2b0df-120">このため、次のような標準的な実装があります。</span><span class="sxs-lookup"><span data-stu-id="2b0df-120">Because of this, it has a standard implementation:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    // Perform async cleanup.
    await DisposeAsyncCore();

    // Dispose of managed resources.
    Dispose(false);
    // Suppress finalization.
    GC.SuppressFinalize(this);
}
```

> [!NOTE]
> <span data-ttu-id="2b0df-121">非同期の破棄パターンでの、その破棄パターンとの主な違いの 1 つは、<xref:System.IAsyncDisposable.DisposeAsync> から `Dispose(bool)` オーバーロード メソッドへの呼び出しで、`false` が引数として渡されることです。</span><span class="sxs-lookup"><span data-stu-id="2b0df-121">One primary difference in the async dispose pattern compared to the dispose pattern, is that the call from <xref:System.IAsyncDisposable.DisposeAsync> to the `Dispose(bool)` overload method is given `false` as an argument.</span></span> <span data-ttu-id="2b0df-122">一方、<xref:System.IDisposable.Dispose?displayProperty=nameWithType> メソッドを実装する場合は、`true` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="2b0df-122">When implementing the <xref:System.IDisposable.Dispose?displayProperty=nameWithType> method, however; `true` is passed instead.</span></span> <span data-ttu-id="2b0df-123">これにより、同期の破棄パターンとの機能の等価性が確保されるほか、ファイナライザーのコード パスが引き続き呼び出されるようにできます。</span><span class="sxs-lookup"><span data-stu-id="2b0df-123">This helps ensure functional equivalence with the synchronous dispose pattern, and further ensures that finalizer code paths still get invoked.</span></span> <span data-ttu-id="2b0df-124">言い換えると、`DisposeAsyncCore()` メソッドでは管理対象リソースが非同期的に破棄されるため、同期的にも破棄される必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2b0df-124">In other words, the `DisposeAsyncCore()` method will dispose of managed resources asynchronously, so you don't want to dispose of them synchronously as well.</span></span> <span data-ttu-id="2b0df-125">したがって、`Dispose(true)` ではなく `Dispose(false)` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2b0df-125">Therefore, call `Dispose(false)` instead of `Dispose(true)`.</span></span>

## <a name="implement-the-async-dispose-pattern"></a><span data-ttu-id="2b0df-126">非同期の破棄パターンの実装</span><span class="sxs-lookup"><span data-stu-id="2b0df-126">Implement the async dispose pattern</span></span>

<span data-ttu-id="2b0df-127">非シールド クラスは、継承される可能性があるため、潜在的な基底クラスと見なす必要があります。</span><span class="sxs-lookup"><span data-stu-id="2b0df-127">All non-sealed classes should be considered a potential base class, because they could be inherited.</span></span> <span data-ttu-id="2b0df-128">潜在的な基底クラスに対して非同期の破棄パターンを実装する場合、`protected virtual ValueTask DisposeAsyncCore()` メソッドを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2b0df-128">If you implement the async dispose pattern for any potential base class, you must provide the `protected virtual ValueTask DisposeAsyncCore()` method.</span></span> <span data-ttu-id="2b0df-129">次に、<xref:System.Text.Json.Utf8JsonWriter?displayProperty=nameWithType> を使用する非同期の破棄パターンの実装例を示します。</span><span class="sxs-lookup"><span data-stu-id="2b0df-129">Here is an example implementation of the async dispose pattern that uses a <xref:System.Text.Json.Utf8JsonWriter?displayProperty=nameWithType>.</span></span>

:::code language="csharp" source="../../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.asyncdisposable/disposeasync.cs":::

<span data-ttu-id="2b0df-130">上の例では、<xref:System.Text.Json.Utf8JsonWriter> を使用しています。`System.Text.Json` の詳細については、[Newtonsoft.Json から System.Text.Json への移行](../serialization/system-text-json-migrate-from-newtonsoft-how-to.md)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2b0df-130">The previous example used the <xref:System.Text.Json.Utf8JsonWriter>, for more information on `System.Text.Json`, see, [migrate from Newtonsoft.Json to System.Text.Json](../serialization/system-text-json-migrate-from-newtonsoft-how-to.md).</span></span>

## <a name="using-async-disposable"></a><span data-ttu-id="2b0df-131">非同期の破棄可能の使用</span><span class="sxs-lookup"><span data-stu-id="2b0df-131">Using async disposable</span></span>

<span data-ttu-id="2b0df-132"><xref:System.IAsyncDisposable> インターフェイスを実装するオブジェクトを適切に使用するには、[await](../../csharp/language-reference/operators/await.md) キーワードと [using](../../csharp/language-reference/keywords/using.md) キーワードを一緒に使用します。</span><span class="sxs-lookup"><span data-stu-id="2b0df-132">To properly consume an object that implements the <xref:System.IAsyncDisposable> interface, you use the [await](../../csharp/language-reference/operators/await.md), and [using](../../csharp/language-reference/keywords/using.md) keywords together.</span></span> <span data-ttu-id="2b0df-133">次の例では、`ExampleAsyncDisposable` クラスがインスタンス化され、`await using` ステートメントでラップされています。</span><span class="sxs-lookup"><span data-stu-id="2b0df-133">Consider the following example, where the `ExampleAsyncDisposable` class is instantiated, then wrapped in an `await using` statement.</span></span>

:::code language="csharp" source="../../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.asyncdisposable/proper-await-using.cs":::

> [!IMPORTANT]
> <span data-ttu-id="2b0df-134">元のコンテキストやスケジューラでタスクの継続をマーシャリングする方法を構成するには、<xref:System.IAsyncDisposable> インターフェイスの <xref:System.Threading.Tasks.TaskAsyncEnumerableExtensions.ConfigureAwait(System.IAsyncDisposable,System.Boolean)> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="2b0df-134">Use the <xref:System.Threading.Tasks.TaskAsyncEnumerableExtensions.ConfigureAwait(System.IAsyncDisposable,System.Boolean)> extension method of the <xref:System.IAsyncDisposable> interface to configure how the continuation of the task is marshalled on its original context or scheduler.</span></span> <span data-ttu-id="2b0df-135">`ConfigureAwait` の詳細については、「[ConfigureAwait に関する FAQ](https://devblogs.microsoft.com/dotnet/configureawait-faq/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2b0df-135">For more information on `ConfigureAwait`, see [ConfigureAwait FAQ](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span></span>

<span data-ttu-id="2b0df-136">`ConfigureAwait` を使用する必要がない場合、`await using` ステートメントは次のように簡略化できます。</span><span class="sxs-lookup"><span data-stu-id="2b0df-136">For situations where the usage of `ConfigureAwait` is not needed, the `await using` statement could be simplified as follows:</span></span>

:::code language="csharp" source="../../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.asyncdisposable/await-using-non-configureawait.cs":::

<span data-ttu-id="2b0df-137">さらに、[using 宣言](../../csharp/whats-new/csharp-8.md#using-declarations)の暗黙的なスコープを使用するように記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="2b0df-137">Furthermore, it could be written to use the implicit scoping of a [using declaration](../../csharp/whats-new/csharp-8.md#using-declarations).</span></span>

:::code language="csharp" source="../../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.asyncdisposable/await-using-declaration.cs":::

## <a name="stacked-usings"></a><span data-ttu-id="2b0df-138">using の積み重ね</span><span class="sxs-lookup"><span data-stu-id="2b0df-138">Stacked usings</span></span>

<span data-ttu-id="2b0df-139"><xref:System.IAsyncDisposable> を実装する複数のオブジェクトを作成して使用する場合、`using` ステートメントを誤った条件で積み重ねると、<xref:System.IAsyncDisposable.DisposeAsync> を呼び出すことができなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2b0df-139">In situations where you create and use multiple objects that implement <xref:System.IAsyncDisposable>, it's possible that stacking `using` statements in errant conditions could prevent calls to <xref:System.IAsyncDisposable.DisposeAsync>.</span></span> <span data-ttu-id="2b0df-140">潜在的な問題を防ぐため、積み重ねを避け、次の例のパターンに従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="2b0df-140">In order to help prevent potential concern, you should avoid stacking, and instead follow this example pattern:</span></span>

:::code language="csharp" source="../../../samples/snippets/csharp/VS_Snippets_CLR/conceptual.asyncdisposable/stacked-await-usings.cs":::

## <a name="see-also"></a><span data-ttu-id="2b0df-141">関連項目</span><span class="sxs-lookup"><span data-stu-id="2b0df-141">See also</span></span>

- <xref:System.IAsyncDisposable>
- <xref:System.IAsyncDisposable.DisposeAsync?displayProperty=nameWithType>
- <xref:System.Threading.Tasks.TaskAsyncEnumerableExtensions.ConfigureAwait(System.IAsyncDisposable,System.Boolean)>