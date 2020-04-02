---
title: gRPC
description: gRPC、クラウドネイティブアプリケーションにおけるその役割、および HTTP RESTful 通信との違いについて説明します。
author: robvet
ms.date: 03/31/2020
ms.openlocfilehash: 28a07ad5ec105d3fc5b65e4cf0ac0cd85eb16627
ms.sourcegitcommit: 79b0dd8bfc63f33a02137121dd23475887ecefda
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80524209"
---
# <a name="grpc"></a>gRPC

[!INCLUDE [book-preview](../../../includes/book-preview.md)]

ここまでの本では[、REST ベース](https://docs.microsoft.com/azure/architecture/best-practices/api-design)のコミュニケーションに焦点を当てていました。 REST は、エンティティ リソースに対する CRUD ベースの操作を定義する柔軟なアーキテクチャ スタイルであることを確認しました。 クライアントは、要求/応答通信モデルを使用して、HTTP 全体のリソースと対話します。 REST は広く実装されていますが、新しい通信技術 gRPC はクラウドネイティブ コミュニティ全体で大きな勢いを増しています。

## <a name="what-is-grpc"></a>gRPCとは何ですか?

gRPC は、古い[リモート プロシージャ コール (RPC)](https://en.wikipedia.org/wiki/Remote_procedure_call)プロトコルを進化させる最新の高性能フレームワークです。 gRPC は、アプリケーション レベルでは、クライアントとバックエンド サービス間のメッセージングを合理化します。 Googleから生まれたgRPCはオープンソースであり、[クラウドネイティブ製品のクラウドネイティブコンピューティング財団(CNCF)](https://www.cncf.io/)エコシステムの一部です。 CNCF は gRPC を[インキュベート プロジェクトと見なします](https://github.com/cncf/toc/blob/master/process/graduation_criteria.adoc)。 インキュベートとは、エンドユーザーがプロダクションアプリケーションで技術を使用していることを意味し、プロジェクトには健全な貢献者が多数あります。

一般的な gRPC クライアント アプリでは、ビジネス操作を実装するローカルのインプロセス関数が公開されます。 そのローカル関数は、リモートマシン上の別の関数を呼び出します。 ローカル呼び出しと思われるものは、基本的にリモート サービスへの透過的なアウトプロセス呼び出しになります。 RPC 配管は、コンピュータ間のポイント ツー ポイント ネットワーク通信、シリアル化、および実行を抽象化します。

クラウド ネイティブ アプリケーションでは、開発者はプログラミング言語、フレームワーク、テクノロジ間で作業することがよくあります。 この*相互運用性*により、メッセージ コントラクトと、クロスプラットフォーム通信に必要な機能が複雑になります。  gRPC は、これらの問題を抽象化する「均一水平層」を提供します。 開発者は、ビジネス機能に焦点を当てたネイティブ プラットフォームでコードを記述し、gRPC は通信のプラミングを処理します。

gRPC は、Java、JavaScript、C#、Go、Swift、NodeJS など、最も一般的な開発スタック全体で包括的なサポートを提供します。

## <a name="grpc-benefits"></a>gRPC の利点

gRPC は、そのトランスポート プロトコルに HTTP/2 を使用します。 HTTP 1.1 との互換性はありますが、HTTP/2 には多くの高度な機能があります。

- データ転送用のバイナリ プロトコル - データをクリア テキストとして送信する HTTP 1.1 とは異なり。
- 同じ接続で複数の並列要求を送信するための多重化サポート - HTTP 1.1 は、一度に 1 つの要求/応答メッセージに処理を制限します。
- クライアント要求とサーバー応答の両方を同時に送信するための双方向の全二重通信。
- 組み込みのストリーミングにより、大きなデータセットを非同期的にストリーミングする要求と応答を可能にします。

gRPCは軽量で高パフォーマンスです。 メッセージが 60 ~ 80% 小さい JSON シリアル化よりも最大 8 倍高速になる可能性があります。 マイクロソフト[の Windows 通信財団 (WCF) では](https://docs.microsoft.com/dotnet/framework/wcf/whats-wcf)、gRPC のパフォーマンスは、高度に最適化された[NetTCP バインディング](https://docs.microsoft.com/dotnet/api/system.servicemodel.nettcpbinding?view=netframework-4.8)の速度と効率を超えています。 NetTCP とは異なり、 マイクロソフトスタックを支持する、 gRPC はクロスプラットフォームです。

## <a name="protocol-buffers"></a>プロトコル バッファー

gRPC はプロトコル バッファと呼ばれるオープンソース技術[を採用しています](https://developers.google.com/protocol-buffers/docs/overview)。 サービスが相互に送信する構造化されたメッセージをシリアル化するための、効率性とプラットフォームに依存しないシリアル化形式を提供します。 クロスプラットフォーム インターフェイス定義言語 (IDL) を使用して、開発者は各マイクロサービスのサービス コントラクトを定義します。 テキスト ベース`.proto`のファイルとして実装されたコントラクトは、各サービスのメソッド、入力、および出力を記述します。 同じコントラクト ファイルを、異なる開発プラットフォームで構築された gRPC クライアントとサービスに使用できます。

Protobuf コンパイラは、proto ファイルを`protoc`使用して、ターゲット プラットフォームのクライアントコードとサービスコードの両方を生成します。 コードには、次のコンポーネントが含まれています。

- クライアントとサービスによって共有される、メッセージのサービス操作とデータ要素を表す厳密に型指定されたオブジェクト。
- リモート gRPC サービスが継承および拡張できる、必要なネットワーク配管を持つ厳密に型指定された基本クラス。
- リモート gRPC サービスを呼び出すために必要な配管を含むクライアント スタブ。

実行時に、各メッセージは標準の Protobuf 表現としてシリアル化され、クライアントとリモート サービスの間で交換されます。 JSON や XML とは異なり、Protobuf メッセージはコンパイル済みバイナリ バイトとしてシリアル化されます。

マイクロソフト アーキテクチャ サイトから入手できる[、WCF 開発者向けの gRPC](https://docs.microsoft.com/dotnet/architecture/grpc-for-wcf-developers/)のマニュアルでは、gRPC とプロトコル バッファーの詳細な説明を提供します。

## <a name="grpc-support-in-net"></a>NET での gRPC サポート

gRPC は .NET Core 3.0 SDK 以降に統合されています。 次のツールは、それをサポートしています。

- Web 開発ワークロードがインストールされた Visual Studio 2019、バージョン 16.3 以降。
- Visual Studio Code
- ドットネット CLI

SDK には、エンドポイントルーティング、組み込み IoC、およびログ記録用のツールが含まれています。 オープンソースの Kestrel Web サーバーは、HTTP/2 接続をサポートしています。 図 4-20 は、gRPC サービスのスケルトン プロジェクトをスキャフォールディングする Visual Studio 2019 テンプレートを示しています。 NET Core が Windows、Linux、および macOS を完全にサポートしていることに注意してください。

![ビジュアル スタジオ 2019 での gRPC サポート](./media/visual-studio-2019-grpc-template.png)

**図 4-20** ビジュアル スタジオ 2019 での gRPC のサポート
  
図 4-21 は、組み込みのスキャフォールディングから生成されたスケルトン gRPC サービスを示しています。  

![ビジュアル スタジオ 2019 で gRPC プロジェクト](./media/grpc-project.png  )

**図 4-21** ビジュアル スタジオ 2019 で gRPC プロジェクト

前の図では、proto 記述ファイルとサービスコードをメモします。 すぐにわかるように、Visual Studio はスタートアップ クラスと基になるプロジェクト ファイルの両方で追加の構成を生成します。

## <a name="grpc-usage"></a>gRPC の使用

次のシナリオでは gRPC を優先します。

- 処理を続行するために即時応答が必要な同期バックエンドマイクロサービス間の通信。
- 混合プログラミング プラットフォームをサポートする必要があるポリグロット環境。
- パフォーマンスが重要な低遅延および高スループット通信。
- ポイントツーポイントリアルタイム通信 - gRPCはポーリングなしでリアルタイムでメッセージをプッシュすることができ、双方向ストリーミングに優れたサポートを提供します。
- ネットワークに制約のある環境 – バイナリ gRPC メッセージは、常に同等のテキストベースの JSON メッセージよりも小さくなります。

この時点で、gRPC は主にバックエンド サービスで使用されます。 最近のブラウザでは、フロントエンド gRPC クライアントをサポートするために必要な HTTP/2 コントロールのレベルを提供できません。 しかし、JavaScriptまたはBlazor WebAssembly技術を使用して構築されたブラウザベースのアプリからのgRPC通信を可能にする[初期のイニシアチブ](https://devblogs.microsoft.com/aspnet/grpc-web-experiment/)があります。 [net 用 gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md)を使用すると、ASP.NETコア gRPC アプリがブラウザー アプリで gRPC 機能をサポートできるようになります。

- 厳密に型指定されたコード生成クライアント
- コンパクトなプロトブーフメッセージ
- サーバー ストリーミング

## <a name="grpc-implementation"></a>gRPC の実装

マイクロソフトのマイクロサービス参照アーキテクチャ[であるコンテナーの eShop](https://github.com/dotnet-architecture/eShopOnContainers)は、.NET Core アプリケーションで gRPC サービスを実装する方法を示しています。 図 4-22 に、バックエンド アーキテクチャを示します。

![コンテナ上の eShop のバックエンド アーキテクチャ](./media/eshop-with-aggregators.png)

**図 4-22** コンテナ上の eShop のバックエンド アーキテクチャ

前の図では、複数の API ゲートウェイを公開することで、フロントエンド[パターン](https://docs.microsoft.com/azure/architecture/patterns/backends-for-frontends)(BFF) を eShop がどのように採用しているかを確認します。 この章では、BFF パターンについて前に説明しました。 Web ショッピング API ゲートウェイとバックエンド ショッピング マイクロサービスの間にあるアグリゲーター マイクロサービス (グレー) に細心の注意を払います。 Aggregator は、クライアントから 1 つの要求を受け取り、それをさまざまなマイクロサービスにディスパッチし、結果を集約して、要求元のクライアントに返送します。 このような操作では、通常、即時応答を生成するために同期通信が必要です。 eShop では、図 4-23 に示すように、アグリゲータからのバックエンド呼び出しは gRPC を使用して実行されます。

![コンテナ上の e ショップで gRPC](./media/grpc-implementation.png)

**図 4-23**.  コンテナ上の e ショップで gRPC

gRPC 通信には、クライアントコンポーネントとサーバーコンポーネントの両方が必要です。 前の図では、ショッピング アグリゲータが gRPC クライアントを実装する方法に注意してください。 クライアントは、バックエンド マイクロサービスに対して同期 gRPC 呼び出し (赤色) を行い、それぞれが gRPC サーバーを実装します。 クライアントとサーバーの両方が .NET Core 3.0 SDK の組み込みの gRPC の組み込みを利用します。 クライアント側*スタブは、* リモート gRPC 呼び出しを呼び出すための配管を提供します。 サーバー側コンポーネントは、カスタム サービス クラスが継承および使用できる gRPC 配管を提供します。

RESTful API と gRPC 通信の両方を公開するマイクロサービスでは、トラフィックを管理するために複数のエンドポイントが必要です。 RESTful 呼び出しの HTTP トラフィックをリッスンするエンドポイントと、gRPC 呼び出し用のエンドポイントを開きます。 gRPC エンドポイントは、gRPC 通信に必要な HTTP/2 プロトコル用に構成する必要があります。

非同期通信パターンを使用してマイクロサービスを分離しようと努力していますが、一部の操作では直接呼び出しが必要です。 マイクロサービス間の直接同期通信の主な選択肢は gRPC です。 HTTP/2およびプロトコルバッファに基づく高性能通信プロトコルは、完璧な選択になります。

## <a name="looking-ahead"></a>将来を見据えて

今後も、gRPCはクラウドネイティブシステムの牽引力を獲得していきます。 パフォーマンス上の利点と開発の容易さは、非常に魅力的です。 ただし、REST は長い間使用される可能性があります。 公開されている API と下位互換性の理由で優れています。

>[!div class="step-by-step"]
>[前次](service-to-service-communication.md)
>[Next](service-mesh-communication-infrastructure.md)