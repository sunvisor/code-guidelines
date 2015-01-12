#読みやすい JavaScript を書く

「読みやすい」JavaScript を書くことの重要性は、単に誇張することはできません。
理解しやすいコードを書くことで、あなたのチームのメンバーや将来のあなた自身に対して、親切にしましょう。

ロバート グラスは、その著書 *Facts and Fallacies of Seftware Engineergin* (訳注: 邦題「ソフトウエア開発 55の真実と10のウソ」) にて、
単に「既存の製品を理解する」ことが、開発時間のおよそ30%を消費すると述べています。

グラスはソフトウェア・メンテナンス・サイクルのコンテクストの中でこの点を表現します。そして、すぐに議論をメンテナンスの方へ動かす間、あなたのコードベースを明白で筋が通っていものにするために以下の点を優先させることを、Sencha は推奨します。

  - [Naming Conventions](#Naming_Conventions)
  - [Comments and Documentation](#Comments_Documentation)
  - [Documenting Overrides](#Documenting_Overrides)
  - [Spacing and White Space](#Spacing_White_Space)
  - [Line Length](#Line_Length)
  - [Block Length](#Block_Length)
  - [File Length](#File_Length)

## <a name="Naming_Conventions" />名付け規則

> *「コンピューターサイエンスの二つの難しいこと。キャッシュインバリデーション、名前をつけること、離れたところのエラー」*

多くの人は、人生の中であまり名前をつけることはしません。ペット、子ども、なにか個人的に意味のあるもの...
一般的に名前をつけるというのは難しいことです。なぜならプロセスの最後まで変更されないのが普通 (例えばペットの名前を変えることはめったにありません) だからです。

ソフトウェア開発者は、年に数千ものもの (変数、クラス、アプリケーションなど) に名前をつけなくてはなりません。
それぞれの名前は、名付けようとする概念の本質や用途をあらわそうとします。
しかし、覚えきれないほどの名前を使うので、数ヶ月後あるいは数年後にそのコードを理解し修正あるいは拡張するのを楽にするために、名前は、一貫してつけられなければなりません。
このアイデアは、ときに「自己文書化コード」と呼ばれます。

では、名付け規則が適用される範囲と、Sencha がそれぞれのケースをどう処理しているかを見ていきましょう。

### <a name="Namespaces" />Namespaces, Classes, and Constructors

Sencha では、トップレベルのネームスペース、クラス、コンストラクタを作る時には、常に *TitleCase* を使います。

    // "MyClass" はコンストラクタ :: new MyClass();
    MyClass = function() {};

中間の名前空間は短く、解説的にして、小文字で表記します。

    // "Foo" はトップレベルの名前空間
    // "bar" は、中間レベルの名前空間
    // "Baz" は、クラス名
    Foo.bar.Baz = {};
    Ext.data.reader.Json = {};

### <a name="Functions" />関数

Sencha では、関数を作る時には常に、*camelCase* を使います。
また、クロージャーでカプセル化されていないプライベートメソッドの名前には、先頭に "_" (アンダースコア) をつけることを推奨します。

    // 関数式
    var sortSomeStuff = function() {};
    
    // 関数宣言
    function findSomething() {}
    
    // オブジェクトの関数も同じ考え方
    var someObject = {
        objectMethod : function() {},
    
        _privateMethod : function() {}
    };

注: 関数式と関数宣言を使うことに関しては、[関数](Preventing_JavaScript_Errors.md#Functions) のセクションに詳しい情報があります。

### <a name="Local_Vars" />ローカル変数とオブジェクトのプロパティ

ローカル変数を宣言する際は、常に `var` を使います。そうしないとグローバル変数を作成することになり、グローバル名前空間を汚染することになります。
後の[定数及びグローバル変数](#Constants_Global_Vars)で詳細をご覧ください。

    // 悪い例
    foo = true;
    
    // 良い例
    var foo = true;

Sencha では、ローカル変数やオブジェクトのプロパティを作る時には、常に *camelCase* を使います。
また、プライベートプロパティの名前には、先頭に "_" (アンダースコア) をつけることを推奨します。

    // ローカル変数
    var fooBar = true;
    
    // オブジェクトのプロパティ
    var someObject = {
        someProperty : true,
    
        _privateProperty : true
    };

変数には意味のある名前をつけます。
そうすることで用途や変数の機能を明白に (また簡潔に) します。
反復演算のカウンター以外では一文字変数はやめましょう。

    // 悪い例、短かすぎ。説明的ではなく、数値の "1" と混同しやすい
    var l = group.length;
    
    // 悪い、変数名が不必要に長い
    var mainClassConfigVariableSectionOneRefreshInterval = 5000;
    
    // 良い、変数名は簡潔だが意味が通っている
    var len = group.length;
    
    // 反復処理のカウンターは例外
    var i;
    for (i = 0; i < len; i++) {}

一つの `var` 宣言を使って、複数の変数を生成します。その方が読みやすいからです。
Sencha は、それぞれの変数の割り当ては新しい行で宣言することを推奨します。
値を割り当てない変数宣言は最後に、同一行に宣言できます。

こうすると、現在のスコープでの変数の初期状態についてコードを読む人にとっての、視覚的なヒントになります。

    // 悪い
    var foo = 1;
    var bar = 2;
    var baz;
    var fuz;
    
    // 良い
    var foo = 1,
        bar = 2,
        baz, fuz;
        
### <a name="Constants_Global_Vars" />定数とグローバル変数

Sencha では、その変数が特別であることを明確に視覚的に示すために、グローバル変数を生成する時には、*CONSTANT_CASE* を推奨します。

    // bad
    userID = '12345';
    
    // good
    USER_ID = '12345';

そうは言っても、Sencha はグローバル変数、定数を全く使わないことを好みますが。

我々は企業アプリケーションでは、その代わりに適切な名前空間に配置されたクラスを使うことでのメリットがあると感じています。それはどこで値が定義されたかが常に明白だからです。

    // より良い
    MyApp.authentication.User = {
        id : '12345'
    };

### <a name="Special_Cases" />特別な場合

このほかの特別な場合も存在します -- 例えば、`this` を参照する名前です。

内部的な規則として、Sencha では、クロージャーの中で `this` への参照を保持する必要があるときに `me` という名前を使います。
全員が賛成しているわけではありませんが -- クリスチャン ジョナサン [a notable example](https://gist.github.com/cjohansen/4135065) --
より素晴らしい点は、コードベース全体で一貫してこれらの特別な場合を管理することです。

    Person.logger = function() {
        var me = this; // "me" will be used consistently
    
        return function() {
            console.log(me);
        };
    };

もう一つの重要な点は、`this` はキーワードであり圧縮できないということです。
Sencha フレームワークでは、我々は「4のルール」を守ります。
特定のスコープ内で、`this` が 4回以上参照されたら、`this` をローカル変数 `me` でキャッシュします。そうするとミニファイされるソースをより小さくできます。

    // 悪い
    function foo() {
        this.x = 1;
        this.y = 2;
        this.z = 3;
        this.u = 4;
    }
     
    // 良い
    function foo() {
        var me = this;
        me.x = 1;
        me.y = 2;
        me.z = 3;
        me.u = 4;
    }
    
    // ミニファイの出力の比較
    function f(){this.x=1;this.y=2;this.z=3}
    function f(){var e=this;e.x=1;e.y=2;e.z=3}
    
    function f(){this.x=1;this.y=2;this.z=3;this.u=4;}
    function f(){var e=this;e.x=1;e.y=2;e.z=3;e.u=4;} // 4 でより短くなります
    
    function f(){this.x=1;this.y=2;this.z=3;this.u=4;this.v=5;}
    function f(){var e=this;e.x=1;e.y=2;e.z=3;e.u=4;e.v=5;}
    
### <a name="Reserved_Words" />予約語

予約語をキーとして使っていけません。
Internet Explorer の古いバージョンで動作しないからです。
予約語の代わりに分かりやすい同義語を使います。

    // bad
    var model = {
        name    : 'Foo',
        private : true // 予約語
    };
    
    // good
    var model = {
        name   : 'Foo',
        hidden : true
    };


注: 「分かりやすい同義語」は実際の単語にします。
    
    // 悪い
    var car = {
        class : 'Ford' // 予約語
    };
    
    // 悪い
    var car = {
        klass : 'Ford' // お願い。二度とこんなことしないで
    };
    
    // 良い
    var car = {
        brand : 'Ford'
    };
    
## <a name="Comments_Documentation" />コメントと文書化

一般的に言って、良いコードは自己説明的だとされています。
しかしながら、コメントは読みやすいコードにするために二つの重要な役割を果たします。
文書化と意図 (インラインコメントによる) です。

### <a name="Documentation" />文書化

システム全体の文書化は、大きなコードベースを開発する際にきわめて重要です。
[JSDuck](https://github.com/senchalabs/jsduck)
のようなツールを使うと、コードベースの API リファレンスを簡単に作ることができます。

[Sencha](http://docs.sencha.com/extjs/5.0/apidocs/)
は、内部的に JSDuck を使っています。
JSDuck は、 JavaDoc スタイルのブロックコメントを追跡します。
[JSDuck wiki](https://github.com/senchalabs/jsduck/wiki)
で、詳細をご覧になれます。

    
    /**
     * @class MyApp.foo.Bar
     */
    MyApp.foo.Bar = function() {
        var baz = true;
    
        return {
            /**
             * @method
             */
            utilityMethod : function() {
                return baz;
            }
        };
    };

### <a name="Inline_Comments" />インライン コメント

多くの開発者が、コードは「自己文書化」であるべきと感じていて、それによってインライン・コメントはつけるべきでないと感じています。
Sencha は、その思考法の頑固さに必ずしも同意できません。
我々は、コードの意図または目的が完全に明白でないときには、常にコメントをするものだと思っています。しかし、コードそのものは論理的に追いかける際に十分明白であるようにしましょう。

    // 多くの場合、コントローラIDは、その名前と同じです。
    // しかし、コントローラに手動でIDが与えられると、そのように
    // コレクションの中のキーになります。
    // ですから、それを見つけないと、我々は既存のコントローラをループして、
    // クラス名を使ってそれを見つけようとします。
    if (!controller) {
       all = controllers.items;
       for (i = 0, len = all.length; i < len; ++i) {
           cls = all[i];
           className = cls.getModuleClassName();
           if (className && className === name) {
               controller = cls;
               break;
           }
       }
    }

正規表現式は、本質的に混乱する記法ですから、常にコメントで説明を書きましょう。

    // ローマ数字の入力に一致する
    var romanNums = /^M{0,4}(CM|CD|D?C{0,3})(XC|XL|L?X{0,3})(IX|IV|V?I{0,3})$/;

コードブロック全体をコメントアウトするのは避けましょう。
それは、用途がなくコードを肥大化させるだけですから。

    // どうして次のコードが製品版に残っているの？
    items    : [
        //{
        //    xtype        : 'booleancolumn',
        //    width        : 5,
        //    resizable    : false,
        //    defaultWidth : 5,
        //    sortable     : false,
        //    dataIndex    : 'isOwn',
        //    groupable    : false,
        //    hideable     : false,
        //    lockable     : false,
        //    tdCls        : 'indicator',
        //    falseText    : ' ',
        //    trueText     : ' '
        //},
        //{
        //    xtype     : 'gridcolumn',
        //    dataIndex : 'key',
        //    text      : 'Binding Key',
        //    flex      : 1
        //},
        {
            xtype     : 'templatecolumn',
            dataIndex : 'boundTo',
            text      : 'Bound To',
            flex      : 1,
            tpl       : '\\{{boundTo}\\}'
        },
        {
            xtype     : 'gridcolumn',
            dataIndex : 'value',
            text      : 'Value',
            flex      : 1,
            renderer  : function (value, metaData, record, rowIndex, colIndex, store, view) {
                var v = value;

                if (value === null) {
                    v = 'null';
                }

                if (record.data.text === 'undefined') {
                    v = 'undefined';
                }

                return '<span class="highlight ' + record.get('type') + ' ' + v + '">' + v + '</span>';
            }
        }
    ]

## <a name="Documenting_Overrides" />文書化のオーバーライド

デフォルトをオーバーライドする必要があるか、機能を継承した場合、その変更を完全に明白にするために、インライン コメントとブロック コメントを積極的に活用しましょう。

    // EXTJS-12345 のバグのオーバーライド
    Ext.define('MyApp.override.CustomNumberField', {
        override : 'Ext.form.field.Number',
     
        initComponent: function() {
            var me = this,
                allowed;
     
            me.callParent();
     
            me.setMinValue(me.minValue);
            me.setMaxValue(me.maxValue);
     
            // 設定されたオプションをベースにマスキング／ストリッピングする正規表現を構築
            if (me.disableKeyFilter !== true) {
                allowed = me.baseChars + '';
                if (me.allowDecimals) {
                    //OVERRIDE THIS LINE...
                    //allowed += me.decimalSeparator;
                    allowed += ',.';
                }
            }
        }
    });
    
この文書化は、アップグレード処理の際に便利です。
たとえば、バグ EXTJS-12345 は最新版でフィックスされたら、このオーバーライドは完全に取り除くことができます。

## <a name="Spacing_White_Space" />インデントや空白

多くの開発者には、スペースをあける時のタブとスペースの話題に関する断固たる意見があるものです。
タブのサイズは予測できないので、Sencha は自身のコードでは 4つのスペースを利用することにしています。
読みやすいコードを保証することができる唯一の方法は、スペースの利用を実施することです。
最終的な目的は一貫性があることだけなので、どちらを選ぼうともそれらを交ぜてはいけません。
    
    // 悪い
    function doSomething(isTrue) {
     // < 1つのスペース
     if (isTrue) {
        // <<< 3 つのスペース
    }// もう混乱し始めてます
    }
    
    // good
    function doSomething(isTrue) {
        // <<<< 4 つのスペース
        if (isTrue) {
            // <<<< 4 ここでも 4つのスペース
        }
    }

一方、Senchaも、読みやすいコードを作るのに必要なホワイトスペースを使うことを推奨します。

## <a name="Line_Length" />行の長さ

一行あたりの文字数制限については、誰もが同意するというわけではありませんが、
Sencha では一般的に行の幅を制限しようとしています。
この制限は任意で（例えば80または100文字）厳しく押しつけることはしませんが、目的は開発者が水平スクロールする量を減らすのが目的です。

誰でも線につき性格のために特定の制限に同意するというわけではない、しかし、Senchaは一般的に行の幅を制限しようとしています。
この制限は任意で（例えば80または100文字）、厳しく実施されることができない、しかし、ゴールは水平スクロールの量を開発者のために減らすことになっている。

明確な制限より長いひもは、ストリング連結を使っている複数の線の向こうに書かれなければならない。
決められた制限よりも長い文字列は、文字列連結を使って複数行にわたって記述します。

## <a name="Block_Length" />メソッドやブロックの長さ

コードブロックがどれくらいの長さになったら、機能をより小さなユーティリティ メソッドに分割することを考えますか？

良い経験則は、メソッドやコードブロックの長さを (例えば50または100行に) 制限することです。それによりそれほど大きくならずに済みます。
短いメソッドはテストしやすく、開発者がすぐに理解することができます。

## <a name="File_Length" />ファイルの長さ

ファイルの行数がどれくらいになったら機能をミックスインやモジュールに分割することを考えますか？

メソッド／ブロックの長さと同じように、コメントもファイルの長さに影響します。
仮想クラスは、インターフェースや基本ラインを定義するので、通常より長くなりやすくなります。
それでもやはり、任意のファイル長 (500または1000行) を定めると、クラスをリファクタリングする必要があるかどうかの兆候を把握できます。
