---
title: プライベート コンストラクター - C# プログラミング ガイド
description: プライベート コンストラクターは、C# では特別なインスタンス コンストラクターであり、オブジェクトの作成方法を制限するために使用されます。 これは、ファクトリ メソッド、またはその他のコンストラクション表現形式で使用できます。
ms.date: 07/20/2015
helpviewer_keywords:
- C# language, private constructors
- private constructors [C#]
ms.assetid: 29eeaa7d-8d81-453c-94b9-0e2800172621
ms.openlocfilehash: a6b86ccb870da0262bcbc516e176e00d17724f9f
ms.sourcegitcommit: 3d84eac0818099c9949035feb96bbe0346358504
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/21/2020
ms.locfileid: "86864060"
---
# <a name="private-constructors-c-programming-guide"></a>プライベート コンストラクター (C# プログラミング ガイド)
プライベート コンストラクターは、特別なインスタンス コンストラクターです。 通常は、静的メンバーだけを含むクラスで使用されます。 クラスに 1 つ以上のプライベート コンストラクターがあり、パブリック コンストラクターがない場合、他のクラス (入れ子になったクラスを除く) は、このクラスのインスタンスを作成できません。 次に例を示します。  
  
 [!code-csharp[csProgGuideObjects#11](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideObjects/CS/Objects.cs#11)]  
  
 空のコンストラクターを宣言すると、パラメーターなしのコンストラクターは自動生成されません。 コンストラクターにアクセス修飾子を指定しない場合でも、コンストラクターは既定でプライベートになります。 しかし、通常は、[プライベート](../../language-reference/keywords/private.md)修飾子を明示的に使用して、クラスをインスタンス化できないことを明確に示します。  
  
 プライベート コンストラクターは、<xref:System.Math> クラスなどのインスタンス フィールドやメソッドが存在しない場合や、クラスのインスタンスを取得するためにメソッドが呼び出される場合に、クラスのインスタンスが作成されないようにするために使用します。 クラス内のすべてのメソッドが静的な場合は、クラス全体を静的にすることを検討してください。 詳細については、「[静的クラスと静的クラス メンバー](./static-classes-and-static-class-members.md)」を参照してください。  
  
## <a name="example"></a>例  
 次に示すのは、プライベート コンストラクターを使用するクラスの例です。  
  
 [!code-csharp[csProgGuideObjects#12](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideObjects/CS/Objects.cs#12)]  
  
 この例で、次のステートメントをコメント解除すると、保護レベルのためにコンストラクターにアクセスできなくなり、エラーが発生します。  
  
 [!code-csharp[csProgGuideObjects#13](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideObjects/CS/Objects.cs#13)]  
  
## <a name="c-language-specification"></a>C# 言語仕様  

詳細については、「[C# 言語仕様](/dotnet/csharp/language-reference/language-specification/introduction)」の[プライベート コンストラクター](~/_csharplang/spec/classes.md#private-constructors)に関するセクションを参照してください。 言語仕様は、C# の構文と使用法に関する信頼性のある情報源です。
  
## <a name="see-also"></a>関連項目

- [C# プログラミング ガイド](../index.md)
- [クラスと構造体](./index.md)
- [コンストラクター](./constructors.md)
- [ファイナライザー](./destructors.md)
- [private](../../language-reference/keywords/private.md)
- [public](../../language-reference/keywords/public.md)
