# DBを使用したWebアプリ初心者向け課題
DBを扱う理由とWebアプリの基本を学ぶ

## 学んでほしいこと
 - CRUD
 - Spring Boot
   - 基本構成
     - プロパティファイル
     - Controller
     - Service
     - Dto
     - Mapper
   - DI
   - 使用した依存関係について
 - データベース
   - アプリとの関係性
   - 基本的な運用

## 前提
 - [データベース初心者向け課題](db.md)と[Webアプリ初心者向け課題](web.md)を行っていること。

## 1. Spring Bootのプロジェクトを作成
以下の要件でSpring Bootのプロジェクトを作成する。
 - Spring スターター・プロジェクトから作成
 - 名前: SampleWebApp
 - タイプ: Gradle Project
 - Javaバージョン: 17
 - グループ: jp.co.web
 - 成果物: SampleWebApp
 - パッケージ: jp.co.web
 - Spring Boot バージョン: 2.6.4
 - 依存関係: Spring Web, Spring Boot DevTools, Thymeleaf, Lombok, MyBatis Framework, PostgreSQL Driver, Validate

## 2. データベースを作成
以下の要件でデータベースを作成する。  
※ここで言うユーザーはデータベースユーザーを指すことに注意してください
 - centos01のデータベースにpostgresユーザーで**web**というデータベースを作成する
 - postgresユーザーでid(int)(プライマリキー), name(text), age(int)のカラムを持つ**users**テーブルを作成する
 - postgresユーザーで**users_id_seq**というシーケンスを作成する
 - t-userにusersテーブルとusers_id_seqシーケンスへ全ての操作権限を付与する

## 3. Spring BootにDtoを作成する
1. 「src/main/java/」配下にjp.co.web.dtoパッケージを作成する(パッケージは「/」を「.」と扱う)。
2. 1で作成したパッケージ配下にUserDto.java(クラス)を以下の条件で作成する。  
  a. ゲッターとセッターはlombokのアノテーションを使用する  
  b. id(Integer), name(String), age(Integer)を定義する  
  c. Nullを許可しない  
  d. ageのみ10~60の値の制約を設ける  
  e. Componentアノテーションを付与する

```
package jp.co.web.dto;

import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;

import org.springframework.stereotype.Component;

import lombok.Data;

@Data
@Component
public class UserDto {

  @NotNull
  private Integer id;

  @NotNull
  private String name;

  @NotNull
  @Min(10)
  @Max(60)
  private Integer age;
}
```
## 4. Spring BootにMapperを作成する
1. 「src/main/java/」配下にjp.co.users.repositoryパッケージを作成する。
2. 1で作成したパッケージ配下にUserMapper.java(クラス)を以下の条件で作成する。  
  a. インターフェースとする  
  b. Mapperアノテーションを付与する  
  c. 全件取得用関数をDtoのリストを戻り値として作成  
  d. 1件取得用関数をDtoを戻り値として作成  
  e. ユーザー登録用関数をIntegerを戻り値として作成  
  f. ユーザー情報更新用関数をIntegerを戻り値として作成 　
  g. ユーザー情報削除用関数をIntegerを戻り値として作成

```
package jp.co.web.repository;

import java.util.List;

import org.apache.ibatis.annotations.Mapper;

import jp.co.web.dto.UserDto;

@Mapper
public interface UserMapper {

  public List<UserDto> selectAll();

  public UserDto selectOne(Integer id);

  public Integer insertOne(UserDto dto);

  public Integer updateOne(UserDto dto);

  public Integer deleteOne(Integer id);
}
```
 
## 5. Mapperが使用するSQLファイルを作成する
1. 「src/main/resource/」配下にjp.co.web.repositoryパッケージを作成する。
2. 1で作成したパッケージ配下にUserMapper.xml(ファイル)を作成する。 
3. UserMapper.javaに対応させたいSQLをそれぞれ書く(insertOneのidはシーケンスを使用する)。

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="jp.co.web.repository.UserMapper">
	<select id="selectAll" resultType="jp.co.web.dto.UserDto">
		SELECT
			id,
			name,
			age
		FROM
			users
	</select>
	<select id="selectOne" parameterType="int" resultType="jp.co.web.dto.UserDto">
		SELECT
			id,
			name,
			age
		FROM
			users
		WHERE
			id = #{id}
	</select>
	<insert id="insertOne" parameterType="jp.co.web.dto.UserDto">
		INSERT INTO
			users
		VALUES (
			nextval('users_id_seq'),
			#{name},
			#{age}
		)
	</insert>
	<update id="updateOne" parameterType="jp.co.web.dto.UserDto">
		UPDATE
			users
		SET
			name = #{name},
			age = #{age}
		WHERE
				id = #{id}
	</update>
	<delete id="deleteOne" parameterType="int">
		DELETE FROM
			users
		WHERE
			id = #{id}
	</delete>
</mapper>
```

## 6. Spring BootにServiceを作成する
1. 「src/main/java/」配下にjp.co.web.serviceパッケージを作成する。
2. 1で作成したパッケージ配下にUserService.java(クラス)を以下の条件で作成する。  
  a. UserMapperをDIする  
  b. Serviceアノテーションを付与する  
  c. Mapperの各関数をそのまま戻り値とするServiceを作成する  
  ※今回の場合はMapperをそのまま使うだけで良いが、本来はServiceを置くことでMapperを返す前に中間処理を加えたりする

```
package jp.co.web.service;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;

import jp.co.web.dto.UserDto;
import jp.co.web.repository.UserMapper;

@Service
public class UserService {

  @Autowired
  private UserMapper mapper;

  public List<UserDto> selectAll() {

    return mapper.selectAll();
  }

  public UserDto selectOne(Integer id) {

    return mapper.selectOne(id);
  }

  public Integer insertOne(UserDto dto) {

    return mapper.insertOne(dto);
  }

  public Integer updateOne(UserDto dto) {

    return mapper.updateOne(dto);
  }

  public Integer deleteOne(Integer id) {

    return mapper.deleteOne(id);
  }
}
```
## 7. 各画面表示用テンプレートを作成
1. 「src/main/resources/templates/」配下にsampleパッケージを作成する(templates.sampleとなる)。
2. templates.sample配下に各画面表示用のHTMLファイルを作成する。
3. 作成するのは以下の4画面分のファイル
 - index.html(一覧画面)
 - form.html(新規作成画面)
 - edit.html(編集画面)
 - check.html(指定年齢未満のユーザー数確認画面)

index.html
```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>CRUD Sample</title>
</head>
<body>
    <h2>一覧ページ</h2>
    <table>
        <tr><th>id</th><th>name</th><th>age</th></tr>
        <tr th:each="dto : ${dtoList}">
            <td th:text="${dto.id}"></td>
            <td th:text="${dto.name}"></td>
            <td th:text="${dto.age}"></td>
            <td>
                <a href="#" th:href="@{'/sample/edit/' + ${dto.id}}"><button>編集</button></a>
            </td>
            <td>
                <form action="#" th:action="@{'/sample/delete/' + ${dto.id}}" method="post">
                    <button>削除</button>
                </form>
            </td>
        </tr>
    </table>
    <div><a href="#" th:href="@{/sample/form}">入力フォーム</a></div>
</body>
</html>
```

form.html
```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>CRUD Sample</title>
</head>
<body>
    <h2>入力フォーム</h2>
    <form method="post" action="#" th:action="@{/sample/form}" th:object="${dto}">
        <label for="name">名前</label>
        <input type="text" id="name" name="name" th:value="${dto.name}"><br>
        <label for="age">年齢</label>
        <input type="text" id="age" name="age" th:value="${dto.age}"><br>
        <input type="submit" value="送信">
    </form>
    <div><a href="#" th:href="@{/sample}">トップページ</a></div>
</body>
</html>
```

edit.html
```
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>CRUD Sample</title>
</head>
<body>
    <h2>編集フォーム</h2>
    <form method="post" action="#" th:action="@{'/sample/edit/' + ${dto.id}}" th:object="${dto}">
        <label for="name">名前</label>
        <input type="text" id="name" name="name" th:value="${dto.name}"><br>
        <label for="age">年齢</label>
        <input type="text" id="age" name="age" th:value="${dto.age}"><br>
        <input type="submit" value="送信">
    </form>
    <div><a href="#" th:href="@{/sample}">トップページ</a></div>
</body>
</html>
```

## 8. Spring BootにControllerを作成する
1. 「src/main/java/」配下にjp.co.web.controllerパッケージを作成する。
2. 1で作成したパッケージ配下にUserController.java(クラス)を以下の条件で作成する。  
  a. UserServiceをDIする  
  b. Controllerアノテーションを付与する  
  c. 各画面用のメソッドを作成する

```
package jp.co.web.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import jp.co.web.dto.UserDto;
import jp.co.web.service.UserService;

@Controller
@RequestMapping("/sample")
public class UserController {

  @Autowired
  private UserService service;

  //一覧画面の表示
  @GetMapping
  public String index(Model model) {
    model.addAttribute("dtoList", service.selectAll());
    return "sample/index";
  }

  //新規入力フォームの表示
  @GetMapping("/form")
  public String form(@ModelAttribute("dto") UserDto dto) {
    return "sample/form";
  }

  //新規入力データの保存
  @PostMapping("/form")
  public String create(UserDto dto) {
    service.insertOne(dto);
    return "redirect:/sample";
  }

  //編集フォームの表示
  @GetMapping("/edit/{id}")
  public String edit(@ModelAttribute("dto") UserDto dto, @PathVariable Integer id) {
    UserDto result = service.selectOne(id);
    dto.setId(result.getId());
    dto.setName(result.getName());
    dto.setAge(result.getAge());
    return "sample/edit";
  }

  //編集データの保存
  @PostMapping("/edit/{id}")
  public String update(UserDto dto, @PathVariable Integer id) {
    dto.setId(id);
    service.updateOne(dto);
    return "redirect:/sample";
  }

  //データの削除
  @PostMapping("/delete/{id}")
  public String delete(@PathVariable Integer id) {
    service.deleteOne(id);
    return "redirect:/sample";
  }
}
```
## 9. Spring Bootのプロパティファイルの設定
1. application.propertiesをapplication.ymlにリネームする
2. application.ymlに以下の設定をする。  
  a. portは8080  
  b. PostgreSQLの接続情報を環境変数でオーバーライドできる形で記入する  
  c. アプリ側でコネクションプーリングは使わない  
  d. SQLのログを取得する  
  e. Mybatisはキャッシュを利用しない  
  f. プリペアドを使い回す  
  ※必要であれば以下のようにSSHトンネリングを行う
  >ssh -i ~/.ssh/id_rsa t-user@32.56.131.4 -L 5432:32.56.131.4:5432

```
# ウェブ
server:
  port: 8080

# データベース 
spring:
  datasource:
    driverClassName: org.postgresql.Driver
    url: jdbc:postgresql://${DBHOST:localhost}:${DBPORT:5432}/${DBNAME:web}?serverTimezone=JST
    username: ${DBUSER:t-user}
    password: ${DBPASS:TestCentOS}
    # コネクションプーリングしない
    type: org.springframework.jdbc.datasource.DriverManagerDataSource

# SQLのログをMapperから出力する
logging:
  level:
    jp:
      co:
        web:
         repository: DEBUG

# Mybatisの設定
mybatis:
  configuration:
    # キャッシュを使わない
    cache-enabled: false
    # PreparedStatementを使います動作
    defaultExecutorType: REUSE
```
## 10.　動作確認
Spring Bootアプリケーションとして起動し、 http://localhost:8080/sample にアクセスしてWebアプリを触ってみる
 - 画面の遷移
 - データの登録
 - データの編集
 - データの削除

### 予告
このアプリは以下のような問題を抱えているので解決する。
 - データ登録時と編集時に入力データのバリデーションが行えていない
 - DB接続エラーなどのエクセプションが起きた場合などのエラーハンドリングがされていない
 - ログを見ても処理内容が追えない
