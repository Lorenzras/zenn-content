---
title: "【Playwright】並列機能とシャーディングでテスト実行時間を大幅短縮！GitHub"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["E2Eテスト", "Playwright", "GitHubActions", "CI", "並列処理"]
published: false
---
## 初めに

改修範囲が広がると、その分検証範囲も広がり、「E2Eテストに時間がかかりすぎる！」と感じることはありませんか？
特に手動テストでは、画面を操作しながら動作を確認するたびに手間と時間が膨らんでいきます。さらに、仕様変更やリファクタリングのたびにリグレッションがないか慎重にチェックする必要があり、その負荷は開発スピードだけでなく開発者のモチベーションにも悪影響を及ぼします。

そんな時、[最も注目されているPlaywright](https://qiita.com/otLenz/items/95debb97dcec30da3a5c)が強力な味方になるかもしれません。
高速なテスト実行、並列処理、シャーディングといった機能を活用すれば、E2Eテストの時間を大幅に短縮しつつ、テストの信頼性を向上させることができます。

本記事では、Playwrightを活用してE2Eテストの実行時間を短縮する方法を紹介します。

## 並列実行

Playwrightには並列実行機能が備わっています。これにより、テストを複数のスレッドで同時に実行することができ、テストの実行時間を大幅に短縮できます。

例えば、30秒かかるテストが20あるとします。

```javascript
test.describe("20 example tests", () => {
    test('Test - 1', async ({ page }) => {
      await new Promise(resolve => setTimeout(resolve, 30000));
    });
    
    test('Test - 2', async ({ page }) => {
      await new Promise(resolve => setTimeout(resolve, 30000));
    });
    .
    .
    // （…中略…）
    .
    test('Test - 19', async ({ page }) => {
      await new Promise(resolve => setTimeout(resolve, 30000));
    });
    
    test('Test - 20', async ({ page }) => {
      await new Promise(resolve => setTimeout(resolve, 30000));
    });
});

```

これを一つずつ実行するとどうなるでしょうか？

どうなるでしょうか？‥‥ 30秒×20＝600秒＝**10分ほどかかってしまいます！**


しかし、Playwrightの並列実行機能を使えば、この時間を大幅に短縮できます。Playwrightは、複数のワーカープロセスを同時に動作させ、テストを並列実行することが可能です。では、これらのテストを5つのワーカーで並列実行するとどうなるでしょうか？・・・

このコマンドを実行すると、

```bash
npx playwright test --workers 5
```

次のような出力が表示されます。

```
Running 20 tests using 5 workers

7 [chromium] > example.spec.ts:4:5> Example tests > Test - 7 (30.5s)
2 [chromium] > example.spec.ts:4:5> Example tests > Test - 2 (30.2s)
3 [chromium] > example.spec.ts:4:5> Example tests > Test - 3 (30.4s)
...省略

20 passed (2.1m)
```

5つのワーカーを使用することで、20個のテストを**2分で実行することに成功しました**！まさに5人の強力なテスター仲間を増やしたような効果です！

Playwrightでは、システムのリソースやテスト内容に応じて、ワーカー数を調整可能です。

デフォルト動作ではテストファイル単位の並列実行ですが、設定を変更すれば一つのテストファイル内の複数テストを並列実行することもできます。さらに、ワーカー数の増減や完全並列・部分並列も柔軟に設定できるので、プロジェクトに最適な形でテストを効率化できます。詳細は[Playwright](https://playwright.dev/docs/test-parallel)の公式ドキュメントをご確認ください。

https://playwright.dev/docs/test-parallel


## シャーディング（Sharding）

「テストが大きすぎて、ワーカーを増やしてもリソースが足りない…」 そんな時こそ、シャーディングが効果的です！

複数のマシンを使ってテストケースを均等に分割し、それぞれのマシンで並列実行することで、リソースを効率的に活用できます。Playwrightは、このシナリオを実現するためのシャーディング機能を提供しており、大規模なテストスイートを複数のマシンに分散させ、実行時間を大幅に短縮できます。

例えば、テストスイートを5シャードに分割し、各マシンでそのうちの1シャードを実行する場合、以下のようにコマンドを実行します。

```bash
npx playwright test --shard=1/5
```

そして、次のような出力が表示されます。

```bash
Running tests using 4 workers, shard 1 of 5

...省略

4 passed (31.0s)
```

同様に、他のマシンでも `--shard=2/5`、`--shard=3/5` といったコマンドを実行することで、テストを分散して効率的に実行できます。

これにより、すべてのテストが **31秒で完了することができました。**

https://playwright.dev/docs/test-sharding

## Github Actionsでのシャーディング例

PlaywrightテストをCIに組み込むことで、好きなタイミングで自動的にE2Eテストを実行し、リグレッション（退行）バグを早期に検出できます。今

さぁ、次はGitHub Actionsでシャーディングを設定する方法を見てみましょう。

:::message 

**GitHub Actionsの無料利用制限にご注意ください**  
GitHub Actionsには、無料利用枠として**毎月2,000分**のビルド時間が提供されています（パブリックリポジトリの場合は無制限）。大規模なプロジェクトや頻繁なテスト実行を行う場合、この制限を超えてしまう可能性があります。

:::

```yaml
name: Playwright Tests
on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]
jobs:
  playwright-tests:
    timeout-minutes: 60
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        shardIndex: [1, 2, 3, 4, 5]
        shardTotal: [5]
    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: lts/*
    - name: Install dependencies
      run: npm ci
    - name: Install Playwright browsers
      run: npx playwright install --with-deps

    - name: Run Playwright tests
      run: npx playwright test --shard=${{ matrix.shardIndex }}/${{ matrix.shardTotal }}

    - name: Upload blob report to GitHub Actions Artifacts
      if: ${{ !cancelled() }}
      uses: actions/upload-artifact@v4
      with:
        name: blob-report-${{ matrix.shardIndex }}
        path: blob-report
        retention-days: 1
  merge-reports:
    # Playwrightテストの実行後、一部のシャードが失敗した場合でもレポートをマージする
    if: ${{ !cancelled() }}
    needs: [playwright-tests]

    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: lts/*
    - name: Install dependencies
      run: npm ci

    - name: Download blob reports from GitHub Actions Artifacts
      uses: actions/download-artifact@v4
      with:
        path: all-blob-reports
        pattern: blob-report-*
        merge-multiple: true

    - name: Merge into HTML Report
      run: npx playwright merge-reports --reporter html ./all-blob-reports

    - name: Upload HTML report
      uses: actions/upload-artifact@v4
      with:
        name: html-report--attempt-${{ github.run_attempt }}
        path: playwright-report
        retention-days: 14        
```


::::details 設定の説明はこちら！

1. ジョブ設定にマトリックスオプションを追加
`shardTotal: [5]` オプションを使用して、作成するシャードの総数を指定し、`shardIndex: [1, 2, 3, 4, 5]` で各シャードの番号を配列として定義します。

2. Playwrightテストの実行
`--shard=${{ matrix.shardIndex }}/${{ matrix.shardTotal }}` オプションを指定してPlaywrightテストを実行します。これにより、各シャードに対してテストコマンドが実行されます。

3. Blobレポートのアップロード
最後に、BlobレポートをGitHub ActionsのArtifactsにアップロードします。これにより、ワークフロー内の他のジョブでもレポートを利用できるようになります。

4. 全シャードの完了後にレポートを結合
すべてのシャードが完了した後、別のジョブを実行してレポートを結合し、統合されたHTMLレポートを生成できます。実行順序を保証するために、`merge-reports` ジョブが `playwright-tests` ジョブに依存するよう、`needs: [playwright-tests]` を追加します。

::::

コミットしたタイミングでテストが自動的に実行され、レポートも生成されました！もちろん、必要に応じてタイミングなど調整できます。詳しくは [GitHub Actions のドキュメント](https://docs.github.com/ja/actions) をご参考ください。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3951404/4150b1af-1914-3786-6f16-79e0b7d0ce87.png)

レポートを開くとこんな感じです。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3951404/541d3028-34ce-5a6f-87e4-94b67f8c8077.png)

見ての通り、**全テストを３1.5秒で実行することが出来ました。**

:::message

各シャードは環境構築に数分かかる場合がありますが、実際のテストは約30秒しかかかりません。ただし、これはテストの内容やマシンのスペックによっても異なる可能性があります。

:::

## 終わりに

「どの程度の自動E2Eテストを導入すべきか」は、プロジェクトの特性やチームの体制によって異なります。リスクベースで優先度をつけ、重要な部分から自動化を進めることで、より効果的かつ効率的なテストを実現できるでしょう。Playwrightは、テストピラミッドから受け継がれた従来の考え方に革新をもたらし、高速で信頼性の高いテストを実現するための強力なツールとなっています。

手動テストが完全になくなるわけではないが、適切なバランスで高速なE2Eテストにより、継続的CIで実行することが可能になりました。もはや一晩中テストを行う必要はありません。

https://github.com/Lorenzras/playwright-sharding
