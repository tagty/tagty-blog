---
title: "Reformを使ってモデルをシンプルに保つ"
date: 2019-03-12T21:01:48+09:00
draft: false
---

複雑なフォームを扱っていると、モデルが複雑になってしまう場合があります。
そのような場合にどのような対処法があるでしょうか？

## フォームオブジェクトを導入する
フォームオブジェクトを導入すると、モデルからフォームに関するコードを取り除くことができます。

## フォームオブジェクトを使わない場合
例えば、以下のようなアルバムの情報を編集するフォームがあるとします。

アルバムのタイトルとアーティストの名前と曲のタイトルを設定できるフォームです。

*新規登録ページ*

![new](/images/refactor_with_reform/new.png)

*詳細ページ*

![show](/images/refactor_with_reform/show.png)

アルバムのタイトルとアーティストの名前は必須項目になっています。

![validation](/images/refactor_with_reform/validation.png)

*クラス図*

![class](/images/refactor_with_reform/class.png)

`accepts_nested_attributes_for` を使えば、Albumモデルは以下のようなコードになります。

app/models/album.rb

``` ruby
class Album < ApplicationRecord
  has_one :artist
  has_many :songs

  validates :title, presence: true
  accepts_nested_attributes_for :artist, :songs
end
```

モデルのコードは、ネストするクラスが増えたり、バリデーションの条件が増えたりすると複雑になっていきます。

それを解決する方法の一つとして、フォームオブジェクトの導入があります。

## Reformを導入する

今回はフォームオブジェクトの導入に[Reform](http://trailblazer.to/gems/reform/)というgemを使います。

変更は[このように](https://github.com/tagty/reform_sample/commit/eafed5366f71f557a0113d6f5a4f79b679820274)なります。

フォームオブジェクトは以下のように設定しています。

app/forms/album_form.rb

``` ruby
class AlbumForm < Reform::Form
  property :title
  validates :title, presence: true

  property :artist do
    property :name
    validates :name, presence: true
  end

  collection :songs do
    property :title
  end
end
```

フォームオブジェクト導入後のAlbumモデルは以下のようになります。

app/models/album.rb

``` ruby
class Album < ApplicationRecord
  has_one :artist
  has_many :songs
end
```

フォームオブジェクトを導入することによって、モデルをシンプルに保つことができました。
