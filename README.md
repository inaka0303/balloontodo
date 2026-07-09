# ふうせんToDo

スマホとPCで普段使いするための構成です。

## 費用感

- 静的ホスティングは GitHub Pages の公開リポジトリなら GitHub Free で使えます。
- Firebase は Spark プランなら支払い方法なしで始められます。
- Googleログインなどの Firebase Authentication は、通常の認証で月間 50,000 MAU まで無料枠があります。
- Cloud Firestore は Spark プランで、保存 1 GiB、読み取り 50,000 回/日、書き込み 20,000 回/日、削除 20,000 回/日の無料枠があります。

個人のToDo同期なら普通は無料枠内に収まります。ただし無料枠は無制限ではなく、Firebaseの料金表が正です。

## ローカル確認

```sh
python3 -m http.server 5173
```

開くURL:

```text
http://localhost:5173/
```

`firebase-config.js` が空なら、今まで通りこの端末だけに保存されます。

## Firebase同期を有効にする

1. Firebase Consoleで新規プロジェクトを作る。
2. Authentication > Sign-in method で Google を有効化する。
3. Firestore Database を作る。開始モードはどちらでもよいですが、すぐ下のルールに差し替えてください。
4. Project settings > General > Your apps で Web app を追加する。
5. 表示された `firebaseConfig` の値を `firebase-config.js` に貼る。

最低限のFirestoreルール:

```js
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

データは次の場所に保存されます。

```text
users/{uid}/state/tasks
```

## 公開

GitHub Pagesで公開する場合:

1. このフォルダをGitHubリポジトリに入れる。
2. GitHubのリポジトリ設定で Pages を開く。
3. Deploy from a branch を選び、`main` / root を公開元にする。
4. 発行されたURLを Firebase Authentication > Settings > Authorized domains に追加する。
5. スマホでURLを開き、ブラウザの共有メニューからホーム画面に追加する。

初回同期時、クラウド側に既存データがある場合はクラウド側を優先します。そのとき端末内のローカルデータは `localStorage` に `fusen.tasks.backup.*` としてバックアップします。
