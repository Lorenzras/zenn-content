---
title: "2025年版最新版：注目のWebアプリテストツールの人気度！"
emoji: "📈"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["E2Eテスト", "Cypress", "Playwright", "Puppeteer", "Selenium"]
published: true
---
# 初めに

テストツールを選ぶ際、人気度を重視する理由はシンプルです。人気のあるツールは、コミュニティのサポートが活発で、ドキュメントやチュートリアルが充実しているため、問題が発生したときに解決策を見つけやすいからです。また、活発なユーザー基盤があると、新しい機能や改善が頻繁に行われ、ツールが時代に追いつきやすくなります。つまり、人気があるツールは安心して使えるというわけです！

本記事では、2025年に注目すべきE2Eテストツールの人気度を比較したいと思います！

# 比較対象の選定

以下の特徴を持つツールに焦点を当てます：

- オープンソース
- Node.js環境で実行できる
- TypeScriptでの実装が可能
- 成熟しており、比較的人気がある

これらの条件を満たすツールの中から、以下の3つを選定しました：

- [Cypress](https://www.cypress.io/)

https://www.cypress.io/

- [Playwright](https://playwright.dev/)

https://playwright.dev/

- [Selenium WebDriver](https://www.selenium.dev/documentation/webdriver/)

https://www.selenium.dev/documentation/webdriver/


:::note warn
注意
Selenium WebDriverは、他のツールとは異なり、単体ではテストフレームワークとして機能しません。これは（ブラウザの自動化を行う）ツールに過ぎません。ウェブアプリのテストを行うには、MochaやJestなどのテストフレームワークと組み合わせて使用する必要があります。
:::

# Googleトレンド

[Googleトレンド](https://trends.google.com/trends/explore?date=2020-12-22%202025-01-22&q=%22cypress%20test%22,%22playwright%20test%22,%22selenium%20test%22&hl=en)を使って比較することで、特定のツールや技術がどれほど注目されているか、時期ごとの関心の変化を把握できます。

![](/images/e2e-test-tool-popularity/google-trends.png)

GoogleトレンドのグラフでPlaywrightがCypressを追い越し、現在もSeleniumより人気が高いこと確認できます。

:::message

一部のツール名はスクレイピングなどの用途でも使用されているため、テストの文脈での人気度を統計的に絞り込むために、「test」というキーワードを追加しました。
:::

# npmトレンド

[npmトレンド](https://npmtrends.com/cypress-vs-playwright-vs-selenium-webdriver)を比較することで、ツールのインストール数や利用頻度を確認でき、実際にどれだけ多くの開発者に使用されているかを把握できます。

![](/images/e2e-test-tool-popularity/npm-trends.png)

Googleトレンドと同様にPlaywrightがCypressを上回り、Seleniumよりも多くの開発者が利用されていることが確認できます。

# GitHub

GitHubでの人気度を比較することで、ツールの信頼性や活発なサポート、コミュニティの規模を把握でき、実際の利用状況や進化のスピードを確認できます。

| ツール | スター数 | フォーク数 | PRマージ数 ※ | コミット数 ※ | 年数 | 
|-|-|-|-|-|-|
| [Cypress](https://github.com/cypress-io/cypress) | 47,669 | 3,210 | 51 | 211 | 7年前 |
| [Playwright](https://github.com/microsoft/playwright) | 68,692  | 3,797 | 130 |139 | 5年前 |
| [Selenium](https://github.com/SeleniumHQ/selenium) | 31,356  | 8,297 | 19 (全173) | 29 (全238) | 12年前 |

※ 直近1カ月 = 2024-12-24から2025-01-24まで

Playwrightは最も多くのスター数を持ち、比較的新しいツールです。Cypressはスター数、フォーク数、PR数、コミット数が中程度で、人気があり活発に利用されているツールです。Seleniumは最も古いツールでありながら、フォーク数、PR数、コミット数が最も多いです。Seleniumのリポジトリは、ブラウザ自動化をサポートする複数の言語用パッケージを含む[モノレポ](https://qiita.com/470_aaa/items/b129900e98df234a1670#monorepo%E3%81%A8%E3%81%AF)です。直近1カ月のjavaScriptパッケージが含むPRマージ数は19件で、コミット数は29件です。

:::message

マージされたPR数とコミット数はリポジトリの健康状態を示す指標の一つです。しかし、dependabotのような非人間によるコミットも含まれる可能性があるため、これらの指標は参考程度にしてください

:::

# おわりに

テストツールを選ぶ際には、人気度だけでなく、チームのニーズやプロジェクトの要件、ツールの機能や拡張性など、さまざまな視点から総合的に判断することが重要です。今回の比較は、あくまで各ツールの現在の人気動向を把握する一つの参考として捉えてください。それぞれのツールには独自の強みと適した用途がありますので、実際のプロジェクトに最適な選択をするためには、実際に使ってみたり、コミュニティの意見を調べたりすることをお勧めします。

生成AI時代になった今、これらのツールの人気度も上昇しているため、その関係性について次回調べたいと思います。
