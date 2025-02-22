---
kind: documentation
title: CI の表示に関するトラブルシューティング
---

{{< site-region region="gov" >}}
<div class="alert alert-warning">選択したサイト ({{< region-param key="dd_site_name" >}}) では現在 CI Visibility は利用できません。</div>
{{< /site-region >}}

### Jenkins インスタンスがインスツルメントされていますが、Datadog にデータが表示されていません

1. 1 つ以上のパイプラインが実行を完了していることを確認します。パイプラインの実行情報は、パイプラインが完了しないと送信されません。
2. Datadog Agent ホストが適切に構成されており、Datadog プラグインから到達可能であることを確認してください。Jenkins プラグインコンフィギュレーション UI の **Check connectivity with the Datadog Agent** (Datadog Agent との接続を確認する) ボタンをクリックすると、接続をテストできます。
3. Jenkins のログにエラーがないか確認します。Datadog プラグインのデバッグレベルのログを有効にするには、[`logging.properties` ファイルを作成][13]して、`org.datadog.level = ALL` という行を追加します。
4. それでも結果が表示されない場合は、[サポートまでお問い合わせ][1]ください。トラブルシューティングのお手伝いをします。

### テストがインスツルメントされていますが、Datadog にデータが表示されていません

1. インスツルメントしている言語の [Setup Tracing on CI Tests][2] ページに移動し、_Compatibility_ セクションを確認します。使用しているテストフレームワークがサポートされていることを確認してください。
2. [Test Runs][3] セクションでテスト結果が表示されるかどうかを確認します。 こに結果が表示されているが、[Tests][4] セクションには表示されていない場合、Git 情報が欠落しています。トラブルシューティングについては、[データが Test Runs には表示されるが、Tests には表示されない](#data-appears-in-test-runs-but-not-tests)を参照してください。
3. Datadog Agent を通してデータを報告している場合は、テストが実行されているホストで Datadog Agent が実行されている (`localhost:8126` でアクセス可能) ことを確認します。他のホスト名またはポートでアクセス可能な場合は、`DD_AGENT_HOST` で設定された適切な Agent ホスト名および `DD_TRACE_AGENT_PORT` 環境変数の適切なポートでテストを実行していることを確認します。トレーサーで[デバッグモード][5]をアクティブにして、Agent に接続できるかどうかを確認できます。
4. それでも結果が表示されない場合は、[サポートまでお問い合わせ][1]ください。トラブルシューティングのお手伝いをします。

### パイプラインが見つかりません

進行中のパイプラインから送られてくる不完全なデータをクリックすると、「パイプラインが見つかりません」というメッセージが表示される不具合を修正しました。ステージ、ジョブ、カスタムコマンドのデータは、順次受信されます。パイプラインが終了するまで待ち、再度お試しください。

### データがテストの実行には表示されるが、テストには表示されない

**Test Runs** タブにテスト結果データが表示されているが、**Tests** タブには表示されない場合は、Git メタデータ (リポジトリ、コミット、ブランチ) が欠落している可能性があります。これを確認するには、[Test Runs][3] セクションでテスト実行を開き、`git.repository_url`、`git.commit.sha`、または `git.branch` がないことを確認します。これらのタグが入力されていない場合、[Tests][4] セクションには何も表示されません。

1. トレーサーはまず、CI プロバイダーが設定した環境変数があればそれを使って、Git の情報を収集します。サポートされている CI プロバイダーごとにトレーサーが読み込もうとする環境変数の一覧は、[コンテナ内でのテストの実行][6]を参照してください。これにより、少なくともリポジトリ、コミットハッシュ、およびブランチ情報が入力されます。
2. 次に、トレーサーはローカルの `.git` フォルダがあれば、そこで `git` コマンドを実行して Git のメタデータを取得します。これは、コミットメッセージや作者、コミッターなどの Git のメタデータフィールドをすべて取得します。`.git` フォルダが存在し、`git` バイナリがインストールされていて、`$PATH` に入っていることを確認してください。この情報は、前のステップで検出されなかった属性を入力するために使用されます。
3. また、環境変数を使用して手動で Git 情報を提供することもできます。この環境変数は、前の手順のいずれかで検出された情報を上書きします。Git 情報を提供するためにサポートされている環境変数は以下の通りです。

   `DD_GIT_REPOSITORY_URL` **(必須)**
   : コードが格納されているリポジトリの URL。HTTP と SSH の両方の URL に対応しています。<br/>
   **例**: `git@github.com:MyCompany/MyApp.git`、`https://github.com/MyCompany/MyApp.git`

   `DD_GIT_COMMIT_SHA` **(必須)**
   : フル (40 文字長の SHA1) コミットハッシュ。<br/>
   **例**: `a18ebf361cc831f5535e58ec4fae04ffd98d8152`

   `DD_GIT_BRANCH`
   : テスト中の Git ブランチ。タグ情報を指定する場合は、空のままにしておきます。<br/>
   **例**: `develop`

   `DD_GIT_TAG`
   : テスト中の Git タグ (該当する場合)。ブランチ情報を指定する場合は、空のままにしておきます。<br/>
   **例**: `1.0.1`

   `DD_GIT_COMMIT_MESSAGE`
   : コミットメッセージ。<br/>
   **例**: `Set release number`

   `DD_GIT_COMMIT_AUTHOR_NAME`
   : コミット作成者名。<br/>
   **例**: `John Smith`

   `DD_GIT_COMMIT_AUTHOR_EMAIL`
   : コミット作成者メールアドレス。<br/>
   **例**: `john@example.com`

   `DD_GIT_COMMIT_AUTHOR_DATE`
   : コミット作成日 (ISO 8601 形式)。<br/>
   **例**: `2021-03-12T16:00:28Z`

   `DD_GIT_COMMIT_COMMITTER_NAME`
   : コミットのコミッター名。<br/>
   **例**: `Jane Smith`

   `DD_GIT_COMMIT_COMMITTER_EMAIL`
   : コミットのコミッターメールアドレス。<br/>
   **例**: `jane@example.com`

   `DD_GIT_COMMIT_COMMITTER_DATE`
   : コミットのコミッター日 (ISO 8601 形式)。<br/>
   **例**: `2021-03-12T16:00:28Z`

4. CI プロバイダーの環境変数が見つからない場合、テスト結果は Git メタデータなしで送信されます。

### テストウォールタイムが空です

テストウォールタイムが表示されない場合、CI プロバイダーのメタデータが欠落している可能性があります。確認するには、[テスト実行][3]セクションでテストを開き、`ci.pipeline.id`、`ci.pipeline.name`、`ci.pipeline.number`、または `ci.job.url` タグがないことを確認します。これらのタグが入力されていない場合、ウォールタイムの列には何も表示されません。

1. トレーサーは、CI プロバイダーが設定した環境変数を使って、この情報を収集します。サポートされている CI プロバイダーごとにトレーサーが読み込もうとする環境変数の一覧は、[コンテナ内でのテストの実行][6]を参照してください。環境変数に期待通りの値が設定されていることを確認します。
2. サポートされている CI プロバイダーでテストを実行していることを確認してください。サポートされている CI プロバイダーの一覧は、[コンテナ内でのテストの実行][6]を参照してください。これらの CI プロバイダーのみが、テストのメタデータを CI 情報でリッチ化するための情報を抽出することができます。
3. それでもウォールタイムが表示されない場合は、[Datadog サポート][1]にお問い合わせください。

### テストウォールタイムが想定と異なります

#### ウォールタイムの算出方法
ウォールタイムは、与えられたパイプラインの最初のテストの開始時刻と最後のテストの終了時刻の間の時間差として定義されます。

これは以下のアルゴリズムで行われます。

1. CI 情報を元にハッシュを計算し、テストをグループ化します。
    1. もしテストに `ci.job.url` が含まれていたら、ハッシュを計算するためにこのタグを使用します。
    2. もしテストに `ci.job.url` が含まれていなければ、 `ci.pipeline.id` + `ci.pipeline.name` + `ci.pipeline.number` を使用してハッシュを計算します。
2. 算出されたウォールタイムは、指定されたハッシュと関連付けられます。**注**: テストを実行するジョブが複数ある場合、ウォールタイムはジョブごとに計算され、計算されたすべてのウォールタイムの中から最大値が表示されます。

#### ウォールタイムの算出に関する問題の可能性
Ruby の [timecop][7] や Python の [FreezeGun][8] など、時間に依存するコードをテストするためのライブラリを使用している場合、テストのタイムスタンプが間違っていて、ウォールタイムを算出している可能性があります。そのような場合は、テストを終了する前に、時間の変更をロールバックするようにしてください。

### テストステータスの数値が想定と異なる

テストステータスの数値は、収集された一意のテストに基づいて計算されます。テストの一意性は、そのスイートと名前だけでなく、テストパラメーターとテスト構成によっても定義されます。

#### 予想より低い数値

予想より数値が低い場合は、ライブラリかテストデータ収集に使用しているツールのどちらかが、テストパラメーターや一部のテスト構成を収集できない可能性があります。

1. JUnit のテストレポートファイルをアップロードする場合
    1. 異なる環境構成で同じテストを実行する場合、[アップロード時にそれらの構成タグを設定していることを確認します][9]。
    2. パラメーター化されたテストを実行している場合、JUnit のレポートにはその情報がない可能性が非常に高いです。[テストデータを報告するためにネイティブのライブラリを使ってみてください][10]。
2. それでも期待する結果が得られない場合は、トラブルシューティングについて [Datadog サポートにお問い合わせください][1]。

#### 合格/不合格/スキップの数値が予想と違う

同じコミットに対して、異なるステータスで同じテストを複数回収集した場合、集計結果は以下の表のアルゴリズムに従います。

| **テストステータス - 最初の試行** | **テストステータス - 再試行 1 回目** | **結果** |
|-----------------------------|----------------------------|------------|
| `Passed`                    | `Passed`                   | `Passed`   |
| `Passed`                    | `Failed`                   | `Passed`   |
| `Passed`                    | `Skipped`                  | `Passed`   |
| `Failed`                    | `Passed`                   | `Passed`   |
| `Failed`                    | `Failed`                   | `Failed`   |
| `Failed`                    | `Skipped`                  | `Failed`   |
| `Skipped`                   | `Passed`                   | `Passed`   |
| `Skipped`                   | `Failed`                   | `Failed`   |
| `Skipped`                   | `Skipped`                  | `Skipped`  |

### デフォルトブランチが正しくない

#### 製品への影響

デフォルトブランチは、製品の一部の機能を動かすために使用されます。すなわち、

- Tests ページのデフォルトブランチのリスト: このリストには、デフォルトのブランチのみが表示されます。間違ったデフォルトブランチを設定すると、デフォルトブランチリストのデータが欠落したり、不正確にな ることがあります。

- デフォルトでないブランチのウォールタイム比較: Tests ページで、ブランチビューで、**VS Default** 列は、現在のブランチのウォールタイムとデフォルトブランチのウォールタイムを比較して計算されます。

- 新しい不安定なテスト。現在、デフォルトブランチでは不安定に分類されていないテスト。デフォルトブランチが適切に設定されていない場合、新たに検出された不安定なテストの数が間違っている可能性があります。

- パイプラインリスト: パイプラインリストには、デフォルトのブランチのみが表示されます。間違ったデフォルトブランチを設定すると、パイプラインリストのデータが欠落したり、不正確にな ることがあります。

#### デフォルトブランチの修正方法

管理者権限をお持ちの方は、[リポジトリ設定ページ][11]から更新することができます。

### Intelligent Test Runner が動作していない

[Intelligent Test Runner][12] は、コミット履歴と過去のテスト実行時のコードカバレッジ情報を分析し、どのテストを実行する必要があり、どのテストをスキップするのが安全かを判断することで動作します。Intelligent Test Runner が正しく動作するためには、最小限の情報が存在する必要があります。

- リポジトリには、過去 1 か月間に少なくとも 2 回のコミット履歴が必要です。
- 過去のコミットでテストコードカバレッジを収集している必要があり、これは Intelligent Test Runner が有効になっているテスト実行で発生します。
- git クローンにはコミット履歴とツリー履歴が含まれている必要があります。シャロー git クローン (`git clone --depth=0`) はサポートされていません。

これらの制限により、Intelligent Test Runner を初めて有効にした場合、スキップされたテストが確認できず、コードカバレッジが自動的に収集されるため、テストの実行時間が通常より遅くなる場合があります。

Intelligent Test Runner は、過去 1 か月間のコミット履歴とテストコードカバレッジ情報のみを考慮します。さらに、コミット後 1 週間以上経過したコードカバレッジ情報は考慮されません。

CI ジョブがシャロー git クローンを使用している場合、コマンド `git clone --filter=blob:none` を使用して部分的な git クローンを使用するように変更することができます。

### Intelligent Test Runner が誤ってテストをスキップしてしまう

Intelligent Test Runner は、コードカバレッジに基づいてテストの影響度分析を行い、あるコミットやコミッ トのセットによってどのテストが影響を受けるかを判断します。この戦略は大部分のテストに有効ですが、Intelligent Test Runner が実行すべきテストをスキップしてしまうシナリオも知られています。

- ライブラリの依存関係の変更。
- コンパイラーオプションの変更。
- 外部サービスの変更。
- データ駆動型テストにおけるデータファイルの変更。

これらのケースを含むコミットを認可する場合、Git のコミットメッセージのどこかに `ITR:NoSkip` (大文字小文字を区別しません) を追加すれば、Intelligent Test Runner でテストスキップを強制的に無効にすることができます。

### さらにサポートが必要ですか？

さらにヘルプが必要な場合は、[Datadog サポート][1]までお問い合わせください。

[1]: /ja/help/
[2]: /ja/continuous_integration/tests/
[3]: https://app.datadoghq.com/ci/test-runs
[4]: https://app.datadoghq.com/ci/test-services
[5]: /ja/tracing/troubleshooting/tracer_debug_logs
[6]: /ja/continuous_integration/tests/containers/
[7]: https://github.com/travisjeffery/timecop
[8]: https://github.com/spulec/freezegun
[9]: /ja/continuous_integration/tests/junit_upload/?tabs=linux#collecting-environment-configuration-metadata
[10]: /ja/continuous_integration/tests/
[11]: https://app.datadoghq.com/ci/settings/repository
[12]: /ja/continuous_integration/intelligent_test_runner/
[13]: https://www.jenkins.io/doc/book/system-administration/viewing-logs/