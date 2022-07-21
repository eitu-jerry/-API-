# Clayful REST API 정리

## 공식 문서 https://dev.clayful.io/ko/http/apis

## <span style="color:#95D2EC;">Base url</span> : https://api.clayful.io/

## <span style="color:#ECBE5B;"><b>유저 생성
- POST
- v1/customers
- ### <span style="color:#F4C095;">Payload 데이터
        email             : String
        password          : String
        mobile            : String
        name              : Object
            name.full     : String

## <span style="color:#ECBE5B;"><b>유저 업데이트
- PUT
- v1/customers/{customerId}
- ### <span style="color:#F4C095;">Payload 데이터
        email             : String
        password          : String
        mobile            : String
        name              : Object
            name.full     : String
        birthdate         : Date
        gender            : String
        country           : String ("KR")
        language          : String ("ko")
        currency          : String ("KRW")

## <span style="color:#ECBE5B;"><b>유저 인증
- POST
- v1/customers/auth
- ### <span style="color:#F4C095;">Payload 데이터
        userId          : String (userId 혹은 email 만 필수)
        email           : String
        password        : String
- ### <span style="color:#FFA69E;">Response 데이터
        customer        : String (userId)
        token           : String (userToken)
        expiresIn       : Int (토큰 유효시간)

## <span style="color:#ECBE5B;"><b>유저 소셜 가입 or 로그인
- POST
- v1/customers/auth/{vendor}
- vendor : kakao, naver (소셜 계정 종류)
- ### <span style="color:#F4C095;">Payload 데이터
        token           : String (소셜 로그인 후 발급되는 authToken)
- ### <span style="color:#FFA69E;">Response
        customer        : String (userId)
        token           : String (userToken)
        expiresIn       : Int (토큰 유효시간)

## <span style="color:#ECBE5B;"><b>유저 인증 메일 발송
- POST
- v1/customers/verifications/emails
- ### <span style="color:#F4C095;">Payload 데이터
        userId          : String (userId 혹은 email 만 필수)
        email           : String
        expiresIn          : Number (600 -> 10)
        scope           : String (verification 혹은 reset-password)
- 반환값 없이 인증 링크가 포함된 메일이 발송됨
- secret 키 저장 필요

## <span style="color:#ECBE5B;"><b>유저 인증
- POST
- v1/customers/{customerId}/verified
- ### <span style="color:#F4C095;">Payload 데이터
        secret          : String (인증 메일을 통해 넘어온 secret)
- ### <span style="color:#FFA69E;">Response 데이터
        verified        : Boolean

## <span style="color:#ECBE5B;"><b>비밀번호 변경
- PUT
- v1/customers/{customerId}/password
- ### <span style="color:#F4C095;">Payload 데이터
        secret          : String (인증 메일을 통해 넘어온 secret)
        password        : String (변경할 비밀번호)
- ### <span style="color:#FFA69E;">Response 데이터
        reset           : Boolean

## <span style="color:#ECBE5B;"><b>상품 목록 호출
- GET
- v1/products
- ### <span style="color:#8FB9AA;">Query 데이터
        search:name     : String (값 없을땐 null)
        search:keywords : String (값 없을땐 null)
        sort            : String (랭킹 페이지에서 호출시 필요, -totalReview)
        
## <span style="color:#ECBE5B;"><b>상품 단일 개체 호출
- GET
- v1/products/{productId}
- productId : String (호출할 상품 id)

## <span style="color:#ECBE5B;"><b>상품 카트에 담기
- POST
- v1/customers/{customerId}/cart/items
- ### <span style="color:#F4C095;">Payload 데이터
        product         : String (상품 id)
        variant         : String (상품 세부항목 id)
        quantity        : Number (수량)
        shippingMethod  : String (배송 방식 id)
- ### <span style="color:#FFA69E;">Response 데이터
        _id             : String (카트에 담긴 항목 id)

## <span style="color:#ECBE5B;"><b>상품 카트 수량 변경
- PUT
- v1/customers/{customerId}/cart/items/{itemId}
- ### <span style="color:#F4C095;">Payload 데이터
        quantity         : Number (상품 수량)

## <span style="color:#ECBE5B;"><b>상품 카트에서 지우기
- DELETE
- v1/customers/{customerId}/cart/items/{itemId}

## <span style="color:#ECBE5B;"><b>상품 카트 목록 호출
- POST
- v1/customers/{customerId}/cart

## <span style="color:#ECBE5B;"><b>상품 카트 주문
- POST
- /v1/customers/{customerId}/cart/checkout/{type}
- type : order 혹은 subscription(정기구독)
- 샘플 주문 태그 : UHAWQN6P3Y8V
- ### <span style="color:#F4C095;">Payload 데이터
        items                                 : List of CartItem (장바구니 중 일부만 주문할 때 필요)
        currency                              : String ("KRW")
        paymentMethod                         : String ("clayful-iamport")
        address                               : Object
            address.shipping                  : Object
                address.shipping.postcode     : String
                address.shipping.country      : String
                address.shipping.city         : String
                address.shipping.address1     : String
                address.shipping.address2     : String
                address.shipping.mobile       : String
                address.shipping.name         : Object
                address.shipping.name.full    : String 
            address.billing                   : Object
                address.billing.postcode      : String
                address.billing.country       : String
                address.billing.city          : String
                address.billing.address1      : String
                address.billing.address2      : String
                address.billing.mobile        : String
                address.billing.name          : Object
                address.billing.name.full     : String 
        request                               : String
        tags                                  : List of String
- ### <span style="color:#FFA69E;">Response 데이터
        order           : Object
        - order._id     : String (결제 시 필요하므로 따로 저장)

## <span style="color:#ECBE5B;"><b>주문 내역 조회
- GET
- /v1/orders
- status != placed 인 내역만 표시 (status.equals("placed") == 결제 대기 == 결제 실패)
- ### <span style="color:#8FB9AA;">Query 데이터
        customer         : String (고객 id)
        createdAt>       : Date (주문 생성 날짜)
        
## <span style="color:#ECBE5B;"><b>주문 취소
- POST
- /v1/orders/{orderId}/cancellation
- ### <span style="color:#F4C095;">Payload 데이터
        by               : String (결제 실패시 store, 고객 취소시 customer)
        reason           : String (주문 취소 사유)
        
## <span style="color:#ECBE5B;"><b>주문 내역 삭제
- DELETE
- /v1/orders/{orderId}
- 고객이 결제 도중 결제 취소 시 주문 내역 삭제       

## <span style="color:#ECBE5B;"><b>환불 신청
- POST
- /v1/orders/{orderId}/refunds
- ### <span style="color:#F4C095;">Payload 데이터
        reason                  : String (주문 취소 사유)
        items                   : List of Object
                items.item      : String (환불할 항목의 id)
                items.quantity  : Number (환불할 항목의 개수)
        shipments               : List of String (환불할 배송비 리스트)

## <span style="color:#ECBE5B;"><b>상품 리뷰 조회
- GET
- /v1/products/reviews/published
- ### <span style="color:#8FB9AA;">Query 데이터
        product         : String (조회할 상품 id)

## <span style="color:#ECBE5B;"><b>상품 리뷰 생성
- POST
- /v1/products/reviews
- ### <span style="color:#F4C095;">Payload 데이터
        order           : String (주문에 대한 리뷰 작성시 필요, 없으면 null)
        product         : String (상품 id)
        customer        : String (고객 id)
        title           : String
        body            : String
        images          : List of String
        rating          : Number (0.0 ~ 5.0)
        published       : Boolean (샘플로드 리뷰의 경우 검수 후 노출이 필수, false 고정)
        
## <span style="color:#ECBE5B;"><b>이미지 업로드
- https://www.notion.so/83f3f024c1884e219abf7ace06c79f9d
- POST
- /v1/images
- Content-Type: multipart/form-data
- ### <span style="color:#F4C095;">Payload 데이터
        model           : Form data (리뷰 이미지 올릴 시 Review로 지정)
        application     : Form data (리뷰 이미지 올릴 시 images로 지정)
        customer        : Form data file (리뷰 이미지 파일, content-type image/jpeg 고정)

## <span style="color:#ECBE5B;"><b>아임포트 결제
- 아이폰은 강청녕 대표님께 인수인계 받을 것

# nCloud API 정리

## <span style="color:#ECBE5B;"><b>제품 상세 페이지 웹뷰
- http://110.165.17.124/sampleroad/sampleroad_chart.html
- ### 필요 파라미터
        Params0 ~ Params4 : Int (추후 이름은 변경될 수 있음)
        
## <span style="color:#ECBE5B;"><b>제품 상세 페이지 찜하기
- http://110.165.17.124/sampleroad/db/sr_product_pick.php
- ### 필요 파라미터
        change          : Any (제품 찜 여부를 변경할때만 파라미터 넘기기, 단순 조회일때는 안넘김)
        customer_id     : String
        product_id      : String
- ### Response
        error           : String
        - "0"           : (change != null ? 찜 변경 실패 : 찜 목록에 없음)
        - "1"           : (change != null ? 찜 목록에 추가 : 찜 목록에 있음)
        - "2"           : (찜 목록에서 삭제)

## <span style="color:#ECBE5B;"><b>이벤트 리스트 조회
- http://110.165.17.124/sampleroad/db/sr_event_select.php

## <span style="color:#ECBE5B;"><b>이벤트 참여
- http://110.165.17.124/sampleroad/db/sr_event_insert.php
- ### 필요 파라미터
        customer_id     : String
        event_id        : String
- ### Response
        error           : String
        - "0"           : 알 수 없는 이유로 insert 실패 
        - "1"           : 성공
        - "2"           : 이미 지원한 이벤트인 경우
        - "3"           : 이벤트 정원이 초과된 경우

## <span style="color:#ECBE5B;"><b>알람 조회
- http://110.165.17.124/sampleroad/db/sr_alarm_select.php
- ### 필요 파라미터
        customer_id     : String
        

## <span style="color:#ECBE5B;"><b>알람 닫기
- http://110.165.17.124/sampleroad/db/sr_alarm_checked_insert.php
- ### 필요 파라미터
        customer_id     : String
        alarm_id        : String
- ### Response
        error           : String
        - "0"           : 알 수 없는 이유로 insert 실패 
        - "1"           : 성공
        - "2"           : 이미 유저가 닫은 알람
