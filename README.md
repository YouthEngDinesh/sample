sequence diagram
    autonumber
    participant UI as SCR-001 メイン操作盤フォーム
    participant App as app.js (handleSubmitTransfer)
    participant API as Backend (POST /api/equipment/command)
    participant HW as 実機物理搬送機構 (Hardware)

    UI->>App: 「搬送指示実行」ボタンタップ (Submit)
    Note over App: 【クライアントバリデーション】<br>・CarrierID未入力 ➔ UI-001表示<br>・搬送元未選択 ➔ UI-002表示<br>・搬送先未選択 ➔ UI-003表示
    
    alt バリデーションエラー発生
        App-->>UI: エラーエリアに警告文言をインジェクション ＆ 処理中断(return)
    else バリデーション正常通過
        App->>UI: 確認ダイアログポップアップ表示（「搬送指示を実行しますか？」）
        UI-->>App: ユーザーによる「OK」確定
        App->>API: POST形式でペイロード送信 (command: "TRANSFER", carrierId, source, destination)
        
        alt サーバー受付成功 (HTTP 200/201)
            API-->>App: 成功レスポンス返却
            App->>UI: トースト通知「ジョブ受付成功」ポップアップ ＆ フォームクリア
            App->>UI: 画面のロケーションを「SCR-004 (ジョブ監視)」へ強制リダイレクト
            Note over UI: 自動的に最新のキューパイプラインテーブルが再レンダリングされる
        else サーバー側インターロック等による拒否 (HTTP 500)
            API-->>App: 異常応答
            App->>UI: 「[UI-006] サーバーエラーが発生しました」を警告表示
        end
    end
