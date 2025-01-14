## IV. バックエンドサービス
### バックエンドサービスをアタッチされたリソースとして扱う

*バックエンドサービス* はアプリケーションが通常の動作の中でネットワーク越しに利用するすべてのサービスを言う。例としては、データストア（例: [MySQL](http://dev.mysql.com/) や [CouchDB](http://couchdb.apache.org/)）、メッセージキューイングシステム（例: [RabbitMQ](http://www.rabbitmq.com/) や [Beanstalkd](https://beanstalkd.github.io)）、電子メールを送信するための SMTP サービス（例: [Postfix](http://www.postfix.org/)）、キャッシュシステム（例: [Memcached](http://memcached.org/)）などがある。

従来、データストアなどのバックエンドサービスは、デプロイされたアプリケーションと同じシステム管理者によって管理されていた。このようなローカルで管理されるサービスに加えて、サードパーティによって提供、管理されるサービスを利用することもある。例としては、SMTP サービス（例: [Postmark](http://postmarkapp.com/)）、メトリクス収集システム（例: [New Relic](http://newrelic.com/) や [Loggly](http://www.loggly.com/)）、ストレージサービス（例: [Amazon S3](http://aws.amazon.com/s3/)）、API アクセス可能な消費者向けサービス（例: [Twitter](http://dev.twitter.com/) や [Google Maps](https://developers.google.com/maps/)、[Last.fm](http://www.last.fm/api)）などがある。

**Twelve-Factor App のコードは、ローカルサービスとサードパーティサービスを区別しない。** アプリケーションにとっては、どちらもアタッチされたリソースであり、[設定](./config)に格納された URL やその他のロケーター、認証情報でアクセスする。Twelve-Factor App の[デプロイ](./codebase)は、アプリケーションのコードに変更を加えることなく、ローカルで管理されるMySQLデータベースをサードパーティに管理されるサービス（[Amazon RDS](http://aws.amazon.com/rds/) など）に切り替えることができるべきである。同様に、ローカルの SMTP サーバーも、コードを変更することなくサードパーティの SMTP サービス（Postmark など）に切り替えることができるべきである。どちらの場合も、変更が必要なのは設定の中のリソースハンドルのみである。

それぞれのバックエンドサービスは *リソース* である。例えば、1つの MySQL データベースはリソースである。2つの MySQL データベース（アプリケーション層でのシャーディングに使う）は2つの異なるリソースと見なせる。Twelve-Factor App はこれらのデータベースを *アタッチされたリソース* として扱う。これは、アタッチされたリソースとアタッチする対象のデプロイが疎結合であることを意味する。

<img src="/images/attached-resources.png" class="full" alt="4つのバックエンドサービスがアタッチされた本番デプロイ" />

リソースは自由にデプロイにアタッチしたり、デプロイからデタッチしたりできる。例えば、ハードウェアの問題によってアプリケーションのデータベースの動作がおかしい場合、アプリケーションの管理者は最新のバックアップから新しいデータベースサーバーを立ち上げる。そして現在の本番データベースをデタッチし、新しいデータベースをアタッチする -- コードを一切変更せずに。

