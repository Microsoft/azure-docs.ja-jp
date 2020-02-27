---
title: Azure AD における属性マッピングの式の書き方
description: Azure Active Directory で SaaS アプリ オブジェクトを自動プロビジョニングしているときに、式マッピングを使用して属性値を許容される形式に変換する方法について説明します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: a01f7f48dd93983edf4be4b797f62afede273c66
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77065778"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory における属性マッピングの式の書き方
SaaS アプリケーションに対してプロビジョニングを構成するときに指定できる属性マッピングの種類の 1 つは、式マッピングです。 この場合は、ユーザーのデータを SaaS アプリケーションが許容可能な形式に変換することができる、スクリプトのような式を記述する必要があります。

## <a name="syntax-overview"></a>構文の概要
属性マッピングの式の構文は、Visual Basic のApplications (VBA) 関数に似ています。

* 式全体は、関数の形式で定義する必要があります。名前の後にかっこで囲んだ引数を続けます。 <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* 各関数内で他の関数を入れ子にすることができます。 次に例を示します。 <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* 関数には、次の 3 つの異なる種類の引数を渡すことができます。
  
  1. 属性。角かっこで囲む必要があります。 例: [attributeName]
  2. 文字列定数。二重引用符で囲む必要があります。 次に例を示します。"米国"
  3. 他の関数 次に例を示します。FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 文字列定数では、文字列に円記号 (\) または引用符 (") を含める必要がある場合は、円記号 (\) でエスケープする必要があります。 次に例を示します。"会社名:\\"Contoso\\""

## <a name="list-of-functions"></a>関数の一覧
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp;[RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Append
**関数:**<br> Append(source, suffix)

**説明:**<br> source 文字列値を受け取り、その末尾に suffix を追加します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |通常は、source オブジェクトの属性の名前。 |
| **suffix** |Required |String |source 値の末尾に追加する文字列。 |

---
### <a name="bitand"></a>BitAnd
**関数:**<br> BitAnd(value1, value2)

**説明:**<br> この関数は両方のパラメーターをバイナリ表現に変換し、ビットを次に設定します。

0 - value1 と value2 内の対応するビットの 1 つまたは両方が 0 の場合                                                  
1 - 対応するビットの両方が 1 の場合。                                    

つまり、両方のパラメーターの対応するビットが 1 の場合を除くすべてのケースで 0 を返します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value1** |Required |num |value2 と共に AND で処理する数値|
| **value2** |Required |num |value1 と共に AND で処理する数値|

**例:**<br>
BitAnd(&HF, &HF7)                                                                                
11110111 AND 00000111 = 00000111。BitAnd からは 7 が返されます。その 2 進数は 00000111 です。

---
### <a name="cbool"></a>CBool
**関数:**<br> CBool(Expression)

**説明:**<br> CBool からは、式の評価結果に基づいてブール値が返されます。 式の評価結果が 0 以外の値の場合は CBool によって True が返され、それ以外の場合は False が返されます。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **式 (expression)** |Required | expression | 任意の有効な式 |

**例:**<br>
CBool([attribute1] = [attribute2])                                                                    
両方の属性が同じ値を持つ場合は、True を返します。

---
### <a name="coalesce"></a>Coalesce
**関数:**<br> Coalesce(source1, source2, ..., defaultValue)

**説明:**<br> NULL ではない最初のソース値が返されます。 すべての引数が NULL で、defaultValue が存在する場合、defaultValue が返されます。 すべての引数が NULL で、defaultValue が存在しない場合、Coalesce からは NULL が返されます。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source1  … sourceN** | Required | String |必須、回数は可変。 通常は、source オブジェクトの属性の名前。 |
| **defaultValue** | 省略可能 | String | すべてのソース値が NULL の場合に使用される既定値。 空の文字列 ("") を指定できます。

---
### <a name="converttobase64"></a>ConvertToBase64
**関数:**<br> ConvertToBase64(source)

**説明:**<br> ConvertToBase64 関数は、文字列を Unicode の base64 文字列に変換します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |base 64 に変換される文字列|

**例:**<br>
ConvertToBase64("Hello world!")                                                                                                        
"SABlAGwAbABvACAAdwBvAHIAbABkACEA" を返します。

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**関数:**<br> ConvertToUTF8Hex(source)

**説明:**<br> ConvertToUTF8Hex 関数は、文字列を UTF8 の 16 進数でエンコードされた値に変換します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |UTF8 Hex に変換される文字列|

**例:**<br>
ConvertToUTF8Hex("Hello world!")                                                                                                         
48656C6C6F20776F726C6421 を返します。

---
### <a name="count"></a>Count
**関数:**<br> Count(attribute)

**説明:**<br> Count 関数は、複数値の属性内の要素数を返します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |Required |属性 (attribute) |要素がカウントされる複数値の属性|

---
### <a name="cstr"></a>CStr
**関数:**<br> CStr(value)

**説明:**<br> CStr 関数では、値が文字列データ型に変換されます。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value** |Required | 数値、参照、またはブール値 | 数値、参照属性、ブール値を指定できます。 |

**例:**<br>
CStr([dn])                                                            
"cn=Joe,dc=contoso,dc=com" が返されます

---
### <a name="datefromnum"></a>DateFromNum
**関数:**<br> DateFromNum(value)

**説明:**<br> DateFromNum 関数は、AD の日付形式の値を DateTime 型に変換します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value** |Required | Date | DateTime 型に変換される AD 日付 |

**例:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
2012-01-01 23:00:00 を表す DateTime を返します。

---
### <a name="formatdatetime"></a>FormatDateTime
**関数:**<br> FormatDateTime(source, inputFormat, outputFormat)

**説明:**<br> 1 つの形式の日付文字列を受け取り、別の形式に変換します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |通常は、source オブジェクトの属性の名前。 |
| **inputFormat** |Required |String |有効な形式の source 値。 サポートされる形式については、[https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) をご覧ください。 |
| **outputFormat** |Required |String |出力日付の形式。 |

---
### <a name="guid"></a>Guid
**関数:**<br> Guid()

**説明:**<br> GUID 関数は、新しいランダムな GUID を生成します。

---
### <a name="instr"></a>InStr
**関数:**<br> InStr(value1,value2,start,compareType)

**説明:**<br> InStr 関数は文字列内の最初の部分文字列を検索します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value1** |Required |String |検索対象の文字列 |
| **value2** |Required |String |検索する文字列 |
| **start** |省略可能 |整数 |部分文字列の検索を開始する位置|
| **compareType** |省略可能 |列挙型 |vbTextCompare か vbBinaryCompare になります |

**例:**<br>
InStr("The quick brown fox","quick")                                                                             
評価結果は 5 になります。

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
評価結果は 7 になります。

---
### <a name="isnull"></a>IsNull
**関数:**<br> IsNull(Expression)

**説明:**<br> 式の評価結果が Null の場合、IsNull 関数は true を返します。 属性の場合、Null は属性の不在によって表されます。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **式 (expression)** |Required |expression |評価の対象となる式 |

**例:**<br>
IsNull([displayName])                                                                                                
属性がない場合は True が返されます。

---
### <a name="isnullorempty"></a>IsNullorEmpty
**関数:**<br> IsNullOrEmpty(Expression)

**説明:**<br> 式が null または空の文字列の場合、IsNullOrEmpty 関数は true を返します。 属性の場合は、属性がないか、存在しても空の文字列の場合、評価結果は True になります。
この関数の逆の関数は IsPresent です。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **式 (expression)** |Required |expression |評価の対象となる式 |

**例:**<br>
IsNullOrEmpty([displayName])                                               
属性がないか、空の文字列の場合は True が返されます。

---
### <a name="ispresent"></a>IsPresent
**関数:**<br> IsPresent(Expression)

**説明:**<br> 式の評価結果が Null でもなく、空でもない文字列の場合、IsPresent 関数は true を返します。 この関数の逆関数は IsNullOrEmpty です。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **式 (expression)** |Required |expression |評価の対象となる式 |

**例:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString
**関数:**<br> IsString(Expression)

**説明:**<br> 式を文字列型として評価できる場合、IsString 関数の評価結果は True になります。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **式 (expression)** |Required |expression |評価の対象となる式 |

---
### <a name="item"></a>Item
**関数:**<br> Item(attribute, index)

**説明:**<br> Item 関数は複数値の文字列/属性から 1 つの項目を返します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |Required |Attribute |検索対象の複数値の属性 |
| **インデックス** |Required |整数 | 複数値の文字列内の項目のインデックス|

**例:**<br>
Item([proxyAddresses], 1)

---
### <a name="join"></a>Join
**関数:**<br> Join(separator, source1, source2, …)

**説明:**<br> Join() は Append() によく似ていますが、Join() では複数の **source** 文字列値を 1 つの文字列に結合できます。文字列値は **separator** で区切って指定します。

source 値の 1 つが複数値属性である場合は、その属性のすべての値を結合し、separator 値で区切ります。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **separator** |Required |String |source 値を 1 つの文字列に連結するときに、各値を区切るのに使用する文字列。 区切り記号が必要ない場合は、“” とすることができます。 |
| **source1  … sourceN** |必須、回数は可変 |String |結合する文字列値。 |

---
### <a name="left"></a>Left
**関数:**<br> Left(String,NumChars)

**説明:**<br> Left 関数は文字列の左端から数えて指定した文字数分の文字を返します。 numChars = 0 の場合、空の文字列を返します。
numChars < 0 の場合、入力文字列を返します。
string が null の場合、空の文字列を返します。
string に含まれる文字数が numChars で指定した数より少ない場合は、string と同一の文字列 (パラメーター 1 のすべての文字が含まれる) が返されます。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **String** |Required |Attribute | 返される文字を含む文字列 |
| **NumChars** |Required |整数 | 文字列の先頭 (左端) から取得する文字数を示す値|

**例:**<br>
Left("John Doe", 3)                                                            
"Joh" が返されます

---
### <a name="mid"></a>Mid
**関数:**<br> Mid(source, start, length)

**説明:**<br> source 値の部分文字列を返します。 部分文字列は、source 文字列の文字のみを含む文字列です。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |通常、属性の名前。 |
| **start** |Required |整数 (integer) |部分文字列が始まる **source** 文字列のインデックス。 文字列内の最初の文字のインデックスは 1、2 番目の文字のインデックスは 2です (以降同様)。 |
| **length** |Required |整数 (integer) |部分文字列の長さ。 length が **source** 文字列の外で終わる場合は、**start** インデックスから **source** 文字列の末尾までの部分文字列を返します。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**関数:**<br> NormalizeDiacritics(source)

**説明:**<br> 1 つの文字列引数が必要です。 文字列を返しますが、分音記号はそれ以外の同等の記号で置換されます。 通常、分音記号 (アクセント記号) を含む姓と名を、さまざまなユーザー識別子 (ユーザー プリンシパル名、SAM アカウント名、電子メール アドレスなど) で使用できる有効な値に変換するために使用します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String | 通常は、名または姓の属性です。 |

---
### <a name="not"></a>Not
**関数:**<br> Not(source)

**説明:**<br> **source** のブール値を反転します。 **source** 値が "*True*" の場合は "*False*" を返します。 "False" の場合は "*True*" を返します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |Boolean String |有効な **source** 値は "True" または "False" です。 |

---
### <a name="numfromdate"></a>NumFromDate
**関数:**<br> NumFromDate(value)

**説明:**<br> NumFromDate 関数は、DateTime 値を、[accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires) などの属性を設定するために必要な Active Directory 形式に変換します。 この関数を使用して、Workday や SuccessFactors などのクラウド HR アプリから受信した DateTime 値を、それと等価な AD 表現に変換します。 

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value** |Required | String | サポートされる形式の日付と時刻の文字列。 サポートされる形式については、 https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx をご覧ください。 |

**例:**<br>
* Workday の例 <br>
  *2020-12-31-08:00* 形式の Workday の *ContractEndDate* 属性を、AD の *accountExpires* フィールドにマップする場合に、この関数を使用して、ロケールに合わせてタイムゾーン オフセットを変更する方法を次に示します。 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors の例 <br>
  *M/d/yyyy hh:mm:ss tt* 形式の SuccessFactors の *endDate* 属性を、AD の *accountExpires* フィールドにマップする場合に、この関数を使用して、ロケールに合わせてタイムゾーン オフセットを変更する方法を次に示します。
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**関数:**<br> RemoveDuplicates(attribute)

**説明:**<br> RemoveDuplicates 関数は複数値の文字列を受け取り、各値が一意になるように処理します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |Required |複数値の属性 |重複が削除される複数値の属性|

**例:**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
重複する値がすべて削除された、校正済みの proxyAddress 属性が返されます

---
### <a name="replace"></a>Replace
**関数:**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**説明:**<br>
文字列内の値を置換します。 指定されたパラメーターに応じて異なる動作をします。

* **oldValue** と **replacementValue** が指定された場合:
  
  * **source** に含まれるすべての **oldValue** を **replacementValue** に置換します。
* **oldValue** と **template** が指定された場合:
  
  * **template** に含まれるすべての **OldValue** を **source** 値に置換します。
* **regexPattern** と **replacementValue** が指定された場合:

  * この関数で **regexPattern** が **source** の文字列に適用され、regex グループ名を使って **replacementValue** の文字列を作成できます。
* **regexPattern**、**regexGroupName**、**replacementValue** が指定された場合:
  
  * この関数で **regexPattern** が **source** の文字列に適用され、**regexGroupName** と一致するすべての値が **replacementValue** に置き換えられます
* **regexPattern**、**regexGroupName**、**replacementAttributeName** が指定された場合:
  
  * **source** に値が指定されていない場合は、**source** を返します。
  * **source** に値がある場合、この関数で **regexPattern** が **source** の文字列に適用され、**regexGroupName** と一致するすべての値が **replacementAttributeName** に関連付けられた値に置き換えられます

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |通常は、**source** オブジェクトの属性の名前。 |
| **oldValue** |省略可能 |String |**source** または **template** に含まれる置換前の値。 |
| **regexPattern** |省略可能 |String |**source**に含まれる置換前の値の正規表現パターン。 または、**replacementPropertyName** が使われるときは、**replacementPropertyName** から値を抽出するパターン。 |
| **regexGroupName** |省略可能 |String |**regexPattern**内のグループの名前。 **replacementPropertyName** を使用した場合にのみ、このグループの値が **replacementPropertyName** から **replacementValue** として抽出されます。 |
| **replacementValue** |省略可能 |String |古い値を置き換える新しい値。 |
| **replacementAttributeName** |省略可能 |String |置換値に使用する属性の名前 |
| **template** |省略可能 |String |**template** の値を指定した場合、template 内で **oldValue** が検索され、**source** の値で置換されます。 |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**関数:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**説明:**<br> 少なくとも 2 つの引数が必要です。引数は、式を使用して定義されている一意値生成ルールです。 関数では、各ルールが評価された後、生成された値の対象となるアプリ/ディレクトリでの一意性が確認されます。 最初に見つかった一意の値が返されます。 すべての値がターゲットに既に存在する場合、エントリはエスクローされて、理由が監査ログに記録されます。 渡すことができる引数の数に上限はありません。

> [!NOTE]
> - これは最上位の関数であり、入れ子にはできません。
> - この関数は、照合の優先順位を持つ属性には適用できません。  
> - この関数は、エントリの作成に使用されることだけを目的としたものです。 属性で使用するときは、 **[Apply Mapping]\(マッピングの適用\)** プロパティを **[オブジェクトの作成中のみ]** に設定します。
> - 現在、この関数は "Workday to Active Directory User Provisioning" でのみサポートされています。 他のプロビジョニング アプリでは使用できません。 


**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |2 つ以上必要であり、上限はありません |String | 評価する一意値生成ルールの一覧。 |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**関数:**<br> SingleAppRoleAssignment([appRoleAssignments])

**説明:**<br> 指定したアプリケーションで 1 人のユーザーに割り当てられた appRoleAssignments の全一覧から、単一の appRoleAssignment を返します。 appRoleAssignments オブジェクトを単一のロール名文字列に変換するために、この関数が必要になります。 ベスト プラクティスは、確実に 1 つの appRoleAssignment だけが一度に 1 人のユーザーに割り当てられるようにすることです。複数のロールが割り当てられると、返されるロール文字列を予測できません。 

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Required |String |**[appRoleAssignments]** オブジェクト |

---
### <a name="split"></a>Split
**関数:**<br> Split(source, delimiter)

**説明:**<br> 指定された区切り記号文字を使用して、文字列を複数値の配列に分割します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |**source** 値。 |
| **delimiter** |Required |String |文字列の分割に使用される文字を指定します (例: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**関数:**<br> StripSpaces(source)

**説明:**<br> source 文字列からすべての空白文字 (" ") を削除します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |**source** 値。 |

---
### <a name="switch"></a>Switch
**関数:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**説明:**<br> **source** 値が **key** と一致するときに、その **key** の **value** を返します。 **source** 値がどの key とも一致しない場合は、**defaultValue** を返します。  **key** と **value** パラメーターは、常にペアで指定する必要があります。 この関数には、常に、偶数個のパラメーターを指定する必要があります。 この関数は、manager などの参照属性には使用しないでください。 

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |**Source** 値。 |
| **defaultValue** |省略可能 |String |source がどの key とも一致しないときに使用される既定値。 空の文字列 ("") を指定できます。 |
| **key** |Required |String |**source** 値と比較する **key**。 |
| **value** |Required |String |key と一致する **source** の置換値。 |

---
### <a name="tolower"></a>ToLower
**関数:**<br> ToLower(source, culture)

**説明:**<br> *source* 文字列値を受け取り、指定されたカルチャ ルールを使用して小文字に変換します。 *culture* 情報が指定されていない場合は、インバリアント カルチャが使用されます。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |通常は、source オブジェクトの属性の名前。 |
| **culture** |省略可能 |String |RFC 4646 に基づくカルチャ名の形式は、*languagecode2-country/regioncode2* です。ここで、*languagecode2* は 2 文字の言語コードで、*country/regioncode2* は 2 文字のサブカルチャ コードです。 例には、日本語 (日本) の場合の ja-JP と英語 (米国) の場合の en-US が含まれています。 2 文字の言語コードが使用できない場合は、ISO 639-2 から派生した 3 文字のコードが使用されます。|

---
### <a name="toupper"></a>ToUpper
**関数:**<br> ToUpper(source, culture)

**説明:**<br> *source* 文字列値を受け取り、指定されたカルチャ ルールを使用して大文字に変換します。 *culture* 情報が指定されていない場合は、インバリアント カルチャが使用されます。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |Required |String |通常は、source オブジェクトの属性の名前。 |
| **culture** |省略可能 |String |RFC 4646 に基づくカルチャ名の形式は、*languagecode2-country/regioncode2* です。ここで、*languagecode2* は 2 文字の言語コードで、*country/regioncode2* は 2 文字のサブカルチャ コードです。 例には、日本語 (日本) の場合の ja-JP と英語 (米国) の場合の en-US が含まれています。 2 文字の言語コードが使用できない場合は、ISO 639-2 から派生した 3 文字のコードが使用されます。|

---
### <a name="word"></a>Word
**関数:**<br> Word(String,WordNumber,Delimiters)

**説明:**<br> Word 関数は、使用する区切り記号と返す単語の番号を表すパラメーターに基づいて、文字列内に含まれる単語を返します。 delimiters 内のいずれかの文字で区切られた string 内の各文字列が、単語として識別されます。

num < 1 の場合、空の文字列を返します。
string が null の場合、空の文字列を返します。
string に含まれる単語の数が指定より少ないか、区切り記号文字で識別されるどの単語も string に含まれていない場合は、空の文字列が返されます。

**パラメーター:**<br> 

| Name | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **String** |Required |複数値の属性 |返される単語を含む文字列|
| **WordNumber** |Required | 整数 | 返すべき単語の番号を指定する数値|
| **delimiters** |Required |String| 単語を識別するために使用される区切り記号を表す文字列|

**例:**<br>
Word("The quick brown fox",3," ")                                                                                       
"brown" を返します。

Word("This,string!has&many separators",3,",!&#")                                                                       
"has" が返されます

---

## <a name="examples"></a>例
### <a name="strip-known-domain-name"></a>既知のドメイン名をストリップする
ユーザーの電子メールから既知のドメイン名をストリップして、ユーザー名を取得する必要があります。 <br>
たとえば、ドメインが "contoso.com" の場合は、次の式を使用することができます。

**Expression:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**サンプル入力/出力:** <br>

* **入力** (mail): "john.doe@contoso.com"
* **出力**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>ユーザー名に定数のサフィックスを追加する
Salesforce Sandbox を使用している場合は、ユーザー名を同期する前に、すべてのユーザー名に追加のサフィックスを追加する必要があります。

**Expression:** <br>
`Append([userPrincipalName], ".test")`

**サンプル入力/出力:** <br>

* **入力**: (userPrincipalName): "John.Doe@contoso.com"
* **出力**:  "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>姓の一部と名の一部を連結することでユーザー エイリアスを生成する
ユーザーの名の最初の 3 文字とユーザーの姓の最初の 5 文字を取得することでユーザー エイリアスを生成する必要があります。

**Expression:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**サンプル入力/出力:** <br>

* **入力** (givenName):"John"
* **入力** (surname):"Doe"
* **出力**:"JohDoe"

### <a name="remove-diacritics-from-a-string"></a>文字列から分音記号を削除する
アクセント記号を含む文字を、アクセント記号を含まない同等の文字に置換する必要があります。

**Expression:** <br>
NormalizeDiacritics([givenName])

**サンプル入力/出力:** <br>

* **入力** (givenName):"Zoë"
* **出力**:"Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>文字列を複数値の配列に分割します
コンマ区切りの一覧になっている文字列を受け取り、Salesforce の PermissionSets 属性などの複数値の属性にプラグインできる配列に分割する必要があります。 この例では、アクセス許可セットの一覧が、Azure AD の extensionAttribute5 に格納されています。

**Expression:** <br>
Split([extensionAttribute5], ",")

**サンプル入力/出力:** <br>

* **INPUT** (extensionAttribute5):"PermissionSetOne, PermisionSetTwo"
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>特定の形式の文字列として日付を出力する
SaaS アプリケーションに特定の形式で日付を送信します。 <br>
たとえば、ServiceNow 向けに日付の書式を設定します。

**Expression:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**サンプル入力/出力:**

* **入力** (extensionAttribute1):"20150123105347.1Z"
* **出力**:"2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>定義済みのオプション セットに基づいて値を置換する

Azure AD に格納されている都道府県コードに基づいて、ユーザーのタイム ゾーンを定義する必要があります。 <br>
都道府県コードが定義済みオプションのいずれにも一致しない場合は、既定値 "Australia/Sydney" を使用します。

**Expression:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**サンプル入力/出力:**

* **入力** (state):"QLD"
* **出力**:"Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>正規表現を使用して文字を置換します
正規表現の値と一致する文字を見つけて削除する必要があります。

**Expression:** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**サンプル入力/出力:**

* **INPUT** (mailNickname: "john_doe72"
* **出力**:"72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>生成された userPrincipalName (UPN) の値を小文字に変換します
次の例では、PreferredFirstName および PreferredLastName ソース フィールドを連結することで UPN 値が生成され、ToLower 関数は、すべての文字を小文字に変換するために、生成された文字列で機能します。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**サンプル入力/出力:**

* **入力** (PreferredFirstName):"John"
* **入力** (PreferredLastName):"Smith"
* **出力**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>userPrincipalName (UPN) 属性用に一意の値を生成する
ユーザーの名、ミドル ネーム、姓を基にして UPN 属性の値を生成し、値を UPN 属性に割り当てる前に、対象の AD ディレクトリで値が一意であることを確認する必要があります。

**Expression:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**サンプル入力/出力:**

* **入力** (PreferredFirstName):"John"
* **入力** (PreferredLastName):"Smith"
* **出力**: John.Smith@contoso.com の UPN 値がディレクトリにまだ存在しない場合は "John.Smith@contoso.com"
* **出力**: John.Smith@contoso.com の UPN 値がディレクトリに既に存在する場合は "J.Smith@contoso.com"
* **出力**: 上記の 2 つの UPN 値がディレクトリに既に存在する場合は "Jo.Smith@contoso.com"

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>NULL でない場合はフロー メール値、それ以外の場合は userPrincipalName
メール属性が存在する場合は、それをフローすることをおすすめします。 存在しない場合、代わりに userPrincipalName の値をフローしてください。

**Expression:** <br>
`Coalesce([mail],[userPrincipalName])`

**サンプル入力/出力:** <br>

* **入力** (mail):NULL
* **入力** (userPrincipalName): "John.Doe@contoso.com"
* **出力**:  "John.Doe@contoso.com"

## <a name="related-articles"></a>関連記事
* [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](../app-provisioning/customize-application-attributes.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [アカウント プロビジョニング通知](../app-provisioning/user-provisioning.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)
