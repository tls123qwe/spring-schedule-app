# 📡 API 상세 명세

- 모든 요청과 응답은 `Content-Type: application/json`을 사용합니다.
- 🔒 표시 API는 로그인 후 발급된 세션 쿠키(`JSESSIONID`)가 필요합니다.

---

## 1. 인증

### 회원가입
`POST /auth/signup`

```json
{
  "userName": "요시",
  "email": "yoshi@test.com",
  "password": "password1234"
}
```

**Response `201 Created`**
```json
{
  "id": 1,
  "userName": "요시",
  "email": "yoshi@test.com",
  "createdAt": "2026-07-09T12:00:00",
  "modifiedAt": "2026-07-09T12:00:00"
}
```

| Code | Description |
| --- | --- |
| 201 Created | 회원가입 성공 |
| 400 Bad Request | 이미 사용 중인 이메일 |

### 로그인
`POST /auth/login`

```json
{
  "email": "yoshi@test.com",
  "password": "password1234"
}
```

**Response `200 OK`** (본문 없음, 세션 쿠키 발급)

| Code | Description |
| --- | --- |
| 200 OK | 로그인 성공 |
| 400 Bad Request | 존재하지 않는 유저 / 비밀번호 불일치 |

### 로그아웃
`POST /auth/logout`

**Response `204 No Content`**

---

## 2. 유저

### 전체 조회 / 단건 조회
`GET /users`  
`GET /users/{userId}`

**Response `200 OK`**
```json
{
  "userId": 1,
  "userName": "요시",
  "email": "yoshi@test.com",
  "createdAt": "2026-07-09T12:00:00",
  "modifiedAt": "2026-07-09T12:00:00"
}
```
전체 조회는 위 객체의 배열을 반환합니다.

### 수정
`PUT /users/{userId}`

```json
{
  "userName": "요시2",
  "email": "yoshi2@test.com",
  "password": "password1234"
}
```
`password`는 본인 확인용이며, 이름과 이메일만 변경됩니다.

**Response `200 OK`** — 유저 응답과 동일

### 삭제
`DELETE /users/{userId}`

```json
{
  "password": "password1234"
}
```

**Response `204 No Content`**

---

## 3. 일정

### 생성 🔒
`POST /schedules`

작성자는 세션의 로그인 정보로 지정되므로 요청에 포함하지 않습니다.

```json
{
  "title": "스프링 공부",
  "contents": "세션 기반 일정 생성 구현",
  "password": "1234"
}
```

**Response `201 Created`**
```json
{
  "id": 1,
  "author": "요시",
  "title": "스프링 공부",
  "contents": "세션 기반 일정 생성 구현",
  "createdAt": "2026-07-09T12:00:00",
  "modifiedAt": "2026-07-09T12:00:00"
}
```

### 전체 조회 / 단건 조회 / 유저별 조회
`GET /schedules`  
`GET /schedules/{scheduleId}`  
`GET /users/{userId}/schedules`

일정 응답에는 해당 일정의 댓글 목록이 포함됩니다.

**Response `200 OK`**
```json
{
  "id": 1,
  "author": "요시",
  "title": "스프링 공부",
  "contents": "세션 기반 일정 생성 구현",
  "createdAt": "2026-07-09T12:00:00",
  "modifiedAt": "2026-07-09T12:00:00",
  "comments": [
    {
      "commentId": 1,
      "userName": "요시",
      "contents": "화이팅!",
      "createdAt": "2026-07-09T12:10:00",
      "modifiedAt": "2026-07-09T12:10:00"
    }
  ]
}
```
전체 조회와 유저별 조회는 위 객체의 배열을 반환합니다.

### 수정 🔒
`PUT /schedules/{scheduleId}`

로그인한 유저가 작성자이고, 일정 비밀번호가 일치할 때만 수정됩니다.

```json
{
  "title": "수정된 일정",
  "contents": "수정된 내용",
  "password": "1234"
}
```

**Response `200 OK`** — 생성 응답과 동일한 형식

| Code | Description |
| --- | --- |
| 200 OK | 수정 성공 |
| 400 Bad Request | 비밀번호 불일치 |
| 401 Unauthorized | 로그인 필요 |
| 403 Forbidden | 작성자가 아님 |
| 404 Not Found | 존재하지 않는 일정 |

### 삭제
`DELETE /schedules/{scheduleId}`

```json
{
  "password": "1234"
}
```

**Response `204 No Content`**

---

## 4. 댓글

### 생성 🔒
`POST /schedules/{scheduleId}/comments`

```json
{
  "contents": "화이팅!"
}
```

**Response `201 Created`**
```json
{
  "commentId": 1,
  "scheduleId": 1,
  "userName": "요시",
  "contents": "화이팅!",
  "createdAt": "2026-07-09T12:10:00",
  "modifiedAt": "2026-07-09T12:10:00"
}
```

### 일정별 조회 / 단건 조회
`GET /schedules/{scheduleId}/comments`  
`GET /schedules/{scheduleId}/comments/{commentId}`

**Response `200 OK`**
```json
{
  "commentId": 1,
  "userName": "요시",
  "contents": "화이팅!",
  "createdAt": "2026-07-09T12:10:00",
  "modifiedAt": "2026-07-09T12:10:00"
}
```
일정별 조회는 위 객체의 배열을 반환합니다.

### 수정
`PUT /schedules/{scheduleId}/comments/{commentId}`

```json
{
  "contents": "수정된 댓글"
}
```

**Response `200 OK`** — 단건 조회와 동일한 형식

### 삭제
`DELETE /schedules/{scheduleId}/comments/{commentId}`

**Response `204 No Content`**
