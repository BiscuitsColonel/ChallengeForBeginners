# Linux初心者向け課題
Linuxをなんとなくで触れてみる。

## 学んでほしいこと
 - VirtualBox
 - Linux
 - ディレクトリとファイル
 - 標準出力
 - SSH
 - 正規表現

## 1. VirtualBoxのインストール
VirtualBoxをOracleのページからダウンロードしてインストールする。  
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
 - 仮想ディスクサイズ： 8GB
 - 仮想ハードディスクのファイルタイプ：VDI
 - 可変or固定：可変サイズ

### 2-2. VirtualBox上に新規仮想マシンを作成
以下の要件で作成する。
 - VM名：centos02
 - タイプ：Linux
 - バージョン：Red Hat(64-bit)
 - メモリ：1024MB
 - ディスク名：centos02
 - ディスクサイズ： 8GB
 - ハードディスクのファイルタイプ：VDI
 - 可変or固定：可変サイズ

## 3. ISOファイルの割り当て
### 3-1. インストール用ISOイメージをダウンロード
CentOSのインストール用ISOイメージをダウンロードする。  
バージョンは **CentOS7.9.2009** とする。

### 3-2. ISOファイルを割り当てる
各VMのストレージにあるIDEコントローラーへISOファイルをディスクとして追加する。

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
 - インストール先：パーティションを自動構成する
 - ネットワーク：すべて有効
 - ホスト名：centos01
 - ROOTパスワード：TestCentOS
 - ユーザーの作成：しない

### 5-2. 二つ目のVMの要件
以下の要件でインストールする。
 - タイムゾーン：東京
 - キーボード：日本語
 - ソフトウェアの選択：最小限のインストール
 - インストール先：パーティションを自動構成する
 - ネットワーク：すべて有効
 - ホスト名：centos02
 - ROOTパスワード：TestCentOS
 - ユーザーの作成：しない

## 6. パッケージの更新
各VMですべてのパッケージを更新する。

## 7. 不要サービスの無効化
各VMで **firewalld** のサービスを停止し、自動起動設定を **off** にする。

## 8. SELinuxの無効化
各VMで無効化の設定をし、OSを再起動してもSELinuxが起動しないようにする。

## 9. 個人ユーザの作成
各VMでユーザを以下の要件で作成する。
 - ユーザ名：t-user
 - ホームディレクトリ：作成する
 - パスワード：TestCentOS

## 10. 手順9で作成した個人ユーザーで鍵を使ったSSH接続を可能とさせる
要件は以下です。
 - centos01のt-userからcentos02のt-userへ鍵認証のSSH接続をできるようにしたい
 - キーペアの作成はcentos01で行う
 - centos02にcentos01で作成した公開鍵を登録する

## 11. シェルスクリプトの作成
centos02で以下の要件でシェルスクリプトを作成する。
 - **/home/t-user/bin** ディレクトリを作成
 - 絶対パスは **/home/t-user/bin/test.sh** とする
 - パーミッションは **755** とする
 - ホスト名と現在時刻(フォーマットは問わない)を標準出力するものを作成

## 12. Webサーバの構築
### 12-1. Apacheのインストール
centos02でApacheをインストールする。

### 12-2. Apacheの起動
centos02でApacheを起動する。  
自動起動設定は **on** にする。

### 12-3. ディレクトリの所有者を変更
centos2の **/var/www/html** ディレクトリの所有者とグループを **t-user** に変更する。

## 13. シェルスクリプトの定期実行
centos02で以下の要件でcronを設定
 - 実行ユーザーは **t-user**
 - **1分** ごとに実行
 - 手順11で作成したシェルスクリプトの標準出力を **/var/www/html/index.html** ファイルへ上書き出力する

## 14. 確認 
### 14-1. 鍵認証SSH接続の確認
**centos01のt-user**から**centos02のt-user**へIPアドレスを指定して**パスワードを使用せず**にSSH接続できることを確認する

### 14-2. ウェブ画面の確認
centos02のウェブ画面をブラウザ(「http://」+「centos02のIPアドレス」で検索)から確認する。  
1分毎に表示される時刻が変化することが確認できる。

## おまけ
### 1. シェルスクリプトの作成
centos01で以下の要件でシェルスクリプトを作成する。
 - 内容が空っぽの1.txt~10.txtを作成する
 - シェルスクリプト中に「for」もしくは「while」コマンドを使用する
 - シェルスクリプト中に「if」と「test」コマンドを使用する
 - ただし 「3の倍数」.txt となるファイルは作成しない

### 2. 1で作成されたシェルスクリプトを実行
実行結果として作成されるファイルが以下のようになることを確認する。  

```
1.txt  10.txt  2.txt  4.txt  5.txt  7.txt  8.txt
```
### 3. 「ls」コマンドの標準出力を「|(パイプ)」で「grep」コマンドに渡し、ファイル名が「1」もしくは「4」で始まる(「正規表現」を用いること)ファイルを標準出力に出力する
実行結果が以下のようになることを確認する。 

```
1.txt
10.txt
4.txt
```
