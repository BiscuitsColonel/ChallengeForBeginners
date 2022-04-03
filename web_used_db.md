# DBを使用したWebアプリ初心者向け課題
DBを扱う理由とWebアプリの基本を学ぶ

## 学んでほしいこと
 - CRUD
 - Api
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
   - 使用した依存関係について
 - データベース
   - アプリとの関係性
   - 基本的な運用

## 前提
 - [データベース初心者向け課題](db.md)と[Webアプリ初心者向け課題](web.md)を行っていること。

## 1. Spring Bootのプロジェクトを作成
以下の要件でSpring Bootのプロジェクトを作成する。
 - Spring スターター・プロジェクトから作成
 - 名前: BaseBallApp
 - タイプ: Gradle Project
 - Javaバージョン: 17
 - グループ: jp.co.baseball
 - 成果物: BaseBallApp
 - パッケージ: jp.co.baseball
 - Spring Boot バージョン: 2.6.4
 - 依存関係: Spring Web, Spring Boot DevTools, Thymeleaf, Lombok, MyBatis Framework, PostgreSQL Driver, Validate

## 2. データベースを作成
以下の要件でデータベースを作成する。  
※ここで言うユーザーはデータベースユーザーを指すことに注意してください
 - centos01のデータベースにpostgresユーザーでbaseballというデータベースを作成する
 - postgresユーザーでid(int)(プライマリキー), name(text), age(int), team(text), position(text)のカラムを持つplayers_tblテーブルを作成する
 - postgresユーザーでplayers_seqシーケンスを作成する
 - t-userにplayers_tblテーブルへ全ての操作権限を付与する

## 3. Spring BootにDtoを作成する
1. 「src/main/java/」配下にjp.co.baseball.dtoパッケージを作成する(パッケージは「/」を「.」と扱う)。
2. 1で作成したパッケージ配下にPlayersDto.java(クラス)を以下の条件で作成する。  
  a. ゲッターとセッターはlombokのアノテーションを使用する  
  b. name(String), age(Integer), team(String), position(String)を定義する  
  c. Nullを許可しない  
  d. ageのみ10~60の値の制約を設ける
  e. Componentアノテーションを付与する

```
package jp.co.baseball.dto;

import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotBlank;

import org.springframework.stereotype.Component;

import lombok.Data;

@Data
@Component
public class PlayersDto {

  @NotBlank
  private Integer id;

  @NotBlank
  private String name;

  @NotBlank
  @Min(10)
  @Max(60)
  private Integer age;

  @NotBlank
  private String team;

  @NotBlank
  private String position;
}

```
## 4. Spring BootにMapperを作成する
1. 「src/main/java/」配下にjp.co.baseball.repositoryパッケージを作成する。
2. 1で作成したパッケージ配下にPlayersMapper.java(クラス)を以下の条件で作成する。  
  a. インターフェースとする  
  b. Mapperアノテーションを付与する  
  c. 全件取得用関数をDtoのリストを戻り値として作成  
  d. 1件取得用関数をDtoを戻り値として作成  
  e. 選手登録用関数をIntegerを戻り値として作成  
  f. 選手登録削除用関数をIntegerを戻り値として作成

```
package jp.co.baseball.repository;

import java.util.List;

import org.apache.ibatis.annotations.Mapper;

import jp.co.baseball.dto.PlayersDto;

@Mapper
public interface PlayersMapper {

  // 全件取得
  public List<PlayersDto> findAll();

  // 1件取得
  public PlayersDto findOne(Integer id);
  
  // 選手登録
  public Integer insertOne(PlayersDto dto);
  
  // 選手登録削除
  public Integer deleteOne(Integer id);
}

```
 
