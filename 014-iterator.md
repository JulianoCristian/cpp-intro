# イテレーターの基礎

vectorの章ではvectorの要素にアクセスする方法としてメンバー関数`at(i)`を学んだ。`at(i)`はi番目の要素にアクセスできる。ただし最初の要素は0番目だ。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    int x = v.at(2) ; // 3
    v.at(2) = 0 ;
    // vは{1,2,0,4,5}
}
~~~

この章ではvectorの要素にアクセスする方法としてイテレーター(iterator)を学ぶ。

## イテレーターの取得方法

イテレーターは`std::begin(v)`で取得する。vはvectorの変数だ。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;
    auto i = std::begin(v) ;
}
~~~


## イテレーターの参照する要素に対する読み書き。

イテレーターはvectorの先頭の要素を指し示している。イテレーターの指し示す要素を参照するには`*`を使う。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    auto i = std::begin(v) ;

    int x = *i ; // 1

    *i = 0 ;
    // vは{0,2,3,4,5} 
}
~~~

`*i`を読み込むと指し示す要素の値を読むことができ、`*i`に代入をすると指し示す要素の値を変えることができる。


## イテレーターの参照する要素を変更

現在指している要素の次の要素を指すように変更するには`++`を使う。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    auto i = std::begin(v) ;

    *i ; // 1
    ++i ;
    *i ; // 2
    ++i ;
    *i ; // 3
}
~~~

現在指している要素の前の要素を指すように変更するには`--`を使う。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    auto i = std::begin(v) ;

    *i ; // 1
    ++i ;
    *i ; // 2
    --i ;
    *i ; // 1
}
~~~



vectorの全要素を先頭からイテレーターでアクセスするには、要素数だけ`++i`すればよいことになる。


~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;
    auto iter = std::begin(v) ;

    for ( std::size_t i = 0 ; i != std::size(v) ; ++i, ++iter )
    {
        std::cout << *iter << "\n"s ;
    }
}
~~~

これは動く。ただしもっとマシな方法がある。イテレーターの比較だ。

## イテレーターの比較

イテレーターは比較できる。同じ順番の要素を指すイテレーターは等しく、そうではないイテレーターは等しくない。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    auto x = std::begin(v) ;
    auto y = x ;

    // x, yは0番目の要素を指す

    bool b1 = (x == y) ; // true
    bool b2 = (x != y) ; // false

    ++x ; // xは1番目の要素を指す。

    bool b3 = (x == y) ; // false
    bool b4 = (x != y) ; // true
}
~~~


## 最後の次の要素へのイテレーター

`std::begin(v)`はvectorの変数vの最初の要素を指し示すイテレーターを取得する。

`std::end(v)`はvectorの変数vの最後の次の要素を指し示すイテレーターを取得する。

~~~cpp
int main()
{
    std::vector<int> v = { 1,2,3,4,5 };

    // 最後の次の要素を指し示すイテレーター
    auto i = std::end(v) ;
}
~~~

最後の次の要素とは何か。ある`vector<int>`の変数の中身が`{1,2,3,4,5}`のとき、最初の0番目の要素の値は1だ。最後の4番目の要素の値は5だ。最後の次の要素とは、値が5の最後の要素の次の要素だ。そのような要素は実際には存在しないが、`std::end`は概念として最後の次の要素を返す。

最後の次の要素を指し示すイテレーターに対して、`*`で要素にアクセスを試みるとエラーになる。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    auto i = std::end(v) ;

    *i ; // エラー
}
~~~

最後の次の要素を`++`しようとするとエラーになる。`--`することはできる。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;
    auto i = std::end(v) ;

    --i ;   // 最後の要素を指す
    *i ;    // 5
    ++i ;   // 最後の次の要素を指す
    *i ;    // エラー
}
~~~

実際には存在しない最後の次の要素を指し示すイテレーターは何の役に立つのか。答えはイテレーターの比較だ。

実際には存在しない最後の次の要素を指すイテレーターに'*'を使って要素にアクセスするのはエラーだが、イテレーター同士の比較はできる。すでに説明したように、イテレーターの比較は同じ要素を指す場合はtrue、違う要素を指す場合はfalseになる。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3} ;

    // xは最初の要素を指す
    auto x = std::begin(v) ;
    // yは最後の次の要素を指す
    auto y = std::end(v) ;


    x == y ; // false
    ++x ; // xは最初の次の要素を指す
    x == y ; // false
    ++x ; // xは最後の要素を指す
    x == y ; // false
    ++x ; // xは最後の次の要素を指す
    x == y ; // true
}
~~~

`std::end`で取得する最後の次の要素を指すイテレーターと比較することで、イテレーターが最後の次の要素を指し示す状態に到達したことを判定できる。

ということは、`vector`の要素を先頭から最後まで順番に出力するプログラムは、以下のように書ける。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    for ( auto iter = std::begin(v), last = std::end(v) ;
          iter != last ; ++iter )
    {
        std::cout << *iter << "\n"s ;
    }
}
~~~

## なんでもイテレーター

イテレーターというのは要素にアクセスする回りくどくて面倒な方法に見える。イテレーターという面倒なものを使わずに、`vector::at(i)`でi番目の要素にアクセスするほうが楽ではないか。そう考える読者もいるだろう。イテレーターの利点はその汎用性にある。イテレーターの作法に従うことで、様々な処理が同じコードで書けるようになるのだ。

たとえば、vectorの要素を先頭から順番に出力する処理を振り返ってみよう。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    for ( std::size_t i = 0 ; i != std::size(v) ; ++i )
    {
        std::cout << v.at(i) << "\n"s ;
    }
}
~~~

このコードは`vector`にしか使えないコードだ。イテレーターで書き直してみよう。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    for ( auto iter = std::begin(v), last = std::end(v) ;
          iter != last ; ++iter )
    {
        std::cout << *iter << "\n"s ;
    }
}
~~~

そして、この要素を先頭から出力する処理を関数にしてみよう。

~~~cpp
auto output_all = []( auto first, auto last )
{
    for ( auto iter = first ; iter != last ; ++iter )
    {
        std::cout << *iter << "\n"s ;
    }
} ;

int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    output_all( std::begin(v), std::end(v) ) ;
}
~~~

この`関数output_all`は`vector`以外のイテレーターにも対応している。C++には様々なイテレーターがある。例えば標準入力から値を受け取るイテレーターがある。早速使ってみよう。


~~~c++
int main()
{
    std::istream_iterator<int> first( std::cin ), last ;

    output_all( first, last ) ;
}
~~~

このプログラムは標準入力から`int`型の値を受け取り、それをそのまま標準出力する。

C++には他にも、カレントディレクトリーにあるファイルの一覧を取得するイテレーターがある。

~~~c++
int main()
{
    std::filesystem::directory_iterator first("./"), last ;

    output_all( first, last ) ;
}
~~~

`関数output_all`のコードは何も変えていないのに、様々なイテレーターに対応できる。イテレーターというお作法に乗っ取ることで、様々な処理が可能になるのだ。

これは出力にも言えることだ。`関数output_all`は`std::cout`に出力していた。これをイテレーターに対する書き込みに変えてみよう。

~~~c++
auto output_all = []( auto first, auto last, auto output_iter )
{
    for ( auto iter = first ; iter != last ; ++iter, ++output_iter )
    {
        *output_iter = *iter ;
    }
} ;
~~~

書き換えた`関数output_iter`は新しく`output_iter`という引数を取る。これはイテレーターだ。`std::cout`に出力する代わりに、このイテレーターに書き込むように変更している。

こうすることによって、出力にも様々なイテレーターが使える。

標準出力に出力するイテレーターがある。

~~~c++
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;
    
    output_all( std::begin(v), std::end(v),
                std::ostream_iterator<int>(std::cout) ) ;
}
~~~

vectorも出力先にできる。つまりvectorのコピーだ。

~~~c++
int main()
{
    std::vector<int> source = {1,2,3,4,5} ;
    std::vector<int> destination(5) ;

    output_all( std::begin(source), std::end(source), std::begin( destination ) ) ;
}
~~~


`desitination(5)`というのは、`vector`にあらかじめ5個の要素を入れておくという意味だ。あらかじめ入っている要素の値はintの場合ゼロになる。

この他にもイテレーターは様々ある。自分でイテレーターを作ることもできる。そして、`関数output_all`はイテレーターにさえ対応していれば様々な処理にコードを一行たりとも変えずに使えるのだ。

## イテレーターと添字の範囲


イテレーターは順序のある値の集合を表現するために、最初の要素への参照と、最後の次の要素への参照のペアを用いる。

たとえば、{1,2,3,4,5}という順序の値の集合があった場合、イテレーターは最初の要素である1と最後の一つ次の要素である5の次の架空の要素を指し示す。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    auto i = std::begin(v) ;
    auto j = std::end(v) ;
}
~~~

このようにして範囲を表現することを、`半閉鎖(half-closed)`とか、`[i,j)`などと表現する。

この状態から{2,3,4,5}のような値の集合を表現したい場合、イテレーターiをインクリメントすればよい。

~~~
++i ;
~~~

これで`[i,j)`は{2,3,4,5}になった。

このような範囲の表現方法に疑問を感じる読者もいるだろう。なぜ最後の次の要素という本来存在しない架空の要素をあたかも参照しているかのようなイテレーターが必要なのか。最後の要素を参照するのではだめなのか。

そのような範囲の表現方法は、`閉鎖(closed)`とか`[i,j]`などと表現する。

実はこの方法はvectorの要素の順番を指定する方法と同じなのだ。

{1,2,3,4,5}と5個の順序ある要素からなるvectorでは、最初の要素は0番目で、最後の要素は4番目だ。1番目から5番目ではない。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    v.at(0) ; // 最初の要素: 1
    v.at(4) ; // 最後の要素: 5
}
~~~

ではなぜなのか。なぜvectorではn個の要素の順番を0番目からn-1番目として表現するのか。

実はC++に限らず、現在使われているすべてのプログラミングはインデックスを0から始めている。かつてはインデックスを1からはじめる言語も存在したが、そのような言語は今は使われていない。

この疑問はエドガー・ダイクストラがWhy numbering should start at zero(EWD831)で解説している。

2, 3, ..., 12の範囲の自然数を表現するのに、慣習的に以下の4つの表記がある。

$$
a) 2 \le i \lt 13
$$


$$
b) 1 \lt i \le 12
$$

$$
c) 2 \le i \le 12
$$

$$
d) 2 \lt i \lt 13
$$

C++のイテレーターはa)を元にしている。

この4つのうち、a)とb)は上限から下限を引くと、範囲にある自然数の個数である11になる。

この性質はとても便利なのでC++でも、イテレーター同士の引き算ができるようになっている。イテレーターi, j($i \le j$)で`j - i`をした結果はイテレーターの範囲の要素の個数だ。

~~~cpp
int main()
{
    std::vector<int> v = {2,3,4,5,6,7,8,9,10,11,12} ;

    auto i = std::begin(v) ;
    auto j = std::end(v) ;

    // 11
    // イテレーターの範囲の要素の個数
    std::cout << j - i << "\n"s ;

    ++i ; // 先頭の次の要素を指す
    // 10
    std::cout << j - i ; 
}
~~~

a)とb)はどちらがいいのだろうか。b)を元にイテレーターを設計すると以下のようになる。

~~~c++
// b)案を採用する場合
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    // 最初の一つ前の架空の要素を指す
    auto i = std::begin(v) ;
    // 最後の要素を指す
    auto j = std::end(v) ;

    // 最初の要素を指すようにする。
    ++i ;

    // iが最後の要素を指すとループを抜ける
    for ( ; i != j ; ++i )
    {
        std::cout << *i ;
    }
    // 最後の要素を処理する
    std::cout << *i ;
    

}
~~~

a)の方がよい。

~~~cpp
// a)案を採用する場合
int main()
{
    std::vector<int> v = {1,2,3,4,5} ;

    // 最初の要素を指す
    auto i = std::begin(v) ;
    // 最後の次の要素を指す
    auto j = std::end(v) ;

    // iが最後の次の要素を指すとループを抜ける
    for ( ; i != j ; ++i )
    {
        std::cout << *i ;
    }

    // すべての要素について処理を終えている
}
~~~

b)案では末尾から先頭まで後ろ向きに要素を一巡する操作はやりやすいが、実際には先頭から末尾まで一巡する操作の方が多い。

C++では要素の順番を数値で指し示す時、最初の要素は0番目であり、次の要素は1番目であり、N個目の要素はN-1番目になっている。この数値で指し示すことを`添字`とか`インデックス`というがなぜ最初の要素を1番目にしないのか。

C++では様々なところでa)を採用している。これを添字に適用すると、最初の要素が1番目から始まる場合、N個の要素を参照する添字の範囲は$1 \le i \lt N+1$になる。そのような場合、以下のようなコードになる。

~~~c++
// 最初の要素が1番目の場合
int main()
{
    // 5個の要素を持つvector
    std::vector<int> v = {1,2,3,4,5} ;

    // iの値の範囲は1から5まで
    for ( std::size_t i = 1 ; i < 6 ; ++i )
    {
        std::cout << v.at(i) ;
    }    
}
~~~

要素数は5個なのに6が出てくる。最初の要素が0番目の場合、N個の要素を参照する添字の範囲は$0 \le i \lt N$になる。

~~~cpp
// 最初の要素が0番目の場合
int main()
{
    // 5個の要素を持つvector
    std::vector<int> v = {1,2,3,4,5} ;

    // iの値の範囲は0から5まで
    for ( std::size_t i = 0 ; i < 5 ; ++i )
    {
        std::cout << v.at(i) ;
    }    
}
~~~

一貫性のために最初の要素は0番目となっている。

また、空の集合にも対応できる。


~~~cpp
int main()
{
    // 空
    std::vector<int> v ;

    // 空なので何も出力されない
    for (   auto i = std::begin(v), j = std::end(v) ;
            i != j ; ++i )
    {
        std::cout << *i ;
    }
}
~~~

変数vは空なので`i != j`はfalseとなり、for文の中の文は一度も実行されない。