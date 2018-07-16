#  **스윗트래커 배송추적 API v0.3**

​



​        																  작성자 : 허재성

​															     작성일자 : 2018–02-28









​


####1.  개요

*    본 문서는 배송정보연계를 위한 API로 스윗트래커와의 계약을 통해 배송정보 연동을 원하는 파트너에게 제공되는 API문서입니다.

* 본 문서는 https 프로토콜을 통해 데이터를 POST method를 사용하여 전송하고, json 형식의 결과 데이터를 parsing 하여 처리할 수 있음을 전제로 작성되었습니다.

  ​

####2.  선결조건

   파트너는 배송연계 API를 사용하여 메시지 전송을 하기 위해 아래의 조건이 선결되어야 합니다.

*    파트너는 스윗트래커의 담당자를 통해 메시지 전송을 위한 아이디와 식별을 위한 키를 전달 받아야 하니다.

     ​

####3.  API스펙

##### 3.1 API 서버 Host

* 개발서버

  https://dev-tracking-api.sweettracker.net


* 운영서버

  https://tracking-api.sweettracker.net

  ​

##### 3.2 서비스 흐름(시퀀스)

````sequence
Title: 배송추적 시퀀스
파트너->스윗트래커: 배송추적 요청 POST
스윗트래커->파트너: 요청수신 결과 JSON
스윗트래커->택배사: 트래킹 정보 요청
택배사->스윗트래커: 트래킹 정보 수집
스윗트래커->파트너: 트래킹 정보 수신 POST
파트너->스윗트래커: 정보수신 결과 JSON
````



##### 3.3 운송장 추적 요청 API

배송추적을 요청하기 위해 운송장 정보를 API를 통해 전송합니다.

[Request]

* path : {host}/add_invoice

* method : POST

* parameter

  | name         | type   | desc                  |
  | ------------ | ------ | --------------------- |
  | num          | string | 운송장번호(공백또는 "-"제거)     |
  | code         | string | 배송사 코드                |
  | fid          | string | 해당 건의 결과 전송에 쓰이는 식별 값 |
  | callback_url | string | 결과를 전달받을 URL          |
  | tier         | string | 발급받은 tier             |
  | key          | string | 발급받은 key              |

* example

  ````html
  <form action="https://dev-tracking-api.sweettracker.net/add_invoice" method="post">
      <input name="num" value="1234567890">
      <input name="code" value="04">
      <input name="fid" value="100000123">
      <input name="callback_url" value="http://api.sweettracker.net/getdata">
      <input name="tier" value="sweet">
      <input name="key" value="2rjf8sffdsb">
      <button>Send</button>
  </form>
  ````

[Response]

응답바디는 JSON 객체로 아래 값을 참고 해주세요.

* parameter

  | name      | type    | desc              |
  | --------- | ------- | ----------------- |
  | success   | boolean | 성공여부(true, false) |
  | num       | string  | 운송장 번호            |
  | fid       | string  | 식별 값              |
  | e_code    | string  | 에러메시지 코드          |
  | e_message | string  | 에러메시지             |

* example

  **성공:**

  ````json
  { 
      “success”:true, 
      “num”:”1234567890” 
      “fid”: “11112” 
  } 
  ````

  **실패:**

  ````json
  { 
      “success”:false, 
      “num”:”304329318403”, 
      “fid”: “11112”, 
      “e_message”:”송장번호 규칙에 어긋납니다.”, 
      “e_code”:”02” 
  }
  ````

  ​

[ErrorCode]

운송장 추적 요청시 리턴되는 에러값은 아래를 참조해주세요.

| code | desc               |
| ---- | ------------------ |
| 01   | 필수 파라메터가 빈 값이거나 없음 |
| 02   | 운송장번호 유효성 검사 실패    |
| 03   | 이미 등록된 운송장번호       |
| 04   | 지원하지 않는 택배사 코드     |
| 99   | 시스템 오류             |

##### 3.4 운송장 추적정보 수신API(Callback_URL)

배송추적정보를 수신하기위해 파트너쪽에서 Callback_URL을 아래의 스펙에 맞춰 개발을 진행해주셔야 합니다.

[Request]

* method : POST

* encoding : UTF-8

* parameter

  | name         | type        | desc                                  |
  | ------------ | ----------- | ------------------------------------- |
  | secret_value | string(100) | 인증키(현재사용안함)                  |
  | fid          | string(30)  | 식별값                                |
  | invoice_no   | string(50)  | 운송장번호                            |
  | level        | int         | 배송단계(1~6단계), -99 배송 스캔 오류 |
  | time_trans   | datetime    | 택배사 처리시간                       |
  | time_sweet   | datetime    | 스윗트래커 등록시간                   |
  | where        | string(100) | 택배 위치                             |
  | telno_office | string(50)  | 사업소 기반 전화번호                  |
  | telno_man    | string(50)  | 배송기사 전화번호                     |
  | details      | string(255) | 배송상세 정보                         |
  | recv_addr    | string(100) | 수취인 주소                           |
  | recv_name    | string(100) | 수취인 이름                           |
  | send_name    | string(100) | 발신인 이름                           |
  | man          | string(50)  | 배송기사 이름                         |

* example

  ````html
  <form action="{Callback_URL}" method="post">
      <input name="secret_value" value="yqzaciv0zlw5rtjt">
      <input name="fid" value="10000000507577001">
      <input name="courier_code" value="04">
      <input name="invoice_no" value="692637908420">
      <input name="level" value="6">
      <input name="time_trans" value="2016-05-05 20:59:45">
      <input name="time_sweet" value="2016-05-05 21:46:54">
      <input name="where" value="충남천안청당">
      <input name="telno_office" value="041-584-8502">
      <input name="telno_man" value="01077417325">
      <input name="details" value="배달완료">
      <input name="recv_addr" value="서울시 중구 ***">
      <input name="recv_name" value="장영*">
      <input name="send_name" value="홍길*">  
      <input name="man" value="배달원">    
      <button>Send</button>
  </form>
  ````
  스캔 오류일때 level값 -99로 전달
  ````html
  <form action="{Callback_URL}" method="post">
      <input name="secret_value" value="yqzaciv0zlw5rtjt">
      <input name="fid" value="10000000507577001">
      <input name="courier_code" value="04">
      <input name="invoice_no" value="692637908420">
      <input name="level" value="-99">
      <input name="time_trans" value="2016-05-05 20:59:45">
      <input name="time_sweet" value="2016-05-05 21:46:54">
      <input name="where" value="충남천안청당">
      <input name="telno_office" value="041-584-8502">
      <input name="telno_man" value="01077417325">
      <input name="details" value="배달완료">
      <input name="recv_addr" value="서울시 중구 ***">
      <input name="recv_name" value="장영*">
      <input name="send_name" value="홍길*">  
      <input name="man" value="배달원">    
      <button>Send</button>
  </form>
  ````
  ​

[Response]

응답바디는 JSON 객체로 아래 값을 참고 해주세요.

* parameter

  | name    | type    | desc                |
  | ------- | ------- | ------------------- |
  | code    | boolean | 성공여부(true,false)    |
  | message | string  | 실패사유(success, fail) |

* example

  **성공:**

  ```json
  { 
      “code”:true, 
      “message”:”success” 
  } 
  ```
  ​
  ​

  **실패:**

  ```json
  { 
  	“code”:false, 
      “message”:”fail - invalid fid” 
  }
  ```


##### 3.5 운송장번호 유효성 체크

배송사의 운송장번호 유효성 체크를 위해 API를 사용합니다.

[Request]

- path : {host}/validate

- method : POST

- parameter

  | name | type   | desc              |
  | ---- | ------ | ----------------- |
  | num  | string | 운송장번호(공백또는 "-"제거) |
  | code | string | 배송사 코드            |
  |      |        |                   |

- example

  ```html
  <form action="https://dev-tracking-api.sweettracker.net/validate" method="post">
      <input name="num" value="1234567890">
      <input name="code" value="04">
      <button>Send</button>
  </form>
  ```

[Response]

응답바디는 JSON 객체로 아래 값을 참고 해주세요.

- parameter

  | name    | type    | desc              |
  | ------- | ------- | ----------------- |
  | success | boolean | 성공여부(true, false) |
  | num     | string  | 운송장 번호            |
  | code    | string  | 배송사 코드            |

  ​

- example

  **성공:**

  ```json
  { 
      “success”:true, 
      “num”:”1234567890” 
      “code”: “04” 
  } 
  ```

  **실패:**

  ```json
  { 
      “success”:false, 
      “num”:”1234567890”, 
      “code”: “04”
  }
  ```

  ​



#### 4.  코드정의

##### 4.1 배송사코드

| code | 배송사              |
| ---- | ------------------- |
| 01   | 우체국              |
| 04   | CJ대한통운          |
| 05   | 한진택배            |
| 06   | 로젠택배            |
| 08   | 롯데택배            |
| 11   | 일양로지스          |
| 12   | EMS                 |
| 13   | DHL                 |
| 14   | UPS                 |
| 15   | GTX                 |
| 16   | 한의사랑택배        |
| 17   | 천일택배            |
| 18   | 건영택배            |
| 19   | 고려택배            |
| 20   | 한덱스              |
| 21   | Fedex               |
| 22   | 대신택배            |
| 23   | 경동택배            |
| 24   | CVSnet 편의점 택배  |
| 25   | TNT Express         |
| 26   | USPS                |
| 27   | TPL                 |
| 28   | GSMNtoN             |
| 29   | 에어보이익스프레스  |
| 30   | KGL네트웍스         |
| 32   | 합동택배            |
| 33   | DHL Global Mail     |
| 34   | i-Parcel            |
| 35   | 포시즌 익스프레스   |
| 37   | 범한판토스          |
| 38   | APEX                |
| 39   | 드림택배            |
| 40   | 굿투럭              |
| 41   | GSI Express         |
| 42   | CJ대한통운 국제특송 |
| 44   | SLX                 |
| 45   | 호남택배            |
| 52   | 세방            |
| 99   | 롯데택배 해외특송   |


  ​


#### 4.1 배송사코드

| level | desc      |
| ----- | --------- |
| 1     | 배송준비 상태   |
| 2     | 집화완료 상태   |
| 3     | 배송진행 중 상태 |
| 4     | 지점도착 상태   |
| 5     | 배송출발 상태   |
| 6     | 배송완료 상태   |