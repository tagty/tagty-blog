---
title: "Haskellで日本語形態素解析APIを使うにはどうすればよいか？"
date: 2018-08-07T09:55:00+09:00
draft: false
---

ハッカーと画家のスパムへの対策に登場したベイジアンフィルタをHaskellで書きたいと思った。スパム判定のためには形態素解析が必要である。YAHOO!が日本語形態素解析APIを公開している。Haskellで日本語形態素解析APIを使うにはどうすればよいかを調べた。

# 2.日本語形態素解析APIをHaskellで利用する
ベイジアンフィルタを実装するために[機械学習 はじめよう](http://gihyo.jp/dev/serial/01/machine-learning)の[第3回ベイジアンフィルタを実装してみよう](http://gihyo.jp/dev/serial/01/machine-learning/0003)を参考にすることにした。この記事では、形態素解析にYAHOO!デベロッパーネットワークの[日本語形態素解析](https://developer.yahoo.co.jp/webapi/jlp/ma/v1/parse.html)を利用している。HaskellでWeb APIを利用するには、HTTPのライブラリが必要である。ライブラリには[HTTP: A library for client-side HTTP](http://hackage.haskell.org/package/HTTP)を利用した。以下に実装を示す。

``` haskell
import Network.HTTP.Conduit
import qualified Data.ByteString.Lazy as L

appid = APIID
-- 9 : 名詞, 10 : 動詞
uniq_filter = "9|10"
sentence = "庭には二羽ニワトリがいる。"
apiUri = "https://jlp.yahooapis.jp/MAService/V1/parse?appid=" ++ appid ++ "&results=ma,uniq&uniq_filter=" ++ uniq_filter ++ "&sentence=" ++ sentence

main :: IO ()
main = do
    simpleHttp apiUri >>= L.putStr
```

以下のレスポンスを得ることができた。

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<ResultSet xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="urn:yahoo:jp:jlp" xsi:schemaLocation="urn:yahoo:jp:jlp https://jlp.yahooapis.jp/MAService/V1/parseResponse.xsd">
  <ma_result>
    <total_count>8</total_count>
    <filtered_count>8</filtered_count>
    <word_list>
      <word>
        <surface>庭</surface>
        <reading>にわ</reading>
        <pos>名詞</pos>
      </word>
      <word>
        <surface>に</surface>
        <reading>に</reading>
        <pos>助詞</pos>
      </word>
      <word>
        <surface>は</surface>
        <reading>は</reading>
        <pos>助詞</pos>
      </word>
      <word>
        <surface>二羽</surface>
        <reading>にわ</reading>
        <pos>名詞</pos>
      </word>
      <word>
        <surface>ニワトリ</surface>
        <reading>にわとり</reading>
        <pos>名詞</pos>
      </word>
      <word>
        <surface>が</surface>
        <reading>が</reading>
        <pos>助詞</pos>
      </word>
      <word>
        <surface>いる</surface>
        <reading>いる</reading>
        <pos>動詞</pos>
      </word>
      <word>
        <surface>。</surface>
        <reading>。</reading>
        <pos>特殊</pos>
      </word>
    </word_list>
  </ma_result>
  <uniq_result>
    <total_count>8</total_count>
    <filtered_count>4</filtered_count>
    <word_list>
      <word>
        <count>1</count>
        <surface>いる</surface>
        <reading />
        <pos>動詞</pos>
      </word>
      <word>
        <count>1</count>
        <surface>ニワトリ</surface>
        <reading />
        <pos>名詞</pos>
      </word>
      <word>
        <count>1</count>
        <surface>二羽</surface>
        <reading />
        <pos>名詞</pos>
      </word>
      <word>
        <count>1</count>
        <surface>庭</surface>
        <reading />
        <pos>名詞</pos>
      </word>
    </word_list>
  </uniq_result>
</ResultSet>
```

