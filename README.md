# 初心者向け課題
URLとかは貼りません!  
エンジニアは自分でググってなんぼです(とは言え軽く手順は記載します)。  
この課題がクリアできればエンジニア向きだと思いますので頑張ってください:thumbsup:

## 1. VirtualBoxのインストール
VirtualBoxをOracleのページからダウンロードしてインストールしてください。  
特にバージョンは指定しないです。

## 2. VMの作成
VMを2つ作成する。

### 2-1. VirtualBox上に新規仮想マシンを作成
以下の要件で作成する。
 - VM名：centos01
 - タイプ：Linux
 - バージョン：Red Hat(64-bit)
 - メモリ：1024MB
 - 仮想ディスク名：centos01
 - 仮想ディスクサイズ： 5GB
 - 仮想ハードディスクのファイルタイプ：VDI
 - 可変or固定：可変サイズ

### 2-2. VirtualBox上に新規仮想マシンを作成
以下の要件で作成する。
 - VM名：centos02
 - タイプ：Linux
 - バージョン：Red Hat(64-bit)
 - メモリ：1024MB
 - ディスク名：centos02
 - ディスクサイズ： 5GB
 - ハードディスクのファイルタイプ：VDI
 - 可変or固定：可変サイズ

## 3. ISOファイルの割り当て
### 3-1. インストール用ISOイメージをダウンロード
CentOSのインストール用ISOイメージをダウンロードしてください。
バージョンは **CentOS7.9.2009** でお願いします。

### 3-2. ISOファイルを割り当てる
各VMのストレージにあるIDEコントローラーへISOファイルをディスクとして追加

## 4. ネットワークの設定
### 4-1. ホストオンリーネットワークを作成
以下の要件でホストネットワークマネージャーから作成する。
 - DHCPサーバは有効
 - アダプターは手動で設定

### 4-2. ネットワークアダプタ2を追加
各VMのネットワークにアダプタ2を有効化し、ホストオンリーアダプタの割り当てをする。

## 5. CentOSのインストール
### 5-1. 一つ目のVMの要件
以下の要件でインストールする。
 - タイムゾーン：東京
 - キーボード：日本語
 - ソフトウェアの選択：最小限のインストール
 - インストール先：パーティションを自動動構成する
 - ネットワーク：すべて有効
 - ホスト名：ホスト名 centos01
 - ROOTパスワード：TestCentOS
 - ユーザーの作成：しない

### 5-2. 二つ目のVMの要件
以下の要件でインストールする。
 - タイムゾーン：東京
 - キーボード：日本語
 - ソフトウェアの選択：最小限のインストール
 - インストール先：パーティションを自動動構成する
 - ネットワーク：すべて有効
 - ホスト名：ホスト名 centos02
 - ROOTパスワード：TestCentOS
 - ユーザーの作成：しない

## 6. パッケージの更新
各VMですべてのパッケージを更新する。

## 7. 不要サービスの無効化
以下のサービスを停止し、自動起動設定をoffにする。
 - postfix
 - firewalld

## 8. SELinuxの無効化
無効化の設定をし、OSを再起動してもSELinuxが起動しないようにする。

## 9. 個人ユーザの作成
ユーザを以下の要件で作成してください。
 - ユーザ名：k-ryunosuke
 - ホームディレクトリ：作成する
 - パスワード：TestCentOS
