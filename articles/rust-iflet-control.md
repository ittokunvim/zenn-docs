---
title: "Rustで配列内の特定の値のみ処理する様々な方法"
emoji: "🦀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["rust", "iflet"]
published: true
---
ここでは様々な方法を駆使して配列内の特定の値を処理する様々な方法が書かれています。

ここでは`filter`メソッドと`find`メソッドを使ったやり方で、
特定の値を抽出する方法について説明していきます。

`find`メソッドを使用したやり方は、趣味でRustのBevyでゲーム開発をしている中で見つけたもので、
`filter`メソッドはこの記事を公開した時に記事を見てくれた方から教えてもらったものです。

それぞれのメソッドの書き方や、違いなども説明していきますのでよろしくお願いします。

## まずはデータを定義

まずは検証を行うためのデータを定義します。

今回は人のデータ`Person`構造体と、性別のタイプ`Gender`列挙型を定義していきます。

`derive`には比較するための`PartialEq`、コンソールに出力するための`Debug`を追加します。

あとは値をわかりやすく定義するために`new`メソッドを追加しています。

`new`メソッドを追加することで、`Person::new(name, age, gender)`の形式で
値を定義することができるようになります。

```rust
#[derive(PartialEq, Debug)]
struct Person {
    name: String,
    age: usize,
    gender: Gender,
}

#[derive(PartialEq, Debug)]
enum Gender {
    Male,
    Female,
}

impl Person {
    fn new(name: &str, age: usize, gender: Gender) -> Self {
        let name = name.to_string();
        Person {
            name,
            age,
            gender,
        }
    }
}
```

次に`main`関数内で値を定義していきましょう！
以下のコードでは複数の`Person`を定義し、その値を配列の変数に入れてまとめています。

```rust
fn main() {
    // まずはデータを定義
    let john = Person::new("John", 20, Gender::Male);
    let marry = Person::new("Marry", 21, Gender::Female);
    let bob = Person::new("Bob", 18, Gender::Male);
    let carol = Person::new("Carol", 24, Gender::Female);
    let person_list = [john, marry, bob, carol];
}
```

これで準備はできました！次は実践していきます！

## for文を使った処理

説明の前に`for`文での処理を紹介します。
悪い例ではないのですが、これから紹介する実装方法に比べて、少し分かりにくいものになっています。

以下のコードでは配列を`for`文でループして一番上に「条件に合わない値を飛ばす」処理を追加して
「条件に合った値を出力する」処理が書かれています。

このコードでも動作はします。「値をループさせて特定の値でなければ飛ばして、特定の値なら出力する」
みたいにパッと見て処理の内容がわかって分かりやすいのですが、条件式が分かりづらく感じます。

```rust
fn main() {
    // ...
    forloop(&person_list);
}

fn forloop(person_list: &[Person; 4]) {
    for (index, person) in person_list.iter().enumerate() {
        if person.gender != Gender::Male {
            continue;
        }
        println!("index: {}", index);
        println!("person: {:?}", person);
    }
}
```

## filterメソッドを使った処理

では`filter`メソッドを使用した処理を見ていきましょう。
このコードは上記のコードと同じ結果になります。

ここでは`Person`のデータが入った配列の変数をループさせ、
`filter`メソッドで条件に合った値のみを抽出し、その値のみを処理するといった流れになります。

以下のコードは`for`文の例よりも、見やすく分かりやすいように見えます。
特に条件文がわかりやすくなったことで「どのような値を抽出するのか」が
パッと見てわかるようになったと思います。

```rust
fn main() {
    // ...
    loopfilter(&person_list);
}

fn loopfilter(person_list: &[Person; 4]) {
    person_list
        .iter()
        .enumerate()
        .filter(|(_index, person)| person.gender == Gender::Male)
        .for_each(|(index, person)| {
            println!("index: {}", index);
            println!("person: {:?}", person);
        });
}
```

## findメソッドを使った処理

次に`find`メソッドを使用した処理を見ていきましょう。
このコードは上記のコードと少し異なる結果になります。

ここでは`Person`のデータが入った配列の変数をループさせ、
`find`メソッドを使って値を取り出し、その値を`Some((index, person))`に代入します。
そして代入された値は`find`メソッド下の鉤括弧の中で処理されます。

結果が少し異なるといった点は、`find`メソッドで条件に合った最初の値のみ処理が実行される点です。
つまりループをして`find`メソッドで条件に合った値が見つかった場合、
それ以降にループされる値がもしも条件に合っていても処理が実行されず無視されます。

つまり最初の値のみ処理されると言うことは`Some((index, person))`に代入される値は1つと言うことになります。

`find`メソッドのメリットとしてはループされる値の中で、
条件に合った1つだけの値を取得したい時などに良いです。

```rust
fn main() {
    // ...
    ifletfind(&person_list);
}

fn ifletfind(person_list: &[Person; 4]) {
    if let Some((index, person)) = person_list
        .iter()
        .enumerate()
        .find(|(_, person_value)| person_value.gender == Gender::Male)
    {
        println!("index: {}", index);
        println!("person: {:?}", person);
    }
}
```

### if letとは

`find`メソッドの使用例に`if let`を使いましたのでここで簡単な説明をしています。

`if let`の説明は「Rust By Example 日本語版」で見ることができます。

https://doc.rust-jp.rs/rust-by-example-ja/flow_control/if_let.html

公式が言うには、`match`式を使うほど大掛かりな処理ではなく、
小さな処理を書く時に`match`式だと不自然な書き方になってしまう。

その際に`if let`を使うと綺麗に書くことができるよと書かれています。

つまり、以下のコードのように`number`が1だったら鉤括弧の中の処理を実行するよと言うことです。
もしも`number`が1以外だったら処理は実行されないと言うことですね。
`number`が1以外の時の何か処理を書いておきたいときも、元は`if`文なので`else`を使うこともできるみたいです。


```rust
let number = 1;

if let 1 = number {
    println!("number is 1");
} else {
    println!("number is not 1");
}
```

## コード全容

今回作成したRustのコードは以下のようになります。

```rust
#[derive(PartialEq, Debug)]
struct Person {
    name: String,
    age: usize,
    gender: Gender,
}

#[derive(PartialEq, Debug)]
enum Gender {
    Male,
    Female,
}

impl Person {
    fn new(name: &str, age: usize, gender: Gender) -> Self {
        let name = name.to_string();
        Person {
            name,
            age,
            gender,
        }
    }
}

fn main() {
    // まずはデータを定義
    let john = Person::new("John", 20, Gender::Male);
    let marry = Person::new("Marry", 21, Gender::Female);
    let bob = Person::new("Bob", 18, Gender::Male);
    let carol = Person::new("Carol", 24, Gender::Female);
    let person_list = [john, marry, bob, carol];

    forloop(&person_list);
    loopfilter(&person_list);
    ifletfind(&person_list);
}

fn forloop(person_list: &[Person; 4]) {
    for (index, person) in person_list.iter().enumerate() {
        if person.gender != Gender::Male {
            continue;
        }
        println!("index: {}", index);
        println!("person: {:?}", person);
    }
}

fn loopfilter(person_list: &[Person; 4]) {
    person_list
        .iter()
        .enumerate()
        .filter(|(_index, person)| person.gender == Gender::Male)
        .for_each(|(index, person)| {
            println!("index: {}", index);
            println!("person: {:?}", person);
        });
}

fn ifletfind(person_list: &[Person; 4]) {
    if let Some((index, person)) = person_list
        .iter()
        .enumerate()
        .find(|(_, person_value)| person_value.gender == Gender::Male)
    {
        println!("index: {}", index);
        println!("person: {:?}", person);
    }
}
```

結果は以下のようになります。

```sh
$ cargo run
#
# index: 0
# person: Person { name: "John", age: 20, gender: Male }
# index: 2
# person: Person { name: "Bob", age: 18, gender: Male }
# index: 0
# person: Person { name: "John", age: 20, gender: Male }
# index: 2
# person: Person { name: "Bob", age: 18, gender: Male }
# index: 0
# person: Person { name: "John", age: 20, gender: Male }
```

## まとめ

今回は配列内の特定の値のみ処理する様々な方法について説明してみました。

`find`メソッドを使用した例で`if let`の使い方が分からずその使用を避けていましたが、
ゲーム開発時に使える場面が出てきてこれは使えるぞ！と感動しました。
この感動をバネにこの記事を書いたわけですが、本当に便利な機能だと思います。

`filter`メソッドは記事のコメントで教えてもらった事で、このコードも使えるぞ！と言うことで
記事に`filter`メソッドの実装方法を追加して更新しました。

もしこの記事が何かの参考になれたら幸いです。
ここまで読んでいただきありがとうございました！
