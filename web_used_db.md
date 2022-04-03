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
     - Log
     - Mapper
     - Exception
   - エラー制御
   - バリデーション
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
 - postgresユーザーでid(int)(プライマリキー), name(text), age(int)のカラムを持つusersテーブルを作成する
 - t-userにusersテーブルへ全ての操作権限を付与する

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
3. UserMapper.javaに対応させたいSQLをそれぞれ書く。

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
			#{id},
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
  d. 指定年齢未満のユーザーを数えるServiceを作成する  
  ※cだけだとMapperをそのまま使うだけで良いが、dのようなMapperを加工した処理を表現するためにServiceを作成している

```
package jp.co.web.service;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;

import jp.co.web.dto.UserDto;
import jp.co.web.repository.UserMapper;

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

  public String checkUnderAgeCount(Integer age) {

    Integer count = 0;
    List<UserDto> usersInfo = mapper.selectAll();

    for (UserDto p : usersInfo) {

      if (p.getAge() < age) {

        count++;
      }
    }

    return age + "歳未満のユーザーが" + count + "人存在します。";
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

```index.html
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
        <tr th:each="test : ${testList}">
            <td th:text="${test.id}"></td>
            <td th:text="${test.name}"></td>
            <td th:text="${test.age}"></td>
            <td>
                <a href="#" th:href="@{'/sample/edit/' + ${test.id}}"><button>編集</button></a>
            </td>
            <td>
                <form action="#" th:action="@{'/sample/delete/' + ${test.id}}" method="post">
                    <button>削除</button>
                </form>
            </td>
        </tr>
    </table>
    <div><a href="#" th:href="@{/sample/form}">入力フォーム</a></div>
</body>
</html>
```

```form.html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>CRUD Sample</title>
</head>
<body>
    <h2>入力フォーム</h2>
    <form method="post" action="#" th:action="@{/sample/form}" th:object="${testForm}">
        <label for="name">名前</label>
        <input type="text" id="name" name="name" th:value="${testForm.name}"><br>
        <label for="age">年齢</label>
        <input type="text" id="age" name="age" th:value="${testForm.age}"><br>
        <input type="submit" value="送信">
    </form>
    <div><a href="#" th:href="@{/sample}">トップページ</a></div>
</body>
</html>
```

```edit.html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>CRUD Sample</title>
</head>
<body>
    <h2>編集フォーム</h2>
    <form method="post" action="#" th:action="@{'/sample/edit/' + ${testForm.id}}" th:object="${testForm}">
        <label for="name">名前</label>
        <input type="text" id="name" name="name" th:value="${testForm.name}"><br>
        <label for="age">年齢</label>
        <input type="text" id="age" name="age" th:value="${testForm.age}"><br>
        <input type="submit" value="送信">
    </form>
    <div><a href="#" th:href="@{/sample}">トップページ</a></div>
</body>
</html>
```

```check.html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>CRUD Sample</title>
</head>
<body>
    <h2>指定年齢未満のユーザー数確認</h2>
    <p th:text="${msg}"></p>
    <div><a href="#" th:href="@{/sample}">トップページ</a></div>
</body>
</html>
```
