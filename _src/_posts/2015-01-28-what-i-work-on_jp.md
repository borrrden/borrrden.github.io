---
title: "私の仕事"
layout: "post"
---

最初の投稿として私が一体何を開発しているのか説明したいと思います。[Couchbase Mobile](http://www.couchbase.com/nosql-databases/couchbase-mobile)の一部となっている[Couchbase Lite](http://developer.couchbase.com/mobile/develop/guides/couchbase-lite/)という製品を開発しています（日本語の情報はその内できると思いますが、申し訳ないけど現在英語しかありません）。Couchbase MobileとはNoSQLパラダイムを使ってモバイル端末にデータを保存し、[Sync Gateway](http://developer.couchbase.com/mobile/develop/guides/sync-gateway/)というプログラム経由で簡単にサーバーに同期するライブラリーです。

<img src="http://www.couchbase.com/images/diagrams/mobile-architecture.gif" width=255>

このライブラリーのメリットとしては保存や同期のロジックが全部開発になっています。それぞれのソース（iOS, Android, .NETなど）を[CouchbaseのGitHubページ](https://github.com/couchbase)にてご覧になれます。具体的に言うと私は[.NETバージョン](https://github.com/couchbase/couchbase-lite-net)を担当しています。後ほどライブラリーの中の詳しい説明をするかもしれませんので、どうぞお楽しみに！
