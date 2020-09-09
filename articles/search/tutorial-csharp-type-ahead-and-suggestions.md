---
title: オートコンプリートと検索候補に関する C# チュートリアル
titleSuffix: Azure Cognitive Search
description: ドロップダウン リストを使用してユーザーから検索語句の入力を収集するためのオートコンプリートと検索候補を追加します。 このチュートリアルは、既存のホテル プロジェクトに基づいています。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 07/15/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 1afeca4f627236c5172dd07a44751015c16d2f58
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462022"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>チュートリアル:.NET SDK を使用してオートコンプリートと検索候補を追加する

ユーザーが検索ボックスへの入力を開始したときのオートコンプリート (先行入力クエリと検索候補のドキュメント) を実装する方法について説明します。 このチュートリアルでは、オートコンプリートされたクエリと検索候補の結果を個別に表示した後、それらを統合します。 ユーザーは、2 つまたは 3 つの文字を入力するだけで、使用可能なすべての結果を検索することができます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * 検索候補を追加する
> * 検索候補に強調表示を追加する
> * オートコンプリートを追加する
> * オートコンプリートと検索候補を組み合わせる

## <a name="prerequisites"></a>前提条件

このチュートリアルはシリーズの一部であり、[Azure Cognitive Search での検索結果のページングの C# チュートリアル](tutorial-csharp-paging.md)で作成したページング プロジェクトを基に作成されています。

別途 [3-add-typeahead](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead) チュートリアルのソリューションをダウンロードして実行してもかまいません。

## <a name="add-suggestions"></a>検索候補を追加する

ユーザーに代わりの候補を提示する際の最もシンプルなケース、つまり検索候補のドロップダウン リストから始めましょう。

1. index.cshtml ファイルで、**TextBoxFor** ステートメントの `@id` を **azureautosuggest** に変更します。

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

2. このステートメントに続けて、 **&lt;/div&gt;** で閉じた後に次のスクリプトを入力します。 このスクリプトは、オープンソースの jQuery UI ライブラリにある[オートコンプリート ウィジェット](https://api.jqueryui.com/autocomplete/)を活用して、検索候補のドロップダウン リストを表示します。 

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    上記のスクリプトは、"azureautosuggest" という ID によって検索ボックスに接続されます。 ウィジェットの source オプションには Suggest メソッドを設定し、**highlights** と **fuzzy** という 2 つのクエリ パラメーターを指定して、Suggest API を呼び出しています。この例では、2 つのクエリ パラメーターが、どちらも false に設定されています。 また、検索をトリガーするためには、少なくとも 2 つの文字が必要です。

### <a name="add-references-to-jquery-scripts-to-the-view"></a>ビューに jQuery スクリプトへの参照を追加する

1. jQuery ライブラリにアクセスするには、ビュー ファイルの &lt;head&gt; セクションを次のコードに変更します。

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. また、ここでは新しい jQuery 参照を導入するので、(**Views/Shared** フォルダーの) _Layout.cshtml ファイルから既定の jQuery 参照は削除 (またはコメント アウト) する必要があります。 次の行を見つけて、最初のスクリプト行を次のようにコメントアウトします。 この変更により、jQuery への参照の競合を回避できます。

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    これで、定義済みの Autocomplete jQuery 関数を使用できるようになりました。

### <a name="add-the-suggest-action-to-the-controller"></a>コントローラーに Suggest アクションを追加する

1. home コントローラーに **Suggest** アクションを追加します (たとえば、**Page** アクションの後など)。

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. It is defined in the index schema.
            // The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    **Top** パラメーターは、返される結果の数を指定するものです (指定しない場合、既定値は 5 です)。 _suggester_ は、データの設定時に Azure のインデックス上で指定されます。このチュートリアルのようなクライアント アプリによって指定されるものではありません。 この場合、suggester は "sg" と呼ばれ、**HotelName** フィールドのみを検索します。 

    あいまい一致では、編集距離の上限を 1 とする "ニア ミス" を出力に含めることができるようになります。 **highlights** パラメーターが true に設定されている場合は、太字の HTML タグが出力に追加されます。 次のセクションで、これら 2 つのパラメーターを true に設定します。

2. 構文エラーがいくつか発生する場合があります。 その場合は、次の 2つの **using** ステートメントをファイルの先頭に追加します。

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. アプリケーションを実行します。 たとえば、「po」と入力したときに、いくつかの選択候補が表示されるでしょうか。 次は「pa」を試してみてください。

    ![「po」と入力すると 2 つの検索候補が表示される](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    入力する文字は、単語の途中に含まれる文字ではなく、"_必ず_" 単語の先頭部分である必要があります。

4. ビュー スクリプトで **&fuzzy** を true に設定してから、アプリをもう一度実行します。 ここで「po」と入力します。 検索で、1 文字間違っていると推測されていることに注目してください。
 
    ![fuzzy を true に設定した状態で「pa」と入力する](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    関心をお持ちであれば、あいまい検索で使用されるロジックを詳細に説明している [Azure Cognitive Search での Lucene クエリ構文](./query-lucene-syntax.md)に関するページを参照してください。

## <a name="add-highlighting-to-the-suggestions"></a>検索候補に強調表示を追加する

ユーザーに表示される検索候補の見た目は、**highlights** パラメーターを true に設定することで向上させることができます。 ただし、まずはいくつかのコードをビューに追加して、太字のテキストを表示できるようにする必要があります。

1. ビュー (index.cshtml) 内で、上記で入力した **azureautosuggest** スクリプトの後に、次のスクリプトを追加します。

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

2. 次に、テキスト ボックスの ID を次のように変更します。

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. アプリをもう一度実行すると、入力したテキストが検索候補の中で太字で表示されるはずです。 たとえば、「pa」と入力してみてください。
 
    ![強調表示される「pa」の入力](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. 上記の強調表示のスクリプトで使用されるロジックは、絶対確実なものではありません。 同じ名前内で 2 回出現する用語を入力すると、太字の結果はうまく表示されません。 「mo」と入力してみてください。

    開発者は、どのようなときにスクリプトが "十分に機能" しているか、そしてどのようなときに問題に対処すべきかを把握している必要があります。 このチュートリアルではこれ以上強調表示については説明しませんが、実際のデータに対して強調表示が効果的でない場合は、精度の高いアルゴリズムを見つけることを検討してください。 詳細については、「[検索結果の強調表示](search-pagination-page-layout.md#hit-highlighting)」を参照してください。

## <a name="add-autocomplete"></a>オートコンプリートを追加する

検索候補とは若干異なるもう 1 つのバリエーションとして、オートコンプリートがあります。"先行入力" とも呼ばれ、検索語を補完するものです。 ここでも、ユーザー エクスペリエンスの向上に取り組む前に、最もシンプルな実装から開始します。

1. ビュー内で、これまでのスクリプトの後に、次のスクリプトを入力します。

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

2. 次に、テキスト ボックスの ID を次のように変更します。

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. home コントローラーで、たとえば **Suggest** アクションの下などに、**Autocomplete** アクションを入力する必要があります。

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    検索候補の提示のときのように、オートコンプリート検索内で "sg" と呼ばれる同じ *suggester* 関数を使用していることに注目してください (こうすることで、ホテル名のみオートコンプリートしようとしています)。

    **AutocompleteMode** の設定はいくつか存在し、ここで使用しているのは **OneTermWithContext** です。 その他のオプションについては、[オートコンプリート API](/rest/api/searchservice/autocomplete) に関するページを参照してください。

4. アプリケーションを実行します。 ドロップダウン リストに表示される選択候補が単語単位になっていることに注目してください。 "re" で始まる単語を入力してみてください。 入力される文字数が増えるにつれて、選択候補の数が減ることに注目してください。

    ![基本的なオートコンプリートを使用した入力](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    現時点では、おそらく以前に実行した検索候補のスクリプトの方が、このオートコンプリート スクリプトよりも便利です。 オートコンプリートをより使いやすくするには、検索候補の検索に追加するのが最善の方法です。

## <a name="combine-autocompletion-and-suggestions"></a>オートコンプリートと検索候補を組み合わせる

オートコンプリートと検索候補を組み合わせる作業は、選択肢の中で最も複雑なものですが、ユーザー エクスペリエンスの質はおそらく最も高くなります。 目的は、入力中のテキストに、Azure Cognitive Search がテキストをオートコンプリートする際の最初の選択肢をインラインで表示することです。 また、いくつかの検索候補をドロップダウン リストとして表示するという目的もあります。

しばしば "インライン オートコンプリート" などの名前で呼ばれるこの機能を提供するライブラリはいくつか存在します。 ただし、ここでは、具体的な処理を確認できるように、この機能をネイティブに実装します。 この例では、最初にコントローラーから作業を開始します。

1. 指定した個数の検索候補に加えて、オートコンプリートの結果を 1 つだけ返すアクションを、コントローラーに追加する必要があります。 このアクションは **AutocompleteAndSuggest** と呼ぶことにします。 home コントローラーに次のアクションを追加します。その他の新しいアクションの後に配置してください。

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    1 つのオートコンプリートの選択肢が **results** リストの上部に返されます (すべての検索候補の前に)。

2. ビュー内で、最初に少し工夫をして、薄い灰色のオートコンプリートの単語が、ユーザーが入力中の太字テキストの真下に表示されるようにします。 HTML には、この目的のための相対位置が含まれています。 **TextBoxFor** ステートメント (およびその周囲の &lt;div&gt; ステートメント) を以下のように変更します。**underneath** として識別される 2 つ目の検索ボックスが通常の検索ボックスの真下に来るように、既定の場所から 39 ピクセル下げられていることに注意してください。

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    再度 ID を変更していることに注意してください。この場合は **azureautocomplete** です。

3. さらに、ビュー内で、これまでに入力したすべてのスクリプトの後に、次のスクリプトを入力します。 多くの量があります。

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    ユーザーが入力している内容と一致しなくなったときに、下に表示されるテキストを消去すること、そしてオーバーレイのテキストを正常に表示するために大文字と小文字をユーザーの入力と揃えて設定すること (検索時に "pa" が "PA"、"pA"、"Pa" にも一致してしまうため) の両方を目的として、**interval** 関数がうまく使用されていることを注目してください。

    スクリプト内のコメントを読めば、より詳しく理解することができます。

4. 最後に、2 つの HTML クラスに軽微な調整を行って、透過性を高める必要があります。 hotels.css ファイルの **searchBoxForm** クラスと **searchBox** クラスに次の行を追加します。

    ```html
        background: rgba(0,0,0,0);
    ```

5. その後、アプリを実行します。 検索ボックスに「pa」と入力します。 "pa" を含む 2 つのホテルと共に、オートコンプリートの検索候補として "palace" が表示されていますか。

    ![インライン オートコンプリートと検索候補を使用した入力](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Tab キーを使用してオートコンプリートの検索候補を受け入れ、方向キーと Tab キーを使って検索候補を選択してみてください。その後、マウスとシングル クリックを使ってもう一度試してみてください。 スクリプトがこれらすべての状況を適切に処理できていることを確認します。

    人によっては、この機能を提供するライブラリを読み込む方が簡単だと判断するかもしれませんが、それでも、これでインライン オートコンプリートを動作させる方法を少なくとも 1 つ学べたことになります。

## <a name="takeaways"></a>重要なポイント

このプロジェクトの重要なポイントを示します。

* オートコンプリート ("先行入力" とも呼ばれます) と検索候補を使用すると、ユーザーはいくつかのキーを入力するだけで、意図したものを正確に検索することができます。
* オートコンプリートと検索候補を組み合わせることで、優れたユーザー エクスペリエンスを提供できます。
* オートコンプリート機能は必ずすべての入力フォームでテストします。
* **setInterval** 関数を使用すると、UI 要素の検証と修正に役立つ可能性があります。

## <a name="next-steps"></a>次のステップ

次のチュートリアルでは、1 回のクリックで検索を絞り込めるファセットを使用して、ユーザー エクスペリエンスを向上させる別の方法を確認します。

> [!div class="nextstepaction"]
> [C# チュートリアル: ナビゲーションをサポートするファセットの使用 - Azure Cognitive Search](tutorial-csharp-facets.md)