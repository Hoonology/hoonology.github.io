---
date: 2023-04-04 12:00:00
layout: post
title: 쇼핑몰
subtitle: Fastify를 이용해 DB와 통신하는 서버 만들기
description: Fastify를 이용해 DB와 통신하는 서버 만들기
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681139412/dev-jeans_v2eutk.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681139412/dev-jeans_v2eutk.png 
category: blog
tags:
  - github
  - blog
author: Hoonology
comments: true

---
# INDEX & Goals
- [Fastify를 이용해 DB와 통신하는 서버 만들기](#fastify를-이용해-db와-통신하는-서버-만들기)
    - [1. ```.env``` 파일 작성](#1-우선-elephantsql의-연결-정보를-env-파일에-작성한다)
    - [2. fastify 프로젝트 생성](#fastify-프로젝트-생성)
- [WAS와 데이터베이스 연결](#was와-데이터베이스-연결)
- [CRUD 구현](#crud-구현)
  - [엔드포인트 구분](#엔드포인트-구분)
    - [쿼리문(테이블)](#쿼리문)
      - [/items ( 상품 목록 )](#items--상품-목록)
      - [/cart  (장바구니)](#cart--장바구니)
- [회고](#회고)
---
### 쇼핑몰 요구사항
- [✔] [사용자는 모든 상품을 조회할 수 있다]
- [✔] [사용자는 특정 분류의 상품을 조회할 수 있다(상품분류, 브랜드명, 가격, 상품명)] 
- [✔] [사용자의 타입이 판매자인 경우 자신의 상품을 등록할 수 있다 ]
- [✔] [사용자는 상품을 장바구니에 담을 수 있다]
- [✔] [사용자는 자신의 장바구니를 조회할 수 있다]
- [✔] [사용자는 자신의 장바구니에 있는 상품의 수량을 변경시킬 수 있다]
- [✔] [사용자는 상품을 자신의 장바구니에서 제외할 수 있다]
---
## Fastify를 이용해 DB와 통신하는 서버 만들기

## ERD
![image](https://github.com/Hoonology/Hoonology/assets/105037141/a23baf47-e87c-4a51-a488-3bce210d651b)



## API 문서 
[Swagger](https://app.swaggerhub.com/apis/seay0/shopping_mall/1.0.0#/)

### 1. 우선 ElephantSQL의 연결 정보를 ```.env``` 파일에 작성한다.
  ```bash
  HOSTNAME=satao.db.elephantsql.com
  USERNAME=jvhigyac
  PASSWORD=4uSs-7B21LIZfI17Fzf1acdcJCG87ke
  DATABASE=jvhigyac
  ```
> shoping-bag 폴더에 저장 후 Push 

<br>

### 2. fastify 프로젝트 생성

```bash
npm install -g fastify-cli
```  
shopping-bag 폴더 안에
```
fastify generate shopping-bag
```
shopping-bag 폴더 접근
```bash
cd shopping-bag
# 디펜던시 설치
npm i # node_modules 폴더가 생성된다. 
```
3000번 포트를 통해 WAS 접근한다.
```bash
npm run dev
# 다른 프로세스가 3000번 포트가 연결되어 있을 경우,  
# npm run dev -- --port 3001을 실행하여 3000 대신 포트 3001에서 서버를 시작합니다.
```

## WAS와 데이터베이스 연결

https://github.com/fastify/fastify-postgres 참고
- ```postgres.js``` 파일 내용 (향후 수정 내용)
```bash
fastify.register(require('@fastify/postgres'), {
  connectionString: 'postgres://postgres@localhost/postgres'
})
```

환경변수로 불러오기
``` js
const {
  DATABASE_USER,
  DATABASE_PASSWORD,
  DATABASE_HOST,
  DATABASE_NAME } = process.env
```

위에서 불러온 ```postgres.js```를 아래와 같이 수정 
```js
module.exports = fp(async function (fastify, opts) {
fastify.register(require('@fastify/postgres'), {
connectionString: `postgres://${DATABASE_USER}:${DATABASE_PASSWORD}@${DATABASE_HOST}/${DATABASE_NAME}}`
})
```
---
## CRUD 구현

### 엔드포인트 구분
우리가 직접 구현할 엔드포인트는 3가지로,  ```users```, ```items```, ```cart```가 있다.  
선행으로 ElephantSQL에 아래 쿼리문들을 작성하여 DB 작업을 진행한다.

#### 쿼리문
  - 테이블 생성 ***CREATE***
       ``` sql
    DROP TABLE IF EXISTS public.users;

    CREATE TABLE public.users (
              user_id integer NOT NULL,
              user_name integer NOT NULL,
              is_seller boolean NOT NULL,
              CONSTRAINT users_id_pk PRIMARY KEY (user_id)
	    );
    ```
    ``` sql
    DROP TABLE IF EXISTS public.items;

    CREATE TABLE public.items (
	          item_id integer NOT NULL,
	          category varchar NOT NULL,
              brand varchar NOT NULL,
              price integer NOT NULL,
              item_name varchar NOT NULL,
              CONSTRAINT items_id_pk PRIMARY KEY (item_id)
	    );
    ```
    ``` sql
    DROP TABLE IF EXISTS public.cart;

    CREATE TABLE public.cart (
	          cart_id varchar NOT NULL,
	          user_id integer NOT NULL,
              item_id integer NOT NULL,
              item_cnt integer NOT NULL,
              CONSTRAINT cart_id_pk PRIMARY KEY (cart_id)
	    );
    ```
    


  - 항목 추가 ***INSERT INTO***
    ``` sql
      INSERT INTO users (user_id, username, is_seller)  
      VALUES (2580, 'Dohyun', false);
      INSERT INTO users (user_id, username, is_seller)  
      VALUES (1220, 'Seoyeon', true);
      INSERT INTO users (user_id, username, is_seller)  
      VALUES (3006, 'Seonghoon', false);
      ``` 
      ```sql
        INSERT INTO items (item_id, price, category, brand, item_name)  
        VALUES (1,400,'Clothing','Nike','SwooshHotPants');
        INSERT INTO items (item_id, price, category, brand, item_name)  
        VALUES (2,300,'Clothing','Nike','SwooshT-shirt');
      ```
  
#### * /items : 상품 목록   
- GET

![image](https://github.com/Hoonology/Hoonology/assets/105037141/7fc5ac8f-86aa-461b-9686-caf67dc7dcbe)

   ```js
    const fastify = require('fastify')();

    module.exports = async function(fastify, ops) {
      fastify.get('/', async function(request, reply) {

        const client = await fastify.pg.connect();
        try {
          const { rows } = await client.query(
            'SELECT * FROM items'
          )
          reply.code(200).send(rows)
        } finally {
          client.release()
        }
      })
    }
   ``` 

> fastify를 통해 GET 을 구현하기 위해 위와 같은 코딩을 하고,   
html 및 css 작업을 통해 localhost:3000/items에 접근 시 프론트로 작업된 화면이 보여지게 했다.

- POST (Authorization)
먼저, 판매자에게 토큰을 부여해주는 코드를 만들어보면,
```js
module.exports = {
    //aaa는 유저1, bbb는 유저2로 가정 
    tokenValidator: (token) => {
        let result;
        if (token === "Bearer aaa") { 
            result = false;
            // userId 1()은 DB 에서 구매자 (is_seller = false)
        } else if (token === "Bearer bbb") {
            result = true;
            // userId 2(시연님)은 DB 에서 판매자 (is_seller = true)
        } else if (token === "Bearer ccc") {
            result = false;
            // userId 3()은 DB 에서 구매자 (is_seller = false)
        } 
        return result
    }
}
```
is_seller 가 True인(판매자) 오시연만 ```Bearer bbb``` 토큰을 받게 된다.
> 현재 users 인원은 3명으로, 판매자는 1명이라는 전제 하에 토큰을 1개만 만들었다.

```js
const fastify = require('fastify')();

const { tokenValidator } = require("../controller/tokenValidator")

module.exports = async function(fastify, ops) {
  fastify.post('/', async function (request, reply) {
    try{ 
      const client = await fastify.pg.connect()
      let check_seller = tokenValidator(request.headers.authorization);
      
      if ( check_seller === 1220) {
         // console.log(check_seller); // 1220
        const { rows } = await client.query(
          `INSERT INTO items (item_id, category, brand, price, item_name)
          VALUES ('${request.body.item_id}','${request.body.category}','${request.body.brand}', '${request.body.price}', '${request.body.item_name}')`
        )
        reply.code(201).send('성공적으로 등록되었습니다!');
      }   
      else {
        console.log("Bed Request");
        
      }
    }
    catch(error) {
      //잘못된 유저 에러
      console.log("판매자만 상품을 등록할 수 있습니다!");
      reply.code(401).send('Un authorized');
    }
  });
}

```
check_seller - tokenValidator에 boolean 값을 넣고, 판매자의 토큰 값이면 ```201 code```로  POST를 성공 시킨다.
![image](https://github.com/Hoonology/Hoonology/assets/105037141/b1b68086-231d-4fb3-a6ce-a66c30c85213)


(좌) 400 Bad Request 응답  (우) 201 Created 응답

---

<br>

#### /cart : 장바구니   
- GET: http://localhost:3000/cart?user_id=${user_id} 방식으로 조회한다. ***엔드포인트에 쿼리문을 사용하여 구현하였다.***

```js 
module.exports = async function(fastify, ops) {
fastify.get('/cart', async function(request, reply) {
  const client = await fastify.pg.connect(); 
  const userId = request.query.user_id;
  const query = `SELECT * FROM public.cart WHERE user_id = '${userId}'`;
  const result = await client.query(query);
  return await reply.code(200).send(result.rows);
})    
}
```

- POST

```js
module.exports = async function(fastify, ops) {
    fastify.post('/cart', async function(request, reply) {
        const client = await fastify.pg.connect(); 
        const userId = request.body.user_id;
        const query = `SELECT * FROM public.cart WHERE user_id = '${userId}'`;

               const { rows } = await client.query(
            `INSERT INTO cart (cart_id, user_id, item_id, item_cnt)
            VALUES ('${request.body.cart_id}', '${request.body.user_id}', '${request.body.item_id}','${request.body.item_cnt}')`
            )
                
        const result = await client.query(query);
        return await reply.code(201).send(result.rows);
    })     
  } 
```
- PUT
``` js
module.exports = async function(fastify, ops) {
    fastify.put('/cart', async function(request, reply) {
        const client = await fastify.pg.connect(); 
        const userId = request.body.user_id;
        const query = `SELECT * FROM public.cart WHERE user_id = '${userId}'`;

        const item_id = request.body.item_id;
        const item_cnt = request.body.item_cnt;
        const cart_id = request.body.cart_id;

        

            const { rows } = await client.query(
                `UPDATE public.cart SET item_cnt=${item_cnt} WHERE cart_id='${cart_id}' and item_id=${item_id}`
            )

                
        const result = await client.query(query);

        return await reply.code(201).send(result.rows);
    })     
  } 
  ```
-  DELETE
  ```js
  module.exports = async function(fastify, ops) {
    fastify.delete('/cart', async function(request, reply) {
        const client = await fastify.pg.connect(); 
        const userId = request.body.user_id;
        const query = `SELECT * FROM public.cart WHERE user_id = '${userId}'`;

               const { rows } = await client.query(
            `DELETE FROM public.cart WHERE user_id = ${userId};`
            )
                
        const result = await client.query(query);
        return await reply.code(201).send(result.rows);
    })     
  } 
```
<br>
<br>

# 회고

우선, 팀 프로젝트를 진행하면서 느낀 점이 많다.  
프로젝트 요구 사항이 너무 높다고 불평만 했었는데 시간을 갈아서(?) 만들다 보니 나오는 결과에 만족을 하는 내 자신을 발견했다. 내 만족 가운데 가장 높았던 것은 DB 테이블의 프론트 구현 부분이다. GET 요청으로 fastify로 DB에 접근할 때, json 형식으로 쭉 나열되어 있는 못생긴 모습이 보기 싫어서 표로 만들어냈다 ! 이 외에는 ... 사실 코드를 읽는 데에도 한계가 있다.  
코드 리뷰는 따로 부탁하지 않으면 어려울 것 같다. 특히 js 형식에서의 코드리뷰는 꼭 필수적임  




몇 가지 내가 느낀 점에 대해 공유를 하겠다.
- 레퍼런스를 많이 뒤져봐야겠다.
- 인증 토큰을 받아서 접근할 수 있는 패스를 구성하는 것 이해가 필요하다. (Authorization)
- ***API 문서*** ! 만드는 방법(요청-응답,fastify-DB 등 )
- 깃허브 활용을 정말 정말 잘해야겠다. 깃허브를 포트폴리오로 제출 할 때 커밋 메시지를 본다고 한다. 레포를 다 볼 수 없으니.. 영어도 잘 해야하나 싶다.
- GET은 어느정도 구현할 수 있겠으나, 이 마저도 시간이 오래 걸렸다. POST, DELETE, PUT 구현도 다시 공부해야한다.
- 아직 CS 지식도 많이 딸리니깐 복습 철저히 해보자
- KPT 회고법으로 다음엔 해봐야겠다.
  - KPT 는 회고 과정중에 진행하는 한 부분입니다. Keep/Problem/Try 는 다음을 의미합니다.

    - Keep : 잘하고 있는 점. 계속 했으면 좋겠다 싶은 점.
    - Problem : 뭔가 문제가 있다 싶은 점. 변화가 필요한 점.
    - Try : 잘하고 있는 것을 더 잘하기 위해서, 문제가 있는 점을 해결하기 위해서 우리가 시도해 볼 것들
- 모르는 것을 모른다고 당당하게 말하며 물어보라
- GPT를 잘 활용하자도 중요하지만, '잘 쓰는 방법'을 알아야하는 것! 명심

