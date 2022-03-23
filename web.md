# Webアプリ初心者向け課題
Spring BootでJavaを用いてWebアプリを作ってみる。

## 学んでほしいこと
 - IDEとは
 - Spring Bootとは
 - MVCモデルとは
 - java 

## 1. IDEのインストール
Spring Bootを簡単に扱えるおすすめのIDEは以下。　　
 - Eclipse
 - IntelliJ IDEA

## 2. SpringBootの使用
以下の要件でSpringBootのプロジェクトを作成する。
 - Spring スターター・プロジェクトから作成
 - 名前: HelloWorldApp
 - タイプ: Gradle Project
 - Javaバージョン: 17
 - グループ: jp.co.helloworld
 - 成果物: HelloWorldApp
 - パッケージ: jp.co.helloworld
 - Spring Boot バージョン: 2.6.4
 - 依存関係: Spring Web, Spring Boot DevTools, Thymeleaf

## 3. WebページとしたいHTMLファイルを作成
**src/main/resources/templates**配下に**index.html**を以下の内容で作成する。

```
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Hello</title>
</head>
<body>
	<h1>Hello World</h1>
	<p th:text="${msg1}"></p>
	<h2 th:text="${msg2}"></h2>
</body>
</html>
```

## 4. コントローラを作成
1. **src/main/java/jp/co/helloworld**配下に**controller**パッケージを作成する。
2. 1で作成したパッケージ配下に**SumController.java**(クラス)を以下の内容で作成する。

```
package jp.co.helloworld.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class SumController {

  @RequestMapping("/{num}")
  public String index(@PathVariable int num, Model model) {

    int sum = 0;
    for (int i = 1; i <= num; i++) {

      sum += i;
    }
    model.addAttribute("msg1", "1~" + num + "まで順に数値を足した結果を表示します。");
    model.addAttribute("msg2", "合計値は" + sum + "です。");
    return "index";
  }
}
```
## 5. HelloWorldAppを起動する。
Spring Bootアプリケーションとして起動する。

## 6. 確認
### 1. ブラウザから「http\://localhost:8080/100」で検索した結果が以下の画像と一致するかを確認する。
![hello_web](/img/hello_web.png) 

### 2. curlコマンドの結果が以下と一致するかを確認する。

```
$ curl http://localhost:8080/25
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Hello</title>
</head>
<body>
	<h1>Hello World</h1>
	<p>1~25まで順に数値を足した結果を表示します。</p>
	<h2>合計値は325です。</h2>
</body>
</html>
```
