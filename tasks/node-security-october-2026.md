# Node.js October 2026 security update

## 目的と実行境界

- current request ceiling: implementation-authorized
- Node.js公式の2026年10月セキュリティ修正版22.23.4を開発・ビルド・公開処理で使用する。
- この文書は実装ブランチに同梱する作業計画。停止条件は修正とローカル検証の完了。merge・release・本番操作は含めない。
- BASE_SHA: 47a853dc50e29f1f2812f8d0889435e7ae8ebd68
- 適用規約: root AGENTS.md / WORKFLOW.md / QUALITY.md。CI変更のためPlan Mode必須。

## 対象と非目的

- 対象: rootとpackages/*/package.jsonのVolta指定、.github/workflowsのNode.js指定3件、README.mdの開発コマンド。
- 22.20.0固定とサポート終了した20系指定を22.23.4へ更新する。
- 非目的: アプリ機能、依存パッケージ・lockfile、PWA、保存・起動・共有契約、Cloudflare設定の変更。
- ユーザーデータ・公開API・既存保存形式への変更はない。
- 定期曲マスタ処理は既存タグをcheckoutするため、バージョンファイル参照へ置き換えずworkflowで修正版を指定する。
- ローカル検証でVolta導入済みpnpmが旧Node.jsを選ぶことを確認したため、READMEのコマンドにも修正版Node.jsの指定を追加する。
- 個別workspaceからの直接実行でも旧Node.jsへ戻らないよう、各packageのVolta設定をroot package.jsonから継承する。変更するのはtoolchainメタデータのみ。

## 成功条件と検証

- 対象の全Node.js指定が22.23.4になる。
- 修正版Node.jsで既存lockfileから依存を導入できる。
- pnpm lint / test / build、sync:versions:checkが成功する。
- YAML構文、全setup-node指定、Volta指定、UTF-8・改行形式、git diff --checkを検証する。
- 公開やDB更新は行わず、ビルド・テストで互換性を確認する。
- Node.js本体のCVE再現実験は行わず、公式修正版の実行と全選択経路の設定照合を代替証拠にする。

## 実行とコミット

- 実装所有者: 親agent。独立したread-only investigator / reviewerが見落としと回帰を確認する。
- 1論理コミット: 本計画とNode.js指定の変更。生成物やlockfileは含めない。
- ロールバック: このコミットをrevertできる。ただし修正前Node.jsへ戻るため一時的な互換性対処に限定する。

## 参照

- https://nodejs.org/en/blog/vulnerability/october-2026-security-releases

## ローカル検証結果

- Node.js 22.23.4でfrozen install、lint、test（194件）、build、sync:versions:check成功。
- Volta導入済みpnpmの既存toolchainは22.20.0だったため、上記検証はvolta run --node 22.23.4で再実行して成功を確認した。
- rootと全5workspaceでnode --versionがv22.23.4になることを確認。
- workflow構文・全3selector、Volta継承、依存メタデータ不変、lockfile差分なし、git diff --checkを確認。
- Node.js本体の攻撃到達性・CVEの再現実験は未検証。公式修正版の実行確認を用い、既存機能の互換性はbuild/testで検証した。
- 本番deploy、remote DB、releaseは未実行。
- 独立read-only reviewer: COMPLETE、具体的なbypass・回帰指摘なし。通常のpnpmが旧Node.jsを選ぶ端末上の制限はREADMEの明示実行で回避を確認した。
