---
title: "Rustで配列内の特定の値のみ処理する方法"
emoji: "🦀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["rust", "iflet"]
published: true
---
ここでは`if let`を使用して配列内の特定の値のみ処理する方法が書かれています。

趣味でRustのBevyでゲーム開発をしているのですが、その中で`if let`を使用した処理で、
コードの行数や可読性が上がったので共有してみようかと思いました。

以下に記すコードは見た目がスッキリするし、様々な局面で使用でき応用力があると感じました。

それでは見ていきましょう！

## if letとはなんぞや

`if let`の説明は「Rust By Example 日本語版」で見ることができます。

https://doc.rust-jp.rs/rust-by-example-ja/flow_control/if_let.html

公式が言うには、`match`式を使うほど大掛かりな処理ではなく、
小さな処理を書く時に`match`式だと不自然な書き方になってしまう。

その際に`if let`を使うと綺麗に書くことができるよと書かれています。

つまり、以下のコードのように`number`が1だったら鉤括弧の中の処理を実行するよと言うことです。
もしも`number`が1以外だったら処理は実行されないと言うことですね。
`number`が1以外の時の何か処理を書いておきたいときも、元は`if`文なので`else`を使うこともできるみたいです。


```rust
// めちゃくちゃ手抜きコード
let number = 1;

if let 1 = number {
    println!("number is 1");
} else {
    println!("number is not 1");
}
```

## まずはデータを定義

`if let`を試すためにまずは検証できるデータを定義します。

今回は人のデータ`Person`構造体と性別のタイプ`gender`列挙型を定義していきます。

`derive`には比較するための`PartialEq`、コンソールに出力するための`Debug`を追加します。

あとは値をわかりやすく定義するために`new`メソッドを追加しています。

これで`Person::new(name, age, gender)`と言う形式で値を定義することができます。

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

その前に今回説明する`if let`文を使用する前に使っていたコードをまず紹介します。
ここではビフォーアフターの流れで見ていきます。

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
        if person.name != "Bob".to_string() {
            continue;
        }
        println!("index: {}", index);
        println!("person: {:?}", person);
    }
}
```

## if letを使った処理

次に`if let`を使用した処理です。上記のコードと同様に動作します。

いかがでしょうか！コードが見やすくなったような気がします。

処理の流れとしては「配列をループして条件に合った値を代入し、その値を鉤括弧の中の処理で実行」となっています。

`find`メソッドを使用したことで条件文も見やすくなった気がします。

```rust
fn main() {
    // ...
    iflet(&person_list);
}

fn iflet(person_list: &[Person; 4]) {
    if let Some((index, person)) = person_list
        .iter()
        .enumerate()
        .find(|(_, person_value)| person_value.name == "Bob".to_string())
    {
        println!("index: {}", index);
        println!("person: {:?}", person);
    }
}
```

## まとめ

今回は`if let`を使用した配列内の特定の値のみ処理する方法について説明してみました。

自分は今まで`if let`の使い方が分からず避けていましたが、ゲーム開発時に使える場面が出てきて
これは使えるぞ！と感動しました。
この感動をバネにこの記事を書いたわけですが、本当に便利な機能だと思います。

もしこの記事が何かの参考になれたら幸いです。
ここまで読んでいただきありがとうございました！
