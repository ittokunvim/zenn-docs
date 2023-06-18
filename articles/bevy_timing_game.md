---
title: "Bevyでタイミングゲームを作る"
emoji: "🎮"
type: "tech"
topics:
  - "rust"
  - "game"
  - "bevy"
published: true
published_at: "2023-06-18 02:03"
---

ここでいうタイミングゲームとは、細長いスライダーの中に左右に動くバーがあり、そのバーを真ん中にタイミングを合わせて、高得点を狙うという単純なゲームです。

見た目はこんな感じです。

![タイミングゲーム](https://storage.googleapis.com/zenn-user-upload/00a182b26ff0-20230606.png)

ゲームを作成するにあたり、参考にしたサイトは次のとおりです。

> [Bevy Examples](https://github.com/bevyengine/bevy/blob/main/examples)

ソースコードはこちら

> [timing game](https://github.com/ittokun/bevy-games/blob/main/examples/timing.rs)

ゲームを正常に動作させるためにアセットをダウンロードしておく必要があります。以下のURLから2つのフォントと1つの音源をダウンロードしておいてください。

保存場所は、フォントは`assets/fonts`、音源は`assets/sounds`に保存してください。

> - [FiraSans-Bold.ttf](https://github.com/ittokun/bevy-games/blob/main/assets/fonts/FiraSans-Bold.ttf)
> - [FiraMono-Medium.ttf](https://github.com/ittokun/bevy-games/blob/main/assets/fonts/FiraMono-Medium.ttf)
> - [timing_decide.ogg](https://github.com/ittokun/bevy-games/blob/main/assets/sounds/timing_decide.ogg)

さてここからは`Bevy`を使ったタイミングゲームの作り方を紹介しますが、このゲームエンジンのバージョンについて触れておく必要があります。
今回使用したBevyのバージョンは`0.10.1`です。なので、それ以前や以降のバージョンでは正しく動作しない可能性が大いにあることにご注意ください（実際、`0.9.0`では多分動きません）。

あとは当然ですが[Rustプログラミング言語](https://www.rust-lang.org/ja)が必要になります。インストールしておきましょう。

というわけで見ていきましょう🤟

### まずはプロジェクトを作成する

ゲームを作成するにはまず、プロジェクトが必要です。
`cargo new timing_game`のようにタイミングゲーム用のプロジェクトを作成しても良いですが、ここでは「色々なBevyのゲームがあるプロジェクト」を作成します。

では、以下のコマンドを実行します。

```bash
# Cargoプロジェクトを作成
cargo new bevy-games
# Cargoプロジェクトへ移動
cd bevy-games
```

作成すると`src`ディレクトリがあるはずですが、今回は`examples`ディレクトリで作業を行うため使用しません。置いておいても、削除してもどっちでも構いません。

次に`examples`ディレクトリを作成し、`examples/timing.rs`ファイルを作成します。

```bash
mkdir examples
touch examples/timing.rs
```

一応`timing.rs`ファイルに`Hello World`を書いて動くことを確認しておきましょう。

```rust
fn main() {
    println!("やっはろー！");
}
```

以下のコマンドで、`timing.rs`を実行します。

```bash
cargo run --example timing
```

動作を確認し終えたら、次はBevyをセットアップしていきます🤟

### Bevyを動かす

プロジェクトに、`Bevy`を導入して、動作させていきましょう！

まず`Cargo.toml`に以下の記述を行います。

```toml
[dependencies]
bevy = "0.10.1"
```

ここで一度、`cargo run --example timing`を実行しておいて、Bevyをインストールしておきましょう。ロードが長いので先にプロジェクトに落としておくとスムーズに進みます。

そして、`examples/timing.rs`に以下のコードを記述します。

```rust
use bevy::prelude::*;

const BACKGROUND_COLOR: Color = Color::rgb(0.9, 0.9, 0.9);

fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .insert_resource(ClearColor(BACKGROUND_COLOR))
        .insert_resource(FixedTime::new_from_secs(1.0 / 60.0))
        .add_system(bevy::window::close_on_esc)
        .run();
}
```

上記のコードでは、ゲーム背景色の設定、ゲームのフレーム設定、`esc`キーでゲームを終了する設定を行なっています。

では、このソースコードを動作させてみましょう。真っ黒なウィンドウが表示されれば成功です。

```bash
cargo run --example timing
```

### セットアップを実装

ここでは、ゲームをセットアップする、つまり、初期画面の設定を行います。

まずは、`timing.rs`に以下のコードを記述します。

```rust
const SLIDER_SIZE: Vec2 = Vec2::new(500.0, 50.0);

const CUE_SIZE: Vec2 = Vec2::new(5.0, 50.0);

const SLIDER_DEFAULT_COLOR: Color = Color::rgb(0.8, 0.8, 0.8);
const CUE_COLOR: Color = Color::rgb(0.4, 0.4, 0.4);

fn main() {
    App::new()
        // ...
        .add_startup_system(setup)
        // ...
}

fn setup(mut commands: Commands) {
    // Camera
    commands.spawn(Camera2dBundle::default());

    // Slider
    commands.spawn(SpriteBundle {
        sprite: Sprite {
            color: SLIDER_DEFAULT_COLOR,
            custom_size: Some(SLIDER_SIZE),
            ..default()
        },
        ..default()
    });

    // Cue
    commands.spawn(SpriteBundle {
        sprite: Sprite {
            color: CUE_COLOR,
            custom_size: Some(CUE_SIZE),
            ..default()
        },
        ..default()
    });
}
```

ここでは、カメラのセットと、スライダー（タイミングを判定する細長い棒）、キュー（タイミングを決定する短い棒）の描画を行なっています。

以下のコマンドを実行して、先ほどのウィンドウに細長いグレーの棒と、黄色い短い棒が描画されていれば成功です。

```bash
cargo run --example timing
```

### キューを動かす

ここでは、キューを右に動かす処理を書いていきます。

`timing.rs`に以下のコードを記述します。

```rust
const CUE_SPEED: f32 = 100.0;
const INITIAL_CUE_DIRECTION: Vec2 = Vec2::new(0.5, -0.5);

fn main() {
    App::new()
        // ...
	.add_system(apply_velocity)
	// ...
}

#[derive(Component, Deref, DerefMut)]
struct Velocity(Vec2);

fn setup() {
    // ...

    // Cue
    commands.spawn((
        SpriteBundle {
	    // ...
	},
        Velocity(INITIAL_CUE_DIRECTION.normalize() * CUE_SPEED),
    ));
}

fn apply_velocity(mut query: Query<(&mut Transform, &Velocity)>, time_step: Res<FixedTime>) {
    for (mut transform, velocity) in &mut query {
        transform.translation.x += velocity.x * time_step.period.as_secs_f32();
    }
}
```

ここでは、`Velocity(Vec2)`という速度を表すコンポーネントを定義し、これをキューに追加することでオブジェクトを動くことができるようになります。

そして、`apply_velocity`関数では、Velocity(Vec2)を追加したオブジェクトをどのように動かすか書かれています。このタイミングゲームでは、横にしか移動しないので`x`軸のみを動かしています。

以下のコマンドを実行して、キューが動くことを確認してください。キューが右に動き、そのまま画面外に出ていったら成功です。

```bash
cargo run --example timing
```

### 衝突判定を追加する

今のキューでは右に動いて画面外にいってしまうと、もう帰ってきません。そこで、キューを跳ね返す`Reflector`コンポーネントを左右に追加し、キューを常にスライダーの中で跳ね返させるようにしていきます。

そして、跳ね返される対象である`Cue`もコンポーネントに追加します。この定義をしていないと、跳ね返すオブジェクトは何なのかわからなくなるからです。

リフレクターとキューは以下のように定義します。跳ね返す処理は次で紹介します。

```rust
const REFLECTOR_SIZE: Vec2 = Vec2::new(1.0, 50.0);

const REFRECTOR_COLOR: Color = Color::rgb(0.4, 0.4, 0.4);

// ...

#[derive(Component)]
struct Cue;

#[derive(Component)]
struct Reflector;

// ...

fn setup() {
    // ...

    let refrector_sprite = |slider_pos_x: f32| SpriteBundle {
        sprite: Sprite {
            color: REFRECTOR_COLOR,
            custom_size: Some(REFLECTOR_SIZE),
            ..default()
        },
        transform: Transform {
            translation: Vec3::new(slider_pos_x / 2.0, 0.0, 0.0),
            ..default()
        },
        ..default()
    };

    // left reflector
    commands.spawn((refrector_sprite(-SLIDER_SIZE.x), Reflector));

    // right reflector
    commands.spawn((refrector_sprite(SLIDER_SIZE.x), Reflector));

    // Cue
    commands.spawn((
        SpriteBundle {
	    // ...
        },
        Cue,
        Velocity(INITIAL_CUE_DIRECTION.normalize() * CUE_SPEED),
    ));

    // ...
}
```

これでスライダーの両端にキューを跳ね返すリフレクターを追加することができました。
プログラムを実行してみてリフレクターがゲームに追加されているか確認してみてください。
まだ跳ね返すことはできません。

次に、先ほど作成したリフレクターに衝突判定を追加して、キューを跳ね返すようにしていきましょう。
Bevyには衝突判定を行う機能に`bevy::sprite::collide_aabb::{collide, Collision}`があります。
この機能を使用すると簡単にオブジェクト同士の衝突判定を実装することができます。

では以下のコードを`timing.rs`に記述します。

```rust
use bevy::{
    prelude::*,
    sprite::collide_aabb::{collide, Collision},
};

// ...

fn main() {
    App::new()
        // ...
	.add_system(check_for_collisions)
	// ...
}

// ...

#[derive(Component)]
struct Collider;

// ...

fn setup(mut commands: Commands) {
    // ...

    // left reflector
    commands.spawn((refrector_sprite(-SLIDER_SIZE.x), Reflector, Collider));

    // right reflector
    commands.spawn((refrector_sprite(SLIDER_SIZE.x), Reflector, Collider));

    // ...
}

fn check_for_collisions(
    mut cue_query: Query<(&mut Velocity, &Transform), With<Cue>>,
    collider_query: Query<&Transform, With<Collider>>,
) {
    let (mut cue_velocity, cue_transform) = cue_query.single_mut();

    // check collision with reflectors
    for transform in &collider_query {
        let collision = collide(
            cue_transform.translation,
            CUE_SIZE,
            transform.translation,
            transform.scale.truncate(),
        );

        if let Some(collision) = collision {
            let reflect_x = match collision {
                Collision::Left => cue_velocity.x > 0.0,
                Collision::Right => cue_velocity.x < 0.0,
                _ => false,
            };

            // reflect velocity on the x-axis if we hit something on the x-axis
            if reflect_x {
                cue_velocity.x = -cue_velocity.x;
            }
        }
    }
}
```

ではプログラムを実行してみて、キューが左右に跳ね返されていることを確認してみてください。
であれば成功です。

### タイミングを決める処理を追加する

次はスライダーに`ok, good, perfect`の3つのエリアを追加し、そのエリア内で左右に動くキューでタイミングを決める処理を書いていきます。

ではまずセットアップに3つの色分けをしたエリアを追加します。

```rust
const PERFECT_TIMING_RANGE: f32 = 10.0;
const GOOD_TIMING_RANGE: f32 = 50.0;
const OK_TIMING_RANGE: f32 = 150.0;

const SLIDER_OK_COLOR: Color = Color::rgb(0.7, 0.7, 0.7);
const SLIDER_GOOD_COLOR: Color = Color::rgb(0.6, 0.6, 0.6);
const SLIDER_PERFECT_COLOR: Color = Color::rgb(0.5, 0.5, 0.5);

fn setup(/**/) {
   // Slider ok timing range
    commands.spawn(SpriteBundle {
        sprite: Sprite {
            color: SLIDER_OK_COLOR,
            custom_size: Some(Vec2::new(OK_TIMING_RANGE * 2.0, SLIDER_SIZE.y)),
            ..default()
        },
        ..default()
    });

    // Slider good timing range
    commands.spawn(SpriteBundle {
        sprite: Sprite {
            color: SLIDER_GOOD_COLOR,
            custom_size: Some(Vec2::new(GOOD_TIMING_RANGE * 2.0, SLIDER_SIZE.y)),
            ..default()
        },
        ..default()
    });

    // Slider parfect timing range
    commands.spawn(SpriteBundle {
        sprite: Sprite {
            color: SLIDER_PERFECT_COLOR,
            custom_size: Some(Vec2::new(PERFECT_TIMING_RANGE * 2.0, SLIDER_SIZE.y)),
            ..default()
        },
        ..default()
    });
}
```

次にタイミングを決める関数を作成します。ここではスペースキーでタイミングを決め、キューのｘ軸を見てそのエリア内でタイミングを決めた場合、ターミナルに出力する処理を書いています。

```rust
fn main() {
    App::new()
        // ...
	.add_system(decide_timing)
	// ...
}

fn decide_timing(keyboard_input: Res<Input<KeyCode>>, query: Query<&Transform, With<Cue>>) {
    let cue_transform = query.single();

    if keyboard_input.just_pressed(KeyCode::Space) {
        let cue_translation_x = cue_transform.translation.x;
        println!("{}", cue_translation_x);

        if cue_translation_x < PERFECT_TIMING_RANGE && cue_translation_x > -PERFECT_TIMING_RANGE {
            println!("Perfect timing!");
        } else if cue_translation_x < GOOD_TIMING_RANGE && cue_translation_x > -GOOD_TIMING_RANGE {
            println!("Good timing!");
        } else if cue_translation_x < OK_TIMING_RANGE && cue_translation_x > -OK_TIMING_RANGE {
            println!("OK timing!");
        } else {
            println!("Bad timing!");
        }
    }
}
```

ではプログラムを実行して、タイミングを決めた際にターミナルに文字が表示されていれば成功です。

### スコアボードを追加する

タイミングを決めることができたら次は、それを見えるようにスコアボードを追加します。

スコアボードを実装するために、`Resource`を使用します。これはゲーム内で値を表示や変更するために必要な機能です。

まずはセットアップから始めます。ここではゲーム内にスコアボードを追加しますが文字を表示するためにフォントをダウンロードする必要があります。入れておきましょう。

```rust
const SCOREBOARD_FONT_SIZE: f32 = 40.0;
const SCOREBOARD_TEXT_PADDING: Val = Val::Px(5.0);

fn main() {
    App::new()
        // ...
        .insert_resource(Scoreboard { score: 0 })
        // ...
}

// ...

#[derive(Resource)]
struct Scoreboard {
    score: isize,
}

fn setup(mut commands: Commands, asset_server: Res<AssetServer>) {
    // ...

    // Scoreboard
    commands.spawn(
        TextBundle::from_sections([
            TextSection::new(
                "Score: ",
                TextStyle {
                    font: asset_server.load("fonts/FiraSans-Bold.ttf"),
                    font_size: SCOREBOARD_FONT_SIZE,
                    color: Color::BLACK,
                    ..default()
                },
            ),
            TextSection::from_style(TextStyle {
                font: asset_server.load("fonts/FiraMono-Medium.ttf"),
                font_size: SCOREBOARD_FONT_SIZE,
                color: Color::GRAY,
                ..default()
            }),
        ])
        .with_style(Style {
            position_type: PositionType::Absolute,
            position: UiRect {
                top: SCOREBOARD_TEXT_PADDING,
                left: SCOREBOARD_TEXT_PADDING,
                ..default()
            },
            ..default()
        }),
    );
}
```

ではプログラムを実行してみて、画面左上にスコアボードが表示されていればOKです。

次にスコアボードを更新する処理を書いていきます。

```rust
fn main() {
    App::new()
        // ...
        .add_system(update_scoreboard)
        // ...
}

// ...

fn decide_timing(
    keyboard_input: Res<Input<KeyCode>>,
    mut scoreboard: ResMut<Scoreboard>,
    query: Query<&Transform, With<Cue>>,
) {
    // ...

    if keyboard_input.just_pressed(KeyCode::Space) {
        // ...

        if cue_translation_x < PERFECT_TIMING_RANGE && cue_translation_x > -PERFECT_TIMING_RANGE {
            scoreboard.score += 100;
        } else if cue_translation_x < GOOD_TIMING_RANGE && cue_translation_x > -GOOD_TIMING_RANGE {
            scoreboard.score += 50;
        } else if cue_translation_x < OK_TIMING_RANGE && cue_translation_x > -OK_TIMING_RANGE {
            scoreboard.score += 10;
        } else {
            scoreboard.score -= 100;
        }
    }
}

// ...

fn update_scoreboard(scoreboard: Res<Scoreboard>, mut query: Query<&mut Text>) {
    let mut text = query.single_mut();
    text.sections[1].value = scoreboard.score.to_string();
}
```

ではプログラムを実行してみて、画面左上のスコアボードがタイミングを決めた時に値が変わっていればOKです。

### タイミング決定時に音を鳴らす

最後にタイミングを決めた時に音源を再生する処理を書いていきます。

```rust
fn main() {
    App::new()
        // ...
	.add_event::<TimingEvent>()
	.add_systems((
	    // ...
            play_timing_sound.after(check_for_collisions),
	))
	// ...
}

// ...

#[derive(Default)]
struct TimingEvent;

#[derive(Resource)]
struct TimingSound(Handle<AudioSource>);

fn setup(mut commands: Commands, asset_server: Res<AssetServer>) {
    // ...

    // Sound
    let cue_timing_sound = asset_server.load("sounds/timing_decide.ogg");
    commands.insert_resource(TimingSound(cue_timing_sound));

    // ...
}

fn decide_timing(
    // ...
    mut timing_events: EventWriter<TimingEvent>,
) {
    // ...

    if keyboard_input.just_pressed(KeyCode::Space) {
        // Sends a timing event so that other systems can react to the timing
        timing_events.send_default();
	// ...
    }
}

fn play_timing_sound(
    mut timing_events: EventReader<TimingEvent>,
    audio: Res<Audio>,
    sound: Res<TimingSound>,
) {
    // Play a sound once per frame if a timing occurred.
    if !timing_events.is_empty() {
        // This prevents events staying active on the next frame.
        timing_events.clear();
        audio.play(sound.0.clone());
    }
}
```

ではプログラムを実行してみて、タイミングを決めた際に音が鳴れば成功です。

