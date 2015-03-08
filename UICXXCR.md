# Usagi Ito's C++ Cording Rule Regulation 1.2.0

## C++言語標準

- C++14 に準拠する
    - 原則として処理系の独自拡張機能は用いない
        - インクルードガードは例外として `#pragma once` を用いて構わない
        - OpenMP など標準化された拡張は `clang++` または `g++` で使用可能であれば用いて構わない
        - OpenMP 自動並列化を行う `g++` の Parallel Mode 拡張は用いて構わない
- `clang++` ないし `g++` のコンパイルオプション `-Wall -pedantic` で警告なしとする

## 処理系への対応

### GCC/libstdc++

- `g++` のデバッグビルド時には `-D_GLIBCXX_DEBUG -D_GLIBCXX_DEBUG_PEDANTIC` を付ける

### Clang/libc++

- `clang++ -stdlib=libc++` のデバッグビルド時には `-D_LIBCPP_DEBUG`

### Emscripten

- `em++` のデバッグビルド時には `-s SAFE_HEAP=1` を付ける
- `em++` のデバッグビルド時には `-s ASSERTIONS=2` を付ける
- `em++` のデバッグビルド時には `-s DEMANGLE_SUPPORT=1` を付ける
- `em++` のリリースビルド時には `-s ALLOW_MEMORY_GROWTH=0 -s TOTAL_MEMORY=1073741824` のようにメモリー量を明示する

## 命名

- 命名規則は snake_case とする
    - 例外として、テンプレートの型仮引数、Cプリプロセッサーのマクロ定義は SNAKE_CASE とする
- 原則として `_` をプリフィックスにもサフィックスにも付けない
- 関数は動詞的、それ以外は名詞的な命名をアメリカ英語の単語を基に行う
    - 例外として日付は [ISO8601](http://ja.wikipedia.org/wiki/ISO_8601) 形式に準じる
- アクセサーは `get_` `set_` プリフィックスを用いる
- 状態を真偽値で表す変数や関数では `is_` `has_` プリフィックスを用いる
- 数量を表す変数や関数では `count_of_` `number_of_` プリフィックスを用いる 

## ソースファイル

- ファイル名のサフィックスは `.cxx` `.hxx` を用いる
- `namespace` に対応したディレクトリー階層へ配置する
- クラス、関数の宣言と実装は可能な限りファイルを分離する
    - 翻訳単位を分離したくない場合に向け、 `.hxx` から `.cxx` を `#include` するフラグを `#ifdef` で用意して構わない
- 文字コードは UTF-8N とする

## インデント

- 1 indent = 2 spaces
- tab は用いない
- ワンライナーを除く全ての `{` `}` スコープ内部をインデントする
- アクセス修飾 `public:` `protected:` `private:` はインデントしない
- `switch` における `case xxx:` `default:` ラベルはインデントする
- `goto` のターゲットとする `xxx:` ラベルはインデントしない

## ホワイトスペース

- 関連したステートメントのグループの区切りには空行を入れる
- 空行は1行のみとする
- キーワードごと直後に空白を挿入する
    - 但し、単行演算子の直後には不要とする(例: `*some_object`)
- ポインター(`*`)、参照(`&` `&&`)の直後に空白を挿入する
    - 前には空白を入れない

```cpp
// ダメ
std::int32_t x = 123;
auto *hoge = &x;
x+=2;
x*=3;
if(const fuga = initialize_fuga())
  fuga->piyo("pokopen"s);

// ヨイ
std::int32_t x = 123;
auto* hoge = &x;
x += 2;
x *= 3;

if ( const fuga = initialize_fuga() )
  fuga->piyo( "pokopen"s );
```

## 改行

- 可読性向上のための改行はどれだけ自由に入れても構わない
- 1行あたりの桁数は100桁を限度とする

```cpp
// ダメ
const auto toufu = myouga( shouyu, daizu * daizu, miso * miso ) + shouga( surioroshi, mijingiri, usugiri );

// ヨイ
const auto toufu
  = myouga( shouyu, daizu * daizu, miso * miso )
  + shouga( surioroshi, mijingiri, usugiri )
  ;
  
// ヨイ（この程度ならばヤリスギという事はない）
const auto toufu
  = myouga
    ( shouyu
    , daizu * daizu
    , miso * miso
    )
  + shouga
    ( surioroshi
    , mijingiri
    , usugiri
    )
  ;
  
// ヨイ（そもそも特別な理由が無ければ分割した方がヨイ。
//      デバッグ効率も良く、リリースビルド時には最適化される）
const auto toufu_left
  = myouga
    ( shouyu
    , daizu * daizu
    , miso * miso
    )
  ;

const auto toufu_right
  = shouga
    ( surioroshi
    , mijingiri
    , usugiri
    )
  ;

const auto toufu = toufu_left + toufu_right;
```

## ブレース `{` `}`

- [BSD/エリック・オールマン スタイル](http://ja.wikipedia.org/wiki/%E5%AD%97%E4%B8%8B%E3%81%92%E3%82%B9%E3%82%BF%E3%82%A4%E3%83%AB#BSD.2F.E3.82.AA.E3.83.BC.E3.83.AB.E3.83.9E.E3.83.B3.E3.81.AE.E3.82.B9.E3.82.BF.E3.82.A4.E3.83.AB) に準じる
- `{` はコードブロックのインデント位置へ改行して合わせる
- 対応する `{` と `}` は桁を揃える
- 単一のステートメントに対し不要な場合には `{` `}` によりブロックスコープを定義しない
- 解読が困難でないワンライナーは許容する

```cpp
// ダメ
namespace {

  struct hoge {
    int a;
  };
  
  auto main() -> int {
    return []{ return 123; }();
  }
  
}

// ヨイ
namespace
{

  struct hoge
  {
    int a;
  };
  
  auto main()
    -> int
  { // <-- code `{` after a break line 
    return []{ return 123; }(); // one-liner is OK
  }
  
}
```

## 名前空間

- そのソースファイルのみに依存するグローバル的に振る舞わせるシンボルは無名名前空間を用いる
- 機能ごとに細かくなり過ぎないようにネストを含めて分類し、ディレクトリー配置と併せて機能ごと適切な名前空間を用いる
- 実装詳細を名前空間で分離する場合にはその名前空間の下へ `namespace detail` を用いて実装する
- 原則として `inline namespace` は用いない

## 定数

- 適切な名前空間の下に `namespace constants` を設け `template` 化した `constexpr` で定義する
    - `#define` `enum` は用いない
    - `enum class` は有意なグルーピングに対してのみ用いる
    - 型や範囲の `static_assert` が必要な場合を除き関数を用いない
        - 必要な場合は `namespace detail` に関数版を用意し、一般には非関数として呼び出せるよう定義する
- `π`([円周率](http://ja.wikipedia.org/wiki/%E5%86%86%E5%91%A8%E7%8E%87)) `e`([ネイピア数](http://ja.wikipedia.org/wiki/%E3%83%8D%E3%82%A4%E3%83%94%E3%82%A2%E6%95%B0)) `γ`([オイラーの定数](http://ja.wikipedia.org/wiki/%E3%82%AA%E3%82%A4%E3%83%A9%E3%83%BC%E3%81%AE%E5%AE%9A%E6%95%B0)) とその定数倍や指数関数など [`boost::math::constants`](http://www.boost.org/doc/libs/1_37_0/libs/math/doc/sf_and_dist/html/math_toolkit/toolkit/internals1/constants.html) に定義がある場合はこれを優先して用いる

```cpp
/// @brief 月
namespace moon
{
  /// @brief 定数
  namespace constants
  {
    /// @brief 離心率
    /// @tparam T 取得する離心率の型
    template < typename T >
    constexpr auto orbital_eccentricity = T( 0.0548799 );
  }
}
```

```cpp
using pi = boost::math::constants::pi;
auto some_volume = 4.0f * pi<float>() * std::powf( some_radius, 3.0f ) / 3.0f ;
```

## 数値と単位系

- 原則として単位次元の有効な物理量は [`boost::units`](http://www.boost.org/doc/libs/1_57_0/doc/html/boost_units.html) を用いる
    - 例外として、実装詳細でパフォーマンス上の都合、行列演算、その他複雑な計算等の為にコスト的にやむを得ない場合には物理量に対しても生の値を用いて構わない
- 原則として単位次元はSI単位系を採用する
    - 例外として適当な場合には質量の基本単位としてグラム、あるいは他の単位においてもSI接頭辞と組み合わせた単位を用いて構わない
    - 例外として適当な場合には情報量の接頭辞として[IEEE1541](http://en.wikipedia.org/wiki/IEEE_1541-2002)の定める2進接頭辞を用いて構わない
- `<cstdint>` に含まれない組み込み型(Int128, Float128 等)や任意精度が必要な場合には [`boost::multiprecision`](http://www.boost.org/doc/libs/1_57_0/libs/multiprecision/doc/html/index.html) を用いる
    - パフォーマンスボトルネックが顕著でない場合、整数については cpp\_int 、 実数については cpp\_bin\_float, cpp\_dec\_float 実装を用いる
- 10進数の数値リテラルは小数点の存在する位置から見て3桁ごとに`'`で区切る
- 2進数、16進数の数値リテラルは 1 byte 境界ごとに `'` で区切る
- 翻訳時に最適化可能な計算は計算結果の値ではなく意味の理解しやすい単位での計算式でコードする

```cpp
#include <iostream>
#include <boost/units/io.hpp>
#include <boost/units/pow.hpp>
#include <boost/units/systems/si.hpp>
#include <boost/units/systems/si/prefixes.hpp>

auto main()
  -> int
{
  using namespace std;
  using namespace boost::units;
  using namespace boost::units::si;
  
  auto earth_gravity = 9.8 * meter / pow< 2 >( second );
  auto earth_radius  = 6.4e+6 * meter;
  auto gravitational_constant = 6.7e-11 * newton * pow< 2 >( meter ) / pow< 2 >( kilogram );

  cout
    << "mass of the Earth: "
    << earth_gravity * pow< 2 >( earth_radius ) / gravitational_constant
    ;
}
```

(※このコードを[実行](http://melpon.org/wandbox/permlink/T3A5S0nuekWdQIQB)すると `mass of the Earth: 5.99116e+24 kg` を得られる。)

```cpp
// ダメ
/// @brief 真空中の光速
const auto c = 2.99792458e+8 * meter / second;
/// @brief 1日
const auto day = 86400 * second;

// ヨイ
/// @brief 真空中の光速
const auto c = 2.997'924'58e+8 * meter / second;
/// @brief 1日
const auto day = 24 * 60 * 60 * second;
```


## 文字列

- 基本的に `std::string` を用いる
    - プラットフォームやライブラリーの為に生文字列(`const char*`)や`std::wstring` `std::u16strin` `std::u32string`が必要となる場合は、最小限のスコープで `std::string::data()` で取得して取り扱う
- `using namespace std::string_literals` を用いて `"hoge"s` リテラルで記述する
    - `std::string("hoge")` は用いない
- レキシカルキャストは `std::to_string` を用いる
    - 技巧的な変換(`std::cout << +std::uint8_t( 123 )` など)を用いない
    - 暗黙的な変換(`std::cout << 123` など)を用いない

```cpp
// ダメ
const auto message = "values are: ";
const auto value_1 = std::uint8_t( 123u );
const auto value_2 = std::size_t( value ) + 123ull;
std::cout << message << +value_1 << " " << value_2;

// ヨイ
const auto message = "values are: "s;
const auto value_1 = std::uint8_t( 123u );
const auto value_2 = std::size_t( value_1 ) + 123ull;
std::cout
  << message
  << std::to_string( value_1 )
  << " "
  << std::to_string( value_2 )
  ;
```

## 変数

- 変数は1行に1つずつ宣言する
- 要素集合を表す場合には複数形を用いる
    - 例外として単数形と区別する必要がありかつその区別が困難な単語の場合や複数形の複数形などを表す場合にはサフィックスに `_set` を用いる
- 省略形・略語を使わない
- ポインターや参照など型を示すための接頭辞を付けない
- ごく小さなスコープ内で一時的に必要な命名しがたい変数があるなどのやむを得ない場合を除き `a` `b` などの意味の無い命名をしない
- 連続した関連性の高い変数は型やシンボルを space で[桁揃え](http://ja.wikipedia.org/wiki/%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E4%BD%9C%E6%B3%95#.E6.A1.81.E4.BD.8D.E7.BD.AE.E5.90.88.E3.82.8F.E3.81.9B)する。一時の diff の煩わしさよりも常に最新のコード可読性を優先する
- 他の変数や関数の戻り値の型に依存する変数は `decltype` を用いて宣言する
    - 初期化可能な場合は `auto` を用いる
- 原則として可能なものは `constexpr` `const` を用いて定数とする

```cpp
// ダメ
std::uint8_t* ptrBtn1;
std::string wrnmsg, errmsg;
std::uint32_t x, y = 123;
std::uint32_t two_x;
std::uint32_t two_y = y * 2;
float some_datum1, some_datum2;
std::vector<float> some_data;
std::vector<decltype(some_data)> some_datas;

// ヨイ
std::uint8_t* button1;

std::string warning_message;
std::string error_message;

std::uint32_t x;
decltype(x)   two_x;

const std::uint32_t y     = 123;
const auto          two_y = y * 2;

float some_datum1;
float some_datum2;
std::vector< decltype( some_datum1 ) > some_data;
std::vector< decltype( some_data   ) > some_data_set;
```

## 関数

- 引数リストは入力的なものを手前、出力的なものがあれば後ろへ配置する
- 原則として仮引数の型は `const` 修飾する
- 原則としてメンバー関数は `const` 修飾する
- `auto f();` `decltype(auto) f();` を用いる
    - 原則として戻り値の型は型推論とする
- 原則として `noexcept` とする
- 原則として仮引数を1つだけ取る構築子は `explicit` 修飾する
- 原則としてフリー関数を活用し粗結合性のコードを心がける。
    - クラスメンバー関数の乱用による不要な密結合化を避ける

```cpp
// ダメ
long_name_parameter_type_1& some_long_name_function( long_name_parameter_type_1& p, const long_name_parameter_type_2 q, const long_name_parameter_type_3 r )
{
  p.some_effect_1( p );
  p.some_effect_2( q );
  return p;
}

// ヨイ
decltype( auto ) some_long_name_function
( long_name_parameter_type_1& p
, long_name_parameter_type_2  q
, long_name_parameter_type_2  r
) noexcept
{
  p.some_effect_1( p );
  p.some_effect_2( q );
  return p;
}
```

## `if` `else`

- ブレース `{` `}` のルールに準じる

```cpp
// ダメ
if ( abc ) {
  ppp();
  qqq();
} else if ( def ) {
  rrr();
  sss();
} else {
  ttt();
}

// ヨイ
if ( abc )
{
  ppp();
  qqq();
}
else if ( def )
{
  rrr();
  sss();
}
else
  ttt();
```

## `switch`

- `case` `default` は `switch` の桁に対してインデントする
- カスケーディングを許容する

```cpp
// ダメ
switch {
case aaa:
  xxx();
  break;
case bbb:
  xxx();
  break;
case ccc:
  yyy();
  break;
default:
  xxx();
}

// ヨイ
switch
{
  case aaa:
  case bbb:
    xxx();
    break;
  case ccc:
    yyy();
    break;
  default:
    xxx();
}
```

## 条件演算子

- 用いて構わない
- ごく単純な場合を除き複数行へ分離して可読性を確保する

```cpp
const auto x = a ? b : c;
const auto y = hoge()
  ? fuga
  : piyo
  ;
```

## クラス

- `class` を用いる
    - `struct` は [POD型](http://en.wikipedia.org/wiki/Plain_old_data_structure) または全てのデータメンバーが `public:` な標準配置を満たす型に限り許容する
- 原則として `public:` なネストしたクラスは実装しない
- 原則として全てのデータメンバーは構築子で定義順に初期化する
- 原則としてメンバー型、メンバー変数、メンバー関数をそれぞれ `public:` `protected:` `private:` の順に定義する
- 必要な場合を除き `final` 修飾を用いない
- 必要な場合を除き省略可能な `this` を用いない

## 列挙

- `enum class` を用いる
    - `enum` は用いない
- 基底型を明示する

## テンプレート

- 型引数には `typename` を用いる
    - `class` は用いない 

## ポインター

- 取り扱いには `std::unique_ptr` ないし `std::shared_ptr` を用いる
- null 値として `nullptr` を用いる
    - null 値として `NULL` `0` を用いない

## 動的メモリー確保

- 原則として ``std::vector` 等のコンテナーを用いる
- 必要な場合は `new` `new[]` `delete` `delete[]` を用いる
    - `malloc` `free` 等のC互換アロケーターは用いない

## キャスト

- `static_cast` `dynamic_cast` `std::static_pointer_cast` `std::dynamic_pointer_cast` 等のC++スタイルのキャストを用いる
    - Cスタイルキャストは用いない
- `reinterpret_cast` は必要に応じて用いて構わない
    - 但し、CPUのメモリーアクセス命令に依存性を伴う `reinterpret_cast` は用いない

## 演算子

- 比較演算子は、比較される側の対象を左側、参照値(C++言語機能の参照の意味ではない)とする側を右側へ用いる
- `++` `--` は後置きする意味が無い場合には前置きとする
- `sizeof` は `(` `)` を併用する
- `and` `or` `xor` `not` を用いる
    - `&&` `||` `^` `!` は用いない
- `&` `|` を用いる
    - `bitand` `bitor` は用いない
- `&=` `|=` `^=` `!=` を用いる
    - `and_eq` `or_eq` `xor_eq` `not_eq` は用いない
- `~` を用いる
    - `compl` は用いない

## ラムダ式

- デフォルトのキャプチャーモードは明示する
    - 例外として何もキャプチャーしない場合は `[]` で構わない

## 並行処理

- 単純なハードウェアスレッド並行処理は `<thread>` `<future>` で対応する
- よしなに `std::thread::this_thread::yield()` を忘れずに入れる

## Cプリプロセッサー

- 可能な限り用いない
- `#ifdef X` を `#if defined(X)` に優先して用いる
- 複数行からなるCプリプロセッサーはインデントする

## デバッグビルド・リリースビルド向けの分岐処理

- `#ifdef NDEBUG` をデバッグビルド向けの特殊化に用いる
    - `#ifndef NDEBUG` をリリースビルド向けの特殊化に用いる

※`NDEBUG` Cプリプロセッサーマクロはビルドツール cmake を用いる場合には `-DCMAKE_BUILD_TYPE=release` ないし `-DCMAKE_BUILD_TYPE=relwithdebinfo` の場合に自動的に付与される

## テストとモック

- 一時的な実験用のコードなどを除き、プロダクトのコードは [googletest](https://code.google.com/p/googletest/), [googlemock](https://code.google.com/p/googlemock/) を用いて設計に基づいた単体テストを用意する

```cpp
// 実際にはこれは開発中のソフトウェアの一部機能で .hxx にインラインないし .hxx + .cxx で定義されている部分
// この例では簡単のため直接テスト用のソースに記述する
#include <cstdint>
auto f( const std::uint8_t x ) -> std::uint8_t { return x * x; }

// gtest を用いたテストケースの記述例
#include <gtest/gtest.h>
TEST( test1, 0 ) { EXPECT_EQ( 0, f( 0 ) ); }
TEST( test1, 1 ) { EXPECT_EQ( 1, f( 1 ) ); }
TEST( test1, 2 ) { EXPECT_EQ( 4, f( 2 ) ); }
TEST( test1, 4 ) { EXPECT_EQ( 16, f( 4 ) ); }
TEST( test1, 8 ) { EXPECT_EQ( 64, f( 8 ) ); }
TEST( test1, 16 ) { EXPECT_EQ( 256, f( 16 ) ); }
```

`clang++ -std=c++14 -stdlib=libc++ a.cxx -o a.out -lgtest_main -lgtest -lpthread` して実行すれば以下のようにテスト結果が得られる。

```zsh
Running main() from gtest_main.cc
[==========] Running 6 tests from 1 test case.
[----------] Global test environment set-up.
[----------] 6 tests from test1
[ RUN      ] test1.0
[       OK ] test1.0 (0 ms)
[ RUN      ] test1.1
[       OK ] test1.1 (0 ms)
[ RUN      ] test1.2
[       OK ] test1.2 (0 ms)
[ RUN      ] test1.4
[       OK ] test1.4 (0 ms)
[ RUN      ] test1.8
[       OK ] test1.8 (0 ms)
[ RUN      ] test1.16
a.cxx:12: Failure
Value of: f( 16 )
  Actual: '\0'
Expected: 256
[  FAILED  ] test1.16 (0 ms)
[----------] 6 tests from test1 (0 ms total)

[----------] Global test environment tear-down
[==========] 6 tests from 1 test case ran. (0 ms total)
[  PASSED  ] 5 tests.
[  FAILED  ] 1 test, listed below:
[  FAILED  ] test1.16

 1 FAILED TEST
```

## ロギング

- [wonderland.log](https://github.com/usagi/wonderland.log) を推奨とする
- 以下のライブラリーの使用は非推奨とする
    - [glog](https://code.google.com/p/google-glog/) はスタックトレースの取得にプラットフォーム依存性が強く Emscripten での使用が難しいため非推奨とする

```cpp
#ifdef NDEBUG
  #define WRP_WONDERLAND_LOG_DISABLE
#endif

#include <wonder_rabbit_project/wonderland/log.hxx>

int main ( const int count_of_argument, const char* const* const arguments )
{
  using namespace wonder_rabbit_project::wonderland;
  LOG  << "hello, wonderland.log!";
  LOGE << "error test";
  LOG_IF_FATAL( log::if_fatal::exit );
  LOGF << "fatal test";
}
```

## 実行時引数の取り扱い

- [cmdline](https://github.com/tanakh/cmdline) を推奨とする
- 以下のライブラリーの使用は非推奨とする
    - [gflags](https://code.google.com/p/gflags/) は pthread 依存性があり Emscripten での使用が難しいため非推奨とする

```cpp
#include <iostream>
#include <string>
#include <cmdline.h>

int main ( const int count_of_argument, const char* const* const arguments )
{
  using namespace std;
  cmdline::parser p;
  p.add<string> ( "long name", 'n', "description", false, "default value" );
  p.parse_check ( count_of_argument, const_cast<char**> ( arguments ) );
  cout << "long name is: " << p.get<string> ( "long name" );
}
```

## [Doxygen](http://www.doxygen.jp/commands.html)

- Doxygen コメントの開始は `///` を用いる 
- `@file` `@brief` `@param` `@tparam` `@return` `@exception` が有効なソースファイル冒頭、名前空間、クラス、フリー関数、列挙、メンバー変数、メンバー関数、型の別名、メンバー型についてDoxygenコメントを必ず記述する
    - `@file` は引数を付けずに記述する
- 動作が複雑な場合には `@details` にその複雑な動作をできるだけわかりやすく簡潔に解説する
    - `@details` を `@brief` の後に空行を挟む事で省略する記述方法は用いない
    - `@exception` の代わりに `@throw` を用いない
- `@note` `@warning` については対象に思うことがあれば些細な事でも記述する
- 仕様変更において `@deprecated` `@obsolete` となった対象に対しては適切にマークする
- `@author` `@version` `@date` は用いない

```cpp
/// @brief value をホゲして成否を戻す
/// @param value ホゲする値
/// @return 成功した場合は true
/// @exception std::out_of_range value が unorm 値 でない場合に送出
auto hoge( const float value ) -> bool;
```

## プロジェクト管理

- [cmake](http://www.cmake.org/) を一次的に用いる
- [git](http://git-scm.com/) を用いてリポジトリーのバージョン管理を行う
    - 複数の開発者や master ブランチの品質を維持する目的で開発工程を定義する場合には [github+test-flow](github+test-flow.md) を推奨とする

## リフォーマッターの設定

### [astyle](http://astyle.sourceforge.net/)

#### コマンドラインオプションで指定する場合

```astyle
astyle \
  --style=break \
  --indent=spaces=2 \
  --indent-switches \
  --indent-namespaces \
  --indent-preproc-define \
  --indent-col1-comments \
  --break-blocks \
  --pad-oper \
  --pad-paren \
  --delete-empty-lines \
  --fill-empty-lines \
  --align-pointer=type \
  --align-reference=type \
  --remove-brackets \
  --keep-one-line-blocks
  --keep-one-line-statements \
  --convert-tabs \
  --remove-comment-prefix \
  --max-code-length=100 \
  --mode=c \
  --lineend=linux \
  --suffix=none \
  --options=none \
  `find \
    -type f \
    -iname '*.cxx' \
    -or \
    -iname '*.hxx'`
```

#### `.astylerc` で設定する場合

```astyle
style=break
indent=spaces=2
indent-switches
indent-namespaces
indent-preproc-define
indent-col1-comments
break-blocks
pad-oper
pad-paren
delete-empty-lines
fill-empty-lines
align-pointer=type
align-reference=type
remove-brackets
keep-one-line-blocks
keep-one-line-statements
convert-tabs
remove-comment-prefix
max-code-length=100
mode=c
lineend=linux
suffix=none
```

