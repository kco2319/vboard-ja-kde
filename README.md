# vboard-ja-kde

[mdev588/vboard](https://github.com/mdev588/vboard)（uinput ベースの仮想キーボード、LGPL-2.1）をベースにした、日本語 JIS 配列対応版です。

KDE Plasma 6 (Wayland) 環境で、XWayland 経由の Onboard に長押し／スティッキーキーのフリーズ不具合があったため、代替として作成しました。タッチ操作のみの環境（Ascon AT-08 タブレット等）での運用を想定しています。

## Features

- **JIS (pc106) 配列対応**
  - `@` `[` `]` `:` `^` `-` `;` など、US配列との差分キーをJISの刻印通りに再配置
  - 数字キーのShift記号もJIS準拠（`2→"`、`6→&`、`7→'`、`8→(`、`9→)` 等）
  - ¥キー・ろ(\\)キーを最上段に独立したボタンとして追加（KEY_YEN / KEY_RO）
  - `(バッククォート)キーはMozcの半角/全角切替として機能するため、そのまま送信
- **タッチ操作専用UI**
  - ヘッダーバーの ✕(閉じる)・□(最大化) ボタンは長押し(600ms)方式。誤タップによる意図しないウィンドウ操作を防止
  - リサイズはドラッグ非依存の「⤢+ / ⤢-」ステップ方式（タップごとに40pxずつ増減）。タッチ環境ではドラッグ系のイベントが不安定なための対策
  - 三本線メニューでUI要素の表示/非表示を切替可能
- **背景色・不透明度のカスタマイズ**（ヘッダーバーのコンボボックス・+/-ボタンから変更可能）
- 設定は `~/.config/vboard/settings.conf` に保存され次回起動時に復元

## インストール

```bash
# 依存パッケージ
sudo apt install python3-uinput

# 本体を取得
git clone https://github.com/<your-account>/vboard-ja-kde.git
cd vboard-ja-kde
chmod +x vboard.py
```

### uinput の権限設定

```bash
sudo modprobe uinput
echo "uinput" | sudo tee /etc/modules-load.d/uinput.conf
sudo usermod -aG input $USER
echo 'KERNEL=="uinput", GROUP="input", MODE="0660"' | sudo tee /etc/udev/rules.d/99-uinput.rules
sudo udevadm control --reload-rules && sudo udevadm trigger
```

（グループ変更を反映するには再ログインが必要です）

### 自動起動

```bash
mkdir -p ~/.config/autostart
cat << 'EOF' > ~/.config/autostart/vboard.desktop
[Desktop Entry]
Exec=bash -c 'python3 ~/vboard.py'
Name=Vboard
Type=Application
X-KDE-AutostartScript=true
EOF
```

### Arch Linux (AUR)

`PKGBUILD` を同梱しています。タグ付きリリースを公開後、以下でビルド・インストールできます。

```bash
git clone https://github.com/<your-account>/vboard-ja-kde.git
cd vboard-ja-kde
makepkg -si
```

このパッケージはユーザーごとの `~/.config/autostart` ではなく `/etc/xdg/autostart/` にデスクトップエントリを配置するため、同じ機種に複数のユーザーアカウント(`fk`・`mk` 等)を作成するキッティング運用でも、アカウントごとの自動起動設定が不要です。

## 動作環境

- Debian 13 + KDE Plasma 6 (Wayland)
- Python 3 / GTK 3 (PyGObject) / python-uinput
- キーボードレイアウトが日本語(JIS/jp)に設定されていることが前提です
  （fcitx5 等のIMEが `~/.config/kxkbrc` を英語配列に上書きしてしまう既知の問題があるため、
  レイアウトを固定運用する場合は別途対策が必要です）
**********************************************************
・「KDE システム設定」＞「入力メソッド」タブ＞下部にある「システムキーボードのレイアウトを選択」
・「レイアウト」のプルダウンメニューから、「日本語」を選択し「OK」
・「入力メソッドが選択したレイアウトと一致しません。・・・」のメッセージが表示されるので、「修正」をクリックしてｘで閉じる（→入力メソッドオフ、が「キーボード - 日本語」に書き換えられる）
・右下「入力メソッドを追加」→「キーボード - 日本語(OADG 109A)」を選択し、「追加」
・入力メソッドオン、にある「キーボード - 英語(US)」を「削除」、「適用」
(以上は、以下のサイトを参考にしています）
 [RefreshOS「KDE Plasma」での日本語環境の構築 - アラコキからの Raspberry Pi 電子工作](https://arakoki70.com/?p=12547)
**********************************************************
動作確認機種: Ascon AT-08 (Celeron N4120 / RAM 4GB / eMMC 64GB)

## 既知の制約

- タッチ環境ではポインタのモーションイベント（ドラッグ）が届かないため、ウィンドウの対話的リサイズ（`begin_resize_drag`）は使用していません
- KWinの対話的リサイズ機構と組み合わせるとプラズマシェルがクラッシュする事例を確認しているため、意図的に不採用としています

## ライセンス

本家 [mdev588/vboard](https://github.com/mdev588/vboard) を継承し、**LGPL-2.1** で公開します。詳細は [LICENSE](./LICENSE) を参照してください。

## 関連プロジェクト

継続開発版として [archisman-panigrahi/vboard](https://github.com/archisman-panigrahi/vboard)（GPLv3、JSONレイアウト対応・KDE Wayland公式対応・AUR/PPA/.deb配布あり）が存在します。将来的には本リポジトリのJIS配列対応を継続版へコントリビュートすることも検討しています。

## 変更履歴

本家との差分は [CHANGES.md](./CHANGES.md) を参照してください。
